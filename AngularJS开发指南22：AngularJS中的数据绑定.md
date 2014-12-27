AngularJS中的数据绑定就是模型与视图间的自动同步。这种实现方式让你能专心地处理你的模型。视图总是模型的投影。当模型改变，视图就会反映这种改变，反之亦然。

###传统的模板系统数据绑定

<img src="http://docs.angularjs.org/img/One_Way_Data_Binding.png"/>

大部分模板系统都是这种绑定方式。
方向：如图所示，它们将模型和模板结合生成视图。这个结合过程产生的视图不是动态更新的。更糟的是，任何用户和视图的交互都不会反映到模型。这意味着开发者要自动写视图和模型双向的同步代码。<!--more-->

###AngularJS模板中的数据绑定

<img src="http://docs.angularjs.org/img/Two_Way_Data_Binding.png"/>

如图，AngularJS模板的工作原理不同。不同之处在于：第一，模板（附加了自定义属性等标记的未经编译的HTML）是由浏览器编译的；第二，编译最后产生的是一个动态的视图。这里动态指的是视图的任何变化都会直接反应到模型，反之亦然。这使得模型总是应用状态的唯一标识，这大大简化了开发人员的编程工作。你可以简单地认为视图只是模型的投影。

因为视图只是模型的投影，控制器完全和视图分离了，并且视图对它来说是透明的。这使得测试变得更简单，因为你不需要关心相关的DOM或者浏览器变化了。

##相关主题
AngularJS作用域
AngularJS模板


<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh