表单控件(input, select, textarea )是用来获取用户输入的。表单则是一组有联系的表单控件的集合。

用户能通过表单和表单控件提供验证的服务，知道自己的输入是否合法。这样能让用户交互变得友好，因为用户能通过反馈来修正自己的错误。不过，虽然客户端的验证能够起到很大作用，但也很容易被绕过，所以不能完全依靠客户端验证。 要建立安全的应用，服务器端验证还是必不可少的。

##简单表单
了解AngularJS双向绑定的关键在于了解`ngModel`指令。这个指令通过动态将model和view互相映射，来实现双向绑定。此外它还提供API给其它指令来增强它们的行为。<!--more-->

**index.html：**

	<!doctype html>
	<html ng-app>
	  <head>
	    <script src="http://code.angularjs.org/angular-1.0.2.min.js"></script>
	    <script src="script.js"></script>
	  </head>
	  <body>
	    <div ng-controller="Controller">
	      <form novalidate class="simple-form">
	        Name: <input type="text" ng-model="user.name" /><br />
	        E-mail: <input type="email" ng-model="user.email" /><br />
	        Gender: <input type="radio" ng-model="user.gender" value="male" />male
	        <input type="radio" ng-model="user.gender" value="female" />female<br />
	        <button ng-click="reset()">RESET</button>
	        <button ng-click="update(user)">SAVE</button>
	      </form>
	      <pre>form = {{user | json}}</pre>
	      <pre>master = {{master | json}}</pre>
	    </div>
	  </body>
	</html>

**script.js：**

	function Controller($scope) {
	  $scope.master= {};
	 
	  $scope.update = function(user) {
	    $scope.master= angular.copy(user);
	  };
	 
	  $scope.reset = function() {
	    $scope.user = angular.copy($scope.master);
	  };
	 
	  $scope.reset();
	}


注意例子中的`novalidate`是用来屏蔽浏览器本身的验证功能的。

##使用css的类名

为了能美化表单和表单元素，ngModel指令会自动为元素添加以下css类：


* ng-valid
* ng-invalid
* ng-pristine
* ng-dirty

下面的例子演示了如何使用CSS来显示表单的验证结果。其中的user.name和user.email是必填项，它们没有被填写就提交时，底色会变红。这能避免用户注意力一开始就被分散。只有在与界面交互之后才显示错误。

**index.html：**

	<!doctype html>
	<html ng-app>
	  <head>
	    <script src="http://code.angularjs.org/angular-1.0.2.min.js"></script>
	    <script src="script.js"></script>
	  </head>
	  <body>
	    <div ng-controller="Controller">
	      <form novalidate class="css-form">
	        Name:
	          <input type="text" ng-model="user.name" required /><br />
	        E-mail: <input type="email" ng-model="user.email" required /><br />
	        Gender: <input type="radio" ng-model="user.gender" value="male" />male
	        <input type="radio" ng-model="user.gender" value="female" />female<br />
	        <button ng-click="reset()">RESET</button>
	        <button ng-click="update(user)">SAVE</button>
	      </form>
	    </div>
	    
	    <style type="text/css">
	      .css-form input.ng-invalid.ng-dirty {
	        background-color: #FA787E;
	      }
	    
	      .css-form input.ng-valid.ng-dirty {
	        background-color: #78FA89;
	      }
	    </style>
	  </body>
	</html>

**script.js：**

	function Controller($scope) {
	  $scope.master= {};
	 
	  $scope.update = function(user) {
	    $scope.master= angular.copy(user);
	  };
	 
	  $scope.reset = function() {
	    $scope.user = angular.copy($scope.master);
	  };
	 
	  $scope.reset();
	}


##与表单的状态或者表单元素状态绑定
一个表单就是一个`FormController`的实例。表单实例可以通过name属性选择性地公开到作用域中。同样的，一个表单控件也是一个`NgModelController`的实例。表单控件也能同样的被公开到作用域中。这意味视图能通过绑定的基本功能获取表单或者表单控件的状态。

这些特点让我们能将上面的例子改造成下面这样：

*  只有当表单发生改变时，重置按钮才会被显示出来。
*  只有当表单有改变并且改变的值都是合法的，保存按钮才会被显示出来。
*  能自定义user.email和user.agree的错误信息。

 
**index.html：**

	<!doctype html>
	<html ng-app>
	  <head>
	    <script src="http://code.angularjs.org/angular-1.0.2.min.js"></script>
	    <script src="script.js"></script>
	  </head>
	  <body>
	    <div ng-controller="Controller">
	      <form name="form" class="css-form" novalidate>
	        Name:
	          <input type="text" ng-model="user.name" name="uName" required /><br />
	        E-mail:
	          <input type="email" ng-model="user.email" name="uEmail" required/><br />
	        <div ng-show="form.uEmail.$dirty && form.uEmail.$invalid">Invalid:
	          <span ng-show="form.uEmail.$error.required">Tell us your email.</span>
	          <span ng-show="form.uEmail.$error.email">This is not a valid email.</span>
	        </div>
	    
	        Gender: <input type="radio" ng-model="user.gender" value="male" />male
	        <input type="radio" ng-model="user.gender" value="female" />female<br />
	    
	        <input type="checkbox" ng-model="user.agree" name="userAgree" required />
	        I agree: <input ng-show="user.agree" type="text" ng-model="user.agreeSign"
	                  required /><br />
	        <div ng-show="!user.agree || !user.agreeSign">Please agree and sign.</div>
	    
	        <button ng-click="reset()" ng-disabled="isUnchanged(user)">RESET</button>
	        <button ng-click="update(user)"
	                ng-disabled="form.$invalid || isUnchanged(user)">SAVE</button>
	      </form>
	    </div>
	  </body>
	</html>


**script.js：**

	function Controller($scope) {
	  $scope.master= {};
	 
	  $scope.update = function(user) {
	    $scope.master= angular.copy(user);
	  };
	 
	  $scope.reset = function() {
	    $scope.user = angular.copy($scope.master);
	  };
	 
	  $scope.isUnchanged = function(user) {
	    return angular.equals(user, $scope.master);
	  };
	 
	  $scope.reset();
	}


##自定义验证
AngularJS实现了大部分常见的html5表单输入元素(text, number, url, email, radio, checkbox)，也实现了很多用于验证的指令(required, pattern, minlength, maxlength, min, max)。

想要定义你自己的验证器的话，可以通过在你自己的指令中添加一个验证函数给ngModel的控制器来实现。要想获得控制器的引用，需要在指令中指定依赖，像下面的例子一样。验证函数可以写在两个地方。

*  模型到视图的更新中- 只要绑定的模型改变了，NgModelController#$formatters数组中的函数就会被轮流调用，所以每一个函数都有机会对数据进行格式化或者通过NgModelController#$setValidity来改变表单的验证状态。

*  视图到模型的更新中- 相同的，只要用户与表单实现了就会，NgModelController#$setViewValue就会被调用。 这次是NgModelController#$parsers数组中的函数会被依次调用，每个函数都有机会来改变值或者通过NgModelController#$setValidity来改变表单的验证状态。

下面的例子中，我们创建了两个指令。

*  第一个要验证的是输入是否是整数。例如，1.23就不是符合验证的值，因为它包含了分数部分。注意，我们是将验证数组中的项从头取出，而不是压入。这是因为我们要在输入值被改变（格式化）前，先验证输入的合法性。

*  第二个指令是一个“智能浮点(smart-float)”。它能把"1.2"或者"1,2"都转化为合法的浮点数"1.2"。注意，这里我们不能使用“数字输入类型”，因为H支持TML5的浏览器不允许用户输入像"1,2"这样的非法值。

**index.html：**

	<!doctype html>
	<html ng-app="form-example1">
	  <head>
	    <script src="http://code.angularjs.org/angular-1.0.2.min.js"></script>
	    <script src="script.js"></script>
	  </head>
	  <body>
	    <div ng-controller="Controller">
	      <form name="form" class="css-form" novalidate>
	        <div>
	          Size (integer 0 - 10):
	          <input type="number" ng-model="size" name="size"
	                 min="0" max="10" integer />{{size}}<br />
	          <span ng-show="form.size.$error.integer">This is not valid integer!</span>
	          <span ng-show="form.size.$error.min || form.size.$error.max">
	            The value must be in range 0 to 10!</span>
	        </div>
	    
	        <div>
	          Length (float):
	          <input type="text" ng-model="length" name="length" smart-float />
	          {{length}}<br />
	          <span ng-show="form.length.$error.float">
	            This is not a valid float number!</span>
	        </div>
	      </form>
	    </div>
	  </body>
	</html>

**script.js：**

	var app = angular.module('form-example1', []);
	 
	var INTEGER_REGEXP = /^\-?\d*$/;
	app.directive('integer', function() {
	  return {
	    require: 'ngModel',
	    link: function(scope, elm, attrs, ctrl) {
	      ctrl.$parsers.unshift(function(viewValue) {
	        if (INTEGER_REGEXP.test(viewValue)) {
	          // it is valid
	          ctrl.$setValidity('integer', true);
	          return viewValue;
	        } else {
	          // it is invalid, return undefined (no model update)
	          ctrl.$setValidity('integer', false);
	          return undefined;
	        }
	      });
	    }
	  };
	});
	 
	var FLOAT_REGEXP = /^\-?\d+((\.|\,)\d+)?$/;
	app.directive('smartFloat', function() {
	  return {
	    require: 'ngModel',
	    link: function(scope, elm, attrs, ctrl) {
	      ctrl.$parsers.unshift(function(viewValue) {
	        if (FLOAT_REGEXP.test(viewValue)) {
	          ctrl.$setValidity('float', true);
	          return parseFloat(viewValue.replace(',', '.'));
	        } else {
	          ctrl.$setValidity('float', false);
	          return undefined;
	        }
	      });
	    }
	  };
	});



##实现自定义的表单控件(用ngModel)
AngularJS已经实现了所有基本的HTML表单控件（input,select,textarea），对于大部分情况应该已经够了。但是，你还是可以通过写指令来实现你自己的表单控件。

要和ngModel指令协同工作实现自定义控件，并且实现双向绑定的话，需要：

*  实现render方法。这个方法负责在数据传递给NgModelController#$formatters后来渲染数据。
*  在用户与控件交互并且模型需要被更新时，调用$setViewValue方法。这通常是在DOM的监听事件中完成的。（查看$compileProvider.directive来获取更多信息）

下面的例子演示了如何添加一个“内容可编辑”的数据双向绑定的元素。


**index.html：**

	<!doctype html>
	<html ng-app="form-example2">
	  <head>
	    <script src="http://code.angularjs.org/angular-1.0.2.min.js"></script>
	    <script src="script.js"></script>
	  </head>
	  <body>
	    <div contentEditable="true" ng-model="content" title="Click to edit">Some</div>
	    <pre>model = {{content}}</pre>
	    
	    <style type="text/css">
	      div[contentEditable] {
	        cursor: pointer;
	        background-color: #D0D0D0;
	      }
	    </style>
	  </body>
	</html>

**script.js：**

	angular.module('form-example2', []).directive('contenteditable', function() {
	  return {
	    require: 'ngModel',
	    link: function(scope, elm, attrs, ctrl) {
	      // view -> model
	      elm.bind('blur', function() {
	        scope.$apply(function() {
	          ctrl.$setViewValue(elm.html());
	        });
	      });
	 
	      // model -> view
	      ctrl.$render = function(value) {
	        elm.html(value);
	      };
	 
	      // load init value from DOM
	      ctrl.$setViewValue(elm.html());
	    }
	  };
	});

<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh