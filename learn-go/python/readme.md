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

## 虚拟环境里如何安装jupyter lab
在激活的虚拟环境中，使用以下命令安装 JupyterLab：
```
pip install jupyterlab
```

安装成功后，使用以下命令启动 JupyterLab
```
jupyter lab

或者
jupyter lab --no-browser &
```
如果上述命令仍然无法正常工作，您可以尝试使用虚拟环境的完整路径来执行 jupyter lab 命令。例如：
```
.\.venv\Scripts\jupyter lab

或者（未识别出bash命令）
(.venv) PS D:\workspace_vs_code\.venv\Scripts> ./jupyter lab
```

在浏览器中访问 JupyterLab 用户界面。默认情况下，它应该在 http://localhost:8888/lab 上运行。如果有其他输出，请查看终端中的日志以获取正确的 URL。

## python 3.10中的模式匹配
### 具有位置参数的模式
您可以将位置参数与一些为其属性（例如数据类）提供排序的内置类一起使用。您还可以通过在类中设置特殊属性来定义模式中属性的特定位置__match_args__。如果它设置为 (“x”, “y”)，则以下模式都是等效的（并且都将属性绑定y到var变量）：
```
Point(1, var)
Point(1, y=var)
Point(x=1, y=var)
Point(y=var, x=1)
```

### 嵌套模式
模式可以任意嵌套。例如，如果我们的数据是一个简短的点列表，则可以这样匹配：

```
match points:
    case []:
        print("No points in the list.")
    case [Point(0, 0)]:
        print("The origin is the only point in the list.")
    case [Point(x, y)]:
        print(f"A single point {x}, {y} is in the list.")
    case [Point(0, y1), Point(0, y2)]:
        print(f"Two points on the Y axis at {y1}, {y2} are in the list.")
    case _:
        print("Something else is found in the list.")
```

### demo
```
class Point:
    __match_args__ = ("x", "y")

    def __init__(self, x, y):
        self.x = x
        self.y = y

def location(points):
    match points:
        case []:
            print("No points in the list.")
        case [Point(0, 0)]:
            print("The origin is the only point in the list.")
        case [Point(x, y)]:
            print(f"A single point {x}, {y} is in the list.")
        case [Point(0, y1), Point(0, y2)]:
            print(f"Two points on the Y axis at {y1}, {y2} are in the list.")
        case _:
            print("Something else is found in the list.")

# 测试不同情况
points = []
# points.append(Point(0, 0))
points.append(Point(1, 2))
# points.append(Point(0, 3))

location(points)
```

如果代码里不增加``` __match_args__ = ("x", "y")```，将会出现一下错误
```
Exception has occurred: TypeError
Point() accepts 0 positional sub-patterns (2 given)
  File "D:\workspace_vs_code\match_case_test.py", line 12, in location
    case [Point(0, 0)]:
          ^^^^^^^^^^^
  File "D:\workspace_vs_code\match_case_test.py", line 27, in <module>
    location(points)
TypeError: Point() accepts 0 positional sub-patterns (2 given)
```

## python 类型别名 ?


## Django项目
```
py -m django --version

django-admin startproject mysite

py manage.py startapp polls

python manage.py runserver 127.0.0.1:8000
```

## python 里字符串单引号、双引号、三引号之间的区别

在 Python 中，字符串可以使用单引号（'），双引号（"）或三引号（''' 或 """）来表示。这些不同的引号有不同的用途和特点。

1. 单引号和双引号：
- 单引号和双引号可以互相嵌套使用。
- 可以用于表示普通的字符串。
- 用单引号括起的字符串中可以包含双引号，而用双引号括起的字符串中可以包含单引号。

示例：

```
single_quoted = 'This is a single-quoted string.'
double_quoted = "This is a double-quoted string."
mixed_quoted = "He said, 'Hello world!'"
```

2. 三引号：
- 三引号用于表示多行字符串，在三引号之间的内容可以跨越多行。
- 三引号可以包含多行文本，并且保留字符串中的换行符和缩进。
- 三引号字符串通常用于文档字符串（docstring）或多行注释。

示例：

```
multiline_string = '''This is a
multiline
string.'''
docstring = """
This is a docstring.
It can span multiple lines
and is commonly used to document functions or classes.
"""
```

**注意：**

- 使用单引号、双引号或三引号来定义字符串在大多数情况下功能是相同的，可以根据个人偏好选择使用哪种引号。
- 在编写代码时要保持一致性，选择一种引号风格，并在整个代码中保持一致，这样可以提高代码的可读性和维护性。

## 列表排序

- list.sort(reverse=True) 列表原地排序；列表中的元素必须是同种类型
- sorted(list) 列表排序后返回新的列表；列表中的元素必须是同种类型

### sorted()函数的语法：
```
sorted(iterable, key=None, reverse=False)
```
- iterable：要排序的可迭代对象，例如列表或元组。
- key：用于指定一个函数，根据该函数的返回值进行排序。默认为None，表示按照元素的原始顺序排序。
- reverse：一个布尔值，用于指定是否降序排序。默认为False，表示升序排序。
