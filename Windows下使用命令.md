
# <p align="center">Windows下使用命令</p>

- [01丨使用命令sc.exe查看服务状态管理服务](#01)   
- [02丨数据库初始化](#02)   







---
---
---
<h2 id="01">01丨使用命令sc.exe查看服务状态管理服务</h2>

---

点击开始>运行>输入"cmd"回车，然后在弹出的DOS窗口中输入sc回车就可以看到sc命令的使用帮助了。

　　**sc命令的语法格式**：

　　sc <server> [command] [service name] <option1> <option2>...   

　　**sc命令使用例子**：

```
# 查看所有服务的运行状态
[sc query]   

# 查看某个服务的运行状态
[sc query 服务名]

# 查看某个服务的配置信息
[sc qc 服务名]       

# 启动服务
[sc start 服务名]

# 停止服务
[sc stop 服务名]    

# 删除服务
[sc delete 服务名]    

# 修改服务启动类型。start参数的值可以是demand（手动）、disabled（禁用），auto（自动）
# 注意：start=后面有一个空格
[sc config 服务名 start= auto|demand|disabled]
```


**使用提示**:

1. 如果服务名称中包含有空格，记得在服务名称上加引号。例如sc stop "my service"。

2. “服务名称”和“服务显示名称”是不一样的。sc指令使用的是“服务名称”。
我们通过控制面板=>“管理工具"=>打开"服务"，我们看到服务的显示名称，双击打开某个服务可以看到真正的服务名字。

3. sc start 和 sc stop 功能上类似于 net start 和 net stop，但速度更快且能停止的服务更多。

4. sc delete 命令的实质都是删除HKEY_LOCAL_MACHINE\ SYSTEM\ CurrentControlSet\ Services下的ServiceName分支。所以你也可以用reg命令删除名为ServiceName的服务：
```
reg delete HKLM\ SYSTEM\ CurrentControlSet\ Services\ ServiceName
```
