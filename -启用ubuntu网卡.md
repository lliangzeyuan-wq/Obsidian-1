---
data: 2026-03-13
---
### 网卡

- 开
sudo ip link set ens33 up
- 获取ip
sudo dhclient ens33


ip addr

# 重启网卡
- 先关
sudo ip link set ens33 down 
- 再开
sudo ip link set ens33 up
- 重新获取ip
sudo dhclient ens33


### 中文输入法启用
fcitx5 &


# 共享文件夹位置

Windows ：  /D/VM_Share

Ubuntu :  /mnt/hgfs/VM_Share   


# 阿里云个人访问令牌

pt-ZgC0xqvpRXJdl4hkBOwZ8Q8f_46de40ac-be40-4b3e-8c53-6595c04b16a0