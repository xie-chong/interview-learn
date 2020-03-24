# <p align="center">《Head First JavaScript 程序设计》</p>

<!-- MarkdownTOC -->

[1 | JavaScript速览：进入JavaScript的世界](#01)   
[2 | 编写代码： 更进一步](#02)   
[3 | 函数简介： 养成函数思维](#03)   
[4 | 让数据排排坐： 数组](#04)   
[5 | 理解对象： 对象镇之旅](#05)   
[6 | 与网页交互： 了解DOM](#06)   
[7 | 类型、相等、转换等： 系统地讨论类型](#07)   
[8 | 综合应用： 编写一个应用程序](#08)   
[9 | 异步编程： 处理事件](#09)   
[10 | 函数是一等公民： 自由的函数](#10)   
[11 | 匿名函数、作用域和闭包： 系统的讨论函数](#11)   
[12 | 高级对象构造技巧： 创建对象](#12)   
[13 | 使用原型： 超强的对象创建方式](#13)   
[14 | 附录 遗漏内容： 未涉足的十大主题](#14)   


<!-- /MarkdownTOC -->


---
---
---
<h2 id="01">1 | JavaScript速览：进入JavaScript的世界</h2>

---










---
<h2 id="02">2 | 编写代码： 更进一步</h2>

---















---
<h2 id="03">3 | 函数简介： 养成函数思维</h2>

---














---
<h2 id="04">4 | 让数据排排坐： 数组</h2>

---

bubbles-xiechong.html   
```
<!doctype html>
<html lang="en">
	<head>
		<meta charset="utf-8">
		<title>Bubble-xiechong Factory Test Lab</title>
		<script>
			var scores = [60, 50, 60, 58, 54, 54,
				58, 50, 52, 54, 48, 69,
				34, 55, 51, 52, 44, 51,
				69, 64, 66, 55, 52, 61,
				46, 31, 57, 52, 44, 18,
				41, 53, 55, 61, 51, 44
			];

			//
			// with a for loop
			//
			var highScore = 0;
			var bestSolutions = [];
			for (var i = 0; i < scores.length; i++) {
				console.log("Bubble solution #" + i + " score: " + scores[i]);
				if (scores[i] > highScore) {
					highScore = scores[i];
					bestSolutions = [];
					bestSolutions.push(i);
				} else if (scores[i] == highScore) {
					bestSolutions.push(i);
				}
			}
			console.log("Bubbles tests: " + scores.length);
			console.log("Highest bubble score: " + highScore);
		</script>
	</head>
	<body>
	</body>
</html>
```


