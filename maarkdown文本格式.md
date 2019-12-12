
## 文本对齐方式   

```
<p align="left">居左文本</p>
<p align="center">居中文本</p>
<p align="right">居右文本</p>
```

## 如何在 GitHub 录入数学公式   

GitHub Flavored Markdown 曾经是支持 LaTeX 的，但是现在不支持了……因此若想在 README 或者评论中插入数学公式，就得使用另外的方式。

CodeCogs 提供了一个[在线 LaTeX 编辑器](https://link.jianshu.com/?t=https://www.codecogs.com/latex/eqneditor.php)，可以将输入的数学公式转换为图片，并自动生成 HTML 代码（也支持其他格式）。

比如输入下面的代码：

S = 1 / ( (1 - p) + p / n )

便可以得到：

<img src="https://latex.codecogs.com/gif.latex?S&space;=&space;\frac{1}{(1-p)&plus;\frac{p}{n}}" title="S = \frac{1}{(1-p)+\frac{p}{n}}" />   

由于 Markdown 允许嵌入 HTML，所以直接把生成的代码粘贴过去就可以了。
