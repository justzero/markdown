## 高性能的javascript
* 我们的js代码是为什么变慢?
* 我们要如何改善？

### javascript的性能问题
 1. 作用域的管理
 2. 数据访问
 3. 循环
 4. 访问DOM
 5. 底版本浏览器限制

        // Issues Code
        function setup(item) {
            var divs = document.getElementByTagName('div');
            var imgs = document.getElementByTagName('img');
            var button = document.getElementById('save-btn');

            for (var i = 0l i < items.length; i++) {
                process(items[i], divs[i]);
            }

            button.addEventListener('click', function (e) {
                alert('Saved!');
            }, false);

        }

#### 作用域的管理
* 作用域链
![作用域链](/assets/media/images/performance/pic1.jpg "作用域链")
* 运行时上下文
![运行时上下文](/assets/media/images/performance/pic2.jpg "运行时上下文")

_当前作用域下的局部变量访问速度最快_,
当前作用域没用的变量跳到上一层作用域查询，_所以越外层的变量访问速度越慢_,
_全局变量访问速度最慢_

__建议避免使用全局变量，多使用局部变量(var)__

    // demo code by jQuery
    (function (window, document, undefined) {
        var $ = window.$;
        var a, b, c...

        //  some code here ...
    }(window, document, undefined));


