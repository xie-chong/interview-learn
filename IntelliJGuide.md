
# <p align="center">IntelliJGuide</p>

- [1 | 快捷键](#1)   
- [2 | 常用操作](#2)   
  - [2.1 intellij idea 如何将一个普通项目转换为maven项目](#2.1)   
  - [2.2 如何将多个maven工程合并到一个工程中管理](#2.2)   
- [3 | 项目JDK版本配置](#3)   



---
---
---
<h1 id="1">1 | 快捷键</h1>

---

Alt+Insert （快速生成构造方法和setter，getter）   
Alt+F1	 （快速选择当前编辑的元素）   
Ctrl+Alt+T (Code | Surround With) and choose try / catch.   
Ctrl+Shift+空格	The SmartType code completion may be used after the new keyword.   
Ctrl+Alt+B	To navigate to the implementation(s) of an abstract method(查看某个抽象类或接口的实现)   
Ctrl+Alt+箭头   （追溯方法的工程中，回到上一个方法，或下一个方法）   
Ctrl+H  （查看继承关系）   
Ctrl+W    
Ctrl+Alt+V   
Ctrl+斜杠 comments or uncomments the current line or selected block with single line comments (//...).   
Ctrl+Shift+斜杠 encloses the selected block in a block comment (/*...*/).   
Shift+F1    
ALT + 7 （可以查看类的字段、属性、方法，是否继承等）   
Ctrl+r  （替换内容）   
Alt + F3 （查找变量。先Alt + F3，然后使用 F3 或 shift + F3, 上下移动）   



Ctrl+D in the editor duplicates the selected block or the current line when no block is selected.   

Tab 	Use the Tab key to move between the template fields. See File | Settings | Live Templates for more details.   

Ctrl+空格	he CodeCompletion feature can suggest a name for a variable when you declare it.   

Ctrl+P	If the cursor is between the parentheses of a method call, pressing Ctrl+P brings up a list of valid parameters.   

Ctrl+Shift+Backspace (Navigate | Last Edit Location) brings you back to the last place where you made changes in the code. Pressing 

Ctrl+Shift+Backspace a few times moves you deeper into your changes history.

Use Ctrl+Shift+F7 (Edit | Find | Highlight Usages in File) to quickly highlight usages of some variable in the current file.   

Use F3 and Shift+F3 keys to navigate through highlighted usages.   

Press Esc to remove highlighting.   

Alt+Q (View | Context Info) to see the declaration of the current method without the need to scroll to it.

Ctrl+E (View | Recent Files) brings a popup list of the recently visited files. Choose the desired file and press Enter to open it.

Use F2/Shift+F2 keys to jump between highlighted syntax errors.   

Use Ctrl+Alt+向上箭头/Ctrl+Alt+向下箭头 shortcuts to jump between compiler error messages or search operation results.

To skip warnings right click on the validation side bar / marker bar and choose Go to high priority problems only.

Use Ctrl+J to complete any valid Live Template abbreviation if you don't remember it. For example, type it and press Ctrl+J to see what happens.


When using basic code completion (Ctrl+空格), type any characters that exist anywhere in an identifier.

Use Alt+向上箭头 and Alt+向下箭头 keys to quickly move between methods in the editor.

Ctrl+Shift+J shortcut joins two lines into one and removes unnecessary space to match your code style.

To see the inheritance hierarchy for a selected class, press Ctrl+H (Navigate | Type Hierarchy). You can also invoke the hierarchy view right from the editor to see the hierarchy for the currently edited class.

To easily evaluate the value of any expression while debugging the program, select its text in the editor (you may press a Ctrl+W a few times to efficiently perform this operation) and press Alt+F8.


Use Ctrl+Shift+Enter to complete a current statement such as if, do-while, try-catch, return (or a method call) into a syntactically correct construct (e.g. add curly braces).

By pressing Alt+Shift+F10 you can access the Run/Debug dropdown on the main toolbar, without the need to use your mouse.









---
<h1 id="2">2 | 常用操作</h1>

---


---
<h2 id="2.1">2.1 intellij idea 如何将一个普通项目转换为maven项目</h1>

---

1. 工程文件下新建文件pom.xml，并填写好内容。

2. 在pom.xml 文件上右键 Add as Maven Project。

**pom.xml**    
```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.xiechong</groupId>
	<artifactId>test</artifactId>
	<version>2.0</version>
	<packaging>pom</packaging>


	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.4.RELEASE</version>
	</parent>


</project>
```








---
<h2 id="2.2">2.2 如何将多个maven工程合并到一个工程中管理</h1>

---

1. 新建一个父工程，删除src文件，配置pom.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.crazyit.boot</groupId>
    <artifactId>cloud_fk</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <modules>
        <module>env-test</module>
    </modules>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.4.RELEASE</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>1.5.4.RELEASE</version>
        </dependency>
    </dependencies>

</project>
```

2. 把子工程copy到父工程文件目录里，修改pom.xml

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.crazyit.cloud</groupId>
    <artifactId>env-test</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <parent>
        <groupId>org.crazyit.boot</groupId>
        <artifactId>cloud_fk</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>


</project>
```







---
<h1 id="3">3 | 项目JDK版本配置</h1>

---


- 1. "ctrl+shift+alt+s" , 打开project settings,确定项目的jdk和sdk是是否配置正确:
    - 1.1 project 下的project SDK,是否为项目需要的jdk:
    - 1.2 确定SDK是否选中:
    - 1.3 language level不能比项目的jdk版本高:
    - 1.4 确定modules下dependencies配置的jdk是否正确:

- 2. "run/dug configurations"  >>>需要运行的项目名称 >>>>>>runner  >>>>JRE配置是否正确:

- 3.按下 "ctrl + alt + s "  打开settings确定Java  compiler 的 Target bytecode version 是否选中项目需要的jdk版本:

- 4. 确定D:\apache-maven-3.2.5\conf下的settings的jdk版本配置正确:
```
<profile>  
    <id>jdk-1.8</id>  
  
    <activation>  
      <activeByDefault>true</activeByDefault>  
      <jdk>1.8</jdk>  
    </activation>  
    <properties>  
      <maven.compiler.source>1.8</maven.compiler.source>  
      <maven.compiler.target>1.8</maven.compiler.target>  
      <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>  
    </properties>  
  
    <repositories>  
      <repository>  
        <id>jdk17</id>  
        <name>Repository for JDK 1.8 builds</name>  
        <url>http://www.myhost.com/maven/jdk18</url>  
        <layout>default</layout>  
        <snapshotPolicy>always</snapshotPolicy>  
      </repository>  
    </repositories>  
  </profile>  
```

- 5 确定pom.xml配置是否正确:
```
<plugin>  
   <groupId>org.apache.maven.plugins</groupId>  
   <artifactId>maven-compiler-plugin</artifactId>  
   <version>2.3.2</version>  
   <configuration>  
      <source>1.8</source>  
      <target>1.8</target>  
      <encoding>UTF-8</encoding>  
   </configuration>  
</plugin>  
```

[参考链接](https://www.cnblogs.com/hkgov/p/8074085.html)
