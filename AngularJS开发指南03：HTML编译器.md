## 概览

AngularJS的HTML编译器能让浏览器识别新的HTML语法。它能让你将行为关联到HTML元素或者属性上，甚至能让你创造具有自定义行为的新元素。AngularJS称这种行为扩展为“**指令**”

HTML在编写静态页面时，有很多声明式的结构来控制格式。比如你要把某个内容居中，你不必告诉浏览器“去找到窗口的中点位置，然后跟内容的中间结合”。你只需要添加一个 align="center" 的属性给需要内容居中的元素就行了。这就是声明式语言的强大之处。

但是声明式语言也有力所不能及的地方，原因之一在于你不能用它来让浏览器识别新的语法。比如说，你不要内容居中，而是居左到1/3,这时它就做不到了。所以我们需要一个办法让浏览器能学会新的HTML语法。

AngularJS生来自带一些对创建APP非常有用的**指令**。我们也希望你能自己创造一些对你自己的应用有用的**指令**。这些扩展的**指令**就是你创建APP的 “**特定领域语言（Domain Specific Language）**”。

编译的过程都会在浏览器端发生；服务器端不会参与到其中的任何步骤，也不会做预编译。<!--more-->

## 编译器(complier)

编译器是AngularJS提供的一项服务，它通过遍历DOM来查找和它相关的属性。整个编译的过程分为两个阶段。

*   **编译**： 遍历DOM并且收集所有的相关指令，生成一个链接函数。

*   **链接**： 给指令绑定一个作用域，生成一个动态的视图。作用域模型的任何改变都会反映到视图上，并且视图上的任何用户操作也都会反映到作用域模型。这使得作用域模型成为你的业务逻辑里唯一要关心的东西。

有一些指令，比如`ng-repeat`会为数据集合里的每一项DOM元素都克隆一次。将整个编译过程分为**编译**和**链接**两个阶段的作法改善了整体的性能，因为克隆出来的模板总共只需要被编译一次，然后链接到各自的模型实例上就行了。

## 指令

**指令**指示的是**“当关联的HTML结构进入编译阶段时应该执行的操作”**。指令可以写在元素的名称里，属性里，css类名里，注释里。下面有几个功能相同的使用`ng-bind`指令的例子。

	<span ng-bind="exp"></span>
	<span class="ng-bind: exp;"></span>
	<ng-bind></ng-bind>
	<!-- directive: ng-bind exp -->

指令本质上只是一个当编译器编译到相关DOM时需要执行的函数。你可以在[指令API文档][]中找到更详尽的关于指令的资料。

下面是一条能让元素变得可拖拽的指令。注意`<span>`元素里的那个`draggable`属性。

**index.html:**

	<!doctype html>
	<html ng-app="drag">
	  <head>
		<script src="http://code.angularjs.org/angular-1.1.0.min.js"></script>
		<script src="script.js"></script>
	  </head>
	  <body>
		<span draggable>Drag ME</span>
	  </body>
	</html>
	

**script.js:**

	angular.module('drag', []).
	directive('draggable', function($document) {
		var startX=0, startY=0, x = 0, y = 0;
		return function(scope, element, attr) {
		  element.css({
		   position: 'relative',
		   border: '1px solid red',
		   backgroundColor: 'lightgrey',
		   cursor: 'pointer'
		  });
		  element.bind('mousedown', function(event) {
			startX = event.screenX - x;
			startY = event.screenY - y;
			$document.bind('mousemove', mousemove);
			$document.bind('mouseup', mouseup);
		  });
	 
		  function mousemove(event) {
			y = event.screenY - startY;
			x = event.screenX - startX;
			element.css({
			  top: y + 'px',
			  left:  x + 'px'
			});
		  }
	 
		  function mouseup() {
			$document.unbind('mousemove', mousemove);
			$document.unbind('mouseup', mouseup);
		  }
		}
	 });
    
<fieldset class="angularjs-demo">
    <legend>运行效果</legend>
</fieldset>

通过加入`draggable`属性可以让任何HTML元素都实现这个新的行为。我们这种改进的优美之处在于我们给了浏览器新能力。我们用了一种只要开发者熟悉HTML规则，就会举得很自然的方式扩展了浏览器理解新行为新语法的能力。

## 理解视图

网上有很多的模板系统。他们大多数都是**“将静态的字符模板和数据绑定，生成新字符，然后通过innerHTML插入到页面元素中”**。

![One_Way_Data_Binding][]

这意味着数据上的任何改变，都会导致数据要重新和模板结合生成新字符，再插入到DOM里。这其中会出现的问题有：需要读取用户输入并和模型的数据结合，需要覆写用户的输入，需要手动管理整个更新过程，缺少丰富的表现形式。 

AngularJS则不同，AngularJS编译器使用的是带指令的DOM，而不是字符串模板。它返回的是一个链接函数，这个函数和作用域模型结合就会生成一个动态视图。这个视图和模型的绑定过程是“透明的”。开发者不需要做任何关于更新视图的工作。并且应用没有用到innerHTML，所以我们也不用覆写用户的输入。更特别的是，Angular的指令不仅仅能使用字符串形式的绑定，还可以使用一些指示行为的结构体。

![Two_Way_Data_Binding][]

AngularJS的编译会生成一个**“稳定的DOM”**。这意味绑定了数据模型的DOM元素的实例不会在绑定的生命周期中发生改变。这也意味着代码中可以获取到DOM元素的实例引用并注册事件，不用担心这用引用会在模板和数据的结合时丢失。

<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh

 [指令API文档]: http://www.angularjs.cn/category/docs/api
 [One_Way_Data_Binding]: http://www.angularjs.cn/wp-content/uploads/2012/09/One_Way_Data_Binding.png
 [Two_Way_Data_Binding]: http://www.angularjs.cn/wp-content/uploads/2012/09/Two_Way_Data_Binding.png
