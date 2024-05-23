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

## 2.2 - Pytorch

Download một số packages cần thiết
```
sudo apt update
sudo apt install -y python3.8 python3.8-venv python3.8-dev python3-pip \
libopenmpi-dev libomp-dev libopenblas-dev libblas-dev libeigen3-dev libcublas-dev
```

Tạo môi trường ảo `Python3.8`
```
python3.8 -m venv vnic
source vnic/bin/activate
```

Update 1 số packages cho môi trường ảo
```
pip install -U pip wheel gdown
```
Dowload 2 file và đưa vào thư mục có chứa môi trường ảo vừa mới cài đặt 

[Link download](https://drive.google.com/drive/folders/1zlvJ7xYmXTC3D-1JwzFo_3FsEH-eEUYU?usp=sharing)

Dowload `Pytorch`
```
pip install Cython opencv-python numpy
pip install torch-1.11.0a0+gitbc2c6ed-cp38-cp38-linux_aarch64.whl
pip install torchvision-0.12.0a0+9b5a3fe-cp38-cp38-linux_aarch64.whl
```
Sau khi cài đặt xong pytorch gọi lệnh `pip install ultralytics`

Tự động kích hoạt môi trường ảo khi `cd` vào 1 thư mục có chứa môi trường ảo

- Với bash `.bashrc`
```
cd() {
    builtin cd "$@" || return
    if [ -d "vinc" ]; then
        source vnic/bin/activate
    elif [ -d ".vnic" ]; then
        source .vnic/bin/activate
    fi
}
```

- Với zsh `.zshrc`
```
function chpwd() {
    if [ -d "vinc" ]; then
        source vnic/bin/activate
    elif [ -d ".vnic" ]; then
        source .vnic/bin/activate
    fi
}

```
