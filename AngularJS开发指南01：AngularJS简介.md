## 什么是 AngularJS?

AngularJS 是一个为动态WEB应用设计的结构框架。它能让你使用HTML作为模板语言，通过扩展HTML的语法，让你能更清楚、简洁地构建你的应用组件。它的创新点在于，利用 **数据绑定** 和 **依赖注入**，它使你不用再写大量的代码了。这些全都是通过浏览器端的Javascript实现，这也使得它能够完美地和任何服务器端技术结合。

AngularJS是为了克服HTML在构建应用上的不足而设计的。HTML是一门很好的为静态文本展示设计的声明式语言，但要构建WEB应用的话它就显得乏力了。所以我做了一些工作（你也可以觉得是小花招）来让浏览器做我想要的事。<!--more-->

通常，我们是通过以下技术来解决静态网页技术在构建动态应用上的不足：

*   **类库** - 类库是一些函数的集合，它能帮助你写WEB应用。起主导作用的是你的代码，由你来决定何时使用类库。类库有：`jQuery`等
*   **框架** - 框架是一种特殊的、已经实现了的WEB应用，你只需要对它填充具体的业务逻辑。这里框架是起主导作用的，由它来根据具体的应用逻辑来调用你的代码。框架有：`knockout`、`sproutcore`等。

AngularJS使用了不同的方法，它尝试去补足HTML本身在构建应用方面的缺陷。AngularJS通过使用我们称为**标识符**(directives)的结构，让浏览器能够识别新的语法。例如：

*   使用双大括号`{{}}`语法进行数据绑定；
*   使用DOM控制结构来实现迭代或者隐藏DOM片段；
*   支持表单和表单的验证；
*   能将逻辑代码关联到相关的DOM元素上；
*   能将HTML分组成可重用的组件。

### 端对端的解决方案

AngularJS试图成为成为WEB应用中的一种端对端的解决方案。这意味着它不只是你的WEB应用中的一个小部分，而是一个完整的端对端的解决方案。这会让AngularJS在构建一个**CRUD**（增加Create、查询Retrieve、更新Update、删除Delete）的应用时显得很“固执”（原文为 opinionated,意指没有太多的其他方式）。但是，尽管它很“固执”，它仍然能确保它的“固执”只是在你构建应用的起点，并且你仍能灵活变动。AngularJS的一些出众之处如下：

*   构建一个CRUD应用可能用到的全部内容包括：数据绑定、基本模板标识符、表单验证、路由、深度链接、组件重用、依赖注入。
*   测试方面包括：单元测试、端对端测试、模拟和自动化测试框架。
*   具有目录布局和测试脚本的种子应用作为起点。

### AngularJS的可爱之处

AngularJS通过为开发者呈现一个更高层次的抽象来简化应用的开发。如同其他的抽象技术一样，这也会损失一部分灵活性。换句话说，并不是所有的应用都适合用AngularJS来做。AngularJS主要考虑的是构建CRUD应用。幸运的是，至少90%的WEB应用都是CRUD应用。但是要了解什么适合用AngularJS构建，就得了解什么不适合用AngularJS构建。

如游戏，图形界面编辑器，这种DOM操作很频繁也很复杂的应用，和CRUD应用就有很大的不同，它们不适合用AngularJS来构建。像这种情况用一些更轻量、简单的技术如`jQuery`可能会更好。

## 一个简单的AngularJS实例

下面是一个包含了一个表单的典型CRUD应用。表单值先经过验证，然后用来计算总值，这个总值会被格式化成本地的样式。下面有一些开发者常见的概念，你需要先了解一下：

*   将**数据模型**(data-model)关联到**视图**(UI)上；
*   写、读、验证用户的输入；
*   根据模型计算新的值；
*   将输出格式本地化。

**index.html:**

    <!doctype html>
    <html ng-app>
        <head>
            <script src="http://code.angularjs.org/angular-1.1.0.min.js"></script>
            <script src="script.js"></script>
        </head>
        <body>
            <div ng-controller="InvoiceCntl">
                <b>Invoice:</b>
                <br>
                <br>
                <table>
                    <tr><td>Quantity</td><td>Cost</td></tr>
                    <tr>
                        <td><input type="integer" min="0" ng-model="qty" required ></td>
                        <td><input type="number" ng-model="cost" required ></td>
                    </tr>
                </table>
                <hr>
                <b>Total:</b> {{qty * cost | currency}}
            </div>
        </body>
    </html>
    

**script.js:**

    function InvoiceCntl($scope) {
        $scope.qty = 1;
        $scope.cost = 19.95;
    }
    

**end-to-end test:**

    it('should show of angular binding', function() {
        expect(binding('qty * cost')).toEqual('$19.95');
        input('qty').enter('2');
        input('cost').enter('5.00');
        expect(binding('qty * cost')).toEqual('$10.00');
    });
    
<script type="text/javascript">function InvoiceCntl($scope){$scope.qty = 1;$scope.cost = 19.95;}</script>
<fieldset class="angularjs-demo" ng-app>
  <legend>运行效果</legend>
  <div ng-controller="InvoiceCntl">
    <b>Invoice:</b>
    <table>
      <tr>
        <td>
          Quantity
        </td>
        <td>
          Cost
        </td>
      </tr>
      <tr>
        <td>
          <input type="integer" min="0" ng-model="qty" required />
        </td>
        <td>
          <input type="number" ng-model="cost" required />
        </td>
      </tr>
    </table>
    <hr />
    <b>Total:</b> {{qty * cost | currency}}
  </div>
</fieldset>

试一下上面这个例子，然后我们一起来看下这个例子的工作原理。

在`<html>`标签里, 我们用一个`ng-app`标识符标明这是一个AngularJS应用。这个`ng-app`标识符会使AngularJS**自动初始化**(auto initialize)你的应用。

我们用`<script>`标签来加载AngularJS脚本：

    <script src="http://code.angularjs.org/angular-1.1.0.min.js"></script>

通过设置`<input>`标签里的`ng-model`属性, AngularJS会自动对数据进行双向绑定。我们还同时进行了一些简单的数据验证：

    Quantity: <input type="integer" min="0" ng-model="qty" required >
    Cost: <input type="number" ng-model="cost" required >

这个输入框的widget看起来很普通，但如果认识到以下几点那它就不普通了：

*   当页面加载完后，AngularJS会依照widget里的声明的模型名字（`qty`、`cost`）生成同名变量。你可以把这些变量认为是MVC设计模式中的M(Model)；
*   注意上面widget里的`input`有着特殊的能力。如果你们没有输入数据或者输入的数据无效，这个`input`输入框会自动变红。输入框的这种新特性，能让开发者更容易实现CRUD应用里常见的字段验证功能。

终于，我们可以来看一下神秘的双大括号`{{}}`了:

    Total: {{qty * cost | currency}}

这个`{{表达式}}`标记是AngularJS的数据绑定。其中的表达式可以是表达式和过滤器(`{{ expression | filter }}`)的组合。AngularJS提供了过滤器来对输入输出数据格式化。

上面的这个例子里，`{{}}`里的表达式让AngularJS把从输入框中获得的数据相乘，然后把相乘结果格式化成本地货币样式，然后输出到页面上。

值得一提的是，我们既没有调用任何AngularJS的方法，也没有像用框架一样去编写某个具体逻辑，就是完成了上述功能。这个实现的背后是因为浏览器做了比以往生成 静态页面更多的工作，让它能满足动态WEB应用的需要。AngularJS使得动态WEB应用的开发门槛降到不需要类库或者框架的程度。

## AngularJS的“禅道(理念)”

Angular信奉的是，当组建视图(UI)同时又要写软件逻辑时，声明式的代码会比命令式的代码好得多，尽管命令式的代码非常适合用来表述业务逻辑。

*   将DOM操作和应用逻辑解耦是一种非常好的思路，它能大大改善代码的可调性；
*   将 **测试** 和 **开发** 同等看待是一种非常非常好的思路，测试的难度在很大程度上取决于代码的结构；
*   将客户端和服务器端解耦是一种特别好的做法，它能使两边并行开发，并且使两边代码都能实现重用；
*   如果框架能够在整个开发流程里都引导着开发者：从设计UI，到编写业务逻辑，再到测试，那对开发者将是极大的帮助；
*   “化繁为简，化简为零”总是好的。

AngularJS能将你从以下的噩梦中解脱出来：

*   **使用回调：** 回调的使用会打乱你的代码的可读性，让你的代码变得支离破碎，很难看清本来的业务逻辑。移除一些常见的代码，例如回调，是件好事。大幅度地减少你因为JavaScript这门语言的设计而不得不写的代码，能让你把自己应用的逻辑看得更清楚。
*   **手动编写操作DOM元素的代码：**操作DOM是AJAX应用很基础的一部分，但它也总是很“笨重”并且容易出错。用声明的方式描述的UI界面可随着应用状态的改变而变化，能让你从编写低级的DOM操作代码中解脱出来。绝大部分用AngularJS写的应用里，开发者都不用再自己去写操作DOM的代码，不过如果你想的话还是可以去写。
*   **对UI界面读写数据：** AJAX应用的很大一部是CRUD操作。一个经典的流程是把服务端的数据组建成内部对象，再把对象编成HTML表单，用户修改表单后再验证表单，如果有错再显示错误，然后将数据重新组建成内部对象，再返回给服务器。这个流程里有太多太多要重复写的代码，使得代码看起来总是在描述应用的全部执行流程，而不是具体的业务逻辑和业务细节。
*   **开始前得写大量的基础性的代码：** 通常你需要写很多的基础性的代码才能实现一个“Hello World”的应用。用AngularJS的话，它会提供一些服务让你很容易地正式开始写你的应用，而这些服务都是以一种[Guice-like dependency-injection][1]式的依赖注入自动加入到你的应用中去的,这让你能很快的进入你应用的具体开发。特别的是，你还能全盘掌握自动化测试的初始化过程。

<span class="doc-copyright">**版权声明：** **中文文档[AngularJS中文社区][]** && **英文文档[AngularJS官网][]** && **代码许可[The MIT License][]** && **文档许可[CC BY 3.0][]**</span>

 [1]: http://code.google.com/p/google-guice/
 [AngularJS中文社区]: http://angularjs.cn/
 [AngularJS官网]: http://angularjs.org/
 [The MIT License]: http://baike.baidu.com/view/3159946.htm
 [CC BY 3.0]: http://creativecommons.org/licenses/by/3.0/deed.zh