下面是“创建AngularJS服务”一章中“依赖”例子里的“notify”服务的单元测试。测试用使用了Jasmine spy替代了真实浏览器的alert。<!--more-->

	var mock, notify;
	 
	beforeEach(function() {
	  mock = {alert: jasmine.createSpy()};
	 
	  module(function($provide) {
	    $provide.value('$window', mock);
	  });
	 
	  inject(function($injector) {
	    notify = $injector.get('notify');
	  });
	});
	 
	it('should not alert first two notifications', function() {
	  notify('one');
	  notify('two');
	 
	  expect(mock.alert).not.toHaveBeenCalled();
	});
	 
	it('should alert all after third notification', function() {
	  notify('one');
	  notify('two');
	  notify('three');
	 
	  expect(mock.alert).toHaveBeenCalledWith("one\ntwo\nthree");
	});
	 
	it('should clear messages after alert', function() {
	  notify('one');
	  notify('two');
	  notify('third');
	  notify('more');
	  notify('two');
	  notify('third');
	 
	  expect(mock.alert.callCount).toEqual(2);
	  expect(mock.alert.mostRecentCall.args).toEqual(["more\ntwo\nthird"]);

##相关主题
*  理解AngularJS服务
*  创建AngularJS服务
*  管理服务依赖
*  将AngularJS注入到控制器

##相关API
Angular Service API


<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh