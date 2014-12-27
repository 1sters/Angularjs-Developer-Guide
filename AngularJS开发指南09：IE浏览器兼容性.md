##概览
本章描述了IE在处理自定义的HTML属性和标签时的一些独特之处。如果你要让你的AngularJS应用兼容IE8和IE8以下的版本的话，你需要仔阅读本章。

简易版

要让你的AngularJS应用在IE中正常运行你必须：

1. 确保JSON字符串能被正常解析（IE7需要），你可以使用JSON2或者JSON3来实现。

2. 你不能使用自定义的元素标签，如`<ng:view>`（你只能使用属性的形式，如`<div ng-view>`），或者，

3. 如果你用了自定义的标签名，那你必须按照以下步骤做才能保证IE正常运行：<!--more-->

        <html xmlns:ng="http://angularjs.org">
         <head>
          <!--[if lte IE 8]>
            <script>
               document.createElement('ng-include');
               document.createElement('ng-pluralize');
               document.createElement('ng-view');
               // Optionally these for CSS
              document.createElement('ng:include');
              document.createElement('ng:pluralize');
              document.createElement('ng:view');
             </script>
          <![endif]-->
        </head>
        <body>
            ...
        </body>
        </html>


其中**重要的部分**是:

*  `xmlns:ng` - 命名空间 - 你需要为你使用或者准备使用的每一个自定义标签准备一个命名空间。

*  `document.createElement`(你的标签名) - 自定义标签的创建 - 因为这只是老版本IE的一个问题，所以你需要根据情况使用。对于每一个你没有使用命名空间或者HTML中没有定义的标签，你需要预先声明它彩色能使IE正常工作。

##详细版
IE处理非标准标签名会产生问题。问题可以分为两类，每类都有自己的解决方法。

*  如果标签名是以`my:`前缀开始的: 这会被当成是一个XML的命名空间，并且必须使用`<html xmlns:my="ignored">来声明。

*  如果标签没有`:`符号，但它又不是一个标准的HTML标签。那么就必须预先使用`document.createElement('my-tag')`来创建它。

*  如果你准备使用css选择器来对自定义标签添加样式，那么你就必须先用`document.createElement('my-tag')`来创建一下，不管有没有XML命名空间。

###好消息
值得庆幸的是，IE的这种限制只存在在标签名上，标签属性名没有限制。所以，当在IE上使用`<div my-tag your:tag> </div>.`这样的形式时没有特殊要求。

###如果我没按上面说的做会怎么样？
假设你用了一个非HTML标准的标签，暂时称为`mytag`(称为my:tag 或者 my-tag都可以)：

	<html>
	  <body>
	    <mytag>some text</mytag>
	  </body>
	</html>

它本该被解析成下面这样的DOM：

	#document
	  +- HTML
	     +- BODY
	        +- mytag
	           +- #text: some text

期望的结果是BODY元素包含一个叫做`mytag`的子元素。这个子元素包含文本"some text"。

但是IE不会这样解析（如果没有按之前强调修复IE的步骤做的话）：

	#document
	  +- HTML
	     +- BODY
	        +- mytag
	        +- #text: some text
	        +- /mytag

在IEE中，BODY元素有了三个子元素：

1. 一个自闭和的标签mytag。和自闭和标签<br/>一样。最后的关闭符/是可选的，但是<br>标签并不允许包含子元素。所以浏览器会将<br>some text</br>解析成三个兄弟节点，而不是一个包含文本的节点。

2. 一个纯文字的节点。这本来应该是上面mytag的子节点，而不是兄弟节点。

3. 一个不合法的自闭和标签`/mytag`。 因为标签名不允许含有`/`，所以说它是非法的。另外这个关闭标签不应该是DOM的一部分，因为它是用来描述标签的结尾位置的。

## 自定义标签名元素的CSS样式
要让CSS选择器能正确使用自定义元素的话，先要用`document.createElement('my-tag')`来创建一下元素，不管有没有XML命名空间。

<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh
