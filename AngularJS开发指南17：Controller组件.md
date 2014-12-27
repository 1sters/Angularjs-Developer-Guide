在AngularJS中，控制器是一个Javascript函数（类型/类），用来增强除了根作用域意外的作用域实例的。当你或者AngularJS本身通过`scope.$new`俩创建一个新的子作用域对象时，有一个选项能让你将它当做参数传递给控制器。这能使AngularjS将控制器和这个作用域联系起来，增强作用域的行为。

控制器用于：

*  设置好作用域对象的初始状态。
*  给作用域对象增加行为。

##给作用于对象设置初始状态
一般来说，当你创建应用时，你需要对它的作用域设置初始状态。

AngularJS将对作用域对象调用控制器的构造函数（从某种意义上来说就像使用的Javascript的apply方法），以此来设置作用域的初始状态。这意味着AngularJS不会创建控制器类型的实例（不会使用new方法来调用控制器构造函数）。控制器总是对某个已存在的作用域对象调用。<!--more-->

你可以通过创建一个模型属性来设置初始作用域的初始状态。 比如:

	function GreetingCtrl($scope) { $scope.greeting = 'Hola!'; }

GreetingCtrl控制器创建了一个模板中可以调用的叫`greeting`的模型。

##给作用域对象增加行为
AngularJS作用域对象的行为是由作用域的方法来表示的。这些方法是可以在模板或者说视图中调用的。这些方法和应用模型交互，并且能改变模型。

如我们在模型那一章所说的，任何对象（或者原生的类型）被赋给作用域后就会变成模型。任何赋给作用域的方法，都能在模板或者说视图中被调用，并且能通过表达式或者`ng`事件指令调用。（比如，ngClick）

##正确地使用控制器
总的来说，一个控制器不应该做太多工作。它应该只包含单个视图的业务逻辑。

保持控制器职责单一的最常见做法是将那些不属于控制器的工作抽离到服务中，然后通过依赖注入在控制器中使用这些服务。这在依赖注入服务的章节中会详细讨论。

不要用控制器干下面的事情：

*  控制器应该只关心业务逻辑。DOM操作（表现层逻辑）通常会把测试弄得很难。将任何表现层逻辑放到控制器中都会显著地增加对业务逻辑的测试难度。AngularJS提供`dev_guide.templates.databinding`用来自动进行DOM操作。如果你需要手动操作DOM，将表现层的逻辑抽离到指令中。
*  对输入格式化 — 你应该用AngularJS的表单控制来实现格式化。.
*  对输出格式化 — 该用AngularJS的过滤器实现。.
*  在控制器中运行无状态或者有状态但在控制器中共享的代码 — 该用服务来实现.
*  实例化组件或者控制其它组件的生命周期（比如创建一个服务的实例）.

##将控制器和AngularJS的作用域对象联系起来
你可以显示地用`scope.$new`来将控制器和作用域对象显示地联系起来，或者隐式地通过`ngController`指令或者`$route`服务来联系。

###控制器构造函数和方法的例子
为了阐述AngularJS的控制器组件的运行原理，让我们来创建一个拥有下面这些组件的小应用：

*  一个有两个按钮和一条消息的模板
*  一个叫`spice`的字符串模型。
*  一个拥有两个方法的控制器。方法是用来设置`spice`的值得。

模板中的消息包含了一个对`spice`模型的绑定，它初始的字符串是“very”。这个spice模型会被设置成 chili 或者 jalapeno，这取决于哪个按钮会被点击。消息会通过`data-binding`自动更新。

###一个 spice 控制器例子
	<body ng-controller="SpicyCtrl">
	 <button ng-click="chiliSpicy()">Chili</button>
	 <button ng-click="jalapenoSpicy()">Jalapeño</button>
	 <p>The food is {{spice}} spicy!</p>
	</body>
	 
	function SpicyCtrl($scope) {
	 $scope.spice = 'very';
	 $scope.chiliSpicy = function() {
	   $scope.spice = 'chili';
	 }
	 $scope.jalapenoSpicy = function() {
	  $scope.spice = 'jalapeño';
	 }
	}

例子中有下面这些需要注意：

*  `ngController`指令是用来（隐式地）为模板创建作用域的。并且使用命令中指定的`spicyCtrl`控制器来增强这个作用域。
*  `spicyCtrl`只是一个纯Javascript函数。使用了驼峰式命名法（可选）命名并以**Ctrl**或者**Controller**结尾。
*  对作用域对象赋予一个新的属性会创建或者更新模型。
*  控制器方法能够直接通过赋格作用域对象这个方式创建（如例子中的`chiliSpicy`方法）。
*  控制器中的所用方法都能在模板中调用（在body元素或者子元素中）.
*  NB:AngularJS的老版本（1.0RC之前的）能让你用它来替换$scope方法。但是现在不行了。在作用域中定义的方法中，他和$scope是可以互换的（AngularJS将它设置成$scope），那是在你的控制器构造函数中就不行了。
*  NB: AngularJS的老版本（1.0RC之前的）自动给作用域对象原型添加方法，现在不会了。所有的方法都必须手动添加到作用域。

