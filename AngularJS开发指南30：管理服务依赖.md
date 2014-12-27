AngularJS允许服务声明它的实例和构造依赖的服务。

要声明依赖，你可以在工场方法参数中隐式指明他们，也可以将$inject属性设置成包含了依赖名称的数组，或者是使用数组表示法。不推荐使用$inject属性的这种方法。<!--more-->

使用数组表示法:

	function myModuleCfgFn($provide) {
	  $provide.factory('myService', ['dep1', 'dep2', function(dep1, dep2) {}]);
	}

使用$inject属性:

	function myModuleCfgFn($provide) {
	  var myServiceFactory = function(dep1, dep2) {};
	  myServiceFactory.$inject = ['dep1', 'dep2'];
	  $provide.factory('myService', myServiceFactory);
	}

使用隐式依赖(使用代码压缩是会失效):

	function myModuleCfgFn($provide) {
	  $provide.factory('myService', function(dep1, dep2) {});
	}


下面是一个互相依赖的两个服务的例子。它们也依赖了其他AngularJS提供的服务。

	/**
	 * batchLog service allows for messages to be queued in memory and flushed
	 * to the console.log every 50 seconds.
	 *
	 * @param {*} message Message to be logged.
	 */
	  function batchLogModule($provide){
	    $provide.factory('batchLog', ['$timeout', '$log', function($timeout, $log) {
	      var messageQueue = [];
	 
	      function log() {
	        if (messageQueue.length) {
	          $log('batchLog messages: ', messageQueue);
	          messageQueue = [];
	        }
	        $timeout(log, 50000);
	      }
	 
	      // start periodic checking
	      log();
	 
	      return function(message) {
	        messageQueue.push(message);
	      }
	    }]);
	 
	    /**
	     * routeTemplateMonitor monitors each $route change and logs the current
	     * template via the batchLog service.
	     */
	    $provide.factory('routeTemplateMonitor',
	                ['$route', 'batchLog', '$rootScope',
	         function($route,   batchLog,   $rootScope) {
	      $rootScope.$on('$routeChangeSuccess', function() {
	        batchLog($route.current ? $route.current.template : null);
	      });
	    }]);
	  }
	 
	  // get the main service to kick of the application
	  angular.injector([batchLogModule]).get('routeTemplateMonitor');

上例中有几点要注意：

*  batchLog服务依赖内建的$timeout和$log服务，并且允许消息批量地使用console.log记录。
*  和batchLog服务一样，routeTemplateMonitor服务依赖内建的$route服务。
*  自定义的服务都使用隐式表示和数组法来表示自己的依赖。最重要的是数组中的服务的名字顺序要和工厂函数参数的名字顺序对应。除非依赖是隐式地通过函数参数名表示的，那么就是有声明依赖的数组名称顺序决定依赖注入的顺序。

##相关主题
*  理解AngularJS服务
*  创建AngularJS服务
*  管理服务依赖
*  测试AngularJS服务

##相关API
Angular Service API
Angular Injector API


<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh