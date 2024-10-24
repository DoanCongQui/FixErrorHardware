Install các packages cần thiết
```
sudo apt update
sudo apt install -y python3.8 python3.8-venv python3.8-dev python3-pip \
libopenmpi-dev libomp-dev libopenblas-dev libblas-dev libeigen3-dev libcublas-dev
```
Clone Yolo8 trên github về máy 
```
git clone https://github.com/ultralytics/ultralytics
cd ultralytics
```
Tạo môi trường ảo cho python3.8
```
python3.8 -m venv venv
source venv/bin/activate
```
Update các gói trong pip 
```
pip install -U pip wheel gdown
```
Download pytorch & torchvision 
```
# pytorch 1.11.0
gdown https://drive.google.com/uc?id=1hs9HM0XJ2LPFghcn7ZMOs5qu5HexPXwM
# torchvision 0.12.0
gdown https://drive.google.com/uc?id=1m0d8ruUY8RvCP9eVjZw4Nc8LAwM8yuGV
```
Instal pytorch & torchvision 
```
python3.8 -m pip install torch-*.whl torchvision-*.whl
```
Install Yolo8 
```
pip install .
```
Chạy lệnh này `source /home/vnic/Downloads/ultralytics/venv/bin/activate` hoặc 
```
pythonvenv
```
