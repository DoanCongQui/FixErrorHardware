# Step 1. Update bản Ubuntu cho JetsonNano Kit B01 lên phiên bản 20.04

Sau đây là link hướng dẫn setup: [Update](https://qengineering.eu/install-ubuntu-20.04-on-jetson-nano.html)

# Step 2. PATH cuda cho Jetson Nanop Kit B01

Đầu tiên mở `Terminal` và nhập lệnh `sudo vim ~/.bashrc`. Tiếp theo copy phần sau vào cuối dòng 

```
export PATH=/usr/local/cuda/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
```
Cuối cùng nhập lệnh `exec bash` và kiểm tra xem đã có cuda chưa bằng lệnh `ncvv --version`

## 2.1 - Download 
Download `jtop` Jetson Nano Kit 
```
sudo apt update
sudo pip3 install -U jetson-stats
sudo systemctl restart jtop.service   
```

Sau khi download khơi động lại

# 2.2 - Pytorch
```
https://forums.developer.nvidia.com/t/pytorch-for-jetson/72048
```


