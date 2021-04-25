[01 | 基础知识：快速入门](#01)   
[02 | 列表数据：处理有序数据](#02)   
[03 | 结构化数据：处理结构化数据](#03)   
[04 | 代码重用：函数与模块](#04)   
[05 | 构建一个Web应用：来真格的](#05)   
[06 | 存储和管理数据：数据放在哪里](#06)   
[07 | 使用数据库：具体使用Python的DB-API](#07)   
[08 | 一点点类：抽象行为和状态](#08)   
[09 | 上下文管理协议：挂接使用Python的with语句](#09)   
[10 | 函数修饰符：包装函数](#10)   
[11 | 异常处理：出问题了怎么办](#11)   
[11‘ | 关于线程：处理等待](#11-2)   
[12 | 高级迭代：疯狂地循环](#12)   


## [代码下载](http://python.itcarlow.ie/ed2/)

### 1. 函数返回值 

```
空列表返回[]  空字典返回{}  空集合返回set()  空元组返回()
```
### 2. 发布文件

```
1. 创建文件发布描述（生成setup.py,README.txt）

2. 生成一个发布文件

需要发布的文件与 setup.py、README.txt 放在同一个文件夹下

windows 切换到当前需要发布的文件路径下，执行 py -3 setup.py sdist

linux   执行 python3 setup.py sdist



 会在一个名为dist的文件夹中找到新创建的归档文件，dist文件夹也是由setuptools创建。

3. 安装发布文件

pip表示 Package Installer for Python (Python的包安装工具)


windows 切换到 dist 文件路径下，执行 py -3 -m pip install xxx-.tar.gz

linux   执行 sudo python3 -m pip install xxx-.tar.gz

```

setup.py   
```
from setuptools import setup

setup(
    name = 'vsearch',
    version = '1.0',
    description = 'The Head First Python Search Tools',
    author = 'xiechong',
    author_email = 'xiechongyn@163.com',
    url = 'headfirstlabs.com',
    py_modules = ['vsearch'],
    )
```

### 美观打印的复杂数据结构

```
import pprint

pprint.pprint('xxxx')
```


README.txt 可为空文件


---
<h1 id="05">05 | 构建一个Web应用：来真格的</h1>

---

### Python中的函数参数支持按值还是按引用调用语义？

**Python 的函数参数语义即支持按值调用也支持按引用调用**

解释器会查看对象引用（内存地址）指示的那个值类型，如果变量指示一个可变的值，就会应用按引用调用语义；
如果所指示的数据的类型是不可变的，则会应用按值调用语义。

列表、字典和集合（都是可变的）总是会按引用传入函数，函数代码组中对变量数据结构的任何改变都会反映到调用代码中。

字符串、整数和元组（不可变）总是会按值传入函数，函数中对变量的任何修改是这个函数私有的，不会反映到调用代码中。


### 安装pytest和PEP8插件

```
py -3 -m pip install pytest
```

```
py -3 -m pip install pytest-pep8
```

如果在Windows上，要把“py -3”替换未“sudo python3”

安装了pytest和PEP后，不论使用什么操作系统，检查代码的PEP 8 兼容性，都要执行同一个命令
```
py.test --pep8 vsearch.py
```

若出现   
```
Direct construction of Pep8Item has been deprecated, please use Pep8Item.from_parent.
```

[可以尝试使用](https://stackoverflow.com/questions/65713241/pep8-compliance-testing-is-failing)   
```
pep8 vsearch.py
```

或[pycodestyle](https://zhuanlan.zhihu.com/p/90642746)   



### 安装Flask

Python社区维护了一个集中管理第三方模块的网站，名为PyPI（表示Python Package Index）。

Linux
```
sudo -H python3 -m pip install flask
```

windows
```
py -3 -m pip install flask
```

Flask 依赖另外4个模块： Werkzeug, MarkupSafe, Jinja2 和 itsdangerous .


hello_flask.py
```
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hell() -> str:
    return 'Hello world from Flask!'

app.run()
```

__name__值由Python解释器维护，在程序代码中的任何地方使用这个值时，它会设置为当前活动模块的名字（指出当前活动的命名空间）。

如果要运行应用，最好在操作系统的命令行上直接通过解释器运行代码。


### 函数的参数类型、返回值类型、默认值

```
def search4vowels(phrase :str)->set:
    """ return any vowels found in a supplied phrase."""
    vowels = set('aeiou')
    return vowels.intersection(set(phrase))


def search4letters(phrase :str,letters :str='aeiou')->set:
    """Return a set of 'letters' found in 'phrase'."""
    return set(letters).intersection(set(phrase))
```



### Method Not Allowed
GET请求是Flask的默认HTTP方法，我们可以修改对应的``` @app.route('/search4', methods=['POST'])```来支持特定的请求方式

当然也可以同时支持GET、POST ``` @app.route('/search4', methods=['GET', POST']) ```


### 避免“编辑/停止/启动/测试循环”

Flask允许再调试模式运行web应用，每次Flask注意到代码已经改变时，就会自动重启你的web应用（当然还不只这些）。

打开调试模式：``` app.run(debug=True) ```

只有你正确地完成了代码修改，才会自动重新加载。如果你的代码有错误，Web应用就会转到命令行提示窗口，要想再次运行，
需要修正代码错误，然后手动重启Web应用。

### chapter05

```
 +-------------+            |    +----------------+
 |   webapp    |  --------  |----| vsearch4web.py |
 +-------------+            |    +----------------+
                            |
                            |
                            |
                            |    +--------+    +--------+
                            |----| static |----| hf.css |
                            |    +--------+    +--------+
                            |
                            |    +-----------+    +-----------+    +------------+    +--------------+
                            |----| templates |----| base.html |----| entry.html |----| results.html |
                            |    +-----------+    +-----------+    +------------+    +--------------+
```


render_template调用中最后还多一个逗号，这个额外的都会看起来有些奇怪，不过，这是一个完全合法的Python语法（可选，没有严格要求必须如此）。   

vsearch4web.py
```
from flask import Flask, render_template, request
from vsearch import search4letters

app = Flask(__name__)

@app.route('/')
def hello() -> str:
    return 'Hello world from Flask!'


@app.route('/search4', methods=['POST'])
def do_search() -> 'html':
    phrase = request.form['phrase']
    letters = request.form['letters']
    title = 'Here are your results:'
    results = str(search4letters(phrase, letters))
    return render_template('results.html',
                           the_phrase=phrase,
                           the_letters=letters,
                           the_title=title,
                           the_results=results,)


@app.route('/entry')
def entry_page() -> 'html':
    return render_template('entry.html', the_title='Wecome to search4letters on the web!')
    

app.run(debug=True)
```


### 重定向来避免不想要的错误

需要从Flask中导入redirect，修改vsearch4web.py中的hello函数，其它不用改动

vsearch4web.py
```
from flask import Flask, render_template, request, redirect
from vsearch import search4letters

app = Flask(__name__)

@app.route('/')
def hello() -> '302':
    return redirect('/entry')


@app.route('/search4', methods=['POST'])
def do_search() -> 'html':
    phrase = request.form['phrase']
    letters = request.form['letters']
    title = 'Here are your results:'
    results = str(search4letters(phrase, letters))
    return render_template('results.html',
                           the_phrase=phrase,
                           the_letters=letters,
                           the_title=title,
                           the_results=results,)


@app.route('/entry')
def entry_page() -> 'html':
    return render_template('entry.html', the_title='Wecome to search4letters on the web!')
    

app.run(debug=True)

```

重定向有些浪费，一个请求指向"/" URL，Web应用首先响应一个302重定向，然后Web浏览器会发送另一个请求指向"/entry" URL。
```
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
127.0.0.1 - - [26/Mar/2021 14:57:48] "[32mGET / HTTP/1.1[0m" 302 -
127.0.0.1 - - [26/Mar/2021 14:57:48] "[37mGET /entry HTTP/1.1[0m" 200 -
```

### 函数可以有多个URL

Flask可以为一个给定的函数关联多个URL。Flask会尝试依次与各个URL匹配，如果找到一个匹配，就会执行这个函数。

vsearch4web.py
```
from flask import Flask, render_template, request
from vsearch import search4letters

app = Flask(__name__)


@app.route('/search4', methods=['POST'])
def do_search() -> 'html':
    phrase = request.form['phrase']
    letters = request.form['letters']
    title = 'Here are your results:'
    results = str(search4letters(phrase, letters))
    return render_template('results.html',
                           the_phrase=phrase,
                           the_letters=letters,
                           the_title=title,
                           the_results=results,)


@app.route('/')
@app.route('/entry')
def entry_page() -> 'html':
    return render_template('entry.html', the_title='Wecome to search4letters on the web!')
    

app.run(debug=True)
```

现在访问"/" URL时，Web应用的状态消息只会有一个请求。

```
 * Detected change in 'D:\\learn\\python\\mymodules\\webapp\\vsearch4web.py', reloading
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 826-699-626
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
127.0.0.1 - - [26/Mar/2021 15:02:58] "[37mGET / HTTP/1.1[0m" 200 -
```

### __name__  __main__

dunder.py
```
print('We start off in:', __name__)
if __name__ == '__main__':
    print('And end up in:', __name__)
```

命令窗口执行输出：
```
================= RESTART: D:/learn/python/mymodules/dunder.py =================
We start off in: __main__
And end up in: __main__
```

如果你的程序代码直接由Python执行，dunder.py 中的if语句会返回True，因为活动命名空间时__main__。不过，如果你的
程序作为一个模块导入，if语句总是返回False，因为__name__的值不是__main__，而时所导入的模块名字。

vsearch4web.py
```
from flask import Flask, render_template, request
from vsearch import search4letters

app = Flask(__name__)


@app.route('/search4', methods=['POST'])
def do_search() -> 'html':
    phrase = request.form['phrase']
    letters = request.form['letters']
    title = 'Here are your results:'
    results = str(search4letters(phrase, letters))
    return render_template('results.html',
                           the_phrase=phrase,
                           the_letters=letters,
                           the_title=title,
                           the_results=results,)


@app.route('/')
@app.route('/entry')
def entry_page() -> 'html':
    return render_template('entry.html', the_title='Wecome to search4letters on the web!')
    

if __name__ == '__main__':
    app.run(debug=True)
```

### Web应用部署到 PythonAnywhere



---
<h1 id="06">06 | 存储和管理数据：数据放在哪里</h1>

---


### Python 打开、处理、关闭 文件
打开文件写入：
```
>>> todos = open('todos.txt', 'a')
>>> print('Put out the trash.', file=todos)
>>> print('Feed the cat.', file=todos)
>>> print('Prepare tax return.', file=todos)
>>> todos.close()
```

输出文件内容：
```
>>> tasks = open('todos.txt')
>>> for chore in tasks:
	print(chore, end='')

>>> tasks.close()
```

**open** 的第一个参数是要处理的文件名，第二个参数是可选的（关于open的更多详细内容可以参考Python文档）。
* 'r' 打开一个文件来读数据（默认）；
* 'w' 打开一个文件来写数据；
* 'a' 打开一个文件来追加数据；
* 'x' 打开一个新文件来写数据。

### 比“打开、处理、关闭”更好的“with”

```
with open(todos.txt) as tasks:
	for chore in tasks:
		print(chore, end='')
```

with语句会管理其代码组运行的上下文，结合使用with和open时，解释器会为你完成收尾的清理工作，在需要时调用close。

vsearch4web.py 增加日志输出到vsearch.log文件
```
from flask import Flask, render_template, request
from vsearch import search4letters

app = Flask(__name__)


def log_request(req: 'flask_request', res: str) -> None:
    with open('vsearch.log', 'a') as log:
        print(req, res, file = log)


@app.route('/search4', methods=['POST'])
def do_search() -> 'html':
    phrase = request.form['phrase']
    letters = request.form['letters']
    title = 'Here are your results:'
    results = str(search4letters(phrase, letters))
    log_request(request, results)
    return render_template('results.html',
                           the_phrase=phrase,
                           the_letters=letters,
                           the_title=title,
                           the_results=results,)


@app.route('/')
@app.route('/entry')
def entry_page() -> 'html':
    return render_template('entry.html', the_title='Wecome to search4letters on the web!')
    

if __name__ == '__main__':
    app.run(debug=True)
```

vsearch4web.py 增加查看日志文件的方法
```
from flask import Flask, render_template, request, escape
from vsearch import search4letters

app = Flask(__name__)


def log_request(req: 'flask_request', res: str) -> None:
    with open('vsearch.log', 'a') as log:
        print(req, res, file = log)


@app.route('/search4', methods=['POST'])
def do_search() -> 'html':
    phrase = request.form['phrase']
    letters = request.form['letters']
    title = 'Here are your results:'
    results = str(search4letters(phrase, letters))
    log_request(request, results)
    return render_template('results.html',
                           the_phrase=phrase,
                           the_letters=letters,
                           the_title=title,
                           the_results=results,)


@app.route('/')
@app.route('/entry')
def entry_page() -> 'html':
    return render_template('entry.html', the_title='Wecome to search4letters on the web!')
    

@app.route('/viewlog')
def view_the_log() -> str:
    with open('vsearch.log') as log:
        contents = log.read()
    return escape(contents)


if __name__ == '__main__':
    app.run(debug=True)

```

解释器提供了一个read()方法，它会一次性返回文件的全部内容。

escape用来专用特殊符号。


### 更多的了解请求对象

要了解Python中某个对象包含什么时，可以把这个对象提供给dir内置方法，查看它的方法和属性列表。

```
def log_request(req: 'flask_request', res: str) -> None:
    with open('vsearch.log', 'a') as log:
        print(str(dir(req)), res, file = log)
```


* req.form: 从Web应用的HTML表单提交的数据   
* req.remote_addr: 运行Web浏览器的IP地址   
* req.user_agent: 提交数据的浏览器的标识   


### 记录特定的Web请求属性

```
def log_request(req: 'flask_request', res: str) -> None:
    with open('vsearch.log', 'a') as log:
        print(req.form, file=log)
        print(req.remote_addr, file=log)
        print(req.user_agent, file=log)
        print(res, file=log)
```

```
def log_request(req: 'flask_request', res: str) -> None:
    with open('vsearch.log', 'a') as log:
        print(req.form, file=log, end='|')
        print(req.remote_addr, file=log, end='|')
        print(req.user_agent, file=log, end='|')
        print(res, file=log)
```


```
def log_request(req: 'flask_request', res: str) -> None:
    with open('vsearch.log', 'a') as log:
        print(req.form, req.remote_addr, req.user_agent, res, file=log, sep='|')
```


```
>>> list = []
>>> list.append([])
>>> list1 = ['a','b','c']
>>> list[-1].append(list1)
>>> list
[[['a', 'b', 'c']]]
>>> lists = []
>>> lists.append([])
>>> lists.append(list1)
>>> lists
[[], ['a', 'b', 'c']]
>>> list2=[]
>>> list2.append([])
>>> list2[-1].append('a')
>>> list2[-1].append('b')
>>> list2
[['a', 'b']]
>>> 
>>> 
>>> list3 = []
>>> lisr3.append(lists1)
Traceback (most recent call last):
  File "<pyshell#17>", line 1, in <module>
    lisr3.append(lists1)
NameError: name 'lisr3' is not defined
>>> list3[-1].append(list1)
Traceback (most recent call last):
  File "<pyshell#18>", line 1, in <module>
    list3[-1].append(list1)
IndexError: list index out of range
>>> 
```


### 嵌套列表结合Jinja2模板生成可读的输出（在模板中嵌入显示逻辑，用HTML生成可读的输出）

vsearch4web.py
```
from flask import Flask, render_template, request, escape
from vsearch import search4letters

app = Flask(__name__)


def log_request(req: 'flask_request', res: str) -> None:
    with open('vsearch.log', 'a') as log:
        print(req.form, req.remote_addr, req.user_agent, res, file=log, sep='|')


@app.route('/search4', methods=['POST'])
def do_search() -> 'html':
    phrase = request.form['phrase']
    letters = request.form['letters']
    title = 'Here are your results:'
    results = str(search4letters(phrase, letters))
    log_request(request, results)
    return render_template('results.html',
                           the_phrase=phrase,
                           the_letters=letters,
                           the_title=title,
                           the_results=results,)


@app.route('/')
@app.route('/entry')
def entry_page() -> 'html':
    return render_template('entry.html', the_title='Wecome to search4letters on the web!')
    

@app.route('/viewlog')
def view_the_log() -> 'html':
    contents = []
    with open('vsearch.log') as log:
        for line in log:
            contents.append([])
            for item in line.split('|'):
                contents[-1].append(escape(item))
    titles = ('Form Data', 'Remote_addr', 'User_agent', 'Result')
    return render_template('viewlog.html',
                           the_title = 'View Log',
                           the_row_titles = titles,
                           the_data = contents,)


if __name__ == '__main__':
    app.run(debug=True)

```

viewlog.html
```
<!DOCTYPE html>
<html>
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=windows-1252">
	</head>
	<body>{% extends 'base.html' %}

		{% block body %}

		<h2>{{ the_title}}</h2>

		<table>
			<tr>
				{% for row_title in the_row_titles %}
				<th>{{row_title}}</th>
				{% endfor %}
			</tr>
			{% for log_row in the_data %}
			<tr>
				{% for item in log_row %}
				<td>{{item}}</td>
				{% endfor %}
			</tr>
			{% endfor %}
		</table>
		{% endblock %}
	</body>
</html>
```




---
<h1 id="07">07 | 使用数据库：具体使用Python的DB-API</h1>

---

[MySQL Connector/Python Developer Guide](https://dev.mysql.com/doc/connector-python/en/)

DB-API，所有Python安装都会提供这个API。使用DB-API的好处是，可以轻松替换驱动程序/数据库组合，而只需要对Python代码做非常小的改动。

**任务**：
1. 安装MySQL服务器；
2. 为Python安装一个MySQL数据库驱动程序；
3. 为我们的web应用创建数据库和表；
4. 创建代码来处理这个web应用的数据库和表。

### 安装MySQL服务器
MySQL社区版：http://dev.mysql.com/downloads/mysql/

MariaDB：https://mariadb.org/download/


### 为Python安装一个MySQL数据库驱动程序

MySQL-Connector/Python不能用pip安装。我们需要手动安装这个模块。

下载地址：https://dev.mysql.com/downloads/connector/python/

我自己尝试时可以使用pip安装的
```
python -m pip install mysql-connector
```

### 安装MySQL Connector/Python

需要在安装包所在的目录下执行一下命令：

windows   
```
py -3 setup.py install
```

Linux/Mac OS X   
```
sudo -H python3 setup.py install
```

我自己下载下来的是一个msi文件，安装时提示“Please install the Redistributable then run this installer again.”   
[解决方案参考](https://blog.csdn.net/weixin_39218743/article/details/86524212)

### 创建Web应用的数据库和表

作为MySWL数据库管理员登陆   
```
mysql -u root -p
```

```
mysql> create database vsearchlogDB;
```

创建一个新的MySQL用户，名为vsearch，使用"vsearchpasswd"作为这个新用户的口令，并为这个vsearch用户提供访问vserachlogDB数据库的全部权限。
```
mysql> grant all on vsearchlogDB.* to 'vsearch' identified by 'vsearchpasswd';
```

 使用以下命令从控制台注销：
 ```
 mysql> quit
 ```
 
 ### 确定日志数据的结构

```
create table log
(
    id             int auto_increment primary key,
    ts             timestamp default current_timestamp,
    phrase         varchar(128) not null,
    letters        varchar(32)  not null,
    ip             varchar(16)  not null,
    browser_string varchar(256) not null,
    results        varchar(64)  not null
);
```

查看表结构
```
mysql> describe log;
```

### DB-API步骤
1. 定义连接属性

MySQL-Connector/Python驱动程序允许你将这些连接属性放在一个Python字典中，以便于使用和引用（使用默认端口3306）。
```
dbconfig = {'host':'',
            'user':'',
            'password':'',
            'database':'',}
```

2. 导入数据库驱动程序
```
>>> import mysql.connector
```

3. 建立与服务器的一个连接   
```
>>> conn = mysql.connector.connect(**dbconfig)
```
** 记法告诉connect函数用一个变量提供了一个参数字典。如果看到 **，connect函数会把这个字典参数展开为4个单独的参数，
然后在connect函数中使用这些参数来建立连接。

4. 打开一个游标
要向数据库发送QL命令以及从数据库接收结果，需要一个游标。

```
>>> cursor = conn.cursor()
```

5. 完成SQL查询

head-first-labs的Python程序员喜欢用一个三重引号字符串编写他们想要发送给数据库服务器的SQL，然后把这个字符串赋值给一个名为_SQL的变量。
之所以使用三重引号字符串，这是因为SQL查询通常有多行，而三重引号字符串可以暂时不启用Python解释器的 “行末即语句结束” 规则。使用_SQL作为
变量名是约定。

```
>>> _SQL = """show tables"""
>>> cursor.execute(_SQL)
```

查询结果不会立即显示，必须请求得到结果。

可以使用以下3个游标方法请求结果：
* cursor.fetchone 获取一行结果
* cursor.fetchmany 获取你指定的任意行结果
* cursor.fechall 获取结果中的所有数据行(结果是一个元组列表)。

```
>>> res = cursor.fetchall()
```

```
>>> _SQL = """describe log"""
>>> cursor.execute(_SQL)
>>> res = cursor.fetchall()
```

Python 的DB-API允许在查询中放置“数据占位符”，调用cursor.execute()时可以在这些占位符上体乳具体的值，从而重用一个SQL语句。

```
>>> _SQL = """insert into log (phrase, letters, ip, browser_string, results)
values
('hitch-hiker', 'aeiou', '127.0.0.1', 'Firefox', "{'e','i'}")"""

>>> cursor.execute(_SQL)
```

```
>>> _SQL = """insert into log (phrase, letters, ip, browser_string, results)
values
(%s, %s, %s, %s, %s)"""

>>> cursor.execute(_SQL, ('hitch-hiker', 'aeiou', '127.0.0.1', 'Firefox', "{'e','i'}"))

>>> conn.commit()
```

cursor.execute()最多只能接收2个参数：一个是包含占位符的查询，另外一个是数据值元组。

6. 关闭游标和连接
数据提交到数据库表中后，要进行清理，关闭游标以及连接（游标会返回True来确认已经成功关闭，而连接只是直接关闭）。
```
>>> cursor.close()
True
>>> conn.close()
>>> 
```

### 创建代码处理Web应用的数据库和表

vsearch4web.py
```
from flask import Flask, render_template, request, escape
from vsearch import search4letters
import mysql.connector

app = Flask(__name__)


def log_request(req: 'flask_request', res: str) -> None:
    dbconfig = {'host':'10.83.16.43',
            'user':'root',
            'password':'zU!ykpx3EG)$$1e6',
            'database':'acc_adapter',}
    _SQL = """insert into log (phrase, letters, ip, browser_string, results)
            values
            (%s, %s, %s, %s, %s)"""
    conn = mysql.connector.connect(**dbconfig)
    cursor = conn.cursor()
    cursor.execute(_SQL,(req.form['phrase'],
                         req.form['letters'],
                         req.remote_addr,
                         req.user_agent.browser,
                         res,))
    conn.commit()
    cursor.close()
    conn.close()


@app.route('/search4', methods=['POST'])
def do_search() -> 'html':
    phrase = request.form['phrase']
    letters = request.form['letters']
    title = 'Here are your results:'
    results = str(search4letters(phrase, letters))
    log_request(request, results)
    return render_template('results.html',
                           the_phrase=phrase,
                           the_letters=letters,
                           the_title=title,
                           the_results=results,)


@app.route('/')
@app.route('/entry')
def entry_page() -> 'html':
    return render_template('entry.html', the_title='Wecome to search4letters on the web!')
    

@app.route('/viewlog')
def view_the_log() -> 'html':
    contents = []
    with open('vsearch.log') as log:
        for line in log:
            contents.append([])
            for item in line.split('|'):
                contents[-1].append(escape(item))
    titles = ('Form Data', 'Remote_addr', 'User_agent', 'Result')
    return render_template('viewlog.html',
                           the_title = 'View Log',
                           the_row_titles = titles,
                           the_data = contents,)


if __name__ == '__main__':
    app.run(debug=True)
```

### 如何最好的重用你的数据库代码？

```
def log_request(req: 'flask_request', res: str) -> None:
+---------------------------------------------+
|    dbconfig = {'host':'10.83.16.43',
|            'user':'root',
|            'password':'zU!ykpx3EG)$$1e6',
|            'database':'acc_adapter',}
+---------------------------------------------+

    _SQL = """insert into log (phrase, letters, ip, browser_string, results)
            values
            (%s, %s, %s, %s, %s)"""
			
+---------------------------------------------+
|    conn = mysql.connector.connect(**dbconfig)
|    cursor = conn.cursor()
+---------------------------------------------+

    cursor.execute(_SQL,(req.form['phrase'],
                         req.form['letters'],
                         req.remote_addr,
                         req.user_agent.browser,
                         res,))
+---------------------------------------------+
|    conn.commit()
|    cursor.close()
|    conn.close()
+---------------------------------------------+
```
如上所示，框起来的部分都是可以重用的（如dbconfig字典、创建conn和cursor，以及调用commit和close）

### 确定import语句的位置时要当心

解释器在遇到一个import语句时，导入的模块会完全读入，然后又解释器执行。如果import语句在函数之外，
这个行为没有什么问题，因为所导入的模块（通常）只读一次，然后执行一次。如果import语句出现在一个函
数内部，那么每一次调用这个函数时都会读入和执行。这是一种极为浪费的做法。

### Python提供了上下文管理协议，它允许程序员根据需要挂接with语句（需要使用到Python类来实现）。



---
<h1 id="08">08 | 一点点类：抽象行为和状态</h1>

---

Q: Python到底是一种什么类型的变成语言：面向对象、函数式还是过程式语言？   
A: Python支持从所有这3种流行方法借用的编程范式，而且Python还鼓励程序员根据需要混合使用。

Q: 什么是BIF？     
A: Python中的BIF就是Built-in Functions，即内置函数的意思，内置函数就是为了方便程序员快速的编写脚本程序，Python提供了很多内置函数，只需要直接调用即可。
想要查看Python中的内置函数，在IDLE中输入dir(__builtins__)（注：builtins前后分别是两个下横杠），如下所示：   
```
>>> dir(__builtins__)
```

如果试图创建一个类，而这个类的代码组中没有任何代码，就存在语法错误。

"pass"是一个合法的语句，但它什么也不做，可以想成是一个空语句。

Python社区有一个普遍认可的约定： 函数用小写字母命名（另外有下划线来强调），而类用CamelCase形式命名。

定义一个类，为此要在类名前加一个class关键字，然后提供实现这个类的代码组必须在冒号后面
```
class CountFromBy:
	pass
```
类行为会由它的各个对象共享，而状态不能共享。每个对象会维护其自己的状态。

```
c = CountFromBy()

c.increase()

解释器总会把上面代码转换为以下调用
CountFromBy.increase(c)

所以你写的每一个方法都至少有一个参数（必不可少），这样才能接收这个对象为参数，否则解释器会报错。

实际上，Python社区中有一种约定俗成的做法是为公共方法的第一个参数指定一个特殊的名字：self
```

正确定义类
```
class CountFromBy:

    def __init__(self, v: int=0, i: int=1) -> None:
        self.val = v
        self.incr = i

    def increase(self) -> None:
        self.val += self.incr

    def __repr__(self) -> str:
        return str(self.val)
```

如果不使用self关联属性，那么方法中的变量在方法结束时就会被撤销（作用域），而且它们使用的所有资源会由解释器回收。

### 属性名前面加"self"

函数没有self，函数中的变量在方法结束时就会被撤销，而通常我们仅仅关心函数的返回值。

而方法则有所不同。方法使用属于一个对象的属性值，而在方法结束之后对象的属性仍存在。也就是说。方法结束时，对象的属性
值不会被撤销。

为了保证方法结束后属性值仍有效，属性值必须赋给方法结束时不会撤销的某个东西。这正是第哦啊用这个方法的当前对象，它存储
在self中，这就解释了为什么这个方法代码中每个属性值前面需要加self。

如果需要引用类中的一个属性，必须在属性名前面加上self。self中的值是一个别名，指示调用这个方法的对象。

### 使用之前初始化（属性）值

在Python中，在对变量赋值之前不能使用这个变量，而不论在哪里使用。

在Python中，对象实例化由解释器自动处理，不需要定义构造函数来完成这个工作。由一个名为__init__的魔法方法允许你根据需要初
始化属性。

Object类是解释器内置类（里面提供了每个类行为的“挂钩”），所有其他Python类都自动继承这个类。你的类可以原样使用Object提供
的dunder方法，或者可以根据需要覆盖（提供你自己的方法实现）。


### 收获

* 创建一个对象时，提供给类的所有参数值都会传递到__init__方法。
* 可以结合对象名和属性名来访问属性（对于从一种“更严格的”OOP语言转向Python的读者，要注意我们并不需要创建获取方法或设置方法）。
* 单独使用对象时，解释器返回一个神秘的消息。

```
>>> class CountFromBy:
	pass

>>> j = CountFromBy()
>>> j
<__main__.CountFromBy object at 0x000002C87E140B20>
>>> type(j)
<class '__main__.CountFromBy'>
>>> id(j)
3060131957536
>>> hex(id(j))
'0x2c87e140b20'
>>> 
```

type BIF会显示创建这个对象的类的有关信息。   
id BIF显示对象内存地址的有关信息。   

j显示的整个消息就是type的输出以及id的输出（转换为十六进制）的组合

**可以覆盖dunder “repr” 来指定解释器如何表示对象。**



---
<h1 id="09">09 | 上下文管理协议：挂接使用Python的with语句</h1>

---

尽管标准库中也支持创建简单的上下文管理器（使用contextlib模块），但是如果要使用with控制某个外部对象（如这里的数据库连接），一般认为创建一个遵循
上下文管理协议的类才是正确的方法。

### 用方法管理上下文

如果遵循上下文管理协议，你的类就可以挂接到with语句。这个协议指出，你创建的任何类都必须至少定义两个魔法方法：__enter__和__exit__。这样，解释器就
会自动认为这个类是一个上下文管理器。

#### Dunder "enter"完成建立
一个对象用于with语句时，在with语句的代码组开始之前，解释器会调用这个对象的__enter__方法。这就为你提供了一个机会，可以在dunder enter中执行必要的建立代码。

这个协议还进一步指出dunder enter可以（但不是必须）向with语句返回一个值。

__init__在__enter__之前运行。

#### Dunder "exit"完成清理
一旦with语句的代码组结束，解释器就会调用这个对象的__exit__方法。这发生咋with语句代码组结束之后，这为你提供了一个机会来完成必要的清理。


### 创建一个新的上下文管理器

DBcm.py
```
"""The UseDatabase context manager for working with MySQL/MariaDB.

For more information, see Chapters 7, 8, 9, and 11 of the 2nd edition of
Head First Python.

Simple example usage:

    from DBcm import UseDatabase, SQLError

    config = { 'host': '127.0.0.1',
               'user': 'myUserid',
               'password': 'myPassword',
               'database': 'myDB' }

    with UseDatabase(config) as cursor:
        _SQL = "select * from log"
        cursor.execute(_SQL)
        data = cursor.fetchall()

Enjoy, and have fun.  (Sorry: Python 3 only, due to type hints and new syntax).
"""

##############################################################################
# Context manager for connecting/disconnecting to a database.
##############################################################################

import mysql.connector


class UseDatabase:
    def __init__(self, config: dict):
        """Add the database configuration parameters to the object.

        This class expects a single dictionary argument which needs to assign
        the appropriate values to (at least) the following keys:

            host - the IP address of the host running MySQL/MariaDB.
            user - the MySQL/MariaDB username to use.
            password - the user's password.
            database - the name of the database to use.

        For more options, refer to the mysql-connector-python documentation.
        """
        self.configuration = config

    def __enter__(self) -> 'cursor':
        """Connect to database and create a DB cursor.

        Return the database cursor to the context manager.
        """
        self.conn = mysql.connector.connect(**self.configuration)
        self.cursor = self.conn.cursor()
        return self.cursor

    def __exit__(self, exc_type, exc_value, exc_traceback):
        """Destroy the cursor as well as the connection (after committing).
        """
        self.conn.commit()
        self.cursor.close()
        self.conn.close()
```

### 重新考虑Web应用代码
vsearch4web.py
```
from flask import Flask, render_template, request, escape
from vsearch import search4letters

from DBcm import UseDatabase

app = Flask(__name__)

app.config['dbconfig'] = {'host': '127.0.0.1',
                          'user': 'vsearch',
                          'password': 'vsearchpasswd',
                          'database': 'vsearchlogDB', }


def log_request(req: 'flask_request', res: str) -> None:
    """Log details of the web request and the results."""

    with UseDatabase(app.config['dbconfig']) as cursor:
        _SQL = """insert into log
                  (phrase, letters, ip, browser_string, results)
                  values
                  (%s, %s, %s, %s, %s)"""
        cursor.execute(_SQL, (req.form['phrase'],
                              req.form['letters'],
                              req.remote_addr,
                              req.user_agent.browser,
                              res, ))


@app.route('/search4', methods=['POST'])
def do_search() -> 'html':
    """Extract the posted data; perform the search; return results."""
    phrase = request.form['phrase']
    letters = request.form['letters']
    title = 'Here are your results:'
    results = str(search4letters(phrase, letters))
    log_request(request, results)
    return render_template('results.html',
                           the_title=title,
                           the_phrase=phrase,
                           the_letters=letters,
                           the_results=results,)


@app.route('/')
@app.route('/entry')
def entry_page() -> 'html':
    """Display this webapp's HTML form."""
    return render_template('entry.html',
                           the_title='Welcome to search4letters on the web!')


@app.route('/viewlog')
def view_the_log() -> 'html':
    """Display the contents of the log file as a HTML table."""
    with UseDatabase(app.config['dbconfig']) as cursor:
        _SQL = """select phrase, letters, ip, browser_string, results
                  from log"""
        cursor.execute(_SQL)
        contents = cursor.fetchall()
    titles = ('Phrase', 'Letters', 'Remote_addr', 'User_agent', 'Results')
    return render_template('viewlog.html',
                           the_title='View Log',
                           the_row_titles=titles,
                           the_data=contents,)


if __name__ == '__main__':
    app.run(debug=True)
```


数据库查询cursor.fetchall()出来的结果为list包含元组
```
>>> contents
[(bytearray(b'hitch-hiker'), bytearray(b'aeiou'), bytearray(b'127.0.0.1'), bytearray(b'Firefox'), bytearray(b"{\'e\',\'i\'}")), (bytearray(b'i love you'), bytearray(b'aeiouss'), bytearray(b'127.0.0.1'), bytearray(b'chrome'), bytearray(b"{\'i\', \'u\', \'o\', \'e\'}")), (bytearray(b'\xe6\xb0\xb4\xe6\xb0\xb4\xe6\xb0\xb4\xe6\xb0\xb4\xe9\x83\xa8\xe5\x88\x86\xe5\x9c\xb0\xe6\x96\xb9\xe9\x83\xbd\xe6\x98\xaf'), bytearray(b'aeiou'), bytearray(b'127.0.0.1'), bytearray(b'chrome'), bytearray(b'set()')), (bytearray(b'uuuuuuskcndksj'), bytearray(b'us'), bytearray(b'127.0.0.1'), bytearray(b'chrome'), bytearray(b"{\'u\', \'s\'}"))]
>>> type(contents)
<class 'list'>
>>> tt = contents[1]
>>> type(tt)
<class 'tuple'>
>>> tt[0]
bytearray(b'i love you')
>>> ss0=tt0.decode('utf-8')
>>> ss0
'i love you'

```

故需要在代码中把bytearray转换为字符串
```
@app.route('/viewlog')
def view_the_log() -> 'html':
    """Display the contents of the log file as a HTML table."""
    with UseDatabase(app.config['dbconfig']) as cursor:
        _SQL = """select phrase, letters, ip, browser_string, results
                  from log"""
        cursor.execute(_SQL)
        contents = cursor.fetchall()
        transferContents = []
        for list in contents:
            newList = []
            for t in list:
                newList.append(t.decode('utf-8'))
            transferContents.append(tuple(newList))
    titles = ('Phrase', 'Letters', 'Remote_addr', 'User_agent', 'Results')
    return render_template('viewlog.html',
                           the_title='View Log',
                           the_row_titles=titles,
                           the_data=transferContents,)
```


---
<h1 id="10">10 | 函数修饰符：包装函数</h1>

---

**使用函数修饰符可以为现有函数增加代码而不必修改现有函数代码**。

quick_session.py
```
from flask import Flask, session

app = Flask(__name__)

app.secret_key = 'YouWillNeverGuess'


@app.route('/setuser/<user>')
def setuser(user: str) -> str:
    session['user'] = user
    return 'User value set to: ' + session['user']


@app.route('/getuser')
def getuser() -> str:
    return 'User value is currently set to: ' + session['user']


if __name__ == '__main__':
    app.run(debug=True)
```


### 字典键值测试
```
>>> session = dict()
>>> if sesion['logged_in']:
	print('Found it.')

	
Traceback (most recent call last):
  File "<pyshell#3>", line 1, in <module>
    if sesion['logged_in']:
NameError: name 'sesion' is not defined
>>> if 'logged_in' in session:
	print('Found it.')

	
>>> session['logged_in'] = True
>>> if 'logged_in' in session:
	print('Found it.')

	
Found it.
>>> if session['logged_in']:
	print('Found it.')

	
Found it.
>>> 
```

除非一个键/值对在字典中存在，否则不能检查这个键的值。试图这么做会导致一个KeyError。要避免这一类错误，可以用“in”检查这个键
是否存在，尽管这个键没有值，但代码也不会崩溃（没有“KeyError”）。


### 模拟登陆处理（/login, /logout, /status）

simple_webapp.py
```
from flask import Flask, session

app = Flask(__name__)


@app.route('/')
def hello():
    return 'Hello from the simple webapp.'


@app.route('/page1')
def page1():
    return 'This is page 1.'


@app.route('/page2')
def page2():
    return 'This is page 2.'


@app.route('/page3')
def page3():
    return 'This is page 3.'


@app.route('/login')
def do_login() -> str:
    session['logged_in'] = True
    return 'You are now logged in.'


@app.route('/logout')
def do_logout() -> str:
    session.pop('logged_in')
    return 'You are now logged out.'


@app.route('/status')
def check_status() -> str:
    if 'logged_in' in session:
        return 'You are currently logged in.'
    return 'You are NOT logged in.'


app.secret_key = 'YouWillNeverGuessMySecretKey'

if __name__ == '__main__':
    app.run(debug=True)
```



### 限制对/page1, /page2 和 /page3 URLs的访问

限制对/page1, /page2 和 /page3 URLs的访问，可以选择创建一个公共函数，然后在处理/page1, /page2 和 /page3 URL的函数中根据需要调用这个函数。
但这些代码与函数真正的工作没有任何关系。即：需要检查用户是否登陆，之后才能授权访问，不过这要为每一个URL增加一个函数调用来完成这个工作，
这种做法不太好（各个函数的具体工作与登陆状态检查混杂）。我们将选择一个最佳方法：**创建并使用一个修饰符**。

修饰符很容易发现，他们前面都有一个@符号作为前缀。

了解Python的修饰符机制或编写一个修饰符，你需要知道和了解4个问题：
1. 如何创建一个函数？
2. 如何把一个函数作为参数传递到另一个函数？
3. 如何从函数返回一个函数？
4. 如何处理任意数量和类型的函数参数？

```
>>> msg = "Hello from Head Firt Python 2e"
>>> id(msg)
2103457414720
>>> def hello():
	print(msg)

	
>>> id(hello)
2103457141088
>>> type(hello)
<class 'function'>
>>> type(msg)
<class 'str'>
>>> hello()
Hello from Head Firt Python 2e
>>> 
```

```
>>> def apply(func: object, value: object) -> object:
	return func(value)

>>> apply(print, 42)
42
>>> apply(id, 42)
2103418121808
>>> apply(type, 42)
<class 'int'>
>>> apply(len, 'Marvin')
6
>>> apply(type, apply)
<class 'function'>
>>> 
```

### 函数可以嵌套在函数中
```
>>> def outer():
    def inner():
        print('This is inner')


    print('This is outer, invoking inner.')
    inner()

    
>>> outer()
This is outer, invoking inner.
This is inner
>>> 
```

Q：什么时候使用嵌套函数？
A：如果一个函数很复杂，包含多行代码，把一些函数代码抽取到一个嵌套函数中会很有意义（而且可以让外围函数的代码更易读）。

**外围函数使用return语句返回嵌套函数作为它的返回值。修饰符就是采用这种方法来创建**。



### 从函数返回一个函数

"return"语句调用"inner"，而是把"inner"函数对象返回给调用代码。

```
>>> def outer():
    def inner():
        print('This is inner')


    print('This is outer, invoking inner.')
    return inner

>>> i = outer()
This is outer, invoking inner.
>>> type(i)
<class 'function'>
>>> i()
This is inner
>>> 
```

代码中的i就是outer中创建的inner函数的一个别名。


### 接收一个参数列表

解决：创建一个函数，它能处理任意数量和类型的参数。

Python提供了一种特殊记法，允许指定一个函数可以接收任意数量的参数。这种记法使用*字符表示任意数字，并结合一个参数名（按惯例使用args），来指定函数可以接收一个任意的参数列表
（尽管从理论上讲*args是一个元组）。

```
>>> def myfunc(*args):
	for a in args:
		print(a,end=' ')
	if args:
		print()

		
>>> myfunc(10, 20, 30, 40, 50, 60, 70)
10 20 30 40 50 60 70 
>>> myfunc(1, 'two', 3, 'four', 5, 'six', 7)
1 two 3 four 5 six 7 
>>> 
```

甚至所提供的值可以混合多种类型，“myfunc”仍能正常工作。

#### 还可以直接使用 * 
如果向myfunc提供一个列表作为参数，这个列表（尽管可能包含多个值）会被处理为一项（也就是说，它是一个列表）。为了指示解释器展开这个列表，
使每个列表项作为一个单独的参数，调用函数时要在列表名前面加*字符作为前缀。

```
>>> values = [1, 2, 3, 5, 7, 11]
>>> myfunc(values)
[1, 2, 3, 5, 7, 11] 
>>> myfunc(*values)
1 2 3 5 7 11 
>>> 
```


### 使用 ** 接收任意多个关键字参数

向函数发送值时，可以提供参数名以及关联的值，然后依赖解释器来完成相应的匹配。

```
 search4letters(letters='xyz', phrase='galaxy')
 
 def search4letters(phrase:str, letters:str='aeiou') -> set:

```

Python提供了```**```，它展开为一个关键字参数集合。（按约定）* 用args作为变量名，```**```则使用kwargs，这是“关键字参数”(kwywords)的缩写。

```
>>> def myfunc(*args):
    for a in args:
        print(a, end='')
    if args:
        print()

        
>>> def myfunc2(**kwargs):
    for k, v in kwargs.items():
        print(k, v, sep='->', end='  ')
    if kwargs:
        print()

        
>>> myfunc2(a=10, b=20)
a->10  b->20  
>>> myfunc2()
>>> myfunc2(a=10, b=20, c=30, d=40, e=50, f=60)
a->10  b->20  c->30  d->40  e->50  f->60  
>>> 
```


#### 处理参数字典

```
dbconfig = {'host':'10.00.11.08',
            'user':'root',
            'password':'abcdfghh$$1e6',
            'database':'demo',}
			
 conn = mysql.connector.connect(**dbconfig)
 
 通过在dbconfig参数前面加**前缀，我们告诉解释器要把这个字典处理为键及其关联的一个集合。实际上，者就好像调用connect时提供了4个单独的关键字参数，如下所示：
 conn = mysql.connector.connect('host'='10.00.11.08', 'user'='root', 'password'='abcdfghh$$1e6', 'database'='demo')
 
```

### 接收任意数量和类型的函数参数

创建你自己的函数时：Python允许函数接收一个函数列表（使用*），另外还可以接收任意多个关键字参数（使用**）。更棒的是，你可以结合这两个技术，
创建一个函数来接收任意数量和类型的参数。

myfunc第3个版本
```
def myfunc(*args):
    for a in args:
        print(a, end='')
    if args:
        print()


def myfunc2(**kwargs):
    for k, v in kwargs.items():
        print(k, v, sep='->', end=' ')
    if kwargs:
        print()


def myfunc3(*args, **kwargs):
    if args:
        for a in args:
            print(a, end='  ')
        print()
    if kwargs:
        for k, v in kwargs.items():
            print(k, v, sep='->', end=' ')
        print()
        
```

```
>>> def myfunc3(*args, **kwargs):
    if args:
        for a in args:
            print(a, end='  ')
        print()
    if kwargs:
        for k, v in kwargs.items():
            print(k, v, sep='->', end=' ')
        print()

        
>>> myfunc3()
>>> myfunc3(1, 2,3)
1  2  3  
>>> myfunc3(a=10, b=20, c=30)
a->10 b->20 c->30 
>>> myfunc3(1, 2, 3, a=10, b=20, c=30)
1  2  3  
a->10 b->20 c->30 
>>> 
```



### 创建函数修饰符的规则

修饰符允许你用而外的代码增强一个已有的函数，而不需要修改已有函数的代码。

1. **修饰符是一个函数**   
实际上，就解释器而言，修饰符只是一个函数，不过是管理一个已有函数的函数（通常把这个已有函数称为被修饰函数）。

2. **修饰符取被修饰函数为参数**   
修饰符需要接收被修饰函数为参数。为此，只需要把被修饰函数作为一个函数对象传入修饰符（引用函数而不加小括号，只使用函数名，就会得到函数对象）。

3. **修饰符返回一个新函数**   
修饰符返回一个函数作为其返回值。这个返回的函数需要调用被修饰函数（就像前几页上outer返回inner一样，你的修饰符也会做类似的事情，只不过它返回
的函数需要调用被修饰函数）。

4. **修饰符维护被修饰函数的签名**   
修饰符需要确保它返回的函数与被修饰函数有同样的参数（个数和类型都相同）。函数参数的个数和类型称为其**签名**（因为每个函数的def行是唯一的）。

### 创建函数修饰符

记住：**修饰符只是一个函数，它取一个函数对象为参数**

在checker.py中创建一个新的修饰符，名为check_logged_in(放在单独的模块中，容易重用)

修饰符可能应用于任何现有函数，为保持“通用”，使用一个通用的签名，让它支持任意数量和类型的参数。

```
from flask import session

from functools import wraps

def check_logged_in(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        if 'logged_in' in session:
            return func(*args, **kwargs)
        return 'You are NOT logged in.'
    return wrapper
```


创建自己的修饰符时，一定要导入并使用“functools”模块的“wraps”函数，向解释器标识自己的身份。

wraps函数实现为一个修饰符，所以实际上你并不是调用这个函数，而是用它在你自己的修饰符中修饰wrapper函数。

simple_webapp4.py
```
from flask import Flask, session

from checker import check_logged_in

app = Flask(__name__)


@app.route('/')
def hello():
    return 'Hello from the simple webapp.'


@app.route('/page1')
@check_logged_in
def page1():
    return 'This is page 1.'


@app.route('/page2')
@check_logged_in
def page2():
    return 'This is page 2.'


@app.route('/page3')
@check_logged_in
def page3():
    return 'This is page 3.'


@app.route('/login')
def do_login() -> str:
    session['logged_in'] = True
    return 'You are now logged in.'


@app.route('/logout')
def do_logout() -> str:
    session.pop('logged_in')
    return 'You are now logged out.'


app.secret_key = 'YouWillNeverGuessMySecretKey'

if __name__ == '__main__':
    app.run(debug=True)

```

"逻辑抽象"是Python中广泛使用修饰符的原因之一。

### 提供一个通用的代码模板，可以把它作为基础来编写新的修饰符

tmpl_decorator.py
```

from functools import wraps

def decorator_name(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        # 1. Code to execute BEFORE calling the decorated function.

        # 2. Call the decorated function as required, returning its
        #    results if needed.
             return func(*args, **kwargs)

        # 3. Code to execute INSTEAD of calling the decorated function.
    return wrapper
```

### 再来限制对/viewlog的访问

vsearch4web.py
```
from flask import Flask, render_template, request, escape, session
from vsearch import search4letters

from DBcm import UseDatabase
from checker import check_logged_in

from time import sleep

app = Flask(__name__)

app.config['dbconfig'] = dbconfig = {'host':'10.83.16.43',
            'user':'root',
            'password':'zU!ykpx3EG)$$1e6',
            'database':'acc_adapter',}


@app.route('/login')
def do_login() -> str:
    session['logged_in'] = True
    return 'You are now logged in.'


@app.route('/logout')
def do_logout() -> str:
    session.pop('logged_in')
    return 'You are now logged out.'


def log_request(req: 'flask_request', res: str) -> None:
    """Log details of the web request and the results."""

    # raise Exception("Something awful just happened.")

    with UseDatabase(app.config['dbconfig']) as cursor:
        _SQL = """insert into log
                  (phrase, letters, ip, browser_string, results)
                  values
                  (%s, %s, %s, %s, %s)"""
        cursor.execute(_SQL, (req.form['phrase'],
                              req.form['letters'],
                              req.remote_addr,
                              req.user_agent.browser,
                              res, ))


@app.route('/search4', methods=['POST'])
def do_search() -> 'html':
    """Extract the posted data; perform the search; return results."""
    phrase = request.form['phrase']
    letters = request.form['letters']
    title = 'Here are your results:'
    results = str(search4letters(phrase, letters))
    try:
        log_request(request, results)
    except Exception as err:
        print('***** Logging failed with this error:', str(err))
    return render_template('results.html',
                           the_title=title,
                           the_phrase=phrase,
                           the_letters=letters,
                           the_results=results,)


@app.route('/')
@app.route('/entry')
def entry_page() -> 'html':
    """Display this webapp's HTML form."""
    return render_template('entry.html',
                           the_title='Welcome to search4letters on the web!')


@app.route('/viewlog')
@check_logged_in
def view_the_log() -> 'html':
    """Display the contents of the log file as a HTML table."""
    try:
        with UseDatabase(app.config['dbconfig']) as cursor:
            _SQL = """select phrase, letters, ip, browser_string, results
                    from log"""
            cursor.execute(_SQL)
            contents = cursor.fetchall()
        # raise Exception("Some unknown exception.")
        titles = ('Phrase', 'Letters', 'Remote_addr', 'User_agent', 'Results')
        return render_template('viewlog.html',
                            the_title='View Log',
                            the_row_titles=titles,
                            the_data=contents,)
    except ConnectionError as err:
        print('Is your database switched on? Error:', str(err))
    except CredentialsError as err:
        print('User-id/Password issues. Error:', str(err))
    except SQLError as err:
        print('Is your query correct? Error:', str(err))
    except Exception as err:
        print('Something went wrong:', str(err))    
    return 'Error'


app.secret_key = 'YouWillNeverGuessMySecretKey'

if __name__ == '__main__':
    app.run(debug=True)
```




---
<h1 id="11">11 | 异常处理：出问题了怎么办</h1>

---

[Python内置异常完整列表](https://docs.python.org/3/library/exceptions.html)   

**Python 3 Exception hierarchy**
```
BaseException
 +-- SystemExit
 +-- KeyboardInterrupt
 +-- GeneratorExit
 +-- Exception
      +-- StopIteration
      +-- StopAsyncIteration
      +-- ArithmeticError
      |    +-- FloatingPointError
      |    +-- OverflowError
      |    +-- ZeroDivisionError
      +-- AssertionError
      +-- AttributeError
      +-- BufferError
      +-- EOFError
      +-- ImportError
      |    +-- ModuleNotFoundError
      +-- LookupError
      |    +-- IndexError
      |    +-- KeyError
      +-- MemoryError
      +-- NameError
      |    +-- UnboundLocalError
      +-- OSError
      |    +-- BlockingIOError
      |    +-- ChildProcessError
      |    +-- ConnectionError
      |    |    +-- BrokenPipeError
      |    |    +-- ConnectionAbortedError
      |    |    +-- ConnectionRefusedError
      |    |    +-- ConnectionResetError
      |    +-- FileExistsError
      |    +-- FileNotFoundError
      |    +-- InterruptedError
      |    +-- IsADirectoryError
      |    +-- NotADirectoryError
      |    +-- PermissionError
      |    +-- ProcessLookupError
      |    +-- TimeoutError
      +-- ReferenceError
      +-- RuntimeError
      |    +-- NotImplementedError
      |    +-- RecursionError
      +-- SyntaxError
      |    +-- IndentationError
      |         +-- TabError
      +-- SystemError
      +-- TypeError
      +-- ValueError
      |    +-- UnicodeError
      |         +-- UnicodeDecodeError
      |         +-- UnicodeEncodeError
      |         +-- UnicodeTranslateError
      +-- Warning
           +-- DeprecationWarning
           +-- PendingDeprecationWarning
           +-- RuntimeWarning
           +-- SyntaxWarning
           +-- UserWarning
           +-- FutureWarning
           +-- ImportWarning
           +-- UnicodeWarning
           +-- BytesWarning
           +-- ResourceWarning
```

产生一个运行时错误时，可以捕获这个错误，也可以不捕获："try"允许你捕获所产生的错误，"except"则允许你处理这个异常。

#### 没有做异常处理
try_examples.py
```
with open('myfile.txt') as fh:
    file_data = fh.read()
print(file_data)
```
try_examples.py运行输出结果：
```
Traceback (most recent call last):
  File "D:\learn\python\python-head-first\chapter11\try_examples.py", line 2, in <module>
    with open('myfile.txt') as fh:
FileNotFoundError: [Errno 2] No such file or directory: 'myfile.txt'
>>> 
```

#### 文件不存在（FileNotFoundError）
try_examples2.py
```
try:
    with open('myfile.txt') as fh:
        file_data = fh.read()
    print(file_data)
except FileNotFoundError:
    print('The data file is missing.')
```

try_examples2.py运行输出结果：
```
The data file is missing.
>>> 
```

### try一次，except多次

要想避免产生另一个异常，只需要为try语句增加另一个except代码组，指定异常，并在这个新的except代码组中提供你认为
必要的代码。

#### 文件存在，但没有访问权限（PermissionError）
try_examples3.py
```
try:
    with open('myfile.txt') as fh:
        file_data = fh.read()
    print(file_data)
except FileNotFoundError:
    print('The data file is missing.')
except PermissionError:
    print('This is not allowed.')
```


### 如果产生的异常是你没有遇见到的，会发生什么情况？

Python允许你定义一个“捕获所有异常的”（**catch-all**）except代码组，只要出现一个没有明确指定的运行时异常，就会执行这个代码组。

#### 把myfile.txt从一个文件改为一个文件夹（IsADirectoryError）

try_examples4.py
```
try:
    with open('myfile.txt') as fh:
        file_data = fh.read()
    print(file_data)
except FileNotFoundError:
    print('The data file is missing.')
except PermissionError:
    print('This is not allowed.')
except:
    print('Some other error occurred.')
```

这里最后一个"except"语句是“空的”，它没有指定某个特定的异常，它提供了一个捕获所有异常的异常处理器。

### Python在处理异常时得到与之关联的数据

方法：   
1. 使用sys模块的功能
2. 使用扩展的try/except语句


简单的忽略异常，把下面代码增加到一个try语句的最后（但通常我们一定要编写错误检查代码来处理异常）
```
except:
    pass
```

### 从"sys"了解异常

标准库提供了一个名为sys的模块，可以利用这个模块访问解释器的内部信息（运行时可用的一组变量和函数）。

exc_info 就是这样一个函数，它会提供当前处理的异常的有关信息。调用 exc_info时，这个函数会返回一个包括3个值的元组，其中第一个值指示异常
的**类型**，第二个值详细描述异常的**值**，第三个值包含一个**回溯跟踪对象**，允许访问回溯跟踪消息。如果当前没有异常，exc_info会对各个
元组值返回Python的null值，即：（None， None， None）。

```
>>> import sys
>>> try:
	1/0
except:
	err = sys.exc_info()
	for e in err:
		print(e)

		
<class 'ZeroDivisionError'>
division by zero
<traceback object at 0x0000026F7F7C31C0>
>>> 
```

Python扩展了 **try/except** 语法，可以方便地得到sys.exc_info函数返回的这个信息。它不仅可以做到这一点，而且不需要
你记住导入sys模块，也不用处理这个函数返回的元组。

try_examples5.py
```
try:
    with open('myfile.txt') as fh:
        file_data = fh.read()
    print(file_data)
except FileNotFoundError:
    print('The data file is missing.')
except PermissionError:
    print('This is not allowed.')
except Exception as err:
    print('Some other error occurred:', str(err))
```

一个“空的” except语句就已经能捕获所有异常，不是吗？没错：确实是这样。不过可以用一个as关键字扩展except Exception语句，这允许你将当前的异常对象赋给
一个变量，并创建更有信息含量的错误消息。


### 回到我们的Web应用代码p471

前面我们指出 vsearch4web.py 的 do_search 函数中 log_request 调用可能存在问题（调用失败时该怎么做）。

出现某种类型的错时，Web应用会提供一个不太友好的错误页面作为响应，这可能会让Web应用的用户很困惑。
```
mysql.connector.errors.InterfaceError
```
记录Web请求很重要，但用户对此不关心，他们只想看到搜索结果。


### 安静的处理异常

以一种适当的方式处理所产生的异常，使得Web应用的用户不会注意到这些异常。

为 log_request 调用实现一个捕获所有异常的异常处理器
```
@app.route('/search4', methods=['POST'])
def do_search() -> 'html':
    """Extract the posted data; perform the search; return results."""
    phrase = request.form['phrase']
    letters = request.form['letters']
    title = 'Here are your results:'
    results = str(search4letters(phrase, letters))
    try:
        log_request(request, results)
    except Exception as err:
        print('***** Logging failed with this error:', str(err))
    return render_template('results.html',
                           the_title=title,
                           the_phrase=phrase,
                           the_letters=letters,
                           the_results=results,)
```


关闭后端数据库
```
Can't connect to MySQL server on xxx
```

不正确的用户名连接数据库
```
Access denied for user xxx
```

访问一个不存在的表
```
Table 'xxx' dosen't exist'
```

也可以自己定制异常
```
def log_request(req: 'flask_request', res: str) -> None:
    raise Exception("Something awful just happened.")

    with UseDatabase(app.config['dbconfig']) as cursor:
```


### 处理其他数据库错误

log_request 函数添加了数据库相关的异常处理，但 view_the_log 函数是由Flask调用的，不能在函数中编写代码来保护其调用。
为此，我们可以使用UserDatabase上下文管理器来实现。

首先考虑问题
* 后端数据库可能不可用
* 可能无法登陆正常工作的数据库
* 成功登陆后，数据库查询可能失败
* 可能发送其他（未预见的）情况


### “更多错误”是不是意味着“更多except”？

既然已经了解try/except，可以为 view_the_log 函数增加更多代码，对使用UseDatabase上下文管理器加以保护。
```
@app.route('/viewlog')
@check_logged_in
def view_the_log() -> 'html':
    """Display the contents of the log file as a HTML table."""
    try:
        with UseDatabase(app.config['dbconfig']) as cursor:
            _SQL = """select phrase, letters, ip, browser_string, results
                    from log"""
            cursor.execute(_SQL)
            contents = cursor.fetchall()
        # raise Exception("Some unknown exception.")
        titles = ('Phrase', 'Letters', 'Remote_addr', 'User_agent', 'Results')
        return render_template('viewlog.html',
                            the_title='View Log',
                            the_row_titles=titles,
                            the_data=contents,)
    except ConnectionError as err:
        print('Is your database switched on? Error:', str(err))
    except CredentialsError as err:
        print('User-id/Password issues. Error:', str(err))
    except SQLError as err:
        print('Is your query correct? Error:', str(err))
    except Exception as err:
        print('Something went wrong:', str(err))    
    return 'Error'
```

这种策略当然可行，但当我们针对 InterfaceError 异常增加一个 except 语句，就必须导入 mysql.connector模块（其中定义了这个特定的异常）。

从表面上看，这好像不是一个大问题，不过事实上确实有问题。

### 避免紧耦合的代码

```
import mysql.connector

@app.route('/viewlog')
@check_logged_in
def view_the_log() -> 'html':
    """Display the contents of the log file as a HTML table."""
    try:
        with UseDatabase(app.config['dbconfig']) as cursor:
           ...
 
    except mysql.connector.errors.InterfaceError as err:
        print('Is your database switched on? Error:', str(err))
    except Exception as err:
        print('Something went wrong:', str(err))    
    ...
```

这种方式是有问题的，因为现在vserach4web.py中的代码与MySql数据库是紧耦合的。

如果将来替换数据库为PostgreSQL，对使用UserDatabase的所有代码都需要做修改。我们利用DBcm模块里的UserDatabase提供一个抽象，将vsearch4web.py中的代码与
后端数据库解耦合。


### 再看DBcm模块

DBcm.py
```
import mysql.connector


class UseDatabase:
    def __init__(self, config: dict):
        self.configuration = config

    def __enter__(self) -> 'cursor':
        self.conn = mysql.connector.connect(**self.configuration)
        self.cursor = self.conn.cursor()
        return self.cursor

    def __exit__(self, exc_type, exc_value, exc_traceback):
        self.conn.commit()
        self.cursor.close()
        self.conn.close()
```

useDatabase 实现了3个方法：
* __init__ 在with执行之前提供了一个完成配置的机会
* __enter__ 在with语句开始时执行
* __exit__ 会保证在with代码组结束时执行


### 创建定制异常

我们将调整view_the_log，检查我们的定制异常而不是特定于数据库的mysql.connector.errors.InterfaceError。

确定一个合适的名字，然后定义一个空类，它要继承Python的内置Exception类。一旦定义了一个定制异常，可以使用raise关键字产生这个异常。
之后可以用try/except捕获（和处理）这个异常。

```
>>> class ConnectionError(Exception):
	pass

>>> raise ConnectionError('Cannot connect ... is it time to panic?')
Traceback (most recent call last):
  File "<pyshell#4>", line 1, in <module>
    raise ConnectionError('Cannot connect ... is it time to panic?')
ConnectionError: Cannot connect ... is it time to panic?
>>>
>>>
>>> try:
	raise ConnectionError('Whoops!')
except ConnectionError as err:
	print('Got:', str(err))

	
Got: Whoops!
>>> 
```

修改DBcm模块
```
import mysql.connector

class ConnectionError(Exception):
    """Raised if the backend-database cannot be connected to."""
    pass

class UseDatabase:
    def __init__(self, config: dict):
        self.configuration = config

    def __enter__(self) -> 'cursor':
        try:
            self.conn = mysql.connector.connect(**self.configuration)
            self.cursor = self.conn.cursor()
            return self.cursor
        except mysql.connector.errors.InterfaceError as err:
            raise ConnectionError(err) from err

    def __exit__(self, exc_type, exc_value, exc_traceback) -> None:
        self.conn.commit()
        self.cursor.close()
        self.conn.close()
```

修改vsearch4web.py(一定要从“DBcm”导入“ConnectonError”异常；“except”语句查找“ConnectionError”而不是“InterfaceError”)
```
from DBcm import UseDatabase,ConnectonError
	...
    except ConnectionError as err:
        print('Is your database switched on? Error:', str(err))
	...
```


### “DBcm”还会有什么问题？

SQL查询相关的错误消息和凭据关联的错误消息是不同的，但产生的异常都是 mysql.connector.ProgrammingError。不同于凭据错误，SQL中的错误会
导致执行with语句时产生一个异常。这意味这你需要在多个地方防范这个异常。

凭据错误发生在__enter__，不会执行__exit__方法；SQL查询相关的错误发生在with，会执行__exit__方法。

如果在使用with的地方用一个try/except语句来处理，这个策略有回到了写紧耦合代码的老路上。

如果with代码组中产生的一个异常为捕获，with语句会把这个未捕获异常的详细信息传递到上下文管理器的__exit__方法。

### 创建更多的定制异常

第一个异常名为CredentialsError,__enter__方法中出现ProgrammingError时会产生这个异常。第二个异常名为SQLError，__exit__方法中报告
ProgarammingError时会产生这个异常。

```
class CredentialsError(Exception):
    """Raised if the database is up, but there's a login issue."""
    pass


class SQLError(Exception):
    """Raised if the query caused problems."""
    pass
```

__enter__中可能出现CredentialsError，所以调整这个方法的代码来反映。
```
	...
	
	   def __enter__(self) -> 'cursor':
	        try:
	            self.conn = mysql.connector.connect(**self.configuration)
	            self.cursor = self.conn.cursor()
	            return self.cursor
	        except mysql.connector.errors.InterfaceError as err:
	            raise ConnectionError(err) from err
	        except mysql.connector.errors.ProgrammingError as err:
	            raise CredentialsError(err) from err
	...
```

修改csearch4web.py，增加捕获CredentialsError
```
@app.route('/viewlog')
@check_logged_in
def view_the_log() -> 'html':
    try:
        with UseDatabase(app.config['dbconfig']) as cursor:
            _SQL = """select phrase, letters, ip, browser_string, results
                    from log"""
            cursor.execute(_SQL)
            contents = cursor.fetchall()
        # raise Exception("Some unknown exception.")
        titles = ('Phrase', 'Letters', 'Remote_addr', 'User_agent', 'Results')
        return render_template('viewlog.html',
                            the_title='View Log',
                            the_row_titles=titles,
                            the_data=contents,)
    except ConnectionError as err:
        print('Is your database switched on? Error:', str(err))
    except CredentialsError as err:
        print('User-id/Password issues. Error:', str(err))
    except Exception as err:
        print('Something went wrong:', str(err))    
    return 'Error'
```


### 处理SQLError有所不同

ConnectionError和CredentialsError都是由于执行__enter__方法的代码时出现问题而产生的。产生这些异常时，不会执行相应的with语句。

如果（出于某种原因）SQL查询中包含一个错误，MySQL Connector模块会生成一个ProgrammingError，这个异常出现在上下文管理器中（with
语句中），但未在with语句中捕获，这个异常会作为3个参数传回__exit__方法：异常的类型、异常的值，以及于这个异常关联的回溯跟踪对象。
如果没有产生任何异常，这3个参数（exe_type,exe_value和exe_traceback）都会涉资未None。

在with代码组中产生一个异常但是未捕获时，上下文管理器会终止这个with代码组的代码，跳至__exit__方法，然后执行这个方法。


### 当心代码的位置

如果你计划为__exit__方法增加代码，最好把它放在__exit__中现有代码的后面，以保证肯定会执行这个方法现有代码（并保持上下文管理协议的语义）。


### 完整代码
vsearch4web.py
```
from flask import Flask, render_template, request, escape, session
from vsearch import search4letters

from DBcm import UseDatabase
from checker import check_logged_in

from time import sleep

app = Flask(__name__)

app.config['dbconfig'] = dbconfig = {'host':'10.83.16.43',
            'user':'root',
            'password':'zU!ykpx3EG)$$1e6',
            'database':'acc_adapter',}


@app.route('/login')
def do_login() -> str:
    session['logged_in'] = True
    return 'You are now logged in.'


@app.route('/logout')
def do_logout() -> str:
    session.pop('logged_in')
    return 'You are now logged out.'


def log_request(req: 'flask_request', res: str) -> None:
    """Log details of the web request and the results."""

    # raise Exception("Something awful just happened.")

    with UseDatabase(app.config['dbconfig']) as cursor:
        _SQL = """insert into log
                  (phrase, letters, ip, browser_string, results)
                  values
                  (%s, %s, %s, %s, %s)"""
        cursor.execute(_SQL, (req.form['phrase'],
                              req.form['letters'],
                              req.remote_addr,
                              req.user_agent.browser,
                              res, ))


@app.route('/search4', methods=['POST'])
def do_search() -> 'html':
    """Extract the posted data; perform the search; return results."""
    phrase = request.form['phrase']
    letters = request.form['letters']
    title = 'Here are your results:'
    results = str(search4letters(phrase, letters))
    try:
        log_request(request, results)
    except Exception as err:
        print('***** Logging failed with this error:', str(err))
    return render_template('results.html',
                           the_title=title,
                           the_phrase=phrase,
                           the_letters=letters,
                           the_results=results,)


@app.route('/')
@app.route('/entry')
def entry_page() -> 'html':
    """Display this webapp's HTML form."""
    return render_template('entry.html',
                           the_title='Welcome to search4letters on the web!')


@app.route('/viewlog')
@check_logged_in
def view_the_log() -> 'html':
    """Display the contents of the log file as a HTML table."""
    try:
        with UseDatabase(app.config['dbconfig']) as cursor:
            _SQL = """select phrase, letters, ip, browser_string, results
                    from log"""
            cursor.execute(_SQL)
            contents = cursor.fetchall()
        # raise Exception("Some unknown exception.")
        titles = ('Phrase', 'Letters', 'Remote_addr', 'User_agent', 'Results')
        return render_template('viewlog.html',
                            the_title='View Log',
                            the_row_titles=titles,
                            the_data=contents,)
    except ConnectionError as err:
        print('Is your database switched on? Error:', str(err))
    except CredentialsError as err:
        print('User-id/Password issues. Error:', str(err))
    except SQLError as err:
        print('Is your query correct? Error:', str(err))
    except Exception as err:
        print('Something went wrong:', str(err))    
    return 'Error'


app.secret_key = 'YouWillNeverGuessMySecretKey'

if __name__ == '__main__':
    app.run(debug=True)
```


new-DBcm.py
```
"""The UseDatabase context manager for working with MySQL/MariaDB.

For more information, see Chapters 7, 8, 9, and 11 of the 2nd edition of
Head First Python.

Simple example usage:

    from DBcm import UseDatabase, SQLError

    config = { 'host': '127.0.0.1',
               'user': 'myUserid',
               'password': 'myPassword',
               'database': 'myDB' }

    with UseDatabase(config) as cursor:
        try:
            _SQL = "select * from log"
            cursor.execute(_SQL)
            data = cursor.fetchall()
        except SQLError as err:
            print('Your query broke:', str(err))

Enjoy, and have fun.  (Sorry: Python 3 only, due to type hints and new syntax).
"""

##############################################################################
# Context manager for connecting/disconnecting to a database.
##############################################################################

import mysql.connector


class ConnectionError(Exception):
    """Raised if the backend-database cannot be connected to."""
    pass


class CredentialsError(Exception):
    """Raised if the database is up, but there's a login issue."""
    pass


class SQLError(Exception):
    """Raised if the query caused problems."""
    pass


class UseDatabase:
    def __init__(self, config: dict):
        """Add the database configuration parameters to the object.

        This class expects a single dictionary argument which needs to assign
        the appropriate values to (at least) the following keys:

            host - the IP address of the host running MySQL/MariaDB.
            user - the MySQL/MariaDB username to use.
            password - the user's password.
            database - the name of the database to use.

        For more options, refer to the mysql-connector-python documentation.
        """
        self.configuration = config

    def __enter__(self) -> 'cursor':
        """Connect to database and create a DB cursor.

        Return the database cursor to the context manager.
        Raise ConnnectionError if the database can't be found.
        Raise CredentialsError if the wrong username/password used.
        """
        try:
            self.conn = mysql.connector.connect(**self.configuration)
            self.cursor = self.conn.cursor()
            return self.cursor
        except mysql.connector.errors.InterfaceError as err:
            raise ConnectionError(err) from err
        except mysql.connector.errors.ProgrammingError as err:
            raise CredentialsError(err) from err

    def __exit__(self, exc_type, exc_value, exc_traceback):
        """Destroy the cursor as well as the connection (after committing).

        Raise ProgrammingError as an SQLError, and re-raise anything else.
        """
        self.conn.commit()
        self.cursor.close()
        self.conn.close()
        if exc_type is mysql.connector.errors.ProgrammingError:
            raise SQLError(exc_value)
        elif exc_type:
            raise exc_type(exc_value)
```





---
<h1 id="11-2">11‘ | 关于线程：处理等待</h1>

---

如果一个代码要等待外部系统完成工作，这个代码称为“阻塞代码”，因为程序的执行会阻塞，直到等待结束才会继续。一般经验是，如果阻塞代码
需要很长时间才能运行，这就不是一个好的代码。

比如我们的 log_request函数，它会等待cursor.execute 执行INSERT, view_the_log 函数，执行SQL SELECT 查询时它也会等待。

然而这两个调用操作之后的逻辑是不同的。log_request函数中的 INSERT 不需要阻塞（调用代码并不关心何时写入，它只关心数据确实能写入，
调用代码也不会等待响应），view_the_log 函数中的 SQL SELECT 需要阻塞（它必须等待得到数据后才能继续处理）。

为此我们计划让 log_request函数独立于主Web应用执行，调整Web应用代码，让各个 log_request 调用并发地运行，这意味着你的Web应用不必
再等待 log_request完成（不再有延迟，不论它需要多长时间执行），就可以为另一个用户的另一个请求提供服务。

### 并发代码：有多种选择

1. 第三方模块
2. 标准库

关于Python标准库的各种并发选择，完整的列表请见 https://docs.python.org/3/library/concurrency.html

最有名的一个特性就是threading库，它为操作系统提供的多线程实现提供了一个高层接口。使用时，只需要从threading模块导入Thread类。
```
from threading import Thread
```

要创建一个线程，你要创建一个Thread对象，将一个名为target的参数指定为你希望这个线程执行的函数名，并为另一个名为args的命名参数提供
其他参数（作为一个元组）。再把所创建的Thread对象赋值给你选择的一个变量。


假设现在有一个需要执行30秒才能完成的函数```execute_slowly(glacial, plodding, leaden)```，使用多线程来实现。

```
from threading import Thread

	...
	
t = Thread(taraget=execute_slowly, args=(glacial, plodding, leaden))

t.start()
```

调用“start”时，将由“threading”模块执行与“t”线程相关的函数。调用t.start的代码会继续运行。

调整我们的Web应用，使用 sleep(15) 为log_request代码增加延迟，这时候搜索功能将等待存储完成后才响应。

调整 log_request 为threading多线程调用。
```
@app.route('/search4', methods=['POST'])
def do_search() -> 'html':
    """Extract the posted data; perform the search; return results."""
    phrase = request.form['phrase']
    letters = request.form['letters']
    title = 'Here are your results:'
    results = str(search4letters(phrase, letters))
    try:
        t = Thread(target=log_request, args=(request, results))
        t.start()
    except Exception as err:
        print('***** Logging failed with this error:', str(err))
    return render_template('results.html',
                           the_title=title,
                           the_phrase=phrase,
                           the_letters=letters,
                           the_results=results,)
```

Web应用搜索能正常执行，但保存数据时出错了。
```
127.0.0.1 - - [23/Apr/2021 10:44:29] "[37mPOST /search4 HTTP/1.1[0m" 200 -
Exception in thread Thread-6:
Traceback (most recent call last):
  File "C:\Users\chong.xie\AppData\Local\Programs\Python\Python39\lib\threading.py", line 954, in _bootstrap_inner
    self.run()
  File "C:\Users\chong.xie\AppData\Local\Programs\Python\Python39\lib\threading.py", line 892, in run
    self._target(*self._args, **self._kwargs)
  File "D:\learn\python\mymodules\webapp\vsearch4web.py", line 43, in log_request
    cursor.execute(_SQL, (req.form['phrase'],
  File "C:\Users\chong.xie\AppData\Local\Programs\Python\Python39\lib\site-packages\werkzeug\local.py", line 347, in __getattr__
    return getattr(self._get_current_object(), name)
  File "C:\Users\chong.xie\AppData\Local\Programs\Python\Python39\lib\site-packages\werkzeug\local.py", line 306, in _get_current_object
    return self.__local()
  File "C:\Users\chong.xie\AppData\Local\Programs\Python\Python39\lib\site-packages\flask\globals.py", line 38, in _lookup_req_object
    raise RuntimeError(_request_ctx_err_msg)
RuntimeError: Working outside of request context.

This typically means that you attempted to use functionality that needed
an active HTTP request.  Consult the documentation on testing for
information about how to avoid this problem.
```

这个消息来自Flask，而不是threading模块。
```
This typically means that you attempted to use functionality that needed
an active HTTP request.  Consult the documentation on testing for
information about how to avoid this problem.
```

执行这个线程时，调用用代码（do_search函数）会继续执行。将执行render_template函数（这会很快完成），然后do_search函数结束。

do_search结束时，与这个函数关联的所有数据（它的上下文）会由解释器回收。变量request，phrase，letters 和 results 都不再存在。不过，request 和 results
变量会作为参数传递到log_request，log_request 会在15秒后访问者两个变量，但这两个变量已经不存在了，因为do_search结束。


 实际上，Flask 为此提供了一个很有帮助的修饰符。```copy_current_request_context```，这个修饰符可以确保HTTP请求仍时活动的，也就是说，如果调用一个函数
 时有活动的HTTP请求，以后在线程中执行这个函数时这个请求仍是活动的。
 
### from flask import copy_current_request_context

与其他修饰符一样，在Web应用代码最前面导入列表增加 copy_current_request_context 。用@语法将这个修饰符应用到一个现有函数。

**注意：所修饰的函数必须在调用它的函数中定义，被修饰函数必须嵌套在其调用函数中（作为一个内部函数）**。

对Web应用的vsearch4web.py代码做出相应的修改：
1. 代码最前面导入模块```from flask import copy_current_request_context```
2. 将 log_request 函数嵌套在 do_search 函数中
3. 用 @copy_current_request_context 修饰 log_request
 
```
...
@app.route('/search4', methods=['POST'])
def do_search() -> 'html':
    """Extract the posted data; perform the search; return results."""
    @copy_current_request_context
    def log_request(req: 'flask_request', res: str) -> None:
        sleep(15);
        with UseDatabase(app.config['dbconfig']) as cursor:
            _SQL = """insert into log
                  (phrase, letters, ip, browser_string, results)
                  values
                  (%s, %s, %s, %s, %s)"""
            cursor.execute(_SQL, (req.form['phrase'],
                              req.form['letters'],
                              req.remote_addr,
                              req.user_agent.browser,
                              res, ))

        
    phrase = request.form['phrase']
    letters = request.form['letters']
    title = 'Here are your results:'
    results = str(search4letters(phrase, letters))
    try:
        t = Thread(target=log_request, args=(request, results))
        t.start()
    except Exception as err:
        print('***** Logging failed with this error:', str(err))
    return render_template('results.html',
                           the_title=title,
                           the_phrase=phrase,
                           the_letters=letters,
                           the_results=results,)
...
```




---
<h1 id="12">12 | 高级迭代：疯狂地循环</h1>

---

我们的程序往往会在循环上花大量时间。谈到优化循环时，一般有两种方法：1. 改进循环语法（从而更容易地建立循环）2. 改进循环的执行（使循环
更快地执行）。在Python2中，Python设计者增加了一个实现这两种方法的语言特性，它有一个奇怪的名字：**推导式(comprehension)** 。

### CSV数据读取为列表

```
>>> import os
>>> os.chdir('D:/learn/python/python-head-first/chapter12')
>>> 
>>> with open('buzzers.csv') as raw_data:
	print(raw_data.read())

	
TIME,DESTINATION
09:35,FREEPORT
17:00,FREEPORT
09:55,WEST END
19:00,WEST END
10:45,TREASURE CAY
12:00,TREASURE CAY
11:45,ROCK SOUND
17:55,ROCK SOUND

>>> 
```

标准库专门提供了一个名为csv的模块。

```
>>> import csv
>>> with open('buzzers.csv') as data:
	for line in csv.reader(data):
		print(line)

		
['TIME', 'DESTINATION']
['09:35', 'FREEPORT']
['17:00', 'FREEPORT']
['09:55', 'WEST END']
['19:00', 'WEST END']
['10:45', 'TREASURE CAY']
['12:00', 'TREASURE CAY']
['11:45', 'ROCK SOUND']
['17:55', 'ROCK SOUND']
>>> 
```

csv模块会从文件读取每一行原始数据，然后将它转换为一个包含两个数据项的列表（包括标题信息）。**注意：**返回的各个数据项的类型，它们都是字符串，
尽管每个列表中的第一项（显然）表示一个时间。

### CSV数据读取为字典

```
>>> with open('buzzers.csv') as data:
	for line in csv.DictReader(data):
		print(line)

		
{'TIME': '09:35', 'DESTINATION': 'FREEPORT'}
{'TIME': '17:00', 'DESTINATION': 'FREEPORT'}
{'TIME': '09:55', 'DESTINATION': 'WEST END'}
{'TIME': '19:00', 'DESTINATION': 'WEST END'}
{'TIME': '10:45', 'DESTINATION': 'TREASURE CAY'}
{'TIME': '12:00', 'DESTINATION': 'TREASURE CAY'}
{'TIME': '11:45', 'DESTINATION': 'ROCK SOUND'}
{'TIME': '17:55', 'DESTINATION': 'ROCK SOUND'}
```

使用DictReader时，CSV文件的数据会作为一组字典返回，每个字典的键取自CSV文件的标题行，值来自后面的各行。

```
>>> import pprint

>>> with open('buzzers.csv') as data:
	ignore = data.readline()
	flights = {}
	for line in data:
		k, v = line.split(',')
		flights[k] = v

		
>>> pprint.pprint(flights)
{'09:35': 'FREEPORT\n',
 '09:55': 'WEST END\n',
 '10:45': 'TREASURE CAY\n',
 '11:45': 'ROCK SOUND\n',
 '12:00': 'TREASURE CAY\n',
 '17:00': 'FREEPORT\n',
 '17:55': 'ROCK SOUND\n',
 '19:00': 'WEST END\n'}
>>> 
```

### 去除空白符

```
>>> import pprint

>>> with open('buzzers.csv') as data:
	ignore = data.readline()
	flights = {}
	for line in data:
		k, v = line.strip().split(',')
		flights[k] = v

		
>>> pprint.pprint(flights)
{'09:35': 'FREEPORT',
 '09:55': 'WEST END',
 '10:45': 'TREASURE CAY',
 '11:45': 'ROCK SOUND',
 '12:00': 'TREASURE CAY',
 '17:00': 'FREEPORT',
 '17:55': 'ROCK SOUND',
 '19:00': 'WEST END'}
>>> 
```

左边是一个变量元组，右边的代码会生成一个值列表（如果把方法像这样串在一起，这称为一个“方法链”）
```
k, v = line.strip().split(',')
```

### 时间转换函数

```
>>> from datetime import datetime
>>> 
>>> def convert2ampm(time24: str) -> str:
	return datetime.strptime(time24, '%H:%M').strftime('%I:%M%p')


>>> convert2ampm('19:00')
'07:00PM'
>>> 
```

关于字符串格式指示符的更多信息，参见https://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior



```
from datetime import datetime
import pprint

def convert2ampm(time24: str) -> str:
    return datetime.strptime(time24, '%H:%M').strftime('%I:%M%p')
```

```
from datetime import datetime
import pprint

def convert2ampm(time24: str) -> str:
    return datetime.strptime(time24, '%H:%M').strftime('%I:%M%p')


with open('buzzers.csv') as data:
    ignore = data.readline()
    flights = {}
    for line in data:
        k, v = line.strip().split(',')
        flights[k] = v

    pprint.pprint(flights)
    print()

    flights2 = {}
    for k, v in flights.items():
        flights2[convert2ampm(k)] = v.title()
    pprint.pprint(flights2)
```
结果输出：
```
>>> 
======== RESTART: D:/learn/python/python-head-first/chapter12/convert.py =======
{'09:35': 'FREEPORT',
 '09:55': 'WEST END',
 '10:45': 'TREASURE CAY',
 '11:45': 'ROCK SOUND',
 '12:00': 'TREASURE CAY',
 '17:00': 'FREEPORT',
 '17:55': 'ROCK SOUND',
 '19:00': 'WEST END'}

{'05:00PM': 'Freeport',
 '05:55PM': 'Rock Sound',
 '07:00PM': 'West End',
 '09:35AM': 'Freeport',
 '09:55AM': 'West End',
 '10:45AM': 'Treasure Cay',
 '11:45AM': 'Rock Sound',
 '12:00PM': 'Treasure Cay'}
>>> 
```


### 发现代码中的模式了吗？

* 每个“for”循环前面都创建了一个新的空数据结构；
* 每个“for”循环代码组中包含的代码会处理现有数据，并基于所做的处理将数据增加到这个新数据结构。


### 发现列表的模式

```
>>> fight_times = []
>>> for ft in flights.keys():
	fight_times.append(convert2ampm(ft))

	
>>> pprint.pprint(fight_times)
['09:35AM',
 '05:00PM',
 '09:55AM',
 '07:00PM',
 '10:45AM',
 '12:00PM',
 '11:45AM',
 '05:55PM']
>>> 
>>> 
>>> destinations = []
>>> for dest in flights.values():
	destinations.append(dest.title())

	
>>> pprint.pprint(destinations)
['Freeport',
 'Freeport',
 'West End',
 'West End',
 'Treasure Cay',
 'Treasure Cay',
 'Rock Sound',
 'Rock Sound']
>>> 
````


### 将模式转换为推导式

```
destinations = []
for dest in flights.values():
	destinations.append(dest.title())
```

利用Python的内置推导式特性，可以把上面的3行代码改写为1行代码。

1. 首先从一个新的空列表开始（并指定一个名字） ```more_dests = []```
2. 迭代处理各个目的地（注意这里没有冒号） ```more_dests = [for dest in flights.values()]```
3. 将转换后的数据追加到新列表，但没有具体调用“append” ```more_dests = [dest.title() for dest in flights.values()]```


**学习推导式的原因：**
1. 除了需要更少的代码，Python解释器还优化为可以尽可能快地运行推导式。这意味着推导式比等价的for循环代码执行得更快。
2. 有些地方无法使用for循环，但是可以使用推到式。实际上，目前为止提供得所有推到式都出现再赋值操作符右边，而常规的
for循环无法做到这一点。


### 字典推导式

```
    flights2 = {}
    for k, v in flights.items():
        flights2[convert2ampm(k)] = v.title()
```

等价推导式

```
more_flights = {convert2ampm(k): v.title() for k, v in flights.items()}
```

### 用过滤器扩展推导式

```
>>> just_freeport = {}
>>> for k, v in flights.items():
	if v == 'FREEPORT':
		just_freeport[convert2ampm(k)] = v.title()
```

根据v中的当前值完成过滤，像这样使用if来过滤数据是一种标准技术。这种过滤器也可以再推导式中使用。只需要取出if语句
（去掉冒号），把它放在推导式的末尾。

```
>>> more_flights = {convert2ampm(k): v.title() for k, v in flights.items() if v == 'FREEPORT'}
```


### 用Python的方式处理复杂性

```
+--------------------------------+
|   {'05:00PM': 'Freeport',      |              
| '05:55PM': 'Rock Sound',       |              +-----------------------------------------+
| '07:00PM': 'West End',         |              |{'Freeport': ['09:35AM', '05:00PM'],     |
| '09:35AM': 'Freeport',         |              |'Rock Sound': ['11:45AM', '05:55PM'],    |
| '09:55AM': 'West End',         |  =========>> |'Treasure Cay': ['10:45AM', '12:00PM'],  |
| '10:45AM': 'Treasure Cay',     |              | 'West End': ['09:55AM', '07:00PM']}     |
| '11:45AM': 'Rock Sound',       |              +-----------------------------------------+
| '12:00PM': 'Treasure Cay'}     |
+--------------------------------+
```

得到唯一目的地
```
>>> dests = set(fts.values())
>>> pprint.pprint(dests)
{'Treasure Cay', 'West End', 'Freeport', 'Rock Sound'}
>>> 
```

抽取一个目的地的时间
``` 
>>> wests = []
>>> for k, v in fts.items():
	if v== 'West End':
		wests.append(k)

		
>>> print(wests)
['09:55AM', '07:00PM']
>>> 
```

改为推导式
```
>>> wests2 = [k for k, v in fts.items() if v == 'West End']
>>> print(wests2)
['09:55AM', '07:00PM']
```

抽取所有目的地的飞行时间
```
>>> for dest in set(fts.values()):
	print(dest, '->', [k for k, v in fts.items() if v == dest])

	
Treasure Cay -> ['10:45AM', '12:00PM']
West End -> ['09:55AM', '07:00PM']
Freeport -> ['09:35AM', '05:00PM']
Rock Sound -> ['11:45AM', '05:55PM']
>>> 
```

转换为推导式
```
>>> when = {}
>>> for dest in set(fts.values()):
	when[dest] = [k for k, v in fts.items() if v == dest]

	
>>> pprint.pprint(when)
{'Freeport': ['09:35AM', '05:00PM'],
 'Rock Sound': ['11:45AM', '05:55PM'],
 'Treasure Cay': ['10:45AM', '12:00PM'],
 'West End': ['09:55AM', '07:00PM']}
>>> 
```

把外层循环重写为一个字典推导式
```
>>> when2 = {dest: [k for k, v in fts.items() if v == dest] for dest in set(fts.values())}
>>> 
>>> pprint.pprint(when2)
{'Freeport': ['09:35AM', '05:00PM'],
 'Rock Sound': ['11:45AM', '05:55PM'],
 'Treasure Cay': ['10:45AM', '12:00PM'],
 'West End': ['09:55AM', '07:00PM']}
>>> 
```

这是目前为止最复杂的推导式。这个子字典推导式展示了推导式不同于等价for循环代码的一个特性：可以把推导式放在代码中几乎任何地方，而
for循环做不到这一点，它只能作为语句出现在代码中（也就是说，不能作为表达式的一部分）。

do_convert.py
```

from datetime import datetime
import pprint

def convert2ampm(time24: str) -> str:
    return datetime.strptime(time24, '%H:%M').strftime('%I:%M%p')

with open('buzzers.csv') as data:
    ignore = data.readline()
    flights = {}
    for line in data:
        k, v = line.strip().split(',')
        flights[k] = v

pprint.pprint(flights)
print()

fts = {convert2ampm(k): v.title() for k, v in flights.items()}

pprint.pprint(fts)
print()

when = {dest: [k for k, v in fts.items() if v == dest] for dest in set(fts.values())}

pprint.pprint(when)
print()
```

结果输出
```
>>> 
====== RESTART: D:\learn\python\python-head-first\chapter12\do_convert.py ======
{'09:35': 'FREEPORT',
 '09:55': 'WEST END',
 '10:45': 'TREASURE CAY',
 '11:45': 'ROCK SOUND',
 '12:00': 'TREASURE CAY',
 '17:00': 'FREEPORT',
 '17:55': 'ROCK SOUND',
 '19:00': 'WEST END'}

{'05:00PM': 'Freeport',
 '05:55PM': 'Rock Sound',
 '07:00PM': 'West End',
 '09:35AM': 'Freeport',
 '09:55AM': 'West End',
 '10:45AM': 'Treasure Cay',
 '11:45AM': 'Rock Sound',
 '12:00PM': 'Treasure Cay'}

{'Freeport': ['09:35AM', '05:00PM'],
 'Rock Sound': ['11:45AM', '05:55PM'],
 'Treasure Cay': ['10:45AM', '12:00PM'],
 'West End': ['09:55AM', '07:00PM']}

>>> 
```


### 集合推导式的实际使用

集合推导式（或简写为setcomp）允许你用一行代码创建一个新的集合。集合推导式用大括号包围，字典推导式也是如此，为了区分二者，
可以在字典中寻找冒号字符作为分隔符，而在集合中，冒号没有任何意义。

```
>>> vowels = {'a', 'e', 'i', 'o', 'u'}
>>> message = "Don't forget to pack your towel."
>>> found = set()
>>> for v in vowels:
	if v in message:
		found.add(v)

		
>>> print(found)
{'o', 'a', 'u', 'e'}
>>> 
```

推导式模式
```
>>> found2 = {v for v in vowels if v in message}
>>> print(found2)
{'o', 'a', 'u', 'e'}
```


### 如何发现推导式

* 如果发现代码用 **[** 和 **]** 包围，说明这是一个列表推导式。   
* 如果发现代码用 **大括号** 包围，这就可能是一个推导式（集合推导式或字典推导式，字典推导式很容易发现，因为其中会使用冒号字符作为分隔符）。   
* 如果发现代码用 **(** 和 **)** 包围，不要认为是“元组推导式”，这是一种特殊情况，实际上，根本不存在“元组推导式”。


### 关于“元组推导式”？

“元组推导式”的想法根本没有意义。元组四不可变的，一旦创建一个元组，它就不能再改变，这也意味着，不可能在代码中生成一个元组的值。


创建一个新的空元组，不能追加到一个已有的元组，因为它是不可变的。
```
>>> name = ()
>>> for n in ('John', 'Paul', 'George', 'Ringo'):
	name.append(n)

	
Traceback (most recent call last):
  File "<pyshell#4>", line 2, in <module>
    name.append(n)
AttributeError: 'tuple' object has no attribute 'append'
>>> 
```

### 代码周围加小括号 == 生成器

遇到一个看上去像列表推导式但用小括号包围的代码时，你看到的实际上是一个**生成器**。

看下面示例，第二个循环与第一个循环只有很小的差别：列表推导式两边的中括号（循环1）被替换威小括号（循环2）：
```
>>> for i in [x*3 for x in [1, 2, 3, 4, 5]]:
	print(i)

	
3
6
9
12
15
>>> for i in (x*3 for x in [1, 2, 3, 4, 5]):
	print(i)

	
3
6
9
12
15
>>> 
```

循环2，看起来像一个列表推导式，但并不是：这是一个生成器。使用列表推导式的地方都可以使用生成器，会生成同样的结果。

生成器和列表推导式可以生成通用的数据。不过，他们的执行方式不同。

执行一个列表推导式时，它会在做其他处理之前生成**所有**数据。例如循环1中，这个佛如循环在列表推导式完成之前不会处理
列表推导式生成的任何数据。这说明，如果一个列表推导式要花很长时间生成数据，其他代码就会延迟到这个列表推导式完成之后
才能运行（存在2个问题，必须等待、占用内存）。

生成器代码一旦生成数据，就会是否这个数据（没有等待、只需要一个数据项的内存）。


### 使用列表推导式处理URL

```
>>> import requests
>>> urls = ('http://acc-lxgm-adapter.dev.weshareholdings.com/','http://acc-repay.dev.weshareholdings.com/','http://acc-loan.dev.weshareholdings.com/')
>>> 
>>> for resp in [requests.get(url) for url in urls]:
	print(len(resp.content), '->', resp.status_code, '->', resp.url)

	
341 -> 200 -> http://acc-lxgm-adapter.dev.weshareholdings.com/
331 -> 200 -> http://acc-repay.dev.weshareholdings.com/
338 -> 200 -> http://acc-loan.dev.weshareholdings.com/
>>> 
```

你会注意到，进入for循环代码和看到结果之间有一个延迟。因为，列表推导式向for循环提供结果之前会处理urls中的所有URL。


### 使用生成器处理URL

```
>>> import requests
>>> urls = ('http://acc-lxgm-adapter.dev.weshareholdings.com/','http://acc-repay.dev.weshareholdings.com/','http://acc-loan.dev.weshareholdings.com/')
>>> 
>>> for resp in (requests.get(url) for url in urls):
	print(len(resp.content), '->', resp.status_code, '->', resp.url)

	
341 -> 200 -> http://acc-lxgm-adapter.dev.weshareholdings.com/
331 -> 200 -> http://acc-repay.dev.weshareholdings.com/
338 -> 200 -> http://acc-loan.dev.weshareholdings.com/
>>> 
```

无需等待。只要有数据就开始处理。

### 使用生成器，发生了什么？

如果比较列表推导式和生成器生成的结果，他们是一样的。不过，代码的行为却不同。

列表推导式会等待所有数据都生成，然后才会把这些数据传送到正在等待的for循环，而生成器有所不同，只要数据可用他就会提供数据。
这说明，使用生成器的循环响应性更好，而使用列表推导式的循环会让你等待。

生成器确实很好，但这并不表示要把所有的列表推导式都替换为等价的生成器。需求决定选择，如果你可以接受等待，列表推导式就很好；
否则，就应该考虑使用生成器。


### 定义你的函数要做什么

生成器还有一个有意思的用法：可以嵌套在函数中。

### 生成器函数中使用 yield

url_utils.py
```
import requests

def gen_from_urls(urls: tuple) -> tuple:
    for resp in (requests.get(url) for url in urls):
        yield len(resp.content), resp.status_code, resp.url
```

代码要“返回” requests.get 函数完成GET请求的结果（返回给等待的“for”循环）。你可能想增加下面的代码行为作为for的代码组，
**但不要这么做**。

```
return len(resp.content), resp.status_code, resp.url
```

一个函数执行一个return语句时，这个函数会终止。这里并不希望这样，因为gen_from_urls函数会在for循环中调用，每次调用这个函数
时希望得到一个不同的结果元组。

不过，如果不能执行return，该怎么做呢？

要使用 **yield**。python中增加yield关键字就是为了支持**生成器函数**的创建，能用return的地方都可以使用yield。使用yield时，
你的函数会成为一个生成器函数，可以从任何迭代器“调用”这个函数，在这里就是从for循环调用。


### 跟踪生成器函数

track_generate_function.py
```
from url_utils import gen_from_urls

urls = ('http://acc-lxgm-adapter.dev.weshareholdings.com/','http://acc-repay.dev.weshareholdings.com/','http://acc-loan.dev.weshareholdings.com/')

for resp_len, status, url, in gen_from_urls(urls):
    print(resp_len, '->', status, '->', url)
```

 在for循环（称为“调用代码”）中调用生成器函数gen_from_urls()。
 ```
 for resp_len, status, url, in gen_from_urls(urls):
 ```
 
 解释器跳至gen_from_urls函数，开始执行它的代码，URL元组会复制到这个函数的唯一参数，然后执行生成器函数的for循环。
 这个for循环包含生成器，它取urls元组中的第一个URL，向指定的服务器发送一个GET请求。从服务器返回HTTP响应时，会执
 行yield语句。
 
 这里并不是执行代码然后移到urls元组中的下一个URL（也就是说，继续gen_from_urls中for循环的下一次迭代），yield会把
 它的3个数据传回调用代码。现在不会终止执行，gen_from_urls函数生成器会等待，就好像挂起来一样......
 
 数据（由yield传回）到达调用代码时，for循环的代码组会执行。由于这个代码组包含一个print BIF调用，所有会执行这行代
 码，在屏幕上显示第一个URL的结果。
 
 然后调用代码的for循环会迭代，从某种意义上讲，这会再次调用gen_from_urls。
 
 但不完全是这样，实际上是gen_from_urls从其挂起状态被唤醒，然后继续运行。gen_from_urls中的for循环迭代，取urls元组
 中的下一个URL，与这个URL的相应服务通信。从服务器返回HTTP响应时，会执行yield语句，将它的3个数据传回给调用代码（函数
 可以通过resp对象访问这些数据）。
 
 与前面一样，现在不会终止执行，gen_from_urls生成器函数会再一次等待，就好像挂起来一样......
 
 数据（由yield传回）到达调用代码时，for循环的代码组会再一次执行，显示第二组结果。
 
 调用代码的for循环会迭代，再一次“调用”gen_from_urls，这会再一次唤醒你的生成器函数。yield语句会执行，结果返回给调用代
 码，显示再次更新。
 
 此时URL元组已经处理完。所以生成器函数和调用代码的for循环都会终止。就好像这两个代码轮流执行一样（每次轮换时在二者之间传递数据）。
 
 
 ### 在字典推导式中（以及“for”循环中）使用生成器函数

use_generate_function_in_dict.py
```
import pprint
from url_utils import gen_from_urls

urls = ('http://acc-lxgm-adapter.dev.weshareholdings.com/','http://acc-repay.dev.weshareholdings.com/','http://acc-loan.dev.weshareholdings.com/')

urls_res = {url: size for size, _, url in gen_from_urls(urls)}
pprint.pprint(urls_res)
```

上面例子展示了将 gen_from_urls 用在一个字典推导式中。注意，这个新字典只需要存储URL（作为键）和登陆页面的大小（作为值）。
由于不需要状态码，使用Python的**默认变量名**（一个下划线字符）让解释器忽略这个信息。


### 章节内容回顾

* 处理文件中的数据时，Python提供了多种选择。除了标准的open BIF，还可以使用标准库csv模块的功能来处理CSV格式的数据。
* **方法链**允许用一行代码完成对数据的处理。```string.strip().split()```方法链在Python代码中很常见。
* 注意方法链中方法的顺序。具体来讲，要注意各个方法返回的数据的类型（并确保类型的兼容性）。
* 将数据从一种格式转换为另一种格式时所用的for循环可以改写为一个**推导式**。
* 可以编写推导式处理现有的列表、字典和集合，列表推导式使用最广泛。有经验的Python程序员把这些构造称为listcomps（列表推导式）、
dictcomps（字典推导式）和setcomps（集合推导式）。
* **列表推导式**是用中括号包围的代码，**字典推导式**是用大括号包围的代码（有冒号分隔符）。**集合推导式**也是用大括号包围
的代码（但没有字典推导式中的冒号）。
* 没有“元组推导式”，因为元组是不可变的（所以试图动态创建元组是没有意义的）。
* 如果发现推导式代码用小括号包围，你看到的是一个**生成器**（可以转换为一个函数，使用yield根据需要生成数据）
