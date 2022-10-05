# JavaScript代码编写规范

## 语言规范

###命名

通常来说，使用 ``functionNamesLikeThis`` ， ``variableNamesLikeThis`` ， ``ClassNamesLikeThis`` ， ``EnumNamesLikeThis`` ， ``methodNamesLikeThis`` ， ``CONSTANT_VALUES_LIKE_THIS`` ， ``foo.namespaceNamesLikeThis.bar`` 和 ``filenameslikethis.js`` 这种格式的命名方式。

属性和方法
~~~~~~~~~~~~~~

* *私有* 属性和方法应该以下划线开头命名。

* *保护* 属性和方法应该以无下划线开头命名（像公共属性和方法一样）。

了解更多关于私有成员和保护成员的信息，请阅读 `可见性 <http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#Visibility__private_and_protected_fields_>`_ 部分。

方法和函数参数
~~~~~~~~~~~~~~~~~

可选函数参数以 ``opt_`` 开头。

参数数目可变的函数应该具有以 ``var_args`` 命名的最后一个参数。你可能不会在代码里引用 ``var_args`` ；使用 ``arguments`` 对象。

可选参数和数目可变的参数也可以在注释 ``@param`` 中指定。尽管这两种惯例都被编译器接受，但更加推荐两者一起使用。

getter和setter


EcmaScript 5 不鼓励使用属性的getter和setter。然而，如果使用它们，那么getter就不要改变属性的可见状态。



    /**
    *错误--不要这样做.
    */
    var foo = { get next() { return this.nextId++; } };
    };

存取函数
~~~~~~~~

属性的getter和setter方法不是必需的。然而，如果使用它们，那么读取方法必须以 ``getFoo()`` 命名，并且写入方法必须以 ``setFoo(value)`` 命名。（对于布尔型的读取方法，也可以使用 ``isFoo()`` ，并且这样往往听起来更自然。）


~~~~~~~~

### var关键字

总是用 ``var`` 关键字定义变量。

####描述

如果不显式使用 ``var`` 关键字定义变量，变量会进入到全局上下文中，可能会和已有的变量发生冲突。另外，如果不使用var声明，很难说变量存在的作用域是哪个（可能在局部作用域里，也可能在document或者window上）。所以，要一直使用 ``var`` 关键字定义变量。

### 常量
* 使用字母全部大写（如 ``NAMES_LIKE_THIS`` ）的方式命名

* 可以使用 ``@const`` 来标记一个常量 *指针* （指向变量或属性，自身不可变）

* 由于IE的兼容问题，不要使用 `const关键字 <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const?redirectlocale=en-US&redirectslug=JavaScript%2FReference%2FStatements%2Fconst>`_

####描述


#####常量值


如果一个值是恒定的，它命名中的字母要全部大写（如 ``CONSTANT_VALUE_CASE`` ）。字母全部大写意味着这个值不可以被改写。

原始类型（ ``number`` 、 ``string`` 、 ``boolean`` ）是常量值。

对象的表现会更主观一些，当它们没有暴露出变化的时候，应该认为它们是常量。但是这个不是由编译器决定的。

#####常量指针（变量和属性）

用 ``@const`` 注释的变量和属性意味着它是不能更改的。使用const关键字可以保证在编译的时候保持一致。使用 `const <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const?redirectlocale=en-US&redirectslug=JavaScript%2FReference%2FStatements%2Fconst>`_ 效果相同，但是由于IE的兼容问题，我们不使用const关键字。

另外，不应该修改用 ``@const`` 注释的方法。

#####例子

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

一定要使用分号。

依靠语句间隐式的分割，可能会造成细微的调试的问题，千万不要这样做。

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

####发生了什么？


1. js错误。返回42的函数运行了，因为后面有一对括号，而且传入的参数是一个方法，然后返回的42被调用，导致出错了。

2. 你可能会得到一个“no sush property in undefined”的错误，因为在执行的时候，解释器将会尝试执行 ``x[normalVersion, ffVersion][isIE]()`` 这个方法。

3.  ``die`` 这个方法只有在 ``resultOfOperation()`` 是 ``NaN`` 的时候执行，并且 ``THINGS_TO_EAT`` 将会被赋值为 ``die()`` 的结果。

####为什么？


js语句要求以分号结尾，除非能够正确地推断分号的位置。在这个例子当中，函数声明、对象和数组字面量被写在了一个语句当中。右括号（")"、"}"、"]"）不足以证明这条语句已经结束了，如果下一个字符是运算符或者"("、"{"、"["，js将不会结束语句。

这个错误让人震惊，所以一定要确保用分号结束语句。

####澄清：分号和函数


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

####答案
什么都有可能。局部变量 ``x`` 可能会被 ``foo`` 的一个属性覆盖，它甚至可能有setter方法，在此情况下将其赋值为3可能会执行很多其他代码。不要使用 ``with`` 。
### this
只在构造函数对象、方法，和创建闭包的时候使用。

``this`` 的语义可能会非常诡异。有时它指向全局对象（很多时候）、调用者的作用域链（在 ``eval`` 里）、DOM树的一个节点（当使用HTML属性来做为事件句柄时）、新创建的对象（在一个构造函数中）、或者其他的对象（如果函数被 ``call()`` 或 ``apply()`` 方式调用）。

正因为 ``this`` 很容易被弄错，故将其使用限制在以下必须的地方：

* 在构造函数中

* 在对象的方法中（包括闭包的创建）
### 数组和对象字面量
建议使用。

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

不建议。

强烈禁止修改如 ``Object.prototype`` 和 ``Array.prototype`` 等对象的原型。修改其他内置原型如 ``Function.prototype`` 危险性较小，但在生产环境中还是会引发一些难以调试的问题，也应当避免。

## 风格规范

### 括号

只用在有需要的地方。

通常只在语法或者语义需要的地方有节制地使用。

绝对不要对一元运算符如 ``delete`` 、 ``typeof`` 和 ``void`` 使用括号或者在关键词如 ``return`` 、 ``throw`` 和其他的（ ``case`` 、 ``in`` 或者 ``new`` ）之后使用括号。

### 字符串

使用 ``'`` 代替 ``"`` 。

使用单引号（ ``'`` ）代替双引号（ ``"`` ）来保证一致性。当我们创建包含有HTML的字符串时这样做很有帮助。


    var msg = 'This is some HTML';


### 注释

使用JSDoc。

我们使用 `c++的注释风格 <http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml#Comments>`_ 。
所有的文件、类、方法和属性都应该用合适的 `JSDoc <https://code.google.com/p/jsdoc-toolkit/>`_ 的 `标签 <http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#JSDoc_Tag_Reference>`_ 和 `类型 <http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#JsTypes>`_ 注释。除了直观的方法名称和参数名称外，方法的描述、方法的参数以及方法的返回值也要包含进去。

行内注释应该使用 ``//`` 的形式。

为了避免出现语句片段，要使用正确的大写单词开头，并使用正确的标点符号作为结束。

####注释语法


JSDoc的语法基于 `JavaDoc <http://www.oracle.com/technetwork/java/javase/documentation/index-137868.html>`_ ，许多编译工具从JSDoc注释中获取信息从而进行代码验证和优化，所以这些注释必须符合语法规则。


    /**
    * A JSDoc comment should begin with a slash and 2 asterisks.
    * Inline tags should be enclosed in braces like {@code this}.
    * @desc Block tags should always start on their own line.
    */

####JSDoc 缩进


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

####JSDoc中的HTML


像JavaDoc一样, JSDoc 支持很多的HTML标签，像 ``<code>`` ， ``<pre>`` ， ``<tt>`` ， ``<strong>`` ， ``<ul>`` ， ``<ol>`` ， ``<li>`` ， ``<a>`` 等。

这就意味着不建议采用纯文本的格式。所以，不要在JSDoc里使用空白符进行格式化。


    /**
    * Computes weight based on three factors:
    *  items sent
    *  items received
    *  last timestamp
    */

上面的注释会变成这样：


    Computes weight based on three factors: items sent items received items received last timestamp

所以，用下面的方式代替：


    /**
    * Computes weight based on three factors:
    * <ul>
    * <li>items sent
    * <li>items received
    * <li>last timestamp
    * </ul>
    */

`JavaDoc <http://www.oracle.com/technetwork/java/javase/documentation/index-137868.html>`_ 风格指南对于如何编写良好的doc注释是非常有帮助的。

####顶层/文件层注释


`版权声明 <http://google-styleguide.googlecode.com/svn/trunk/copyright.html>`_ 和作者信息是可选的。顶层注释的目的是为了让不熟悉代码的读者了解文件中有什么。它需要描述文件内容，依赖关系以及兼容性的信息。例如：


    /**
    * @fileoverview Description of file, its uses and information
    * about its dependencies.
    */

####Class评论


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

####方法和功能注释

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

####属性评论

    /** @constructor */
    project.MyClass = function() {
    /**
      * Maximum number of things per pane.
      * @type {number}
      */
      this.someProperty = 4;
    }

####JSDoc标签参考

<table>
  
  <tr>
    <th>标签</th>
    <th>模板及实例</th>
    <th>描述</th>
  <tr>
  <tr>
    <td>@author</td>
    <td>@author username@google.com (first last)
    例如：

        /**
        * @fileoverview Utilities for handling textareas.
        * @author kuth@google.com (Uthur Pendragon)
        */</td>

<td>说明文件的作者是谁，一般只会在 ``@fileoverview`` 里用到。</td>
  </tr>
  <tr>
    <td>@code</td>
    <td>{@code ...}

      例如：

      ::

      /**
       * Moves to the next position in the selection.
       * Throws {@code goog.iter.StopIteration} when it
       * passes the end of the range.
       * @return {Node} The node at the next position.
       */
       goog.dom.RangeIterator.prototype.next = function() {
         // ...
       };
</td>
    <td>表示这是一段代码，他能在文档中正确的格式化。</td>
  </tr>
  <tr>
    <td>@const</td>
    <td>@const
      @const {type}

      例如：

      ::

        /** @const \*/ var MY_BEER = 'stout';
        /**
        * My namespace's favorite kind of beer.
        * @const {string}
        */
        mynamespace.MY_BEER = 'stout';

        /** @const \*/ MyClass.MY_BEER = 'stout';

        /**
        * Initializes the request.
        * @const
        */
        mynamespace.Request.prototype.initialize = function() {
          // This method cannot be overriden in a subclass.
        }
</td>
    <td>说明变量或者属性是只读的，适合内联。

      标记为 ``@const`` 的变量是不可变的。如果变量或属性试图覆盖他的值，那么js编译器会给出警告。

      如果某一个值可以清楚地分辨出是不是常量，可以省略类型声明。变量附加的注释是可选的。

      当一个方法被标记为 ``@const`` ，意味着这个方法不仅不可以被覆盖，而且也不能在子类中重写。

      ``@const`` 的更多信息，请看 `常量 <http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#Constants>`_ 部分
</td>
  </tr>
 <tr>
    <td>@constructor</td>
    <td>@constructor

      例如：

      ::

        /**
        * A rectangle.
        * @constructor
        */
        function GM_Rect() {
          ...
        }
</td>
    <td>在一个类的文档中表示构造函数。
</td>
  </tr>
<tr>
    <td> @define</td>
    <td>@define {Type} description

      例如：

      ::

        /** @define {boolean} */
        var TR_FLAGS_ENABLE_DEBUG = true;

        /** @define {boolean} */
        goog.userAgent.ASSUME_IE = false;
</td>
    <td>指明一个在编译时可以被覆盖的常量。

      在这个例子中，编译器标志 ``--define='goog.userAgent.ASSUME_IE=true'`` 表明在构建文件的时侯变量 ``goog.userAgent.ASSUME_IE`` 可以被赋值为 ``true`` 。
</td>
  </tr>
<tr>
    <td>@deprecated</td>
    <td> @deprecated Description

      例如：

      ::

        /**
        * Determines whether a node is a field.
        * @return {boolean} True if the contents of
        *    the element are editable, but the element
        *    itself is not.
        * @deprecated Use isField().
        */
        BN_EditUtil.isTopEditableField = function(node) {
          // ...
        };
</td>
    <td>说明函数、方法或者属性已经不可用，常说明替代方法或者属性。
</td>
  </tr>
<tr>
    <td>@dict</td>
    <td>@dict Description

      例如：

      ::

        /**
        * @constructor
        * @dict
        */
        function Foo(x) {
          this['x'] = x;
        }
        var obj = new Foo(123);
        var num = obj.x;  // warning
        (/** @dict \*/ { x: 1 }).x = 123;  // warning
</td>
    <td>当构造函数 (例子里的Foo)被标记为 ``@dict`` ，你只能使用括号表示法访问 ``Foo`` 的属性。这个注释也可以直接使用对象表达式。
</td>
  </tr>
<tr>
    <td>@enum</td>
    <td>@enum {Type}

      例如：

      ::

        /**
        * Enum for tri-state values.
        * @enum {number}
        */
        project.TriState = {
          TRUE: 1,
          FALSE: -1,
          MAYBE: 0
        };
</td>
    <td>
</td>
  </tr>
<tr>
    <td>@export</td>
    <td>@export

      例如：

      ::

        /** @export */
        foo.MyPublicClass.prototype.myPublicMethod = function() {
          // ...
        };
</td>
    <td>对于例子中的代码，当编译到 ``--generate_exports`` 标记时，将会产生以下代码：

      ::

        goog.exportSymbol('foo.MyPublicClass.prototype.myPublicMethod',
            foo.MyPublicClass.prototype.myPublicMethod);

      也就是输出了没有编译的代码。使用@export标签时，应该：

      1. 包含 ``//javascript/closure/base.js`` , 或者

      2. 同时定义 ``goog.exportSymbol`` 和 ``goog.exportProperty`` 并且要使用相同的调用方法。
</td>
  </tr>
<tr>
    <td>@expose</td>
    <td>@expose

      例如：

      ::

      /** @expose */
      MyClass.prototype.exposedProperty = 3;
</td>
    <td>声明一个公开的属性，表示这个属性不可以被删除、重命名或者由编译器进行优化。相同名称的属性也不能由编译器通过任何方式进行优化。

      ``@expose`` 不可以出现在代码库里，因为他会阻止这个属性被删除。
</td>
  </tr>
<tr>
    <td>@extends</td>
    <td>@extends Type
      @extends {Type}

      例如：

      ::

      /**
       * Immutable empty node list.
       * @constructor
       * @extends goog.ds.BasicNodeList
       */
      goog.ds.EmptyNodeList = function() {
        ...
      };
</td>
    <td>和 ``@constructor`` 一起使用，表示从哪里继承过来的。类型外的大括号是可选的。
</td>
  </tr>
<tr>
    <td>@externs</td>
    <td>@externs

      例如：

      ::

      /**
       * @fileoverview This is an externs file.
       * @externs
       */

      var document;
</td>
    <td>声明一个外部文件。
</td>
  </tr>
<tr>
    <td>@fileoverview</td>
    <td> @fileoverview Description

      例如：

      ::

      /**
       * @fileoverview Utilities for doing things that require this very long
       * but not indented comment.
       * @author kuth@google.com (Uthur Pendragon)
       */
</td>
    <td>使注释提供文件级别的信息。
</td>
  </tr>
<tr>
    <td> @implements</td>
    <td>@implements Type
      @implements {Type}

      例如：

      ::

      /**
       * A shape.
       * @interface
       */
      function Shape() {};
      Shape.prototype.draw = function() {};

      /**
       * @constructor
       * @implements {Shape}
       */
      function Square() {};
      Square.prototype.draw = function() {
        ...
      };
</td>
    <td>使用 ``@constructor`` 来表示一个类实现了某个接口。类型外的大括号是可选的。
</td>
  </tr>
<tr>
    <td>@inheritDoc</td>
    <td>@inheritDoc

      例如：

      ::

        /** @inheritDoc */
        project.SubClass.prototype.toString() {
          // ...
        };
</td>
    <td>**已废弃。使用@override代替**

      表示一个子类中的方法或者属性覆盖父类的方法或者属性，并且拥有相同的文档。注意， ``@inheritDoc`` 等同 ``@override`` 
</td>
  </tr>
<tr>
    <td>@interface</td>
    <td>@interface

      例如：

      ::

        /**
        * A shape.
        * @interface
        */
        function Shape() {};
        Shape.prototype.draw = function() {};

        /**
        * A polygon.
        * @interface
        * @extends {Shape}
        */
        function Polygon() {};
        Polygon.prototype.getSides = function() {};
</td>
    <td>表示一个函数定义了一个接口。
</td>
  </tr>
<tr>
    <td>@lends</td>
    <td> @lends objectName
      @lends {objectName}

      例如：

      ::

        goog.object.extend(
            Button.prototype,
            /** @lends {Button.prototype} */ {
            isButton: function() { return true; }
            });
</td>
    <td>表示对象的键是另外一个对象的属性。这个标记只能出现在对象字面量中。

      注意，括号中的名称和其他标记中的类型名称不一样，它是一个对象名，表明是从哪个对象“借过来”的属性。例如， ``@type {Foo}`` 意味着Foo的一个实例，但是 ``@lends {Foo}`` 意味着“Foo构造函数”.

      `JSDoc Toolkit docs <https://code.google.com/p/jsdoc-toolkit/wiki/TagLends>`_ 中有关于更多此标记的信息。
</td>
  </tr>
<tr>
    <td>@license or @preserve</td>
    <td>@license Description

      例如：

      ::

        /**
        * @preserve Copyright 2009 SomeThirdParty.
        * Here is the full license text and copyright
        * notice for this file. Note that the notice can span several
        * lines and is only terminated by the closing star and slash:
        */
</td>
    <td>由 ``@licenseor`` 或 ``@preserve`` 标记的内容，会被编译器保留并放到文件的顶部。

      这个标记会让被标记的重要内容（例如法律许可或版权文本）原样输出，换行也是。
</td>
  </tr>
<tr>
    <td>@noalias</td>
    <td>@noalias

      例如：

      ::

        /** @noalias */
        function Range() {}
</td>
    <td> 用在外部文件当中，告诉编译器，这里的变量或者方法不可以重命名。
</td>
  </tr>
<tr>
    <td>@nosideeffects</td>
    <td>@nosideeffects

      例如：

      ::

        /** @nosideeffects */
        function noSideEffectsFn1() {
          // ...
        };
        /** @nosideeffects */
        var noSideEffectsFn2 = function() {
          // ...
        };
        /** @nosideeffects */
        a.prototype.noSideEffectsFn3 = function() {
          // ...
        };
</td>
    <td>用于函数和构造函数，说明调用这个函数没有副作用。如果返回值未被使用，此注释允许编译器移除对该函数的调用。
</td>
  </tr>
<tr>
    <td>@override</td>
    <td>@override

      例如：

      ::

        /**
        * @return {string} Human-readable representation of project.SubClass.
        * @override
        */
        project.SubClass.prototype.toString() {
          // ...
        };
</td>
    <td>表示子类的方法或者属性故意隐藏了父类的方法或属性。如果子类没有其他的文档，方法或属性也会从父类那里继承文档。
</td>
  </tr>
<tr>
    <td>@param</td>
    <td> @param {Type} varname Description

      例如：

      ::

        /**
        * Queries a Baz for items.
        * @param {number} groupNum Subgroup id to query.
        * @param {string|number|null} term An itemName,
        *    or itemId, or null to search everything.
        */
        goog.Baz.prototype.query = function(groupNum, term) {
          // ...
        };
</td>
    <td> 给方法、函数、构造函数的参数添加文档说明。

参数类型 <http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#JsTypes>`_ 一定要写在大括号里。如果类型被省略，编译器将不做类型检测。
</td>
  </tr>
<tr>
    <td>@private</td>
    <td>@private
      @private {type}

      例如：

      ::

        /**
        * Handlers that are listening to this logger.
        * @private {!Array.<Function>}
        */
        this.handlers\_ = [];
</td>
    <td>
与方法或属性名结尾使用一个下划线来联合表明该成员是 `私有的 <http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#Visibility__private_and_protected_fields_>`_ 。随着工具对 ``@private`` 的认可，结尾的下划线可能最终被废弃。
</td>
  </tr>
<tr>
    <td>@protected</td>
    <td> @protected
      @protected {type}

      例如：

      ::

        /**
        * Sets the component's root element to the given element.  Considered
        * protected and final.
        * @param {Element} element Root element for the component.
        * @protected
        */
        goog.ui.Component.prototype.setElementInternal = function(element) {
          // ...
        };
</td>
    <td>用来表明成员或属性是 ``受保护的 <http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#Visibility__private_and_protected_fields_>``_ 。成员或属性应使用没有跟随下划线的名称。
</td>
  </tr>
<tr>
    <td>@return</td>
    <td>@return {Type} Description

      例如：

      ::

        /**
        * @return {string} The hex ID of the last item.
        */
        goog.Baz.prototype.getLastId = function() {
          // ...
          return id;
        };
</td>
    <td>在方法或函数调用时使用，来说明返回类型。给布尔值写注释时，写成类似“这个组件是否可见”比“如果组件可见则为true，否则为false”要好。如果没有返回值，不使用 ``@return`` 标签。

   类型 <http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#JsTypes>`_ 名称必须包含在大括号内。如果省略类型，编译器将不会检查返回值的类型。
</td>
  </tr>
<tr>
    <td>@see</td>
    <td>@see Link

      例如：

      ::

        /**
        * Adds a single item, recklessly.
        * @see #addSafely
        * @see goog.Collect
        * @see goog.RecklessAdder#add
        ...
</td>
    <td>参考查找另一个类或方法。
</td>
  </tr>
<tr>
    <td>@struct</td>
    <td>@struct Description

      例如：

      ::

        /**
        * @constructor
        * @struct
        */
        function Foo(x) {
          this.x = x;
        }
        var obj = new Foo(123);
        var num = obj['x'];  // warning
        obj.y = "asdf";  // warning

        Foo.prototype = /** @struct */ {
          method1: function() {}
        };
        Foo.prototype.method2 = function() {};  // warning
</td>
    <td>当一个构造函数（在本例中 ``Foo`` ）注释为 ``@struct`` ，你只能用点符号访问Foo对象的属性。此外，Foo对象创建后不能加新的属性。此注释也可以直接使用于对象字面量。
</td>
  </tr>
<tr>
    <td>@supported</td>
    <td>@supported Description

      例如：

      ::

        /**
        * @fileoverview Event Manager
        * Provides an abstracted interface to the
        * browsers' event systems.
        * @supported So far tested in IE6 and FF1.5
        */
</td>
    <td>用于在文件信息中说明该文档被哪些浏览器支持
</td>
  </tr>
<tr>
    <td>@suppress</td>
    <td>@suppress {warning1|warning2}

      例如：

      ::

        /**
        * @suppress {deprecated}
        */
        function f() {
          deprecatedVersionOfF();
        }
</td>
    <td>标明禁止工具发出的警告。警告类别用|分隔。
</td>
  </tr>
<tr>
    <td> @template</td>
    <td>@template

      例如：

      ::

        /**
        * @param {function(this:T, ...)} fn
        * @param {T} thisObj
        * @param {...*} var_args
        * @template T
        */
        goog.bind = function(fn, thisObj, var_args) {
          ...
        };
</td>
    <td>这个注释可以用来声明一个 `模板类型名 <http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#Template_types>`_ 。
</td>
  </tr>
<tr>
    <td>@this</td>
    <td>@this {Type}

      例如：

      ::

        pinto.chat.RosterWidget.extern('getRosterElement',
        /**
        * Returns the roster widget element.
        * @this pinto.chat.RosterWidget
        * @return {Element}
        */
        function() {
          return this.getWrappedComponent_().getElement();
        });
</td>
    <td>标明一个特定方法在其上下文中被调用的对象类型。用于 ``this`` 关键字是从一个非原型方法中使用时
</td>
  </tr>
<tr>
    <td>@type</td>
    <td> @type {Type}

      例如：

      ::

      /**
       * The message hex ID.
       * @type {string}
       */
      var hexId = hexId;
</td>
    <td>标识变量，属性或表达式的 `类型 <http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#JsTypes>`_ 。大多数类型不需要大括号，但有些项目为了保持一致性而要求所有类型都使用大括号。
</td>
  </tr>
<tr>
    <td>@typedef</td>
    <td>@typedef

      例如：

      ::

        /** @typedef {(string|number)} */
        goog.NumberLike;
        /** @param {goog.NumberLike} x A number or a string. */
        goog.readNumber = function(x) {
          ...
        }
</td>
    <td>-使用此注释来声明一个更 `复杂的类型 <http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#Typedefs>`_ 的别名。

</td>
  </tr>
</table>
#####1
使用此注释来声明一个更 `复杂的类型 <http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#Typedefs>`_ 的别名。

你也许在第三方代码中看到其他类型JSDoc注释，这些注释出现在 `JSDoc Toolkit标签的参考 <https://code.google.com/p/jsdoc-toolkit/wiki/TagReference>`_ ，但目前在谷歌的代码中不鼓励使用。你应该将他们当作“保留”字，他们包括：

* @augments

* @argument

* @borrows

* @class

* @constant

* @constructs

* @default

* @event

* @example

* @field

* @function

* @ignore

* @inner

* @link

* @memberOf

* @name

* @namespace

* @property

* @public

* @requires

* @returns

* @since

* @static

* @version

####为goog.provide提供依赖


只提供顶级符号。

一个类上定义的所有成员应该放在一个文件中。所以，在一个在相同类中定义的包含多个成员的文件中只应该提供顶级的类（例如枚举、内部类等）。

要这样写：


    goog.provide('namespace.MyClass');

不要这样写：


    goog.provide('namespace.MyClass');
    goog.provide('namespace.MyClass.Enum');
    goog.provide('namespace.MyClass.InnerClass');
    goog.provide('namespace.MyClass.TypeDef');
    goog.provide('namespace.MyClass.CONSTANT');
    goog.provide('namespace.MyClass.staticMethod');

命名空间的成员也应该提供：


    goog.provide('foo.bar');
    goog.provide('foo.bar.method');
    goog.provide('foo.bar.CONSTANT');
