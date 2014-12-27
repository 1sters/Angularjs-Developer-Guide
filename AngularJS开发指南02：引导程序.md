## 概览

这一节解释了AngularJS初始化的过程，以及需要的时候你该如何修改AngularJS的初始化。

## AngularJS的 `<script>` 标签

这个示例展示了我们推荐的整合AngularJS的方法，它被称之为“自动初始化”。

    <!doctype html>
    <html xmlns:ng="http://angularjs.org" ng-app>
        <body>
            ...
        <script src="angular.js"><script>
        </body>
    </html>

<!--more-->
*   将上面代码中的`script`标签放在页面的底部。将`script`标签放在底部缩短应用加载的时间，因为这样HTML的加载不会被`angular.js`脚本的加载阻塞。你可以从<http://code.angularjs.org>获得最新的版本。请不要在你的代码里面引用这个URL，因为它会暴露你的站点的安全隐患。如果只是实验性质的开发，那链接到我们的站点没有什么问题。 
    *   `angular-[version].js` 是具有可读性的版本, 适合开发和调试。
    *   `angular-[version].min.js` 是压缩和混淆后的版本, 适合部署到成型产品中。
*   请将`ng-app`指令 放到你的应用的标签根节点中, 如果你想要AngularJS自动执行整个`<html>`程序就把它放在 `<html>` 标签中。 

        <html ng-app>

*   如果你想使用旧版的指令语法：`ng:`，那还需要将`xml-namespace`写在`<html>`中
    才能使AngularJS在IE下正常工作。(这样做是因为一些历史原因, 我们不推荐继续使用`ng:`的语法。)

        <html xmlns:ng="http://angularjs.org">

## 自动初始化

AngularJS会在`DOMContentLoaded`事件触发时执行，并通过`ng-app`指令 寻找你的应用根作用域。如果 `ng-app`指令找到了，那么AngularJS将会：

*   载入和 指令 内容相关的模块。
*   创建一个应用的“注入器(injector)”。
*   已拥有`ng-app` 指令 的标签为根节点来编译其中的DOM。这使得你可以只指定DOM中的一部分作为你的AngularJS应用。

        <!doctype html>
        <html ng-app="optionalModuleName">
            <body>
                I can add: {{ 1+2 }}.
                <script src="angular.js"></script>
            </body>
        </html>

## 手动初始化

如果你需要主动控制一下初始化的过程，你可以使用手动执行引导程序的方法。比如当你使用“脚本加载器(script loader)”，或者需要在AngularJS编译页面之前做一些操作，你就会用到它了。

下面的例子演示了手动初始化AngularJS的方法。它的效果等同于使用`ng-app`指令 。

    <!doctype html>
    <html xmlns:ng="http://angularjs.org">
        <body>
            Hello {{'World'}}!
            <script src="http://code.angularjs.org/angular.js"></script>
            <script>
                angular.element(document).ready(function() {
                angular.bootstrap(document);
                });
            </script>
        </body>
    </html>

下面是一些你的代码必须遵守的顺序：

1. 等页面和所有的脚本加载完之后，找到HTML模板的根节点——通常就是文档的根节点。
2. 调用 api/angular.bootstrap将模板编译成可执行的、数据双向绑定的应用程序。

<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh
