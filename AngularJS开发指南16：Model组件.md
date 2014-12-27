`model`这个词在AngularJS中既可以表示一个用来表示实体（比如，一个叫做`phones`的`model`，它的值是一个包含多个`phone`的数组）的对象，也可以表示应用中的整个数据模型，这取决于我们所讨论的AngularJS文档中的上下文。

在AngularJS中，一个模型就是一AngularJS作用于对象的任何一个可取的属性。属性的名字就是模型的标示符。它的值可以是任意的Javascript对象（包括数组和原始对象）。

将Javascript对象编程模型的唯一要求是这个对象必须被AngularJS作用域的一个属性引用。这个引用既可以显式也可以隐式地创建。<!--more-->

你可以像下面这样显式地创建一个作用域属性，引用Javascript对象：

*  在Javascript代码中直接将一个对象赋给作用域对象属性；这种情况常见于控制器中：

		function MyCtrl($scope) {
			// create property 'foo' on the MyCtrl's scope
			// and assign it an initial value 'bar'
			$scope.foo = 'bar';
		}

*  在模板中使用表达式：

	`<button ng-click="{{foos='ball'}}">Click me</button>`

*  在模板中使用`ngInit`指令（只适用于实例，不推荐在实际应用中使用）：

	`<body ng-init=" foo = 'bar' ">`

当处理下面这样的模板机构时，AngularJS会隐式地（通过创建一个作用域对象的属性，并将合适地值赋给它来实现）创建模型。

*  从input, select, textarea或者其他表单元素中：
	
	`<input ng-model="query" value="fluffy cloud">`

上面的代码再当前作用域中创建了一个叫做`query`的模型，值被设置成"fluffy cloud"。

*  在`ngRepeater`的迭代声明中：

	`<p ng-repeat="phone in phones"></p>`


上面代码为`phones`数组中的每一项都创建了一个子作用域。并且在自作用于中创建了一个叫做`phone`的对象（模型），它的值被设置成数组中当前的值。

在AngularJS中的下列情况中，Javascript对象也可以变成模型：

*  没有个属性里引用了Javascript对象的作用域对象。

*  所有的包含对象应用属性的作用域对象都已过期并且是会被回收的。

下面的图中描述了隐式地从模板中创建数据库模型：

<img src="http://docs.angularjs.org/img/guide/about_model_final.png"/>

###相关主题
*  AngularJS中的MVC
*  理解Controller组件
*  理解view组件

<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh