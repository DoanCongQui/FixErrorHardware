Cách fix lỗi và update cho một số máy tính nhúng 

`Test Camera`

```
import cv2 as cv
import numpy as np
import logging
import time as tm

from time import time
# from Motor import Motor

# motor = Motor(17, 27, 22, 23, 24, 25)

color1 = (255, 0, 0)
color2 = (0, 255, 0)
color3 = (0, 0, 255)

# Xác định vùng trung tâm 
def region_of_interest(edges):
    mask = np.zeros_like(edges)

    # Lấy 4 điểm vùng trung tâm 
    polygon_255 = np.array([[
        (250, 720), # bottom-left 
        (600, 450), # top-left 
        (779, 447), # top-right 
        (1125, 700) # bottom-left
    ]], np.int32)

    cv.fillPoly(mask, polygon_255, 255)
    cropped_edges = cv.bitwise_and(edges, mask)
    return cropped_edges

# Thuận toán Canny 
def canny(image):
    gray = cv.cvtColor(image, cv.COLOR_RGB2GRAY)
    blur = cv.GaussianBlur(gray, (3, 3), 0)
    canny = cv.Canny(blur, 200, 400)
    return canny

# Thuận toán HoughTransform
def detect_line_segments(cropped_edges):
    rho = 1 
    angle = np.pi / 180  
    min_threshold = 10 
    line_segments = cv.HoughLinesP(cropped_edges, rho, angle, min_threshold, 
                                    np.array([]), minLineLength=10, maxLineGap=15)

    return line_segments

# Tính toán các giá trị lane trái và phải
def make_points(frame, line):
    height, width, _ = frame.shape
    slope, intercept = line
    y1 = height
    y2 = int(y1 * 3/10)  
    x1 = max(-width, min(2 * width, int((y1 - intercept) / slope)))
    x2 = max(-width, min(2 * width, int((y2 - intercept) / slope)))
    return [[x1, y1, x2, y2]]

def average_slope_intercept(frame, line_segments):
    lane_lines = []
    if line_segments is None:
        logging.info('No line_segment segments detected')
        return lane_lines

    height, width, _ = frame.shape
    left_fit = []
    right_fit = []

    boundary = 1/3
    left_region_boundary = width * (1 - boundary)  # left lane line segment should be on left 2/3 of the screen
    right_region_boundary = width * boundary # right lane line segment should be on left 2/3 of the screen

    for line_segment in line_segments:
        for x1, y1, x2, y2 in line_segment:
            if x1 == x2:
                logging.info('skipping vertical line segment (slope=inf): %s' % line_segment)
                continue
            fit = np.polyfit((x1, x2), (y1, y2), 1)
            slope = fit[0]
            intercept = fit[1]
            if slope < 0:
                if x1 < left_region_boundary and x2 < left_region_boundary:
                    left_fit.append((slope, intercept))
            else:
                if x1 > right_region_boundary and x2 > right_region_boundary:
                    right_fit.append((slope, intercept))

    left_fit_average = np.average(left_fit, axis=0)
    if len(left_fit) > 0:
        lane_lines.append(make_points(frame, left_fit_average))

    right_fit_average = np.average(right_fit, axis=0)
    if len(right_fit) > 0:
        lane_lines.append(make_points(frame, right_fit_average))

    return lane_lines

# Hiển thị lane đường 
def display_lines(frame, lines, lineColor=(0, 255, 255), lineWidth=10):
    line_image = np.zeros_like(frame)
    if lines is not None:
        for line in lines:
            for x1, y1, x2, y2 in line:
                cv.line(line_image, (x1, y1), (x2, y2), lineColor, lineWidth)
    line_image = cv.addWeighted(frame, 0.8, line_image, 1, 1)
    return line_image

# Điều khiển xe 
def direction_control(index, min, max, a):
    # Điều kiện lane nằm trong phạm vi xử lý 
    if index <= max and index >= min:
        if index > a and index <= 100:
            print('Turn right')
            # motor.phai(speed=(index/100), t=0.5)
        elif index < -a and index >= -100:
            print('Turn left')
            # motor.trai(speed=abs(index/100), t=0.5)
        else:
            print('Go straight')
            # motor.go_straight()

    # Đìều kiện lane ngoài vùng xử lý
    else:
        if index > 100:
            print('Turn right')
            # motor.phai(speed=1, t=0.5)
        else:
            print('Turn left')
            # motor.trai(speed=1, t=0.5)

# Tính độ lệch của lane 
def calculate_deviation(valueLane, fixedPont):
    # Kiểm tra xem có gia trị lane hay không
    if valueLane is None or not valueLane:
        return None 

    x1, y1, x2, y2 = valueLane[0]
    xP, yP = fixedPont[0]

    # Tính giá trị lệch     
    if ((y1 + y2) / 2) == yP:
        deviation = xP - x1

    return deviation

# Hiển thị độ lệch của lane ra màn hình
def displayDeviation(frame, lane_lines, fixedPont):

    font = cv.FONT_HERSHEY_SIMPLEX
    font_scale = 1
    thickness = 2

    # Kiểm tra line có None không
    if lane_lines is None or not lane_lines:
        return None
    deviation = calculate_deviation(lane_lines[0], fixedPont)

    # Tính đường công và in ra màn hình nếu giá trị lệch không None
    # if deviation is None:
    #     return None
    
    cv.putText(frame, '|', (deviation, 468), font, font_scale, color2, thickness, cv.LINE_AA)
    cv.putText(frame, 'Deviation: ' + str(deviation), (100, 100), cv.FONT_HERSHEY_SIMPLEX, 1, (0, 0, 255), 2, cv.LINE_AA)
    return deviation

# Thanh ngan cố định 
def draw_dot(frame, point):
    # Tọa độ của hai điểm
    xa, xb = point[0]
    point1 = (xa, 468)
    point2 = (xb, 468)

    font = cv.FONT_HERSHEY_SIMPLEX
    font_scale = 1
    thickness = 1
    midpoint = (int((point1[0] + point2[0]) / 2), int((point1[1] + point2[1]) / 2))

    cv.line(frame, point1, point2, color1, thickness)
    cv.putText(frame, '|', (midpoint[0]-5, midpoint[1] +10), font, font_scale, color1, thickness, cv.LINE_AA)

# Điều khiển động cơ 
def run(laneLine, fixPoint):
    if laneLine and len(laneLine) > 0:
        deviation = calculate_deviation(laneLine[0], fixPoint)
        direction_control(deviation, -100, 100, 10)
    else:
        print("No lane lines detected")


# Đọc camera với khung hình 1280x720
# camera = cv.VideoCapture("project_video.mp4") 
camera = cv.VideoCapture(0) 
frame_width = 1280
frame_height = 720
camera.set(cv.CAP_PROP_FRAME_WIDTH, frame_width)
camera.set(cv.CAP_PROP_FRAME_HEIGHT, frame_height)

if not camera.isOpened():
    logging.error('Không có camera')
    exit()

if __name__ == '__main__':
    print('Started')
    now = time()

    fixed_line = [[670, 468]]
    point = [[540, 740]]

    while True:
        try:
            ret, frame = camera.read()
            if not ret:
                logging.error('Không đọc được frame')
                break
            
            # Gọi các hàm xử lý 
            canny_edges = canny(frame)
            roi_image = region_of_interest(canny_edges)
            line_segments = detect_line_segments(roi_image)
            lane_lines = average_slope_intercept(frame, line_segments)
            line_image = display_lines(frame, lane_lines)
            deviation = displayDeviation(line_image, lane_lines, fixed_line)
            
            # Read value theo giây 
            if time() - now >= 0.5:
                now = time()
                run(lane_lines, fixed_line)
            
            # Đưa ra màn hình 
            draw_dot(line_image, point)   
            cv.imshow('Line Image', line_image)
            
            if cv.waitKey(1) & 0xFF == ord('q'):
                break

        except KeyboardInterrupt:
            break

    # Giải phóng dung lượng 
    camera.release()
    cv.destroyAllWindows()
    print('Stopped')

```
