# Setup VNC cho Rasperry Pi 5 

Sau đây là link hướng dẫn:
[Setup VNC](https://linuxopsys.com/setup-x11vnc-on-ubuntu)

Nhập lệnh `sudo ufw enable` trước `sudo ufw reload`

Fix lightdm Pi5
```
https://askubuntu.com/questions/1502950/raspi5-23-10-cannot-switch-to-lightdm-from-gdm3
```
# Chuyển file giữa Linux và raspberry
## Sử dụng SFTP 
Kết nối với local raspberry
```
sftp pi@raspberry.local
```
- `put`: Chuyển từ Linux ==> Ras

- `get`: Chuyển từ Ras ==> Linux

- `exit`: Thoát


## Sử dụng rsync (Remote Sync)

Đồng bộ hóa file từ máy Linux đến Raspberry Pi:

```
rsync -avz /path/to/local/dir/ pi@raspberrypi.local:/path/to/remote/dir/
```

Đồng bộ hóa file từ Raspberry Pi về máy Linux:
```
rsync -avz pi@raspberrypi.local:/path/to/remote/dir/ /path/to/local/dir/
```

## Sử dụng GUI
Download
```
sudo apt-get install filezilla
```
Kết nối vào Ras xem port
```
sudo ss -tuln
```
# Cấu hình ip tĩnh cho Raspberry Pi 
Install dhcpcd và kiểm tra xem hoạt động chưa - Chạy từng lệnh
```
sudo apt install dhcpcd
sudo systemctl start dhcpcd
sudo systemctl startus dhcpcd
```

Chạy lệnh `sudo vim /etc/wpa_supplicant/wpa_supplicant.conf` vào cấu hình lại mạng hãy nhập đúng tên mạng và mật khẩu wifi hiện tại 
- Thêm dòng sau vào cuối dòng và lưu file lại
```
network={
    ssid="Tên_Mạng_WiFi"
    psk="Mật_Khẩu_WiFi"
    key_mgmt=WPA-PSK
    priority=1 // Thư tự ưu tiên
}
```
- Tiếp tục `sudo vim /etc/dhcpcd.conf` đặt đúng tên mạng đã viết ở trên và địa chỉ ip mong muốn
```
interface wlan0
ssid <Ten Mang>
static ip_address=192.168.1.<IP>/24
static routers=192.168.1.1
static domain_name_servers=192.168.1.1 8.8.8.8
```
- Cuối cùng restart dhcpcd `sudo systemctl restart dhcpcd` và `Reboot`

```
sudo nmcli dev wifi connect "Banh Mi" password "123@Abcd"
```

