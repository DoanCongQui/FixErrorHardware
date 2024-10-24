# **Tắt đang nhập gmd3 khi boot vao jetson** 
- Chạy lệnh `sudo vim /etc/gdm3/custom.conf`
```
[daemon]
# Enabling automatic login
AutomaticLoginEnable = true
AutomaticLogin = your_username
```
