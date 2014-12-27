随着软件的规模和复杂度增长。依靠人工来进行测试越来越不现实。

为了解决这个问题，我们建立了**Angular Scenario Runner**来模拟用户交互，以此来帮助你对你的应用进行测试。

##概览
方案测试(scenario tests)使用Javascript写的，你在其中描述在某一个状态的某一个交互中你的应用应该运作。一个方案包括一个或多个`it`代码块（你可以把这些当成你应用的需求），代码块它由命令和期望结果组成。命令告诉*Runner*让应用执行某个操作（比如翻页或者点击按钮），期望告诉*Runner*验证执行后的应用状态（比如字段的值或者当前URL）。如果某个期望不符合，那么**Runner**就会把它标记为“失败”然后继续验证下一个。方案还能有`beforeEach`和`afterEach`代码块，他们会在`it`代码块前（后）执行，不管代码块是否成功。<!--more-->

<img src="http://docs.angularjs.org/img/guide/scenario_runner.png"/>

除了上面所列的，方案里还能使用一些辅助函数来避免代码块中的重复代码。

下面是一个简单的方案：

	describe('Buzz Client', function() {
	it('should filter results', function() {
	  input('user').enter('jacksparrow');
	  element(':button').click();
	  expect(repeater('ul li').count()).toEqual(10);
	  input('filterText').enter('Bees');
	  expect(repeater('ul li').count()).toEqual(1);
	});
	});

这个方案描述了**Buzz Client**应该能过滤用户的输入。它一开始模拟用户输入，然后点击了按钮，然后它验证应该出现10个列表项。再然后它模拟在`filterText`输入框内输入`Bees`，并验证列表项减少到1个。
下面的API是命令和期望中可以用到的：

##API
参阅 <https://github.com/angular/angular.js/blob/master/src/ngScenario/dsl.js>

###pause()
暂停测试执行，知道你在命令行中执行`resume()`（或者在Runner UI中点击继续按钮）。

###sleep(seconds)
将测试暂停一段指定的事件，以秒计。

###browser().navigateTo(url)
将url加载到测试框架中。

###browser().navigateTo(url, fn)
将函数返回的url加载到测试框架中。参数中的url是用来测试输出的。在url是动态地时候，你可以用这个api（意思是在你写测试的时候并不知道url是什么的时候）。

###browser().reload()
在测试框架中刷新加载的页面。

###browser().window().href()
返回测试框架页面中的window.location.href。

###browser().window().path()
返回测试框架页面中的window.location.pathname。

###browser().window().search()
返回测试框架页面中的window.location.search.

###browser().window().hash()
返回测试框架页面中的window.location.hash.


###browser().location().url()
返回测试框架页面中的$location.url().

###browser().location().path()
返回测试框架页面中的$location.path().

###browser().location().search()
返回测试框架页面中的$location.search().

###browser().location().hash()
返回测试框架页面中的$location.hash().

###expect(future).{matcher}
验证当前的future对象是否满足当前的匹配.所有的api都会返回一个future对象，它没在执行后就会对这个对象赋值。**匹配**是用angular.scenario.matcher定义的， 它用future对象的值来验证是否复合期望，比如：`expect(browser().location().href()).toEqual('http://www.google.com')`。

###expect(future).not().{matcher}
验证当前future对象值是否不满足当前匹配。

###using(selector, label)
审查下一个DSL元素选项。

###binding(name)
返回符合给定名字的第一个绑定值。

###input(name).enter(value)
在指定输入框中输入指定值。

###input(name).check()
勾选或者反勾选指定名字的checkbox。

###input(name).select(value)
选择指定名字的单选框。

###input(name).val()
返回指定名字的input元素的值。

###repeater(selector, label).count()
返回用指定jquery选择器选择到的元素的个数。参数中label是用来检测输出的。

###repeater(selector, label).row(index)
返回用指定jquery选择器选择到的元素的指定索引的绑定（数组）。参数中label是用来检测输出的。

###repeater(selector, label).column(binding)
返回用指定jquery选择器选择到的元素的列的值（数组）。参数中label是用来检测输出的。

###select(name).option(value)
返回指定名字的select中指定值的option。

###select(name).option(value1, value2...)
返回指定名字的select中的和给定任意一个值匹配的option。

###element(selector, label).count()
返回符合指定jquery选择器的元素的个数。参数中label是用来检测输出的。

###element(selector, label).click()
模拟指定jquery选择器的元素的点击事件。参数中label是用来检测输出的。

###element(selector, label).query(fn)
执行函数fn(selectedElements, done)，selectedElements是符合jquery选择器的元素，done是函数执行后的回调。参数中label是用来检测输出的。

###element(selector, label).{method}()
返回指定jquery选择器选择的元素的方法。方法可以是如下的jquery方法：val, text, html, height, innerHeight, outerHeight, width, innerWidth, outerWidth, position, scrollLeft, scrollTop, offset.参数中label是用来检测输出的。

###element(selector, label).{method}(value)
执行指定jquery选择器选择的元素的方法。方法可以是如下的jquery方法：val, text, html, height, innerHeight, outerHeight, width, innerWidth, outerWidth, position, scrollLeft, scrollTop, offset.参数中label是用来检测输出的。

###element(selector, label).{method}(key)
返回执行指定jquery选择器选择的元素执行方法的结果，传递key做参数。方法可以是：attr, prop, css。参数中label是用来检测输出的。

###element(selector, label).{method}(key, value)
返回执行指定jquery选择器选择的元素执行方法的结果，传递key和value做参数。方法可以是：attr, prop, css。参数中label是用来检测输出的。

Javascript是动态类型的语言，它在表达式上很强大，但也使得编译器对它没有任何帮助。因此，我们十分强烈地觉得它需要强大的测试工具。我们已经建立这个强大的工具，所以，没有利用不用它了。

<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh