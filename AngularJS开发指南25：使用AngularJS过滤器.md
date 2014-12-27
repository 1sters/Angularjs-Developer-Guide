过滤器可用在任何api或者ng.$rootScoe.Scope的执行过程中，不过一般用来格式化绑定在模板中的表达式。

    {{ expression | filter }}

过滤器一般在处理过程中将数据转变成新的格式。它能使用链式风格，还能接受附加参数。

你可以像下面这样使用链式风格：

    {{ expression | filter1 | filter2 }}

你也可以使用“:”来传递额外的参数给过滤器，比如，将数字123格式化成带有2为小数的形式：

    123 | number:2

下面有些例子，展示了使用不同过滤器格式化之前和之后的样子：<!--more-->

*  无过滤器: {{1234.5678}} => 1234.5678
*  数字过滤器: {{1234.5678|number}} => 1,234.57. 注意“，”号和四舍五入后的后两位。
*  带参数的过滤器: {{1234.5678|number:5}} => 1,234.56780. 过滤器可以接受额外的参数，参数写在“:”的后面。比如，`number`过滤器接受数值型参数来制定需要展示几位小数。

##相关主题
*  理解AngularJS过滤器
*  创建AngularJS过滤器

##相关API
*  Angular Filter API

<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh