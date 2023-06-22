Python

## 在windows操作系统下，使用VsCode创建Python虚拟环境报错，原因是PowerShell安全协议。

可以打开PowerShell，执行以下命令，修改一下参数
```
 Set-ExecutionPolicy -Scope CurrentUser
 
 RemoteSigned

y
```


```

PS C:\Windows\system32> Set-ExecutionPolicy -Scope CurrentUser

位于命令管道位置 1 的 cmdlet Set-ExecutionPolicy
请为以下参数提供值:
ExecutionPolicy: y
Set-ExecutionPolicy : 无法绑定参数“ExecutionPolicy”。无法将值“y”转换为类型“Microsoft.PowerShell.ExecutionPolicy”
。错误:“无法将标识符名称 y 与有效的枚举器名称相匹配。请指定以下枚举器名称之一，然后重试:
Unrestricted, RemoteSigned, AllSigned, Restricted, Default, Bypass, Undefined”
所在位置 行:1 字符: 1
+ Set-ExecutionPolicy -Scope CurrentUser
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (:) [Set-ExecutionPolicy]，ParameterBindingException
    + FullyQualifiedErrorId : CannotConvertArgumentNoMessage,Microsoft.PowerShell.Commands.SetExecutionPolicyCommand

PS C:\Windows\system32> Set-ExecutionPolicy -Scope CurrentUser

位于命令管道位置 1 的 cmdlet Set-ExecutionPolicy
请为以下参数提供值:
ExecutionPolicy: RemoteSigned

执行策略更改
执行策略可帮助你防止执行不信任的脚本。更改执行策略可能会产生安全风险，如 https:/go.microsoft.com/fwlink/?LinkID=135170
中的 about_Execution_Policies 帮助主题所述。是否要更改执行策略?
[Y] 是(Y)  [A] 全是(A)  [N] 否(N)  [L] 全否(L)  [S] 暂停(S)  [?] 帮助 (默认值为“N”): y
PS C:\Windows\system32>
```

## 在vscode创建的虚拟环境里安装第三方库函数
终端命令进入虚拟环境所在的目录
```
D:\workspace_vs_code\.venv\Scripts> pip install numpy
```

如果进入该目录下，安装的第三方库函数安装到了全局环境，那么可能是虚拟环境没有激活

如果您想在您的虚拟环境中安装NumPy，请按照以下步骤操作：

1、首先，确保您已经激活了目标虚拟环境。请在终端中执行适当的激活命令，例如：

在 Windows 上：
```
.venv\Scripts\activate
```
在 macOS 和 Linux 上：
```
source .venv/bin/activate
```
这将激活您的虚拟环境，使得所有后续操作都在该环境中进行。

2、确认您已经激活虚拟环境后，输入以下命令来安装NumPy：

```
pip install numpy
```
这将使用虚拟环境中的pip工具来安装NumPy包。

3、等待安装完成。一旦安装成功，NumPy将会在您的虚拟环境中可用。


