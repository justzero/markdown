## 利用传入参数不同实现方法重载
<span class="label label-warning"> 首先声明：本文只做技术兴趣讨论，无需考虑性能，可读性，可维护性方面问题 </span>
----
首先上代码：

    // 方法重载器
    var addMethon = function (obj, methonName, fn) {
        var oldMethon = obj[methonName];
        obj[methonName] = function () {
            // fn.length -- 方法的形参
            // argument.length -- 方法的实参
            if (fn.length == arguments.length) {
                return fn.apply(this, arguments);
            } else if (typeof oldMethon == 'function') {
                return oldMethon.apply(this, arguments);
            }
        }
    };

    // 测试用例
    var ninjas = {
        value: ['Dean Edwards', 'Sam Stephenson', 'Alex Russell']
    };

    addMethod(ninjas, "find", function () {
        return this.values;
    });

    addMethod(ninjas, "find", function(name) {
        var ret = [];
        for (var i = 0; i < this.values.length; i++)
            if (this.values[i].indexOf(name) == 0)
                ret.push(this.values[i]);
        return ret;
    });

     addMethod(ninjas, "find", function(first, last) {
         var ret = [];
         for (var i = 0; i < this.values.length; i++)
             if (this.values[i] == (first + " " + last))
                 ret.push(this.values[i]);
         return ret;
     });

     assert(ninjas.find().length == 3,
         "Found all ninjas");
     assert(ninjas.find("Sam").length == 1,
         "Found ninja by first name");
     assert(ninjas.find("Dean", "Edwards").length == 1,
         "Found ninja by first and last name");
     assert(ninjas.find("Alex", "X", "Russell") == null,
         "Found nothing");


