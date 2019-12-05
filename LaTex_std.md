# LaTex
\documentclass[12pt, letterpaper]{article}
\usepackage[utf8]{inputenc}
\usepackage{comment} 

% Title
\title{Document Title}
\author{Nobody \thanks{Somebody}}
\date{Some Date}
-------------------以上为导言部分，\begin{document}和\end{document}之间是正文
\begin{document}

\begin{titlepage}
\maketitle
\end{titlepage}

\tableofcontents

\begin{abstract}
This is a simple paragraph at the beginning of the 
document. A brief introduction about the main subject.
\end{abstract}

First document. This is a simple example, with no 
extra parameters or packages included.
 
% Comments 
\begin{comment}
This text won't show up in the compiled pdf
this is just a multi-line comment. Useful
to, for instance, comment out slow-rendering
while working on the draft.
\end{comment}

\end{document}

一、导言部分LaTex命令简介
1. 当文档内容结构复杂，需要保存在多个LaTex文件时
	\input{filename1}  or \include{filename1}
2. \documentclass 用于设置LaTex文件所生成文档的格式
	\documentclass[options]{class}
	options: 12pt-->字体大小  a4paper/letterpaper-->页面规格
	  class: article-->科技论文，报告，软件文档  proc-->法律文书  beamer-->PPT
	eg:\documentclass[12pt, a4paper]{article}
3. \usepackage命令设置在编译LaTex文件时要导入的扩展包
	\usepackage[utf8]{inputenc}
	\usepackage{comment}
4. 封面格式
	\title{Document Title}
	\author{Nobody \thanks{Somebody}}
	\date{Some Date}
    设置所要生成文档的封面内容：文档名，作者，日期等(这只是设置了封面格式，生成封面的是\maketitle命令)
5. 注释
	% Comments
    从百分号%开始到这一行结束是LaTex文件的注释内容，不在编译后生成的pdf文档中显示
6. 保留字符
	# $ ^ & _ { } \
    这些字符在LaTex中有特殊的意义，要想在生成的文档中显示这些字符，LaTex文档中这些字符前加反斜杠\
    如\# \$，因为\\在LaTex中表示换行命令，输出\可使用 $\backslash$
    ~波浪线(tidle)在LaTex中是插入空格命令，可用数学公式环境的 $\sim$向Tex文档中插入波浪线

二、正文部分LaTex命令模块简介
1. 生成封面
	\begin{titlepage}
	\maketitle
	\end{titlepage}
    按照在preamble中设置的封面格式生成文档那个封面
2. 生成文档目录
	\tableofcontents
3. 设置页码数字类型
	\pagenumbering{digit type}
    其中digit type有:
	arabic --> 阿拉伯数字(1,2,3,4)，默认格式
	roman --> 小写罗马数字(i,ii,iii,iv)
	Roman --> 大写罗马数字(I,II,III,IV)
	alph --> 小写拉丁字母(a,b,c,d)
	Alph --> 大写拉丁字母(A,B,C,D)
    设置页码可使用:
	\setcounter{page}{page number}
    如果想让当前页不标页码，可使用:
	\thispagestyle{empty}
    eg:在封面不标记页码，目录页使用小写罗马数字标记页码，正文部分使用阿拉伯数字标记页码
	\begin{document}

	%% Making title pate
	\begin{titlepage}
	\maketitle
	\thispagestyle{empty} 
	\end{titlepage}

	%% Contents
	\pagenumbering{roman}
	\tableofcontents

	\newpage
	\pagenumbering{arabic}
	\setcounter{page}{1}
4. 版面设置
   1)空白
	text mode or math mode: \,  \kern <len>  \hspace{<len>}  \hphantom{<stuff>}  \  ~  \hfill
	only math mode: \!  \>  \;  \:  \quad  \qquad
	eg: \kern 1cm	\hspace{1cm}
   2)段落行间距
	使用{setspace}扩展包，例如:
	\documentclass[UTF8]{article}
	\usepackage{setspace}

	\begin{document}

	%%双倍行间距 
	\begin{spacing}{2.0}
	段落内容。
	\end{spacing}

	%%单倍行间距 
	\begin{spacing}{1.0}
	段落内容。
	\end{spacing}

	\end{document}
   3)段落间空白
	\hspace{1cm}
	\vspace{5pt}
   4)居中
	\begin{center}
	...
	\end{center}
   5)左对齐
	\begin{flushleft}
	...
	\end{flushleft}
   6)右对齐
	\begin{flushright}
	...
	\end{flushright}
   7)verbatim居中显示
	verbatim环境(抄录环境)使源文件的内容原样呈现于最终文档，这些内容不受center，flushleft，flushright
	等命令的影响，若想让verbatim环境中内容居中显示，需要使用verbatimbox等扩展包
	\usepackage{verbatimbox}
   8)线框
	使用\fbox给文字加线框;使用\parbox给段落添加线框
5. 图片插入及引用
	\usepackage[pdftex]{graphicx}
	% 设置图片文件存放路径
	\graphicspath{{../figures}  
	 
	\begin{document}
	% 在正文中引用图片时使用\ref 
	  In Figure \ref{fig:foo} 
	\begin{figure}
	%设置对齐格式
	\centering   %图片居于页面中部
	%指定图形大小和图形名称  
	\includegraphics [width=0.8，height=2.5]{foo.png} 
	%设置标题 
	\caption{Foo} 
	%设置图形引用名称
	\label{fig:foo} 
	\end{figure}
	 
	\end{document}
6. 表格
    LaTex中使用tabular环境定义表格
	\begin{tabular}{llr}
	\hline 			    	\noalign{\bigskip}
	\multicolumn{2}{c}{Item} \\ 	\noalign{\medskip}
	\cline{1-2} 			\noalign{\smallskip}
	Animal    & Description & Price (\$) \\
	\hline
	Gnat      & per gram    & 13.65      \\
		  & each        & 0.01       \\
	Gnu       & stuffed     & 92.50      \\
	Emu       & stuffed     & 33.33      \\
	Armadillo & frozen      & 8.99       \\
	\hline
	\end{tabular}
	1.设置表格中各个单元格对齐格式:
	  \begin{tabular}[pos]{table spec}
    	  eg:
	  \begin{tabular}{l | l || r} -->表格最多有三列，第1,2列之间纵向分割线，2,3列之间纵向双分割线，
					1,2列左对齐，第3列右对齐
	pos选项通常用于设置表格单元中显示内容相对于其基线的纵向位置，在table spec中可使用>{\format}设置字体
	2.&列分割  \hline画一条水平线	\newline在列中换行  \cline{i-j}在水平方向从地i列到第j列画横线段
	  \multicolum{2}{c}{Item}  Item位于1到2行中间
	3.表格中行间距
	  \noalign{\bigskip}  \noalign{\medskip}  \noalign{\smallskip}
	4.三线表
	  LaTex中默认的线条宽度是0.4pt，如果想要使用粗一点的线条，可以使用booktabs环境包，
	  \usepackage{booktabs}
7. 浮动体
   文档中通常需要插入图片或表格以辅助正文表述，称为浮动体，为了避免不合理的分页，到达理想的排版效果，
   LaTex算法可接受位置描述符等参数调整图标位置，位置描述符有:
	h:LaTex会根据其排版美学做调整
	t:将浮动体放在页的顶部
	b:底部   p:单独成页   !:忽略LaTex的排版美学内置参数   H:浮动体强制放在此处

8. 画图
9. 数学公式
   1)行中公式与独立公式
     a)行中公式:
	\begin{math} x^{2}+y^{2}=z^{2} \end{math}
      或 $ x^{2}+y^{2}=z^{2} $
     b)独立公式:
	$$
	x^{2}+y^{2}=z^{2}
	$$
      或 \begin{equation}
	 x^{2}+y^{2}=z^{2}
	 \end{equation}
	使用{equation}命令默认带公式编号，使用$$命令默认不带公式编号
   2)引用公式
	\begin{equation}\label{eq:Pythagorean theorem}
	x^{2}+y^{2}=z^{2}
	\end{equation}
	公式\ref{eq:Pythagorean theorem}是毕达哥拉斯定理
   3)数学公式中插入中文
	$$
	\mbox{例如: }x_{1}, x_{2}, \cdots, x_{N}
	$$ 
	..........
10. 页脚注释和页边注释
11. 字体与字号
    LaTex字体大小默认为10pt(point) 1pt=0.35146毫米(mm)
    字体由小到大:
	\tiny  \scriptsize  \footnotesize  \small  \normalsize  \large  \Large  \LARGE  \huge  \Huge
	\begin{large}
	....
	\end{large}
12. 颜色
	\textcolor[RGB]{255,0,255}{text}
	\textcolor[rgb]{1.00,0.00,1.00}{text}

	\color{blue}
	...
	\color{black}
    使用xcolor扩展包
	\usepackage{xcolor}
	\textcolor{blue}{text}
	{\color{red}some text}
    设置字符背景颜色
	\colorbox{blue}{text}
    设置字符颜色和背景颜色
	\fcolorbox{blue}{yellow}{text}
    自定义颜色\definecolor{name}{model}{color-spec}
	\definecolor{color}{RGB}{255,125,255}
	































