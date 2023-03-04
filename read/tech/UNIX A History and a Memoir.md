**为什么不用中文的书名呢？感觉怪怪的。**



Unix的创造者奠定了操作系统的标准基石，Unix的“分而治之”设计哲学——让每个程序做好一件事；要做一件新的工作，就构建新程序，而不是通过增加新“特性”使旧程序复杂化——被优秀程序员奉为圭臬。

 

例如，Unix软件哲学倡导合用既有软件，完成很多不同任务，而不是从头写个新软件。这个例子简明又生动，它在编程领域体现了“分而治之”的故技：将大任务切分为多个小任务，每个小任务都变得更可控，然后再以各种不可思议的方式将之整合到一起。

 

如何将程序切分为多个部分，放到不同的内存页中，当程序运行时，程序页进出内存的交换量保持最小。



贝尔实验室绩效考核方式的好处在于，它基于由理解某项工作的人的共同评估做出。如道格·麦基尔罗伊所言：“合议是这套体系的极妙之处。谁都不必依赖与单个老板的关系。”

 

操作系统看顾每位登录用户，在用户之间快速切换，令每位用户误以为整台计算机都为我所用。这种技术叫作“分时”



当程序出现严重错误时，操作系统会注意到，并试图通过创建一个保存主存储器状况（即磁芯中的内容）的文件来帮程序员定位错误，这就是“磁芯转储”（core dump）一词的由来。



Unix的另一创新是把磁盘、终端等外围设备都看作文件系统中的文件，磁盘是功能列表中提到的“可拆卸卷”。访问设备的系统调用和访问文件的系统调用是一样的，所以同样的代码既可以操作文件也可以操作设备。



管道是一种机制，由操作系统提供，并通过shell轻松访问。它将程序的输出与另一程序的输入连接起来。



道格原本想让程序与程序能够随意连接，但如何自然地描述一个无约束图并不那么容易，而且还存在语义上的问题：在程序之间流动的数据必须正确排队，而程序的无管理连接有可能容纳不了那么长的队列。而且肯无论如何也想不出实际应用场景。



不妨将程序看成一种过滤器，读取数据进来，以某种方式进行处理，然后输出结果。



正则表达式还可以通过赋予某些字符特殊含义来指定更复杂的模式，这些字符称为元字符（metacharacter）



在之后的多年里，更多的功能被添加进来，Bash（Bourne Again Shell的简写，意为“伯恩再来shell”）已经成为大多数Linux和macOS用户事实上的标准shell。



计算机语言的特点主要有两个方面，语法和语义。语法规定了语言是怎样的，什么符合语法，什么不符合语法。语法还定义了语句和函数如何写，算术和逻辑运算符是什么，它们如何组合成表达式，什么名称是合规的，哪些词是保留字，文本字符串和数字如何表达，程序如何格式化等规则。



编译器是一种程序，它能将用一种语言编写的东西翻译成另一种语言中语义等同的东西。



编译过程的第一环节是对程序进行解析（parse），即通过识别名称、常量、函数定义、控制流、表达式等来确定程序的语法结构，以便在后续处理过程中附加合适的语义。



Yacc为语法分析器生成一个C语言文件，Lex为词法分析器生成另一个C语言文件。这两个C语言文件与其他包含语义的C语言文件组合在一起，由C语言编译器编译成可执行程序。



更有效率的做法是，只重新编译修改后的文件，并将结果与之前编译的其他文件连接起来。



使用某种规格语言来描述程序的各个部分是如何相互依赖的。他写的Make程序分析这些规格，并根据文件修改时间来做尽可能少的重新编译，使所有东西都能同步向前。



makefile文件有点像shell脚本，但它采用声明式语言：说明依赖关系和如何更新组件，但不会明确检查文件创建时间。



预处理器有很多优点。首先，在实现一种语言时，不会受到现有语法的限制，可以使用完全不同的风格



今天，sed在shell脚本中经常被用于以某种方式转换数据流：替换字符，添加或删除不需要的空格，或丢弃不需要的东西。



awk程序是模式和动作的序列。每行输入都要测试所有模式，如果模式匹配，则执行相应动作。模式可以是正则表达式，也可以是数字或者字符串关系。不指定模式就会匹配所有行，不指定动作则会输出匹配行。



让C++成为C语言的超集



从SCCS到RCS、CVS和Subversion，再到今天默认的标准版本控制系统Git，有一条清晰的演化路径。



好代码不需要过多注释。以此类推，伟大的代码根本不需要注释。



GNU（“GNU’s not Unix”的递归缩写[1]）是一个大型软件集合，大部分基于Unix模式

 

肯·汤普森和丹尼斯·里奇的部分天才之处在于，他们善于挑选既有的好点子，而且能够洞察普遍概念或统一主题，将软件系统加以简化。人们有时会用代码行数来评价软件的生产力。在Unix的世界里，生产力却往往以删除了多少特殊情况或代码行数来衡量。



管道是典型的Unix发明，是临时连接程序的一种优雅而高效的方式。



将程序当作工具并组合使用是Unix的特色。编写各自做好一件事的小程序，而不是功能繁多的单个大程序，有很多好处。



（i）让每个程序做好一件事。要做一件新的工作，就构建新程序，而不是通过增加新“特性”使旧程序复杂化。

 

富含难题的环境。正如迪克·汉明所说，不研究重要问题，就不可能做重要工作。



我的首选是那些术有专攻的人，至于具体什么专业领域倒在其次。

 

做好研究的最大秘诀是雇用优才，确保让他们做有趣的事情，着眼长久，而且不横加干涉。
