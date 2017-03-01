# MySql 命令行 note


## 常用命令

---

1. 登陆, 使用下面命令,然后根据提示输入密码:

    ```
    mysql -uroot -p
    ```

1. 显示所有的数据库

    ```
    show database;
    ```
    之后会显示所有的数据库.

1. 选择进入某个数据库

    ```
    use database1;
    ``` 

1. 显示数据库所有的表格:

    ```
    show tables;
    ```

1. 执行语句: 可以直接填写语句, 比如select * from tableNameX;


** 注意: 除了登陆外, 其他的命令都需要以";"结尾**






## Reference

---

1. [初学者使用MySQL_Workbench 6.0CE创建数据库和表，以及在表中插入数据](http://blog.csdn.net/u011719449/article/details/12521437)

