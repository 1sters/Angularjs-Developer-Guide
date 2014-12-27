AngularJS模板是一种声明式的规则。它包含了模型和控制器的信息，最后会被渲染成用户在浏览器中看到的视图。它是静态的DOM，包含HTML，CSS和AngularJS指定的元素和属性。AngularJS元素和属性让angular给模板DOM添加行为，或者变形，成为动态地DOM。

下面是你能在模板中用到的AngularJS元素和属性：

*  指令 — 一个用来扩张已存在的DOM元素或者表现可重用DOM组件的属性或者元素。也可称为widget。
*  混合（Markup） — 双花括号是angular内置的一种混合，它会将表达式绑定到元素。
*  过滤器 — 格式化输出给用户的数据。
*  表单控制 — 让你能验证用户输入

注意：除了在模板中声明元素，你也可以在代码中获取到这些元素。<!--more-->

下面的例子展示了一个简单的模板。它包含标准的HTML标记、AngularJS指令和用双花括号进行绑定的表达式。

	<html ng-app>
	 <!-- Body tag augmented with ngController directive  -->
	 <body ng-controller="MyController">
	   <input ng-model="foo" value="bar">
	   <!-- Button tag with ng-click directive, and
	          string expression 'buttonText'
	          wrapped in "{{ }}" markup -->
	   <button ng-click="changeFoo()">{{buttonText}}</button>
	   <script src="angular.js">
	 </body>
	</html>

在一个简单的单页应用中，模板由HTML，CSS和包含在一个HTML页面（通常是index.html）中的AngularJS指令组成。在更复杂的应用中，你可以通过“**局部模板**”来在一个页面中显示多个视图，这个局部模板是指定义在单独HTML文件中的模板片段。你在主页面中通过结合使用$route服务和ngView指令来导入这些局部模板。AngularJS入门中第7步和第8步展示了如何使用。

##相关主题
*  Angular过滤器
*  Angular表单

##相关API
*  API Reference

<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh