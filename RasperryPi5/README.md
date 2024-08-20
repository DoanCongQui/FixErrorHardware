# Setup VNC cho Rasperry Pi 5 

Sau đây là link hướng dẫn:
[Setup VNC](https://linuxopsys.com/setup-x11vnc-on-ubuntu)

Nhập lệnh `sudo ufw enable` trước `sudo ufw reload`

Fix lightdm Pi5
```
https://askubuntu.com/questions/1502950/raspi5-23-10-cannot-switch-to-lightdm-from-gdm3
```
# Chuyển file giữa Linux và raspberry
Kết nối với local raspberry
```
sftp pi@raspberry.local
```
- `put:`Chuyển từ Linux ==> Ras

- `get:`Chuyển từ Ras ==> Linux
