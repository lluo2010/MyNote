# Intellij shortcut

** 注意: 由于Mac上我们把Ctrl和Command对调了, 所有如果有些快捷键不起作用可以互相对调. **

1. 查看定义: ctrl+B
1. 跳转到接口方法实现处: Ctrl+Alt+B，
1. 转到父类: Ctrl+U
1. 回到最近修改的代码处:Ctrl-Shift-Backspace
1. 查找引用: alt+F7
1. 代码提示:

    + 基本的代码提示用Ctrl+Space
    + 更智能地按类型信息提示Ctrl+Shift+Space.
1. 自动补全:Ctrl+Shift+Enter就能自动补全末尾的字符。而且不只是括号，例如敲完if/for时也可以自动补上{}花括号。
1. 快速修复(Eclipse quickfix): alt+enter.   
1. 全局查找: ctrl+shift+F
1. 当前文件查找: ctrl+F
1. 查看任何东西: shift+shift
1. 查看类: ctrl+N
1. 查看资源/文件: ctrl+shift+N
1. 查看当前类所有方法: ctrl+F12
1. 格式化import列表(自动导入/删除):Ctrl+Alt+o. 和AS同.
1. 格式化代码:Ctrl+Alt+L. 和AS同.
1. 代码修复:Alt+Enter. 代码有黄色警告的,派生某个接口需要实现的或者需要try catch的, 都可以使用Alt+Enter.  同AS.
1. 代码注释:

    + //:Ctrl+/
    + /**/:Ctrl+shift+/


## 代码添加与自动生成

---

1. 查看父类可以重写的方法: Ctrl+O.
1. 自动生成一些代码, 比如构造函数,比如getter, setter,equals and hash(), toString(): Cmd + N.    同AS.
1. TAB自动代码生成:

    + sout+tab生成System.out.println().
    + psvm+tab生成main入口函数.
    + fori+tab生成for循环.
    + int k = 10; k.fori+tab生成下面的:

        ```
            int k = 0;
            for (int i = 0; i < k; i++) {
            }
        ```
    + user.for+Tab生成for(User user : users)
    + user.getBirthday().var+Tab生成Date birthday = user.getBirthday();
    + str.nn+tab生成if (str != null) { }
    + "hello".return+tab生成return "hello";
    + 对于对象, obj.nn+tab生成if (obj!=null){}
    + 对于对象, obj.null+tab生成if (obj==null){}
    + 对于boolean, b.if+tab生成if(b){}
    + 对于boolean, b.else+tab生成if(!b){}
同 AS.




android studio的:

转向父类方法或父类






## Reference

---

1. [十大Intellij IDEA快捷键](http://blog.csdn.net/dc_726/article/details/42784275)
1. [IntelliJ IDEA 的 20 个代码自动完成的特性](http://www.oschina.net/question/12_70799)
