Setup khi mới đầu vào jetson nano
```
sudo apt update -y
sudo apt upgrade -y
```

Tắt đăng nhập khi boot vào jetson nano `sudo vim /etc/gdm3/custom.conf`
```
[daemon]
# Enabling automatic login
AutomaticLoginEnable = true
AutomaticLogin = your_username
```
