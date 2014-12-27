**指令**使我们用来扩展浏览器能力的技术之一。在DOM编译期间，和HTML关联着的指令会被检测到，并且被执行。这使得指令可以为DOM指定行为，或者改变它。

AngularJS有一套完整的、可扩展的、用来帮助web应用开发的指令集，它使得HTML可以转变成“**特定领域语言(DSL)**”。

## 从HTML中调用指令
指令遵循驼峰式命名，如`ngBind`。指令可以通过使用指定符号转化成链式风格的的名称来调用，特定符号包括 `:` ,`-`,`_`。你好可以选择给指令加上前缀，比如“x-”，“data-”来让它符合html的验证规则。这里有以下可以用的指令名称例子：`ng:bind`, `ng-bind`, `ng_bind`, `x-ng-bind` , `data-ng-bind`。<!--more-->

指令可以做为元素名，属性名，类名，或者注释。下面是一些等效调用myDir指令的例子：

    <span my-dir="exp"></span>
    <span class="my-dir: exp;"></span>
    <my-dir></my-dir>
    <!-- directive: my-dir exp -->

指令可以通过很多不同的方式调用，但最后结果都是等效的。下例中对此做了演示。

**index.html：**

	<!doctype html>
	<html ng-app>
	  <head>
	    <script src="http://code.angularjs.org/angular-1.1.0.min.js"></script>
	    <script src="script.js"></script>
	  </head>
	  <body>
	    <div ng-controller="Ctrl1">
	      Hello <input ng-model='name'> <hr/>
	      &ltspan ng:bind="name"&gt <span ng:bind="name"></span> <br/>
	      &ltspan ng_bind="name"&gt <span ng_bind="name"></span> <br/>
	      &ltspan ng-bind="name"&gt <span ng-bind="name"></span> <br/>
	      &ltspan data-ng-bind="name"&gt <span data-ng-bind="name"></span> <br/>
	      &ltspan x-ng-bind="name"&gt <span x-ng-bind="name"></span> <br/>
	    </div>
	  </body>
	</html>

**script.js：**

	function Ctrl1($scope) {
	  $scope.name = 'angular';
	}

**End to end test：**

	it('should show off bindings', function() {
	  expect(element('div[ng-controller="Ctrl1"] span[ng-bind]').text()).toBe('angular');
	});


## 字符串替换式

在编译期间，编译器会用$interpolate服务去检查文本中是否嵌入了表达式。这个表达式会被当成一个监视器一样注册，并且在 作为$digest循环中的一部分，它会自动更新。下面是一个替换式的例子：

	<img src="img/{{username}}.jpg">Hello {{username}}!</img>

## 编译过程和指令匹配

HTML的编译分为三个阶段：

1.  首先浏览器会用它的标准API将HTML解析成DOM。 你需要认清这一点，因为我们的模板必须是可被解析的HTML。这是AngularJS和那些“以字符串为基础而非以DOM元素为基础的”模板系统的区别之处。

2.  DOM的编译是有`$compile`方法来执行的。 这个方法会遍历DOM并找到匹配的指令。一旦找到一个，它就会被加入一个指令列表中，这个列表是用来记录所有和当前DOM相关的指令的。 一旦所有的指令都被确定了，会按照优先级被排序，并且他们的`compile`方法会被调用。 指令的`$compile()`函数能修改DOM结构，并且要负责生成一个link函数（后面会提到）。$compile方法最后返回一个合并起来的链接函数，这是链接函数是每一个指令的compile函数返回的链接函数的集合。

3.  通过调用一步所说的链接函数来将模板与作用域链接起来。这会轮流调用每一个指令的链接函数，让每一个指令都能对DOM注册监听事件，和建立对作用域的的监听。这样最后就形成了作用域的DOM的动态绑定。任何一个作用域的改变都会在DOM上体现出来。

        var $compile = ...; // injected into your code
        var scope = ...;
        
        var html = '<div ng-bind='exp'></div>';
        
        // Step 1: parse HTML into DOM element
        var template = angular.element(html);
        
        // Step 2: compile the template
        var linkFn = $compile(template);
        
        // Step 3: link the compiled template with the scope.
        linkFn(scope);

### 编译和链接分离的合理性分析
你可能会疑惑为什么编译过程和链接过程要分离。要明白其中的原因，你可以先看下面这个带有“重复指令”的例子：

	Hello {{user}}, you have these actions:
	<ul>
	  <li ng-repeat="action in user.actions">
	    {{action.description}}
	  </li>
	</ul>

简短的说，编译和链接的分离是模型和DOM结构能够动态关联的一种需要。

当上面的例子被编译后，编译器会遍历所有节点来寻找指令。例如{{user}}是一个替换式指令，`ngRepeat`是另一个指令。但是`ngRepeat`有一个难题。他需要为`user.actions`中的每一个`action` 构造一个li。这以为着它先要保存一个“干净”的li元素来用作克隆，然后等新的action插入进来时，克隆一个li并插入到ul中。但是仅仅克隆li的话工作还没完。他还需要编译这个li才能把其中的像是{{action.descriptions}}的替换式替换成相应作用域下的值。我们可以用一个简单地方法来克隆和插入li元素然后编译它。但是要编译每一个li的话，使用克隆会速度很慢， 因为编译的工程需要我们遍历DOM树，并找到对应的指令并执行它们。如果我们在一个需要循环100次循环体内执行编译的话，性能问题就会马上凸现出来。

而我们的解决方案就是将编译工程分为两个阶段。编译阶段将指令识别出来并按优先级排序，编译阶段将作用域中的实例和li进行链接。

`ngRepeat` 会阻止li子元素的编译。取而代之的是 `ngRepeat`指令会单独对li进行编译。这个编译结束后会生成一个链接函数，这个函数包含了准备li元素上的所有指令，并等待被绑定到相应克隆出来的li元素上。在执行期，`ngRepeat`之指令会监视表达式，当有新的元素增加到对应的数组之后，它就会新克隆一个li元素，为它创建一个新作用域，并使用链接函数把它和对应作用域链接上。

### 总结

**编译函数** - 编译函数在指令中是很少的， 因为大部分指令都只是为了处理相应的DOM元素实例，而不是改变模板DOM元素。考虑到性能问题，任何指令的实例见能被共享的操作都应该移到编译函数中。

**链接函数** - 指令很少不带有链接函数，链接函数可以让指令对相应克隆元素注册事件，还可以将作用域中的内容复制到DOM中。


## 如何写指令(短版) 

下面这个例子演示一个获取当前时间的指令。

**index.html：**

	<!doctype html>
	<html ng-app="time">
	  <head>
	    <script src="http://code.angularjs.org/angular-1.1.0.min.js"></script>
	    <script src="script.js"></script>
	  </head>
	  <body>
	    <div ng-controller="Ctrl2">
	      Date format: <input ng-model="format"> <hr/>
	      Current time is: <span my-current-time="format"></span>
	    </div>
	  </body>
	</html>

**script.js：**

	function Ctrl2($scope) {
	  $scope.format = 'M/d/yy h:mm:ss a';
	}
	 
	angular.module('time', [])
	  // Register the 'myCurrentTime' directive factory method.
	  // We inject $timeout and dateFilter service since the factory method is DI.
	  .directive('myCurrentTime', function($timeout, dateFilter) {
	    // return the directive link function. (compile function not needed)
	    return function(scope, element, attrs) {
	      var format,  // date format
	          timeoutId; // timeoutId, so that we can cancel the time updates
	 
	      // used to update the UI
	      function updateTime() {
	        element.text(dateFilter(new Date(), format));
	      }
	 
	      // watch the expression, and update the UI on change.
	      scope.$watch(attrs.myCurrentTime, function(value) {
	        format = value;
	        updateTime();
	      });
	 
	      // schedule update in one second
	      function updateLater() {
	        // save the timeoutId for canceling
	        timeoutId = $timeout(function() {
	          updateTime(); // update DOM
	          updateLater(); // schedule another update
	        }, 1000);
	      }
	 
	      // listen on DOM destroy (removal) event, and cancel the next UI update
	      // to prevent updating time ofter the DOM element was removed.
	      element.bind('$destroy', function() {
	        $timeout.cancel(timeoutId);
	      });
	 
	      updateLater(); // kick off the UI update process.
	    }
	  });



## 如何写指令(长版) 

下面是写一个完整的指令的例子。

	var myModule = angular.module(...);
	 
	myModule.directive('directiveName', function factory(injectables) {
	  var directiveDefinitionObject = {
	    priority: 0,
	    template: '<div></div>',
	    templateUrl: 'directive.html',
	    replace: false,
	    transclude: false,
	    restrict: 'A',
	    scope: false,
	    compile: function compile(tElement, tAttrs, transclude) {
	      return {
	        pre: function preLink(scope, iElement, iAttrs, controller) { ... },
	        post: function postLink(scope, iElement, iAttrs, controller) { ... }
	      }
	    },
	    link: function postLink(scope, iElement, iAttrs) { ... }
	  };
	  return directiveDefinitionObject;
	});


大部分情况下你不需要控制这么多细节。 这其中的具体内容下面会有完整的解释。 这一节里我们只关注大部分指令相同的地方。

要简化上面的代码，我们首先要依赖一下基本选项的默认值。使用默认值的话，上面的代码可以简化成：

	var myModule = angular.module(...);
	 
	myModule.directive('directiveName', function factory(injectables) {
	  var directiveDefinitionObject = {
	    compile: function compile(tElement, tAttrs) {
	      return function postLink(scope, iElement, iAttrs) { ... }
	    }
	  };
	  return directiveDefinitionObject;
	});

大部分指令只关心实例，并不需要将模板进行变形，所以我们还可以简化：

	var myModule = angular.module(...);
	 
	myModule.directive('directiveName', function factory(injectables) {
	  return function postLink(scope, iElement, iAttrs) { ... }
	});

### Factory method工厂函数

工厂函数是用来创建指令的。它只会被调用一次：就是当编译器第一次匹配到相应指令的时候。你可以在其中进行任何初始化的工作。调用它时使用的是 `$injector.invoke` ， 所以它遵循所有注入器的规则。

###Directive Definition Object 指令定义对象

指令定义对象给编译器提供了生成指令需要的细节。这个对象的属性有：

*  **名称name** - 当前作用域的名称，在注册是可选的。

*  **优先级priority** - 当一个DOM上有多个指令时，有会需要指定指令执行的顺序。 这个**优先级**就是用来在执行指令的compile函数前先排序的。高优先级的先执行。 相同优先级的指令顺序没有被指定谁先执行。

*  **终端terminal** - 如果被设置为true，那么该指令就会在同一个DOM的指令集和中最后被执行。任何其他“terminal”的指令也仍然会执行，因为同级的指令顺序是没有被定义的。

*  **作用域scope**- 如果被定义成：

	*  那么就会为当前指令创建一个新的作用域。如果有多个在同一个DOM上的指令要求创建新作用域，那么只有一个新的会被创建。 这一创建新作用域的规则不适用于模板的根节点，因为模板的根节点总是会得到一个新的作用域。

	*  {} 对象哈希 - 那么一个新的“孤立的”作用域就会被创建。这个“孤立的”作用域区别于一般作用域的地方在于，它不会以原型继承的方式直接继承自父作用域。这对于创建可重用的组件是非常有用的，因为可重用的组件一般不应该读或写父作用域的数据。
	这个“孤立的”作用域使用一个对象哈希来表示，这个哈希定义了一系列本地作用域属性， 这些本地作用域属性是从父作用域中衍生出来的。这些属性主要用来分析模板的值。这个哈希的键值对是本地属性为键，它的来源为值。

		*  @ 或 @attr - 将本地作用域成员成员和DOM属性绑定。绑定结果总是一个字符串，因为DOM的属性就是字符串。如果DOM属性的名字没有被指定，那么就和本地属性名一样。比如说`<widget my-attr="hello {{name}}">` 和作用域对象: `{ localName:'@myAttr' }`。当`name`值改变的时候， 作用域中的LocalName也会改变。这个`name`是从父作用域中读来的（而不是组件作用域）。
		
		*  = 或 =expression(表达式) - 在本地作用域属性和父作用域属性间建立一个双向的绑定。如果没有指定父作用域属性名称，那就和本地名称一样。 比如 `<widget my-attr="parentModel">` 和作用域对象: `{ localModel:'=myAttr' }`, 本地属性`localModel`会反映父作用域中`parentModel`的值。localModel和parentModel的任一方改变都会影响对方。
		
		*  & 或 &attr - 提供了一种能在父作用域下执行表达式的方法。如果没有指定父作用域属性名称，那就和本地名称一样。 比如 `<widget my-attr="count = count + value">`和作用域对象：`{ localFn:'increment()' }`。本地作用域成员`localFn`会指向一个`increment`表达式的函数包装。通常你可以通过这个表达式从本地作用域给父作用域传值， 操作方法是将本地变量名和值得对应关系传给这个表达式的包装函数。比如说，这个表达式是`increment(amount)`，那么你就可以用调用`localFn({amount:22})`的方式指定amount的值。

*  **控制器controller** - 控制器的构造对象。这个控制器函数是在预编译阶段被执行的，并且它是共享的，其他指令可以通过它的名字得到（参考依赖属性[require attribute]）。这就使得指令间可以互相交流来扩大自己的能力。会传递给这个函数的参数有：

	*  $scope - 当前元素关联的作用域。
	*  $element - 当前元素
	*  $attrs - 当前元素的属性对象。
	*  $transclude - 模板链接功能前绑定到正确的模板作用域：`function(cloneLinkingFn)`。

*  **请求require** - 请求将另一个控制器作为参数传入到当前链接函数。 这个请求需要传递被请求指令的控制器的名字。如果没有找到，就会触发一个**错误**。请求的名字可以加上下面两个前缀：
	
	*  `?` - 不要触发错误，这只是一个可选的请求。
	*  `^` - 没找到的话，在父元素的controller里面也查找有没有。

*  **限制restrict** - EACM中的任意一个之母。它是用来限制指令的声明格式的。如果没有这一项。那就只允许使用属性形式的指令。

	*  E - 元素名称：`<my-directive></my-directive>`
	*  A - 属性： `<div my-directive="exp"> </div>`
	*  C - 类名：`<div class="my-directive: exp;"></div>`
	*  M - 注释： `<!-- directive: my-directive exp -->`


*  `模板template` - 将当前的元素替换掉。 这个替换过程会自动将元素的属性和css类名添加到新元素上。更多细节请查考章节“创建widgets”。

* 模板templateUrl - 和template属性一样，只不过这里指示的是一个模板的URL。因为模板加载是异步的，所有编译和链接都会等到加载完成后再执行。

*  **替换replace** - 如果被设置成true那么现在的元素会被模板替换，而不是被插入到元素中。

*  **编译模板transclude** - 将元素编译好，使得指令可以开始使用它。一般情况下需要和ngTransclude指令一起使用。 使用嵌入的好处在于链接好书可以获取到预绑定在作用域上的函数。在一个典型的初始化过程中，widget会创建一个孤立的作用域，但是嵌入并不是其中一个子成员，而是这孤立作用域的兄弟成员。这使得widget可以有一个私有的状态，并且嵌入被绑定在父作用于上。

	*  true - 嵌入指令的内容。
	*  'element' - 嵌入整个元素，包括优先级较低的指令。

*  **编译compile** - 这就是后面将要讲到的编译函数。
*  **链接link** - 这就是后面将要讲到的链接函数。只有没有提供编译函数时才会用到这个值。

## 编译函数 Compile function
	
	function compile(tElement, tAttrs, transclude) { ... }

编译函数是用来处理需要修改模板DOM的情况的。因为大部分指令都不需要修改模板，所以这个函数也不常用。需要用到的例子有`ngTrepeat`，这个是需要修改模板的，还有`ngView`这个是需要异步载入内容的。编译函数接受以下参数。

*  tElement - template element - 指令所在的元素。对这个元素及其子元素进行变形之类的操作是安全的。

*  tAttrs - template attributes - 这个元素上所有指令声明的属性，这些属性都是在编译函数里共享的， 参考章节“属性”。

*  transclude - 一个嵌入的链接函数`function(scope, cloneLinkingFn)`。

注意：如果模板被克隆了，那么模版实例和链接实例可能不是同一个对象。 所以在编译函数不要进行任何DOM变形之外的操作。 更重要的，DOM监听事件的注册应该在链接函数中做，而不是编译函数中。

编译函数可以返回一个对象或者函数。

*  返回函数 - 等效于在编译函数不存在时，使用配置对象的`link`属性注册的链接函数。

*  返回对象 - 返回一个通过`pre`或`post`属性注册了函数的对象- 使你能更具体的链接函数的执行点。参考下面`pre-linking`和`post-liking`函数的解释。


## 链接函数 Linking function

	function link(scope, iElement, iAttrs, controller) { ... }

链接函数负责注册DOM事件和更新DOM。它是在模板被克隆之后执行的。 它也是大部分指令逻辑代码编写的地方。

* scope - 指令需要监听的作用域。

* iElement - instance element - 指令所在的元素。只有在`postLink`函数中对元素的子元素进行操作才是安全的，因为那时它们才已经全部连接好。

* iAttrs - instance attributes - 实例属性，一个标准化的、所有声明在当前元素上的属性列表，这些属性在所有链接函数间是共享的。参考“属性”。

* controller - 控制器实例，如果至少有一个指令定义了控制器，那么这个控制器就会被传递。控制器也是指令间共享的，指令可以用它来相互通信。

**Pre-linking function**
在子元素被链接前执行。不能用来进行DOM的变形，以为可能导致链接函数找不到正确的元素来链接。

**Post-linking function**
所有元素都被链接后执行。可以操作DOM的变形。

##  属性 Attributes

The Attributes object属性对象 - 作为参数传递给链接函数和编译函数。这使得下列资源可以被使用。

*  标准化的属性名:  因为指令的名称，如`ngBind`可以有很多种变形表示，如`ng:bind`，或者`x-ng-bind`，这个对象使得可以用标准的名称获取到相应的属性。

*  指令间通信：所有指令间共享同一个属性对象的实例，这使得指令可以通过这个属性对象通信。

*  支持替换式：属性中若包含替换式，那么其他指令能够独到替换式的值。

*  监视替换式属性：使用$observe 能监视使用了替换式的属性(比如 `src="{{bar}}"`)。这是一种高效的，也是为唯一的方法来获取变量的值。因为在链接阶段替换式还没有被替换成值，所有变量此时是undefined。

        function linkingFn(scope, elm, attrs, ctrl) {
        // get the attribute value
        console.log(attrs.ngModel);
        
        // change the attribute
        attrs.$set('ngModel', 'new value');
        
        // observe changes to interpolated attribute
        attrs.$observe('ngModel', function(value) {
            console.log('ngModel has changed value to ' + value);
        });
        }
	
## 理解嵌入和作用域
开发者总是希望组件能尽量重用。下面是一个简单地对话框组件的伪代码。


	<button ng-click="show=true">show</button>
	<dialog title="Hello {{username}}."
		visible="show"
		on-cancel="show = false"
		on-ok="show = false; doSomething()">
		Body goes here: {{username}} is {{title}}.
	</dialog>

点击`show`按钮会打开对话框，对话框有一个标题， 标题绑定了一个`username`。还包含了一个我们希望嵌入对话框的`body`。

下面是一个模板对话框组件模板的例子。

	<div ng-show="show">
	  <h3>{{title}}</h3>
	  <div class="body" ng-transclude></div>
	  <div class="footer">
	    <button ng-click="onOk()">Save changes</button>
	    <button ng-click="onCancel()">Close</button>
	  </div>
	</div>

在我们正确设置好作用域前，它不会被正确渲染。
 第一个要处理的问题就是，对话框模板希望标题是被定义好的，但是初始化的时候需要绑定成`username`。然后，onOK和onCancel函数要在作用域里被定义好，才能让button按钮起作用。 这限制了组件的重用。要解决这个映射的问题，我们需要创建一个本地的作用域：

	scope: {
	  title: '=',             // set up title to accept data-binding
	  onOk: '&',              // create a delegate onOk function
	  onCancel: '&',          // create a delegate onCancel function
	  show: '='
	}
	
在组件上创建本地作用域会造成两个问题：

*  孤立 - 如果用户忘了给`title`属性赋值，那么模板就会绑定到父作用域的值上。这是一个比较讨厌的问题。

*  嵌入 - 嵌入的DOM可以获取到组件的本地变量， 这可能是的嵌入需要的用来做数据绑定的属性被覆写。在我们的例子里，组件的`title`属性覆写了嵌入的`title`属性。

要解决作用域不独立的问题，指令需要声明一个新的孤立作用域。它不从父作用域作原型继承， 这样我们也不用担心会发生属性的覆盖。

但是孤立作用域也会导致一个新问题：如果嵌入的DOM是孤立作用域的一个子元素，那它就不会被绑定任何东西。为了解决这个问题，需要在组件创建本地作用域前，  让嵌入的作用域成为子作用域的一个属性，这让组件的作用域和嵌入内容成为兄弟节点。

这可能感觉有点复杂，但是却能给widget的使用者和开发者惊喜。

所以，指令最后会定义成这个样子：

	transclude: true,
	scope: {
	  title: 'bind',          // set up title to accept data-binding
	  onOk: 'expression',     // create a delegate onOk function
	  onCancel: 'expression', // create a delegate onCancel function
	  show: 'accessor'        // create a getter/setter function for visibility.
	}

## 创建组件

通常需要使用更复杂的DOM结构替换单个指令。这允许指令成为一个可以生成应用程序可重用组件的短标志。

<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh