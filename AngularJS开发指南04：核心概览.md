## 概览
这篇文档描述了AngularJS的主要组成部分，以及它们如何协同工作。它们是：

*  [启动(startup)][] - 展示“hello world!”
*  [执行期(runtime)][] - AngularJS 执行期概览
*  [作用域(scope)][] - 视图和控制器的集合区
*  [控制器(controller)][] - 应用的行为
*  [模型(model)][] - 应用的数据
*  [视图(view)][] - 用户能看到的
*  [指令(directives)][] - 扩展HTML语法
*  [过滤器(filters)][] - 数据本地化
*  [注入器(injector)][] - 聚合你的应用
*  [模块(module)][] - 配置注入器
*  [$][] - AngularJS的命名空间(namespace)

<!--more-->
<a name="startup"></a>
## 启动

下面解释了我们是如何把这一切运转起来的(用一张图和一个例子来解释)：

![concepts-startup][]

1. 浏览器载入HTML，然后把它解析成DOM。
2. 浏览器载入angular.js脚本。
3. AngularJS等到`DOMContentLoaded`事件触发。
4. AngularJS寻找`ng-app`指令，这个指令指示了应用的边界。
5. 使用`ng-app`中指定的模块来配置注入器($injector)。
6. 注入器($injector)是用来创建“编译服务($compile service)”和“根作用域($rootScope)”的。
7. 编译服务($compile service)是用来编译DOM并把它链接到根作用域($rootScope)的。
8. `ng-init`指令将“World”赋给作用域里的name这个变量。
9. 通过`{{name}}`的替换，整个表达式变成了“Hello World”。

**index.html:**

	<!doctype html>
	<html ng-app>
	  <head>
	    <script src="http://code.angularjs.org/angular-1.1.0.min.js"></script>
	  </head>
	  <body>
	    <p ng-init=" name='World' ">Hello {{name}}!</p>
	  </body>
	</html>

<fieldset class="angularjs-demo">
    <legend>运行效果</legend>
</fieldset>

<a name="runtime"></a>
## 执行期

下面的图和例子解释了AngularJS如何和浏览器的事件回路(event loop)交互。

![concepts-runtime][]

1. 浏览器的事件循环等待事件的触发。所谓事件包括用户的交互操作、定时事件、或者网络事件(服务器的响应)。
2. 事件触发后，回调会被执行。此时会进入Javascript上下文。通常回调可以用来修改DOM结构。
3. 一旦回调执行完毕，浏览器就会离开Javascript上下文，并且根据DOM的修改重新渲染视图。

AngularJS通过使用自己的事件处理循环，改变了传统的Javascript工作流。这使得Javascript的执行被分成原始部分和拥有AngularJS执行上下文的部分。只有在AngularJS执行上下文中运行的操作，才能享受到AngularJS提供的数据绑定，异常处理，资源管理等功能和服务。你可以使用 `$apply()`来从普通Javascript上下文进入AngularJS执行上下文。记住，大部分情况下（如在控制器，服务中），$apply都已经被用来处理当前事件的相应指令执行过了。只有当你使用自定义的事件回调或者是使用第三方类库的回调时，才需要自己执行`$apply`。

1. 通过调用 `scope.$apply(stimulusFn)`来进入AngularJS的执行上下文，这里的stimulusFn是你希望在AngularJS执行上下文中执行的函数。
2. AngularJS会执行`stimulusFn()`，这个函数一般会改变应用的状态。
3. AngularJS进入$digest循环。这个循环是由两个小循环组成的，这两个小循环用来处理处理$evalAsync队列和$watch列表。这个$digest循环直到模型“稳定”前会一直迭代。这个稳定具体指的是$evalAsync对表为空，并且$watch列表中检测不到任何改变了。
4. 这个$evalAsync队列是用来管理那些“视图渲染前需要在当前栈框架外执行的操作的”。这通常使用 `setTimeout(0)`来完成的。用`setTimeout(0)`会有速度慢的问题。并且，因为浏览器是根据事件队列按顺序渲染视图的，还会造成视图的抖动。
5. $watch列表是一个表达式的集合，这些表达式可能是自上次迭代后发生了改变的。如果有检测到了有改变，那么$watch函数就会被调用，它通常会把新的值更新到DOM中。
6. 一旦AngularJS的$digest循环结束，整个执行就会离开AngularJS和Javascript的上下文。这些都是在浏览器为数据改变而进行重渲染之后进行的。


下面解释了"hello world"的例子是怎么样实现“将用户输入绑定到视图上”的效果。

1. 在编译阶段：
	1. input上的`ng-model`指令给`<input>`输入框绑定了keydown事件；
	2. `{{name}}`这个变量替换表单式建立了一个 $watch 来接受 name 变量改变的通知。
2. 在执行期阶段：
	1. 按下任何一个键(以X键为例)，都会触发一个 input 输入框的keydown事件；
	2. input 上的指令捕捉到 input 里值得改变，然后调用 $apply("name = 'X';")来更新处于AngularJS执行上下文中的模型；
	3. AngularJS将 `name='X'`应用到模型上；
	4. $digest 循环开始；
	5. $watch 列表检测到了name值的变化，然后通知 `{{name}}`变量替换的表达式，这个表达式负责将DOM进行更新；
	6. AngularJS退出执行上下文，然后退出Javascript上下文中的keydown事件；
	7. 浏览器以更新的文本重渲染视图。

**index.html:**

	<!doctype html>
	<html ng-app>
	  <head>
	    <script src="http://code.angularjs.org/angular-1.1.0.min.js"></script>
	  </head>
	  <body>
	    <input ng-model="name">
	    <p>Hello {{name}}!</p>
	  </body>
	</html>

<fieldset class="angularjs-demo">
    <legend>运行效果</legend>
</fieldset>

<a name="scope"></a>
## 作用域(Scope)
作用域是用来检测模型的改变和为表达式提供执行上下文的。它是分层组织起来的，并且层级关系是紧跟着DOM的结构的。(请参考单个指令的文档，有一些指令会导致新的作用域的创建。)

下面这个例子演示了`{{name}}`表达式在不同的作用域下解析成不同的值。这个例子下面的图片显示作用域的边界。

`index.html:`

	<!doctype html>
	<html ng-app>
	  <head>
	    <script src="http://code.angularjs.org/angular-1.1.0.min.js"></script>
	    <script src="script.js"></script>
	  </head>
	  <body>
	    <div ng-controller="GreetCtrl">
	      Hello {{name}}!
	    </div>
	    <div ng-controller="ListCtrl">
	      <ol>
	        <li ng-repeat="name in names">{{name}}</li>
	      </ol>
	    </div>
	  </body>
	</html>


`style.css:`

	.show-scope .doc-example-live.ng-scope,
	.show-scope .doc-example-live .ng-scope  {
	  border: 1px solid red;
	  margin: 3px;
	}

`script.js:`

	function GreetCtrl($scope) {
	  $scope.name = 'World';
	}
	 
	function ListCtrl($scope) {
	  $scope.names = ['Igor', 'Misko', 'Vojta'];
	}


<fieldset class="angularjs-demo">
    <legend>运行效果</legend>
</fieldset>

![concepts-scope][]

<a name="controller"></a>
## 控制器

视图背后的控制代码就是控制器。它的主要工作内容是构造模型，并把模型和回调方法一起发送到视图。 视图可以看做是作用域在模板(HTML)上的“投影(projection)”。而作用域是一个中间地带，它把模型整理好传递给视图，把浏览器事件传递给控制器。控制器和模型的分离非常重要，因为：

*   控制器是由Javascript写的。Javascript是命令式的，命令式的语言适合用来编写应用的行为。控制器不应该包含任何关于渲染代码（DOM引用或者片段）。
*   视图模板是用HTML写的。HTML是声明是的，声明式的语言适合用来编写UI。视图不应该包含任何行为。
*   因为控制器和视图没有直接的调用关系，所以可以使多个视图对应同一个控制器。这对“换肤(re-skinning)”、适配不同设备（比如移动设备和台式机）、测试，都非常重要。

**index.html:**

	<!doctype html>
	<html ng-app>
	  <head>
	    <script src="http://code.angularjs.org/angular-1.1.0.min.js"></script>
	    <script src="script.js"></script>
	  </head>
	  <body>
	    <div ng-controller="MyCtrl">
	      Hello {{name}}!
	      <button ng-click="action()">
	        OK
	      </button>
	    </div>
	  </body>
	</html>

script.js:

	function MyCtrl($scope) {
	  $scope.action = function() {
	    $scope.name = 'OK';
	  }
	  
	  $scope.name = 'World';
	}

<fieldset class="angularjs-demo">
    <legend>运行效果</legend>
</fieldset>

<a name="model"></a>
## 模型

模型就是用来和模板结合生成视图的数据。模型必须在作用域中时可以被引用，这样才能被渲染生成视图。和其他框架不一样的是，Angularjs对模型本身没有任何限制和要求。你不需要继承任何类也不需要实现指定的方法以供调用或者改变模型。 模型可以是原生的对象哈希形式的，也可以是完整对象类型的。简而言之，模型可以是原生的Javascript对象。

<a name="view"></a>
## 视图

所谓视图，就是指用户所看见的。 视图的生命周期由作为一个模板开始，它将和模型合并并最终渲染到浏览器的DOM中。与其他模板系统不同的是，AngularJS使用一种独特的形式来渲染视图。

*   其他模板 - 大部分模板系统工作原理，都是一开始获取一个带有特殊标记的HTML形式字符串。通常情况下模板的特殊标记破换了HTML的语法，以至于模板是不能用HTML编辑器编辑的。然后这个字符串会被送到模板引擎那里解析，并和数据合并。合并的结果是一个可以被浏览器解析的HTML字符串。这个字符串会被`.innerHTML`方法写到DOM中。使用innerHTML会造成浏览器的重新渲染。当模型改变时，这整个流程又要重复一遍。模板的生存周期就是DOM的更新周期。这里我想强调是，这些模板系统模板的基础是字符串。
*   AngularJS - AngularJS和其它模板系统不同。它使用的是DOM而不是字符串。模板仍然是用HTML字符串写的，并且它仍然是HTML。浏览器将它解析成DOM， 然后这个DOM会作为输入传递给模板引擎，也就是我们的编译器。编译器查看其中的指令，找到的指令后，会开始监视指令内容中相应的模型。 这样做，就使得视图能“连续地”更新，不需要模板和数据的重新合并。你的模型也就成了你视图变化的唯一动因。

**index.html:**
	
	<!doctype html>
	<html ng-app>
	  <head>
	    <script src="http://code.angularjs.org/angular-1.1.0.min.js"></script>
	  </head>
	  <body>
	    <div ng-init="list = ['Chrome', 'Safari', 'Firefox', 'IE'] ">
	      <input ng-model="list" ng-list> <br>
	      <input ng-model="list" ng-list> <br>
	      <pre>list={{list}}</pre> <br>
	      <ol>
	        <li ng-repeat="item in list">
	          {{item}}
	        </li>
	      </ol>
	    </div>
	  </body>
	</html>

<fieldset class="angularjs-demo">
    <legend>运行效果</legend>
</fieldset>

<a name="directives"></a>
## 指令

一个指令 就是一种**“由某个属性、元素名称、css类名出现而导致的行为，或者说是DOM的变化”**。指令能让你以一种声明式的方法来扩展HTML表示能力。下面演示了一个增加了数据绑定的“内容可编辑”HTML。

**index.html:**

	<!doctype html>
	<html ng-app="directive">
	  <head>
	    <script src="http://code.angularjs.org/angular-1.1.0.min.js"></script>
	    <script src="script.js"></script>
	  </head>
	  <body>
	    <div contentEditable="true" ng-model="content">Edit Me</div>
	    <pre>model = {{content}}</pre>
	  </body>
	</html>

**script.js:**

	<!doctype html>
	<html ng-app="directive">
	  <head>
	    <script src="http://code.angularjs.org/angular-1.1.0.min.js"></script>
	    <script src="script.js"></script>
	  </head>
	  <body>
	    <div contentEditable="true" ng-model="content">Edit Me</div>
	    <pre>model = {{content}}</pre>
	  </body>
	</html>

**style.css:**

	div[contentEditable] {
	  cursor: pointer;
	  background-color: #D0D0D0;
	  margin-bottom: 1em;
	  padding: 1em;
	}

<fieldset class="angularjs-demo">
    <legend>运行效果</legend>
</fieldset>

<a name="filters"></a>
## Filters过滤器

过滤器扮演着数据翻译的角色。一般他们主要用在数据需要格式化成本地格式的时候。它参照了UNIX过滤的规则，并且也实现了“|”（管道）语法。

**index.html:**

	<!doctype html>
	<html ng-app>
	  <head>
	    <script src="http://code.angularjs.org/angular-1.1.0.min.js"></script>
	  </head>
	  <body>
	    <div ng-init="list = ['Chrome', 'Safari', 'Firefox', 'IE'] ">
	      Number formatting: {{ 1234567890 | number }} <br>
	      array filtering <input ng-model="predicate"> 
	      {{ list | filter:predicate | json }}
	    </div>
	  </body>
	</html>

<fieldset class="angularjs-demo">
    <legend>运行效果</legend>
</fieldset>

<a name="injector"></a><a name="module"></a>
## 模块和注入器

每个AngularJS应用都有一个唯一的注入器。注入器提供一个通过名字查找对象实例的方法。它将所有对象缓存在内部，所以如果重复调用同一名称的对象，每次调用都会得到同一个实例。如果调用的对象不存在，那么注入器就会让**实例工厂(instance factory)**创建一个新的实例。

一个模块就是一种配置注入器实例工厂的方式，我们也称为**“提供者(provider)”**。

	// Create a module
	var myModule = angular.module('myModule', [])
	 
	// Configure the injector
	myModule.factory('serviceA', function() {
	  return {
	    // instead of {}, put your object creation here
	  };
	});
	 
	// create an injector and configure it from 'myModule'
	var $injector = angular.injector('myModule');
	 
	// retrieve an object from the injector by name
	var serviceA = $injector.get('serviceA');
	 
	// always true because of instance cache
	$injector.get('serviceA') === $injector.get('serviceA');


注入器真正强大之处在于它可以用来调用方法和实例化的类型。这个精妙的特性让方法和类型能够通过注入器请求到他们依赖的组件，而不需要自己加载依赖。

	// You write functions such as this one.
	function doSomething(serviceA, serviceB) {
	  // do something here.
	}
	 
	// Angular provides the injector for your application
	var $injector = ...;
	 
	///////////////////////////////////////////////
	// the old-school way of getting dependencies.
	var serviceA = $injector.get('serviceA');
	var serviceB = $injector.get('serviceB');
	 
	// now call the function
	doSomething(serviceA, serviceB);
	 
	///////////////////////////////////////////////
	// the cool way of getting dependencies.
	// the $injector will supply the arguments to the function automatically
	$injector.invoke(doSomething); // This is how the framework calls your functions

注意，你唯一需要写的就是上例中指出的函数，并把需要的依赖写在函数参数里。当AngularJS调用这个函数时，它会自动体充好需要的参数。

看看下面的动态时间的例子，注意它是如何在构造器中列举出依赖的。当`ng-controller`实例化构造器的时候，它自动提供了指明的依赖。没有必要去创建依赖、查找依赖、或者提供一个依赖的引用给注入器。

**index.html:**

	<!doctype html>
	<html ng-app="timeExampleModule">
	  <head>
	    <script src="http://code.angularjs.org/angular-1.1.0.min.js"></script>
	    <script src="script.js"></script>
	  </head>
	  <body>
	    <div ng-controller="ClockCtrl">
	      Current time is: {{ time.now }}
	    </div>
	  </body>
	</html>	

**script.js:**

	angular.module('timeExampleModule', []).
	  // Declare new object call time,
	  // which will be available for injection
	  factory('time', function($timeout) {
	    var time = {};
	    
	    (function tick() {
	      time.now = new Date().toString();
	      $timeout(tick, 1000);
	    })();
	    return time;
	  });
	 
	// Notice that you can simply ask for time 
	// and it will be provided. No need to look for it.
	function ClockCtrl($scope, time) {
	  $scope.time = time;
	}

<fieldset class="angularjs-demo">
    <legend>运行效果</legend>
</fieldset>

<a name="namespace"></a>
## AngularJS 命名空间

为了防止意外的命名冲突， AngularJS为可能冲突的对象名加以前缀"$"。所以请不要在你自己的代码里用"$"做前缀，以免和AngularJS代码发生冲突。

<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh

 [启动(startup)]: 452.html#startup
 [执行期(runtime)]: 452.html#runtime
 [作用域(scope)]: 452.html#scope
 [控制器(controller)]: 452.html#controller
 [模型(model)]: 452.html#model
 [视图(view)]: 452.html#view
 [指令(directives)]: 452.html#directives
 [过滤器(filters)]: 452.html#filters
 [注入器(injector)]: 452.html#injector
 [模块(module)]: 452.html#module
 [$]: 452.html#namespace

 [concepts-startup]: http://code.angularjs.org/1.1.0/docs/img/guide/concepts-startup.png
 [concepts-runtime]: http://code.angularjs.org/1.1.0/docs/img/guide/concepts-runtime.png
 [concepts-scope]: http://code.angularjs.org/1.1.0/docs/img/guide/concepts-scope.png