AngularJS是纯客户端技术，完全用Javascript编写的。它使用的是网页开发的常规技术(HTML,CSS,Javascript)，目的是让网页应用开发更快更容易。

AngularJS简化应用开发的一个重要方法是，将一个些通用的低级开发操作包装起来提供给开发者。AngularJS会自动处理好这些低级操作。它们包括：

*  DOM操作
*  设置好事件的监听和通知
*  输入验证
因为AngularJS会处理大部分这些操作，所以开发者就能更多的专注在应用逻辑上，更少地编写那些重复性的、易错的、低级的代码。

在AngularJS简化开发的同时，它也为客户端带来了一些精巧的技术，它们包括：<!--more-->

*  数据、逻辑、视图的分离
*  数据和视图间的自动绑定
*  通用服务(用可替换的对象实现的通用的应用操作)
*  依赖注入（主要用于聚合服务）
*  可扩展的HTML编译器(完全由JavaScript编写)
*  易于测试

客户端对这些技术的需求其实已经存在很久了。

##单页或者多页应用

你可以用AngularJS来开发单页或者多页的应用，不过主要是用来开发单页的。 AngularJS支持浏览器历史操作，向前向后按钮，单页应用中的收藏操作。

你不会想在每一次页面切换时都载入AngularJS。比如多页应用就常需要切换页面。 但是，如果你只是给已有的多页应用上加上一些AngularJS提供的功能（比如使用AngularJS模板的数据绑定功能）的话，那就有意义了。你可以按照后面的教程来将已有的应用整合成单页应用。

<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh