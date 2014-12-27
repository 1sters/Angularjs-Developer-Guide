在AngularJS中，视图(view)指的是浏览器加载和渲染之后，并且在AngularJS根据模板、控制器、模型信息修改之后的DOM。

<img src="http://docs.angularjs.org/img/guide/about_view_final.png"/>

在AngularJS对MVC的实现中，视图是知道模型和控制器的。视图知道模型的双向绑定。视图通过指令知道的控制器，比如`ngController`和`ngView`指令，也可以通过绑定知道，比如`{{someControllerFunction()}}`。通过这些方式，视图可以调用相应控制器中的方法。


###相关主题
*  AngularJS中的MVC
*  理解Controller组件
*  理解view组件


<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh