## Javascript中的typeof用法

> ### javascript中的数据类型

 1. *Number*
 2. *String*
 3. *Boolean*
 4. *Object*
 5. *Undefine*
 6. *Null*

----

> ### typeof的返回值

<table class="table table-hover">
<thead>
<tr>
<th>#</th>
<th>
<b>Type of</b> <span>val</span></th>
<th>Result</th>
</tr>
</thead>
<tbody ng-controller="typeoflist">
<tr ng-repeat="item in list">
<th>[[item.num]]</th>
<td>[[item.key]]</td>
<td><code>[[item.val]]</code></td>
</tr>
</tbody>
</table>

----

> ### 实例验证
>
> + 常规实例 -- normal case

    // Numbers
    typeof 37 === 'number';
    typeof 3.14 === 'number';
    typeof Math.LN2 === 'number';
    typeof Infinity === 'number';
    typeof NaN === 'number'; // Despite being "Not-A-Number"
    typeof Number(1) === 'number'; // but never use this form!

    // Strings
    typeof "" === 'string';
    typeof "bla" === 'string';
    typeof (typeof 1) === 'string'; // typeof always return a string
    typeof String("abc") === 'string'; // but never use this form!

    // Booleans
    typeof true === 'boolean';
    typeof false === 'boolean';
    typeof Boolean(true) === 'boolean'; // but never use this form!

    // Undefined
    typeof undefined === 'undefined';
    typeof blabla === 'undefined'; // an undefined variable

    // Objects
    typeof {a:1} === 'object';
    typeof [1, 2, 4] === 'object'; // use Array.isArray or Object.prototype.toString.call to differentiate regular objects from arrays
    typeof new Date() === 'object';

    typeof new Boolean(true) === 'object'; // this is confusing. Don't use!
    typeof new Number(1) === 'object'; // this is confusing. Don't use!
    typeof new String("abc") === 'object';  // this is confusing. Don't use!

    // Functions
    typeof function(){} === 'function';
    typeof Math.sin === 'function';
> + 其他怪异情况 -- other quirks

    //  历史遗留问题，据说下个版本的文档要修正
    typeof null === 'object'; // This stands since the beginning of JavaScript
    //  正则表达式 浏览器对ecma5.1的支持问题
    typeof /s/ === 'function'; // Chrome 1-12 ... // Non-conform to ECMAScript 5.1
    typeof /s/ === 'object'; // Firefox 5+ ...    // Conform to ECMAScript 5.1
    //  ie留下的问题
    typeof alert === 'object'   // IE6/7/8
    typeof alert === 'function'   // other broswer

----

> ### 相关资料

+ ECMA5:(http://ecma-international.org/ecma-262/5.1/#sec-11.4.3)
+ MDN:(https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Operators/typeof)
