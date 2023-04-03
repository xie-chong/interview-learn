Python

在windows操作系统下，使用VsCode创建Python虚拟环境报错，原因是PowerShell安全协议。

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