控制器方法可以接受参数，像下面里中演示的：

###控制器方法参数的例子

	<body ng-controller="SpicyCtrl">
	 <input ng-model="customSpice" value="wasabi">
	 <button ng-click="spicy('chili')">Chili</button>
	 <button ng-click="spicy(customSpice)">Custom spice</button>
	 <p>The food is {{spice}} spicy!</p>
	</body>
	 
	function SpicyCtrl($scope) {
	 $scope.spice = 'very';
	 $scope.spicy = function(spice) {
	   $scope.spice = spice;
	 }
	}

注意`SpicyCtrl`控制器只定义了一个叫spicy的方法，它接受一个叫做spice的参数。和这个控制器相关的模板在第一个按钮事件中传递了一个`chili`常量给控制器方法，在第二个按钮中传递一个模型属性。

###控制器继承示例
AngularJS中的控制器继承是基于作用域的继承的。让我们看下面这个例子：

	<body ng-controller="MainCtrl">
	 <p>Good {{timeOfDay}}, {{name}}!</p>
	 <div ng-controller="ChildCtrl">
	   <p>Good {{timeOfDay}}, {{name}}!</p>
	   <p ng-controller="BabyCtrl">Good {{timeOfDay}}, {{name}}!</p>
	</body>
	 
	function MainCtrl($scope) {
	 $scope.timeOfDay = 'morning';
	 $scope.name = 'Nikki';
	}
	 
	function ChildCtrl($scope) {
	 $scope.name = 'Mattie';
	}
	 
	function BabyCtrl($scope) {
	 $scope.timeOfDay = 'evening';
	 $scope.name = 'Gingerbreak Baby';
	}


注意我们是如何在模板中嵌套我们的`ngController`指令的。这个模板结构会使得AngularJS为视图创建四个作用域：

*  根作用域
*  MainCtrl作用域， 它包含了模型timeOfDay和模型name。
*  ChildCtrl作用域，它继承了上层作用域的timeOfDay，复写了name。
*  BabyCtrl作用域，复写了MainCtrl中定义的timeOfDay和ChildCtrl中的name。

控制器的继承和模型继承是同一个原理。所以在我们前面的例子中，所有的模型都用返回相应字符串的控制器方法代替。

注意：常规的原型继承对控制器来说不起作用。因为正如我们之前提到的，控制器不是直接实例化的，而是对作用域对象调用的。

###测试控制器
尽管有很多测试控制器的方法。但最好的是像我们下面这样展示的。这个例子注入了$rootScope和$controller。


控制器函数：

	function myController($scope) {
	   $scope.spices = [{"name":"pasilla", "spiciness":"mild"},
	                  {"name":"jalapeno", "spiceiness":"hot hot hot!"},
	                  {"name":"habanero", "spiceness":"LAVA HOT!!"}];
	 
	   $scope.spice = "habanero";
	}

控制器测试：

	describe('myController function', function() {
	 
	  describe('myController', function() {
	    var scope;
	 
	    beforeEach(inject(function($rootScope, $controller) {
	      scope = $rootScope.$new();
	      var ctrl = $controller(myController, {$scope: scope});
	    }));
	 
	    it('should create "spices" model with 3 spices', function() {
	      expect(scope.spices.length).toBe(3);
	    });
	 
	    it('should set the default value of spice', function() {
	      expect(scope.spice).toBe('habanero');
	    });
	  });
	});

如果你需要测试嵌套的控制器，你需要创建和DOM中相同相同的作用域层级。

	describe('state', function() {
	    var mainScope, childScope, babyScope;
	 
	    beforeEach(inject(function($rootScope, $controller) {
	        mainScope = $rootScope.$new();
	        var mainCtrl = $controller(MainCtrl, {$scope: mainScope});
	        childScope = mainScope.$new();
	        var childCtrl = $controller(ChildCtrl, {$scope: childScope});
	        babyScope = childCtrl.$new();
	        var babyCtrl = $controller(BabyCtrl, {$scope: babyScope});
	    }));
	 
	    it('should have over and selected', function() {
	        expect(mainScope.timeOfDay).toBe('morning');
	        expect(mainScope.name).toBe('Nikki');
	        expect(childScope.timeOfDay).toBe('morning');
	        expect(childScope.name).toBe('Mattie');
	        expect(babyScope.timeOfDay).toBe('evening');
	        expect(babyScope.name).toBe('Gingerbreak Baby');
	    });
	});

###相关主题
*  AngularJS中的MVC
*  理解Controller组件
*  理解view组件


<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh