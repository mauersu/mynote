Wiki 操作入门
写一份逻辑清晰，格式漂亮的文档，也是一名优秀的工程师最基本的素质！清晰的文档和优雅的代码一样，赏心悦目；而混乱的文档和混乱的代码一样，令人生厌。

目录
Wiki 操作入门

格式和排版

段落排版

文字排版

使用技巧

如何在wiki文档中嵌入图形、图片？

编辑 Wiki，可以到 Wiki Sandbox 自行练习

基本准则

使用 Wiki标记符 作为默认编辑器；发布之前使用 预览 看一看效果。尽量不使用附件，除非是 截屏图、演讲ppt。

给每篇文章添加标签，标签用空格分割。

在合适的位置，通常 不是首页添加页面：

如果是通用技术类文章，请在对应的技术topic下新建；

如果是本小组业务相关，请在本小组下新建；

格式和排版
段落排版
正确的使用一级标题（Heading 1），二级标题（Heading 2）等多级标题，以及段落（paragraph）等格式来有层次地组织文档。比如本篇文章，"Wiki 操作入门" 是Heading 1，“基本准则”、“格式和排版”等是 Heading 2。使用方法：光标移动到想要格式化的某个段落上，选择编辑栏最左边的段落格式。

合理的使用有序列表（ordered list）和无序列表（unordered list）来罗列一组观点。有序列表用1、2、3……等数字标识，无序列表前面是圆点。使用方法：光标移动到想要格式化的某个段落上，选择编辑栏上对应的列表格式。

文字排版
文字可以有加粗、斜体、下划线，颜色，删除线等多种装饰，往往用于在段落内突出重点。使用方法：选中想要突出的文字，在编辑栏上选中对应的装饰方式。

使用技巧
如何在wiki文档中嵌入图形、图片？
有多种方法，逐一介绍：

Doc文档转存（不推荐）：
先将文档保存到word中，接着在wiki新建一个页面，在页面右上角找到“工具”-“Doc文件导入”，文档就会按Word中的格式导入到wiki中，几乎无损。
不推荐这种方法是因为导入的图形不能再修改，而且word文档导入后将保留各种奇怪的格式标识，不利于后续的再编辑。

直接插入图片（一般不推荐，除非是需要用到截屏图）：
在其它工具中生成jpg/png等图片，然后在编辑wiki的时候点击上方工具图中的“+”按钮，下拉框中有“image”或者“图片”选项。
直接插入的图片也不便于后期再修改，所以一般不推荐。

使用 Diagramly绘图插件 （所见即所得的绘图插件，推荐用于各种架构图等）： 
 从wiki菜单里选择Insert->Other Macros->Diagramly，就可以了。例如下面的图形就是使用Diagramly插件绘制：






使用PlantUML图形使用PlantUML插件画图（推荐）
 关于UML，可以参考http://www.ibm.com/developerworks/cn/rational/r-uml/ ；
 而PlantUML，可以参考 PlantUML Language Reference Guide.pdf
 

使用Dot图形（推荐用于各种流程图、依赖图等）：
 用过graphviz的同学一定对其强大便利的绘图功能印象深刻，它是用DOT语言画图（参考：DOT语言 ）。在Confluence中，从wiki菜单里选择Insert->Other Macros->PlantUML，就得到一个选项对话框，然后设置"Diagram Type"为dot，确认后就可以试用DOT语言画图了。
例如下面一段简单的代码就可以画出一个复杂的图形：

代码块
Plain Text
digraph g {
  node [shape=plaintext];
  A1 -> B1;
  A2 -> B2;
  A3 -> B3;
 
  A1 -> A2 [label=f];
  A2 -> A3 [label=g];
  B2 -> B3 [label="g'"];
  B1 -> B3 [label="(g o f)'" tailport=s headport=s];
 
  { rank=same; B1; B2; B3; } 
}