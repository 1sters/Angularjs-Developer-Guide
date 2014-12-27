##什么是作用域?
作用域是一个指向应用模型的对象。它是表达式的执行环境。作用域有层次结构，这个层次和相应的DOM几乎是一样的。作用域能监控表达式和传递事件。

###作用域特点

*  作用域提供APIs（$watch）来观察模型的变化。
*  作用域提供APIs($apply)将任何模型的改变从"AngularJS领域( Angular realm)"通过系统映射到视图上。
*  作用域能通过共享模型成员的方式嵌套到应用组件上。一个作用域从父作用域继承属性。
*  作用域提供表达式执行的上下文。比如说表达式`{{username}}`本身是无意义的，除非把它放到指定username属性的作用域中。<!--more-->

###作为数据模型的作用域

作用域是控制器和视图之间的“胶水”。在模板链接阶段，指令设置好作用域的$watch表达式。$watch使得指令能知晓属性的改变，这使得指令能重新渲染和更新DOM中的值。

控制器和指令都持有作用域的引用，但是不持有对方的。这使得控制器能从指令和DOM中脱离出来。这很重要，因为这使得控制器完全不需要知道view的存在，这大大改善了应用的测试。


**index.html：**

	<!doctype html>
	<html ng-app>
	  <head>
	    <script src="http://code.angularjs.org/angular-1.0.2.min.js"></script>
	    <script src="script.js"></script>
	  </head>
	  <body>
	    <div ng-controller="MyController">
	      Your name:
	        <input type="text" ng-model="username">
	        <button ng-click='sayHello()'>greet</button>
	      <hr>
	      {{greeting}}
	    </div>
	  </body>
	</html>

**script.js：**

	function MyController($scope) {
	  $scope.username = 'World';
 	
	  $scope.sayHello = function() {
 	   $scope.greeting = 'Hello ' + $scope.username + '!';
	  };
	}


上例中MyController将值World赋给了作用域中的username。然后作用域将这个赋值的操作通知给input，然后input就会被渲染成预填充了值得样子。这展示控制器如何将数据写入到作用域。

同样的，控制器能将行为添加到作用域，正如你看到的sayHello方法，这个方法是在用户点击'greet'按钮时被调用的。这展示了当属性绑定到HTML input元素上时，它会自动更新。

逻辑上来说，表达式`{{greeting}}`的渲染需要：

*  获取和模板中定义了`{{greeting}}`DOM节点相关的作用域。在这个例子里，就是传入到MyController的作用域。
*  结合上一步获取到的作用域来计算表达式的值，将该值在DOM中替换掉表达式。

你可以把作用域和它的属性当做是用来渲染视图的数据。作用域是视图唯一相关联的变化来源。

从测试角度来说，控制器和分离是我们想要的结果，因为这使得我们能在测试行为的时候排除掉渲染细节的干扰。

	
	it('should say hello', function() {
	  var scopeMock = {};
	  var cntl = new MyController(scopeMock);
	 
	  // Assert that username is pre-filled
	  expect(scopeMock.username).toEqual('World');
	 
	  // Assert that we read new username and greet
	  scopeMock.username = 'angular';
	  scopeMock.sayHello();
	  expect(scopeMock.greeting).toEqual('Hello angular!');
	});

###作用域层级
每一个AngularJS应用都有一个绝对的根作用域。但是可能有多个子作用域。

一个应用可以有多个作用域，因为有一些指令会生成新的子作用域（参考指令的文档看看哪些指令会创建新作用域）。当新作用域被创建的时候，他们会被当成子作用域添加到父作用域下，这使得作用域会变成一个和相应DOM结构一个的树状结构。

当AngularJS执行表达式`{{username}}`，它会首先查找和当前节点相关的作用域中叫做username的属性。如果没找到，那就会继续向上层作用域搜索，直到根作用域。在Javascript中，这被称为原型类型的继承，子作用域以原型的形式继承自父作用域。

下面这个例子展示了应用中的作用域，它们的继承关系。

**index.html：**

	<!doctype html>
	<html ng-app>
	  <head>
	    <script src="http://code.angularjs.org/angular-1.0.2.min.js"></script>
	    <script src="script.js"></script>
	  </head>
	  <body>
	    <div ng-controller="EmployeeController">
	      Manager: {{employee.name}} [ {{department}} ]<br>
	      Reports:
	        <ul>
	          <li ng-repeat="employee in employee.reports">
	            {{employee.name}} [ {{department}} ]
	          </li>
	        </ul>
	      <hr>
	      {{greeting}}
	    </div>
	  </body>
	</html>

**style.css：**

	/* remove .doc-example-live in jsfiddle */
	.doc-example-live .ng-scope {
	  border: 1px dashed red;
	}

**script.js：**

	function EmployeeController($scope) {
	  $scope.department = 'Engineering';
	  $scope.employee = {
	    name: 'Joe the Manager',
	    reports: [
	      {name: 'John Smith'},
	      {name: 'Mary Run'}
	    ]
	  };
	}



注意当作用域和元素相关联的时候，AngularJS会自动给相应元素添加ng-scope类名。这个例子中的作用域范围突出显示了。子作用域的存在是很有必要的，因为迭代器要执行`{{employee.name}}`表达式，它会根据不同的作用域生成不同的值。同样的，`{{department}}`的执行是继承自根作用域的，因为只有根作用域中定义了它。

###从DOM中获取作用域
作用域是作为$scope的数据属性关联到DOM上的，并且能在需要调试的时候被获取到。（这不像那些只能在应用内获取的框架）根作用关联的DOM就是ng-app指令定义的地方。一般来说ng-app都是放在`<html>`元素中的，但是也能放在其他元素中，比如，只有

在调试器中检测作用域：

在你要查看的元素上右键单击，选择菜单中的“审查元素”。你应该会看到浏览器的调试器，并且你选择的元素高亮了显示了。

调试器能让你在控制台中用变量`$0`获取到。

在控制台中获取想关联的作用域：`angular.element($0).scope()`

###作用域事件的传递
作用域中的事件传递是和DOM事件传递类似的。事件可以广播给子作用域或者传递给父作用域。


`index.html：`

	<!doctype html>
	<html ng-app>
	  <head>
	    <script src="http://code.angularjs.org/angular-1.0.2.min.js"></script>
	    <script src="script.js"></script>
	  </head>
	  <body>
	    <div ng-controller="EventController">
	      Root scope <tt>MyEvent</tt> count: {{count}}
	      <ul>
	        <li ng-repeat="i in [1]" ng-controller="EventController">
	          <button ng-click="$emit('MyEvent')">$emit('MyEvent')</button>
	          <button ng-click="$broadcast('MyEvent')">$broadcast('MyEvent')</button>
	          <br>
	          Middle scope <tt>MyEvent</tt> count: {{count}}
	          <ul>
	            <li ng-repeat="item in [1, 2]" ng-controller="EventController">
	              Leaf scope <tt>MyEvent</tt> count: {{count}}
	            </li>
	          </ul>
	        </li>
	      </ul>
	    </div>
	  </body>
	</html>

`script.js：`

	function EventController($scope) {
	  $scope.count = 0;
	  $scope.$on('MyEvent', function() {
	    $scope.count++;
	  });
	}


###作用域的声明周期
浏览器接收到事件后的一般工作流程是执行一个相应的Javascript回调。回调一执行完，浏览器就会重新渲染DOM并且重新回到等待事件的状态。

当浏览器调用AngularJS上下文之外的Javascript代码时，AngularJS是不知道模型的更改的。要正确处理模型的更改，就要使用$apply方法进入AngularJS的执行上下文。只有在$apply方法内执行的模型修改才会正确地被AngularJS处理。比如，一个指令监听DOM事件，比如`ng-click`，它必须在$apply方法中来执行表达式。

执行完表达式之后，$apply会进入$digest阶段。在$digest阶段作用域会检查所有的$watch表达式，并将它们和之前的值比较。这个检查工作是异步执行的。这意味着赋值语句，如`$scope.username="angular"`不会马上导致$watch被通知，取而代之的是它会等到$digest阶段才被通知。这种方式是合理的，因为它将多个模型的更新整合到一个$watch通知里，并且保证了一个$watch通知期间不会有其他$watch执行。如果一个$watch改变了模型的值，那么它会产生一个额外的$digest阶段。

1. **创建**——根作用域是在应用被$injector启动时创建的。在模板链接阶段，有些指令会创建新的子作用域。
2.**观察者注册**——在模板链接阶段，指令会在作用域上注册观察者。这些观察者是用来将模型的改变传递给DOM的。
3. **模型变化**——为了正确地观测到模型变化，你需要并且只能在scope.$apply()中改变他们。（AngularJS的API会隐式地这么做，所以在控制器或者在$http,$timeout等服务中你不需要额外的调用$apply）。
4. **变化的观测**——在$apply的最后，AngularJS会在根作用域中执行一个$digest循环，它会将变化传递给所有子作用域。在$digest循环中，所有的$watch表达式或者函数都会被检测，来观察模型的变化。如果有变化被检测到了，$watch的监听回调就会被调用。
5. **作用域的销毁**——如果子作用域不再有用了。那么子作用域的创建者就会负责用scope.$destroy() API来将它销毁。这会停止$digest再调用子作用域，并且让作用域占用的内容能够被回收。



####作用域和指令
在编译阶段，编译器在DOM中匹配指令。指令通常分为两种：

*  观察型的指令，例如双花括号表达式`{{expression}}`，会用$watch来注册一个监听者。无论表达式什么时候改变，这类型的指令都会被通知，并且能更新视图。
*  监听者型的指令，比如`ng-click`，会向DOM注册一个监听者。当DOM监听者触发，指令会执行相关的表达式并且使用$apply方法更新视图。

当一个外界事件（比如用户操作，计时器或者XHR）触发时，相应的表达式必须在$apply()方法内来由其相应的作用域调用，这样所有的监听者才会被正确地更新。

####会创建作用域的指令
大部分情况下，指令和作用域交互但不会产生新的作用域实例。但是，有些指令，比如`ng-controller`和`ng-repeat`会穿件新的作用域，并关联到相应的DOM元素上，你该可以使用`angular.element(aDomElement).scope()`方法来获得某一个DOM元素相关的作用域。

####控制器和作用域
作用域和控制器在以下几种情况下交互：

*  控制器通过作用域来向模板暴露方法（参考`ng-controller`）
*  控制器定义里能改变模型（作用域的属性）的方法（行为）
*  控制器在模型上注册了观察者。这些观察者会在控制器行为执行后立即被执行

参考`ng-controller`来获取更多信息。

####作用域的$watch操作要注意的
检测属性的改变是AngularJS中一项常用的操作，所以它应该是高效的。要注意的是，执行检测的方法不应该包含任何DOM操作，因为在Javascript对象中，DOM获取要比属性获取慢很多很多。

<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh