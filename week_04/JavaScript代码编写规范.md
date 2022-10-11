# JavaScript代码编写规范

&emsp;&emsp;项目名称：校园闲置物品租赁平台

&emsp;&emsp;文件状态：文档在编写过程中

&emsp;&emsp;文档当前版本：V0.1.14

&emsp;&emsp;作者：特别爱学习小组全体成员

&emsp;&emsp;完成日期：2022年9月16日

&emsp;&emsp;使用人员：校园内对闲置物品有租出需求的学生和老师

&emsp;&emsp;背景：希望创立一个物品租赁平台，让闲置的物品重新焕发活力

## 语言规范

### var关键字

本项目总是用 ``var`` 关键字定义变量。

#### 描述
本项目如果不显式使用 ``var`` 关键字定义变量，变量会进入到全局上下文中，可能会和已有的变量发生冲突。另外，如果不使用var声明，很难说变量存在的作用域是哪个（可能在局部作用域里，也可能在document或者window上）。所以，要一直使用 ``var`` 关键字定义变量。

### 常量
* 本项目使用字母全部大写（如 ``NAMES_LIKE_THIS`` ）的方式命名


### 描述


#### 常量值


在本项目中，如果一个值是恒定的，它命名中的字母要全部大写（如 ``CONSTANT_VALUE_CASE`` ）。字母全部大写意味着这个值不可以被改写。

原始类型（ ``number`` 、 ``string`` 、 ``boolean`` ）是常量值。

对象的表现会更主观一些，当它们没有暴露出变化的时候，应该认为它们是常量。但是这个不是由编译器决定的。

##### 常量指针（变量和属性）

用 ``@const`` 注释的变量和属性意味着它是不能更改的。使用const关键字可以保证在编译的时候保持一致。使用 `const <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const?redirectlocale=en-US&redirectslug=JavaScript%2FReference%2FStatements%2Fconst>`_ 效果相同，但是由于IE的兼容问题，我们不使用const关键字。

另外，不应该修改用 ``@const`` 注释的方法。

##### 例子

注意， ``@const`` 不一定是常量值，但命名类似 ``CONSTANT_VALUE_CASE`` 的 *一定* 是常量指针。


    /**
    * 以毫秒为单位的超时时长
    * @type {number}
    */
    goog.example.TIMEOUT_IN_MILLISECONDS = 60;

1分钟60秒永远也不会改变，这是个常量。全部大写的命名意味其为常量值，所以它不能被重写。
开源的编译器允许这个符号被重写，这是因为 *没有* ``@const`` 标记。


    /**
    * Map of URL to response string.
    * @const
    */
    MyClass.fetchedUrlCache_ = new goog.structs.Map();

在这个例子中，指针没有变过，但是值却是可以变化的，所以这里用了驼峰式的命名，而不是全部大写的命名。
### 分号

本项目一定要使用分号。

依靠语句间隐式的分割，可能会造成细微的调试的问题，在本项目中，不会出现这种做法。

很多时候不写分号是很危险的：


    // 1.
    MyClass.prototype.myMethod = function() {
        return 42;
    }  // 这里没有分号.

    (function() {
        // 一些局部作用域中的初始化代码
    })();

    var x = {
        'i': 1,
        'j': 2
    }  //没有分号.

    // 2.  试着在IE和firefox下做一样的事情.
    //没人会这样写代码，别管他.
    [normalVersion, ffVersion][isIE]();

    var THINGS_TO_EAT = [apples, oysters, sprayOnCheese]  //这里没有分号

    // 3. 条件语句
    -1 == resultOfOperation() || die();

#### 发生了什么？


1. js错误。返回42的函数运行了，因为后面有一对括号，而且传入的参数是一个方法，然后返回的42被调用，导致出错了。

2. 你可能会得到一个“no sush property in undefined”的错误，因为在执行的时候，解释器将会尝试执行 ``x[normalVersion, ffVersion][isIE]()`` 这个方法。

3.  ``die`` 这个方法只有在 ``resultOfOperation()`` 是 ``NaN`` 的时候执行，并且 ``THINGS_TO_EAT`` 将会被赋值为 ``die()`` 的结果。

#### 为什么？


在本项目中，js语句要求以分号结尾，除非能够正确地推断分号的位置。在这个例子当中，函数声明、对象和数组字面量被写在了一个语句当中。右括号（")"、"}"、"]"）不足以证明这条语句已经结束了，如果下一个字符是运算符或者"("、"{"、"["，js将不会结束语句。

这个错误让人震惊，所以一定要确保用分号结束语句。

#### 澄清：分号和函数


函数表达式后面要分号结束，但是函数声明就不需要。例如：


    var foo = function() {
        return true;
    };  // 这里要分号

    function foo() {
        return true;
    }  // 这里不用分号
### with(){}
不建议使用。

使用 ``with`` 会影响程序的语义。因为 ``with`` 的目标对象可能会含有和局部变量冲突的属性，使你程序的语义发生很大的变化。例如，这是做什么用？


    with (foo) {
        var x = 3;
        return x;
    }

#### 答案
什么都有可能。局部变量 ``x`` 可能会被 ``foo`` 的一个属性覆盖，它甚至可能有setter方法，在此情况下将其赋值为3可能会执行很多其他代码。不要使用 ``with`` 。
### this
只在构造函数对象、方法，和创建闭包的时候使用。

``this`` 的语义可能会非常诡异。有时它指向全局对象（很多时候）、调用者的作用域链（在 ``eval`` 里）、DOM树的一个节点（当使用HTML属性来做为事件句柄时）、新创建的对象（在一个构造函数中）、或者其他的对象（如果函数被 ``call()`` 或 ``apply()`` 方式调用）。

正因为 ``this`` 很容易被弄错，故将其使用限制在以下必须的地方：

* 在构造函数中

* 在对象的方法中（包括闭包的创建）
### 数组和对象字面量
在本项目中有所使用。

使用数组和对象字面量来代替数组和对象构造函数。

数组构造函数容易在参数上出错。


    // 长度为3
    var a1 = new Array(x1, x2, x3);

    // 长度为 2
    var a2 = new Array(x1, x2);

    // If x1 is a number and it is a natural number the length will be x1.
    // If x1 is a number but not a natural number this will throw an exception.
    // Otherwise the array will have one element with x1 as its value.
    var a3 = new Array(x1);

    // 长度为0
    var a4 = new Array();

由此，如果有人将代码从2个参数变成了一个参数，那么这个数组就会有一个错误的长度。

为了避免这种怪异的情况，永远使用可读性更好的数组字面量。


    var a = [x1, x2, x3];
    var a2 = [x1, x2];
    var a3 = [x1];
    var a4 = [];

对象构造函数虽然没有相同的问题，但是对于可读性和一致性，还是应该使用对象字面量。


    var o = new Object();

    var o2 = new Object();
    o2.a = 0;
    o2.b = 1;
    o2.c = 2;
    o2['strange key'] = 3;

应该写成：


    var o = {};

    var o2 = {
        a: 0,
        b: 1,
        c: 2,
        'strange key': 3
    };
### 修改内置对象原型

在本项目中未使用。

强烈禁止修改如 ``Object.prototype`` 和 ``Array.prototype`` 等对象的原型。修改其他内置原型如 ``Function.prototype`` 危险性较小，但在生产环境中还是会引发一些难以调试的问题，也应当避免。

## 风格规范

### 括号

在本项目中只用在有需要的地方。

通常只在语法或者语义需要的地方有节制地使用。

绝对不要对一元运算符如 ``delete`` 、 ``typeof`` 和 ``void`` 使用括号或者在关键词如 ``return`` 、 ``throw`` 和其他的（ ``case`` 、 ``in`` 或者 ``new`` ）之后使用括号。

### 字符串

使用 ``'`` 代替 ``"`` 。

使用单引号（ ``'`` ）代替双引号（ ``"`` ）来保证一致性。当我们创建包含有HTML的字符串时这样做很有帮助。


    var msg = 'This is some HTML';


### 注释

在本项目中，使用JSDoc。

我们使用 `c++的注释风格 <http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml#Comments>`_ 。
所有的文件、类、方法和属性都应该用合适的 `JSDoc <https://code.google.com/p/jsdoc-toolkit/>`_ 的 `标签 <http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#JSDoc_Tag_Reference>`_ 和 `类型 <http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#JsTypes>`_ 注释。除了直观的方法名称和参数名称外，方法的描述、方法的参数以及方法的返回值也要包含进去。

行内注释应该使用 ``//`` 的形式。

为了避免出现语句片段，要使用正确的大写单词开头，并使用正确的标点符号作为结束。

#### 注释语法


JSDoc的语法基于 `JavaDoc <http://www.oracle.com/technetwork/java/javase/documentation/index-137868.html>`_ ，许多编译工具从JSDoc注释中获取信息从而进行代码验证和优化，所以这些注释必须符合语法规则。


    /**
    * A JSDoc comment should begin with a slash and 2 asterisks.
    * Inline tags should be enclosed in braces like {@code this}.
    * @desc Block tags should always start on their own line.
    */

#### JSDoc 缩进


如果你不得不进行换行，那么你应该像在代码里那样，使用四个空格进行缩进。


    /**
    * Illustrates line wrapping for long param/return descriptions.
    * @param {string} foo This is a param with a description too long to fit in
    *     one line.
    * @return {number} This returns something that has a description too long to
    *     fit in one line.
    */
    project.MyClass.prototype.method = function(foo) {
      return 5;
    };

不必在 ``@fileoverview`` 标记中使用缩进。

虽然不建议，但依然可以对描述文字进行排版。


    /**
    * This is NOT the preferred indentation method.
    * @param {string} foo This is a param with a description too long to fit in
    *                     one line.
    * @return {number} This returns something that has a description too long to
    *                  fit in one line.
    */
    project.MyClass.prototype.method = function(foo) {
      return 5;
    };


#### 顶层/文件层注释


`版权声明 <http://google-styleguide.googlecode.com/svn/trunk/copyright.html>`_ 和作者信息是可选的。顶层注释的目的是为了让不熟悉代码的读者了解文件中有什么。它需要描述文件内容，依赖关系以及兼容性的信息。例如：


    /**
    * @fileoverview Description of file, its uses and information
    * about its dependencies.
    */

#### Class评论


类必须记录说明与描述和 `一个类型的标签 <http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#constructor-tag>`_ ，标识的构造函数。类必须加以描述，若是构造函数则需标注出。


    /**
    * Class making something fun and easy.
    * @param {string} arg1 An argument that makes this more interesting.
    * @param {Array.<number>} arg2 List of numbers to be processed.
    * @constructor
    * @extends {goog.Disposable}
    */
    project.MyClass = function(arg1, arg2) {
      // ...
    };
    goog.inherits(project.MyClass, goog.Disposable);

#### 方法和功能注释

参数和返回类型应该被记录下来。如果方法描述从参数或返回类型的描述中明确可知则可以省略。方法描述应该由一个第三人称表达的句子开始。


    /**
    * Operates on an instance of MyClass and returns something.
    * @param {project.MyClass} obj Instance of MyClass which leads to a long
    *    comment that needs to be wrapped to two lines.
    * @return {boolean} Whether something occured.
    */
    function PR_someMethod(obj) {
      // ...
    }

#### 属性评论

    /** @constructor */
    project.MyClass = function() {
    /**
      * Maximum number of things per pane.
      * @type {number}
      */
      this.someProperty = 4;
    }

