虽然AngularJS提过了很多有用的服务，但是如果你要创建一个很棒的应用，你可能还是要写自己的服务。你可以通过在模块中注册一个服务工场函数，或者通过Modeul#factory api或者直接通过模块配置函数中的$provide api来实现。

所有的服务都符合依赖注入的原则。它们用一个唯一的名字将自己注册进AngularJS的依赖注入系统（injector），并且声明需要提供给工场函数的依赖。它们的依赖在测试中可以是虚拟的，这使得它们能很好地被测试。<!--more-->

##注册服务
要注册服务，你首先要有一个包含该服务的模块。然后你就能通过模块的api或者使用模块配置函数中的$provide服务来注册你的服务了。下面的伪代码显示了这两种方法。

使用angular.Module api:

	var myModule = angular.module('myModule', []);
	myModule.factory('serviceId', function() {
	  var shinyNewServiceInstance;
	  //factory function body that constructs shinyNewServiceInstance
	  return shinyNewServiceInstance;
	});

使用$provide服务:

	angular.module('myModule', [], function($provide) {
	  $provide.factory('serviceId', function() {
	    var shinyNewServiceInstance;
	    //factory function body that constructs shinyNewServiceInstance
	    return shinyNewServiceInstance;
	  });
	});

注意，你不应该注册一个服务实例，而是一个会在被调用时创建实例的工场函数。

##依赖
服务不仅可以被依赖，还可以有自己的依赖。依赖可以在工场函数的参数中指定。参阅AngularJS的依赖注入系统，和使用依赖的数组表示法和$inject属性来让依赖表示精简化。

下面是一个很简单的服务的例子。这个服务依赖于$window服务（会被当成参数传递给工场函数），并且只是个函数。这个服务的任务是存储所有的通知；在第三个通知以后，服务会用window的alert来输出所有的通知。

	angular.module('myModule', [], function($provide) {
	  $provide.factory('notify', ['$window', function(win) {
	    var msgs = [];
	    return function(msg) {
	      msgs.push(msg);
	      if (msgs.length == 3) {
	        win.alert(msgs.join("\n"));
	        msgs = [];
	      }
	    };
	  }]);
	});


##实例化AngularJS的服务
所有服务都是延迟实例化的。这意味着所有的服务只有在需要时，或者被依赖时才会实例化。换句话说，AngularJS不会实例化服务，除非被要请求了或者被应用直接或间接依赖了。

##作为单例的服务
最好，要注意的是所有AngularJS服务都是单例的。这意味着在每一个注入器中都只有一个需要的服务的实例。因为AngularJS极度讨厌全局的东西，所以是可以创建多个注入器的，并且每个住一起有自己的服务实例。但这种情况很少，除非在测试中，这样的特性才极度重要。

##相关主题
*  理解AngularJS服务
*  管理服务以来
*  将服务注入控制器
*  测试AngularJS服务

##相关API
*  Angular Service API


<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh