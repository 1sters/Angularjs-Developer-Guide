AngularJS过滤器用来格式化输出给用户的数据。除了格式化数据，过滤器还能修改DOM。这使得过滤器通常用来做些如“适时地给输出加入CSS样式”等工作。

比如，你可能有些数据在输出之前需要根据进行本地化。你可以向下面这样使用链式的过滤器来传递表达式：

    name | uppercase

这个表达式执行时会将name的值传递给uppercase过滤器。<!--more-->

##相关主题
*  使用AngularJS过滤器
*  创建AngularJS过滤器

##相关API
*  Angular Filter API

<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh