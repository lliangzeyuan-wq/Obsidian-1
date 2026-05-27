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
