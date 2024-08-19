# Fix Media Video Ubuntu 
```
sudo apt update
sudo apt install ubuntu-restricted-extras
sudo apt install gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

# Fix arm-linux-gcc
Trên hệ thống Ubuntu/Debian, bạn có thể cài đặt phiên bản 32-bit của thư viện libz.so.1 bằng lệnh sau:
```
sudo apt-get install libz1:i386
sudo apt-get install zlib1g:i386
```
Sửa lại để trỏ đến thư viện 32-bit hoặc thêm thư mục chứa thư viện 32-bit
```
export LD_LIBRARY_PATH=/path/to/32-bit/libraries:$LD_LIBRARY_PATH
```
Check: `echo $LD_LIBRARY_PATH`
