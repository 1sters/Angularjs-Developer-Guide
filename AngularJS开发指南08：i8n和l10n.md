**什么是i18n和l10n?**

国际化，简写为i18，指的是使产品快速适应不同语言和文化。本地化，简称l10n，是指使产品在特定文化和语言市场中可用。对开发者来说，国际化一个应用意味着将所有的文字和其他因地区而异的数据从应用中抽离出来。本地化意味着为这些抽离的数据和文字提供翻译和转变成本地的格式。

**AngularJS目前对本地化和国际化的支持程度**

目前，AngularJS支持日期，数字和货币的国际化和本地化。

另外，AngularJS还通过`ngPluralize`指令支持本地多元化。

所有的AngularJS本地化组件都依赖于$locale服务管理本地化规则(locale-specific rule sets)。<!--more-->

如果你想直接看例子，我们提供了一些来展示使用了丰富本地化规则的过滤器。你可以在Github或者AngularJS开发包的i18n/e2e文件夹下找到这些例子。

**什么是地区 id**

一个地区(`locale`)是指一个按地理上，政治上，文化上划分的区域。常用的地区ID由两部分组成:语言代号和国家代号。比如，en-US, en-AU, zh-CN就是合法的地区ID，它们都含有语言代号和国家代号。 因为国家代号是可选的。所以像en, zh, and sk这样的id也是合法的。查看ICU网站来获取更多的地区ID信息。

对于AngularJS支持的地区，AngularJS将数字，事件格式等规则分隔放在不同的文件里，每个文件对应一个指定地区。你在AngularJS文件夹里可以找到一系列支持的的地区。

##使用新的地区规则

有两种方法能使用新的地区规则：

**1. 预捆绑规则**

你可以通过将地区文件与angular.js或者angular.min.js捆绑来实现对你想要使用的地区文件的预绑定。

比如在*nix系统中，你可以按下面这样创建一个包含德国地区规则的angular.js文件：

	cat angular.js i18n/angular-locale_de-ge.js > angular_de-ge.js

当应用使用angular_de-ge.js脚本而不是angular.js脚本时，AngularJS或自动使用德国本地化规则。

**2. 将地区脚本载入到index.html**

你也可以将指定的地区文件载入到index.html中。比如，当有客户端请求来自德国，你可以返回index_de-ge.html，这个文件看起来像下面这样：

	<html ng-app>
	 <head>
	….
	   <script src="angular.js"></script>
	   <script src="i18n/angular-locale_de-ge.js"></script>
	….
	 </head>
	</html>


**两种方法的对比** 
以上两种都需要你能先准备好适合不同地区的页面。你同时也要配置好服务器来返回正确配置好的文件。

但是，第二种方式（在index.html中加载脚本）要更慢一些，因为需要额外加载脚本。

##其他细节

###货币符号

AngularJs的货币过滤器允许你使用地区服务里的默认货币符号，但是你也可以使用你自己定义了货币符号的过滤器。如果你的应用只在一个地区使用，那么使用默认的钱币符很好。但是如果你的应用在多地区使用， 你应该提供你自己的钱币符来确保钱币值能被正确理解。

比如说，如果你想显示1000美元的账户余额，使用下面这种带有钱币过滤器的表达式{{1000|currency}},并且你的应用目前是在en-US地区，那么'$1000.00'会被显示出来。但是，如果用户在其他地方使用（比如说，中国的钓鱼岛）你的应用，它的浏览器会将地区指定成CN，'￥1000.00'就会被显示出来。这会迷惑你的用户。

在这个例子中，你需要通过自己指定钱币过滤器来替换默认的钱币符。这个过滤器要有一个你自己指定的钱币符，比如USD$1,000.00。这样，AngularJS就总是会显示'USD$1000'并且忽略掉地区的转换。

###译文的长度

要记住，译文的长度可能和原文有很大区别。比如说，`June 3, 1977`在西班牙会被翻译成`3 de junio de 1977`。而且还有更多极端的情况。所以，在对你的应用进行国际化时，你需要好好的写CSS规则并且做好测试。

###时区

注意，AngularJS使用的是浏览器的时区设置。所以一个相同的应用会根据不同的设备显示不同的时间格式。Javascript和AngularJS都不能支持使用统一的开发者制定的时区。

<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh