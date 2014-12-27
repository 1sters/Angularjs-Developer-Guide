将服务用作控制器的依赖和将服务用作其他服务的依赖很类似。

因为Javascript是一种动态语言，依赖注入系统无法通过静态类型来知道应该注入什么样的服务（静态类型语言就可以）。所以，你应该$inject的属性来指定服务的名字，这个属性是一个包含这需要注入的服务的名字字符串的数组。名字要和服务注册到系统时的名字匹配。服务的名称的顺序也很重要：当执行工场函数时传递的参数是依照数组里的顺序的。但是工场函数中参数的名字不重要，但最好还是和服务本身的名字一样，下面展示了这样的好处:<!--more-->

	function myController($loc, $log) {
	this.firstMethod = function() {
	 // use $location service
	 $loc.setHash();
	};
	this.secondMethod = function() {
	 // use $log service
	 $log.info('...');
	};
	}
	// which services to inject ?
	myController.$inject = ['$location', '$log'];

**index.html:**

	<!doctype html>
	<html ng-app="MyServiceModule">
	  <head>
	    <script src="http://code.angularjs.org/angular-1.0.2.min.js"></script>
	    <script src="script.js"></script>
	  </head>
	  <body>
	    <div ng-controller="myController">
	      <p>Let's try this simple notify service, injected into the controller...</p>
	      <input ng-init="message='test'" ng-model="message" >
	      <button ng-click="callNotify(message);">NOTIFY</button>
	    </div>
	  </body>
	</html>

**script.js:**

	angular.
	 module('MyServiceModule', []).
	 factory('notify', ['$window', function(win) {
	    var msgs = [];
	    return function(msg) {
	      msgs.push(msg);
	      if (msgs.length == 3) {
	        win.alert(msgs.join("\n"));
	        msgs = [];
	      }
	    };
	  }]);
	 
	function myController(scope, notifyService) {
	  scope.callNotify = function(msg) {
	    notifyService(msg);
	  };
	}
	
	myController.$inject = ['$scope','notify'];

**end to end test：**

	it('should test service', function() {
		expect(element(':input[ng\\:model="message"]').val()).toEqual('test');
	});



###隐式依赖注入
AngularJS依赖注入系统的新特性使得AngularJS可以通过参数名称来判断依赖。让们重写上面的例子，展示一下隐式地依赖$window, $scope：

**index.html:**


	<!doctype html>
	<html ng-app="MyServiceModuleDI">
	  <head>
	    <script src="http://code.angularjs.org/angular-1.0.2.min.js"></script>
	    <script src="script.js"></script>
	  </head>
	  <body>
	    <div ng-controller="myController">
	      <p>Let's try the notify service, that is implicitly injected into the controller...</p>
	      <input ng-init="message='test'" ng-model="message">
	      <button ng-click="callNotify(message);">NOTIFY</button>
	    </div>
	  </body>
	</html>

**script.js:**

	angular.
	 module('MyServiceModuleDI', []).
	 factory('notify', function($window) {
	    var msgs = [];
	    return function(msg) {
	      msgs.push(msg);
	      if (msgs.length == 3) {
	        $window.alert(msgs.join("\n"));
	        msgs = [];
	      }
	    };
	  });
	 
	function myController($scope, notify) {
	  $scope.callNotify = function(msg) {
	    notify(msg);
	  };
	


但是如你要压缩你的代码，你的变量名会被重命名，你就只能显示地指定依赖了。

##相关主题
*  理解AngularJS服务
*  创建AngularJS服务
*  管理服务依赖
*  测试AngularJS服务

##相关API
Angular Service API


<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh