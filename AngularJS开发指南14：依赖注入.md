##依赖注入
依赖注入是一种软件设计模式，用来处理代码的依赖关系。

要更多地了解依赖注入，你可以到wikipedia上查找依赖注入，Martin Fowler写的Inversion of Control，或者到你喜欢的讲设计模式的书中查找。

###简单说说依赖注入
一般来说有三种方法让函数获得它的需要的依赖：

1.  它的依赖是能被创建的，一般用new操作符就行。

2.  能够通过全局变量查找依赖。

3.  依赖能在需要时被导入。

前两种方式都不是很好，因为它们需要对依赖硬编码，使得修改依赖的时候变得困难。特别是在测试的时候不好办，因为对某个部分进行孤立的测试常常需要模拟它的依赖。<!--more-->

第三种方式是最好的，因为它不必在组件中去主动需找和获取依赖，而是由外界将依赖传入。

	function SomeClass(greeter) {
	  this.greeter = greeter
	}
	 
	SomeClass.prototype.doSomething = function(name) {
	  this.greeter.greet(name);
	}

上面的例子中SomeClass不关心去哪里获得叫greeter的依赖，而只是在运行时处理一下greeter。

这是我们想要的结果，但是这也同时把获取依赖的任务交给了负责创建SomeClass的代码。

每一个AngularJS应用都有一个注入器(injector)用来处理依赖的创建。注入器是一个负责查找和创建依赖的服务定位器。

下面是使用注入器服务的例子：

	angular.module('myModule', []).
	 
	  // Teach the injector how to build a 'greeter'
	  // Notice that greeter itself is dependent on '$window'
	  factory('greeter', function($window) {
	    // This is a factory function, and is responsible for 
	    // creating the 'greet' service.
	    return {
	      greet: function(text) {
	        $window.alert(text);
	      }
	    };
	  }).
	  
	// New injector is created from the module. 
	// (This is usually done automatically by angular bootstrap)
	var injector = angular.injector('myModule');
	 
	// Request any dependency from the injector
	var greeter = injector.get('greeter');

通过请求依赖的方式解决了硬编码的问题，但是同样也意味着注入器需要再应用中传递，这违反了迪米特法则。我们通过使用下面这个例子中的声明的方式来将依赖查找都给注入器来解决。

	<!-- Given this HTML -->
	<div ng-controller="MyController">
	  <button ng-click="sayHello()">Hello</button>
	</div>



	// And this controller definition
	function MyController($scope, greeter) {
	  $scope.sayHello = function() {
	    greeter('Hello World');
	  };
	}
	 
	// The 'ng-controller' directive does this behind the scenes
	injector.instantiate(MyController);


注意，通过使用`ng-controller`来实例化控制器类，是的控制器和注入器不再相关联。这是最好的结果。应用代码可以简单的请求依赖而不必处理注入器。这种方式就没有破坏迪米特法则。

##依赖标记
注入器怎么知道需要注入什么依赖呢？

注入器需要应用提供一些标记来表示自己需要的依赖。在关于AngularJS的某些API文档中你会看到函数都是被注入器调用的。注入器需要知道函数需要什么依赖。下面有三个等效的表示的自己需要的依赖的方法。这些方法可以互相替换，并且是等效的。

###推断依赖
最简单的处理依赖的方法，就是假设函数的参数名就是依赖的名字

	function MyController($scope, greeter) {
	  ...
	}

给出一个注入器可以通过检查声明来获取函数名，从而知道需要的依赖的函数。在上面的例子中，`$scope`和`greeter`是需要注入到函数中的依赖。

坦白的来讲，用了这种方法就不能使用`JavaScript minifiers/obfuscators`（一些用来缩短的JS的类库）了，因为它们会改变变量名。这使得这种方法只适合于`pretotyping`和做demo。

###$inject 标记
要允许压缩类库重命名函数参数，同时注入器又能正确处理依赖的话，函数需要使用$inject属性。这个属性是一个包含依赖的名称的数组。

	var MyController = function(renamed$scope, renamedGreeter) {
	  ...
	}
	MyController.$inject = ['$scope', 'greeter'];

注意$inject标记里的值和函数声明的参数是对应的。

这种方式适合用于控制器的声明，因为控制器有了明确的声明标记。

###行内标记
有时候用$inject标记不是很方便，比如用来声明指令的时候。

比如：

	someModule.factory('greeter', function($window) {
	  ...;
	});

使用$inject会导致代码膨胀：
	
	var greeterFactory = function(renamed$window) {
	  ...;
	};
	 
	greeterFactory.$inject = ['$window'];
	 
	someModule.factory('greeter', greeterFactory);

这种情况我们就推荐使用第三种方式

	someModule.factory('greeter', ['$window', function(renamed$window) {
	  ...;
	}]);

记住这三种方式是等效的，并且在AngularJS应用中注入器支持的情况下可以随处用。

##哪里可以使用依赖注入
依赖注入再AngularJS中很普遍。一般用在控制器和工场方法中。

###控制器中的依赖注入
控制器是负责应用行为的类。推荐的控制器声明方法如下：

	var MyController = function(dep1, dep2) {
	  ...
	}
	MyController.$inject = ['dep1', 'dep2'];
	 
	MyController.prototype.aMethod = function() {
	  ...
	}

###工场方法
工场方法负责创建AngularJS中的大部分对象。比如指令，服务，过滤器。工厂方法一般在模块中使用，推荐的方法如下：

	angualar.module('myModule', []).
	  config(['depProvider', function(depProvider){
	    ...
	  }]).
	  factory('serviceId', ['depService', function(depService) {
	    ...
	  }]).
	  directive('directiveName', ['depService', function(depService) {
	    ...
	  }]).
	  filter('filterName', ['depService', function(depService) {
	    ...
	  }]).
	  run(['depService', function(depService) {
	    ...
	  }]);


<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh