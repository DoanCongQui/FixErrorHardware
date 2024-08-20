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
