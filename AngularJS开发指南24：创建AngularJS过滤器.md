写一个你自己的过滤器非常容易：在你的模块中注册一个新的过滤器（可注入的）工厂函数就行了。这个工厂函数必须放回一个新的过滤器函数，这个过滤函数的第一个参数接受的是输入。任何过滤器参数都会被当成附加的参数传递给过滤器。

下面的例子展示了逆转字符串文本。另外，它有条件地将文本大写并填上颜色。<!--more-->

**index.html:**

	<!doctype html>
	<html ng-app="MyReverseModule">
	  <head>
	    <script src="http://code.angularjs.org/angular-1.0.2.min.js"></script>
	    <script src="script.js"></script>
	  </head>
	  <body>
	    <div ng-controller="Ctrl">
	      <input ng-model="greeting" type="greeting"><br>
	      No filter: {{greeting}}<br>
	      Reverse: {{greeting|reverse}}<br>
	      Reverse + uppercase: {{greeting|reverse:true}}<br>
	    </div>
	  </body>
	</html>

**script.js:**

	angular.module('MyReverseModule', []).
	  filter('reverse', function() {
	    return function(input, uppercase) {
	      var out = "";
	      for (var i = 0; i < input.length; i++) {
	        out = input.charAt(i) + out;
	      }
	      // conditional based on optional argument
	      if (uppercase) {
	        out = out.toUpperCase();
	      }
	      return out;
	    }
	  });
	 
	function Ctrl($scope) {
	  $scope.greeting = 'hello';
	}

**End to end test:**

	it('should reverse greeting', function() {
	  expect(binding('greeting|reverse')).toEqual('olleh');
	  input('greeting').enter('ABC');
	  expect(binding('greeting|reverse')).toEqual('CBA');
	});	


###Demo

##相关主题
*  理解AngularJS过滤器
*  AngularJS HTML编译器

##相关API
*  Angular Filter API

<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh