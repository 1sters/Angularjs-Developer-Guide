##什么是模块?
大部分应用都有一个主方法用来实例化、组织、启动应用。AngularJS应用没有主方法，而是使用模块来声明应用应该如何启动。这种方式有以下几个优点：

*  启动过程是声明式的，所以更容易懂。
*  在单元测试是不需要加载全部模块的，因此这种方式有助于写单元测试。
*  可以在特定情况的测试中增加额外的模块，这些模块能更改配置，能帮助进行端对端的测试。
*  第三方代码可以打包成可重用的模块。
*  模块可以以任何先后或者并行的顺序加载（因为模块的执行本身是延迟的）。

##最基本的
好吧，我赶时间，快告诉我怎么写个`hello world`的模块

你需要了解以下两条重要的信息：

*  Module API模块API
*  注意我们是通过在`<html ng-app="myApp">`中进行指定，来实现使用myApp这个模块启动应用的。

###Source
index.html:

	<!doctype html>
	<html ng-app="myApp">
	  <head>
	    <script src="http://code.angularjs.org/angular-1.0.2.min.js"></script>
	    <script src="script.js"></script>
	  </head>
	  <body>
	    <div>
	      {{ 'World' | greet }}
	    </div>
	  </body>
	</html>

script.js:

	var myAppModule = angular.module('myApp', []);
	 
	// configure the module.
	// in this example we will create a greeting filter
	simpleAppModule.filter('greet', function() {
	 return function(name) {
	    return 'Hello, ' + name + '!';
	  };
	});

###Demo

推荐写法
以上这个例子写法很简单，但是不适合用同样写法来写大型应用。我们推荐的是将你的应用拆分成以下几个模块：

*  一个服务模块，用来做服务的声明。
*  一个指令模块，用来做指令的声明。
*  一个过滤器模块，用来做过滤器声明。
*  一个依赖以上模块的应用级模块，它包含初始化代码。

这样拆分的原因是，在你的测试中常常需要忽略掉初始化代码，因为这些代码比较难测试。通过把它拆分出来就能在测试中方便地忽视掉它。通过只加载和当前测试相关的模块，也能使测试更专一。

以上只是一个建议，你可以放心根据你的具体情况来调整。

###Source
index.html:

	<!doctype html>
	<html ng-app="xmpl">
	  <head>
	    <script src="http://code.angularjs.org/angular-1.0.2.min.js"></script>
	    <script src="script.js"></script>
	  </head>
	  <body>
	    <div ng-controller="XmplController">
	      {{ greeting }}!
	    </div>
	  </body>
	</html>

script.js:

	angular.module('xmpl.service', []).
	  value('greeter', {
	    salutation: 'Hello',
	    localize: function(localization) {
	      this.salutation = localization.salutation;
	    },
	    greet: function(name) {
	      return this.salutation + ' ' + name + '!';
	    }
	  }).
	  value('user', {
	    load: function(name) {
	      this.name = name;
	    }
	  });
	 
	angular.module('xmpl.directive', []);
	 
	angular.module('xmpl.filter', []);
	 
	angular.module('xmpl', ['xmpl.service', 'xmpl.directive', 'xmpl.filter']).
	  run(function(greeter, user) {
	    // This is effectively part of the main method initialization code
	    greeter.localize({
	      salutation: 'Bonjour'
	    });
	    user.load('World');
	  })
	 
	 
	// A Controller for your app
	var XmplController = function($scope, greeter, user) {
	  $scope.greeting = greeter.greet(user.name);
	}

###Demo


##模块的加载和依赖
一个模块就是一系列配置和代码块的集合，它们是在启动阶段就附加在到应用上的。一个最简单的模块由两类代码块集合组成的：

1.  配置代码块 - 在提供程序注册和配置阶段执行。只有提供程序和常量可以被注入到配置块中。这是为了防止服务在被配置好之前就被提前初始化。
2.  运行代码块 - 在注入器被创建后执行，被用来启动应用的。只有实例和常量能被注入到运行块中。这是为了防止在运行后还出现对系统的配置。

	angular.module('myModule', []).
	  config(function(injectables) { // provider-injector
	    // This is an example of config block.
	    // You can have as many of these as you want.
	    // You can only inject Providers (not instances)
	    // into the config blocks.
	  }).
	  run(function(injectables) { // instance-injector
	    // This is an example of a run block.
	    // You can have as many of these as you want.
	    // You can only inject instances (not Providers)
	    // into the run blocks
	  });

###配置块
模块有一些配置的简便方法，使用它们效果等同于使用代码块。举个例子：

	angular.module('myModule', []).
	  value('a', 123).
	  factory('a', function() { return 123; }).
	  directive('directiveName', ...).
	  filter('filterName', ...);
	 
	// is same as
	 
	angular.module('myModule', []).
	  config(function($provide, $compileProvider, $filterProvider) {
	    $provide.value('a', 123)
	    $provide.factory('a', function() { return 123; })
	    $compileProvider.directive('directiveName', ...).
	    $filterProvider.register('filterName', ...);
	  });

配置块按照它们注册的顺序依次被应用。唯一的例外是对常量的定义，它们应该放在配置块的开始处。

###运行块
运行块是AngularJS中最像主方法的东西。一个运行块就是一段用来启动应用的代码。它在所有服务都被配置和所有的注入器都被创建后执行。运行块通常包含了一些难以测试的代码，所以它们应该写在单独的模块里，这样在单元测试时就可以忽略它们了。

###依赖
模块可以把其他模块列为它的依赖。“依赖某个模块”意味着需要把这个被依赖的模块需要在本块模块之前被加载。换句话说被依赖模块的配置块会在本模块配置块前被执行。运行块也是一样。任何一个模块都只能被加载一次，即使它被多个模块依赖。

###异步加载
模块是一种用来管理$injector配置的方法，和脚本的加载没有关系。现在网上已有很多控制模块加载的项目，它们可以和AngularJS配合使用。因为在加载期间模块不做任何事情，所以它们可以以任意顺序或者并行方式加载。

##单元测试
单元测试最简单的一种形式是实例化一个应用的子集，并且运行它。对每个注入器来说任何一个模块都只能被夹在一次。一般来说一个应用只有一个注入器。但是在测试中，每个测试都可以有自己的注入器，这也意味着模块可以再每个VM中被加载多次。结构良好的模块能很好的进行单元测试，就像下面这样：

我们假设在所有的例子都是使用下面这种定义：

	angular.module('greetMod', []).
	 
	  factory('alert', function($window) {
	    return function(text) {
	      $window.alert(text);
	    }
	  }).
	 
	  value('salutation', 'Hello').
	 
	  factory('greet', function(alert, salutation) {
	    return function(name) {
	      alert(salutation + ' ' + name + '!');
	    }
	  });

让我们来一些测试：

	describe('myApp', function() {
	  // load the application relevant modules then load a special
	  // test module which overrides the $window with mock version,
	  // so that calling window.alert() will not block the test
	  // runner with a real alert box. This is an example of overriding
	  // configuration information in tests.
	  beforeEach(module('greetMod', function($provide) {
	    $provide.value('$window', {
	      alert: jasmine.createSpy('alert')
	    });
	  }));
	 
	  // The inject() will create the injector and inject the greet and
	  // $window into the tests. The test need not concern itself with
	  // wiring of the application, only with testing it.
	  it('should alert on $window', inject(function(greet, $window) {
	    greet('World');
	    expect($window.alert).toHaveBeenCalledWith('Hello World!');
	  }));
	 
	  // this is another way of overriding configuration in the
	  // tests using an inline module and inject methods.
	  it('should alert using the alert service', function() {
	    var alertSpy = jasmine.createSpy('alert');
	    module(function($provide) {
	      $provide.value('alert', alertSpy);
	    });
	    inject(function(greet) {
	      greet('World');
	      expect(alertSpy).toHaveBeenCalledWith('Hello World!');
	    });
	  });
	});

<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh