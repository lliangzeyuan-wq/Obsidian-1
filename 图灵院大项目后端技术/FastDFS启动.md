- lzy (非root用户)  
- ip：192.168.198.132

- 进入部署目录
cd /home/dfs/docker/dockerfile_local
- 补目录权限（避免启动报错）
sudo chmod -R 777 ./conf ./storage ./tracker
- 启动两个 FastDFS 容器
```
# 先启动调度服务（必须先启）
sudo docker start fdfs_tracker

# 再启动存储服务
sudo docker start fdfs_storage
```
- 验证启动结果
sudo docker ps

**成功标准**：列表中出现 `fdfs_tracker` 和 `fdfs_storage` 两个容器，状态均显示为 `Up`。