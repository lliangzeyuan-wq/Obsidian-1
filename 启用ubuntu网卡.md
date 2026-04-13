---
data: 2026-03-13
---
### 网卡
`sudo ip link set ens33 up`


`sudo dhclient ens33`


`ip addr`




### 中文输入法启用
`fcitx5 &`


我现在测试后端 API 时发现： 1. 注册接口 /api/register 测试成功，可以正常写入数据库； 2. 登录接口 /api/login 测试返回 "接口不存在: /api/login"，登录失败。 请帮我排查并修复以下问题： 1. 在 server.cpp 中补全 /api/login 登录接口逻辑（包括学生ID和密码的验证、Token生成）； 2. 确保接口路径正确，路由注册成功； 3. 测试该接口，确保能正常返回登录成功信息和Token。 另外，确认你的修复后，我会再次执行 curl 命令测试，请给出确认能通的接口代码。