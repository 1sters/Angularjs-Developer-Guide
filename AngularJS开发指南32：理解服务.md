AngularJS服务是一种能执行一个常见操作的单例，比如$http服务是用来操作浏览器的XMLHttpRequest对象的。

要使用AngularJS服务，你只需要在需要的地方（控制器，或其他服务）指出依赖就行了。AngularJS的依赖注入系统会帮你完成剩下的事情。它负责实例化，查找左右依赖，并且按照工场函数要求的样子传递依赖。

AngularJS通过“构造器注入”来注入依赖（通过工场函数来传递依赖）。以为Javascript是动态类型语言，AngularJS无法通过静态类型来识别服务，所以你必须使用$inject属性来显式地指定依赖。比如:<!--more-->

    myController.$inject = ['$location'];

AngularJS web框架提供了一组常用操作的服务。和其他的内建变量或者标识符一样，内建服务名称也总是以"$"开头。另外你也可以创建你自己的服务。


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