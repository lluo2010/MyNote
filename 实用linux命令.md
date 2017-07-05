# 实用Linux命令 note


## 压缩与解压

### zip/unzip

zip命令可以用来解压缩文件，或者对文件进行打包操作。zip是个使用广泛的压缩程序，文件经它压缩后会另外产生具有“.zip”扩展名的压缩文件


1. 压缩zip

    - 压缩单个文件

        ```
        zip b.zip b   //压缩b文件为b.zip

        ```

    - 压缩多个文件

        ```
        zip ab.zip a b   //压缩文件a,b到ab.zip

        ```
    - 压缩文件夹

        ```
        zip -r test1.zip test1  //将test1文件夹下的所有文件枷锁到test1.zip
        ```

1. 解压unzip

    -d<目录>：指定文件解压缩后所要存储的目录；


    - 解压到当前目录

        ```
        unzip a.zip
        ```

    - 解压到指定目录

        ```
        unzip -d ./aaa a.zip  //其中./aaa为当前目录下的aaa目录
        ```


### gzip& gunzip

操作后，源文件被删除， 只能多单个文件进行操作（其实也是可以对目录进行操作， 是对目录里的每个文件进行相同的操作）. 这和zip/unzip不同。

1. 压缩gzip

    ```
    gzip a      //压缩单个文件a，得到a.gz, a会被删除
    ```

1. 解压gunzip

    ```
    gunzip a.gz   //解压文件a.gz。
    ```



## tar

默认tar只是负责归档,加上压缩参数后才有压缩解压缩功能。

### 参数

1. 主选项：

    - c 创建新的档案文件。如果用户想备份一个目录或是一些文件，就要选择这个选项。相当于打包。
    - x 从档案文件中释放文件。相当于拆包。
    - t 列出档案文件的内容，查看已经备份了哪些文件。
    - r --append 向压缩文档里追加文件
    - u --update 更新原压缩包中的文件

特别注意，在参数的下达中， c/x/t 仅能存在一个！不可同时存在！因为不可能同时压缩与解压缩。

1. 辅助选项：

    - z ：是否同时具有 gzip 的属性？亦即是否需要用 gzip 压缩或解压？ 一般格式为xx.tar.gz或xx. tgz
    - j ：是否同时具有 bzip2 的属性？亦即是否需要用 bzip2 压缩或解压？一般格式为xx.tar.bz2  
    - v ：压缩的过程中显示文件！这个常用
    - f ：使用档名，** 请留意，在 f 之后要立即接档名喔！不要再加其他参数！**
    - p ：使用原文件的原来属性（属性不会依据使用者而变）
    - --exclude FILE：在压缩的过程中，不要将 FILE 打包！


### 例子

1. 打包

    ```
    tar -cvf abc.tar a b c
    ```

    ```
    tar -cvf all.tar *  //打包当前文件夹下所有文件及文件夹。
    ```

    ```
    tar -cvf t11.tar t1  //打包文件夹t1下的所有到t11.tar
    ```

    ```
    tar -czvf t11.tar.gz t1 t2  //将两个文件夹t1,t2打进一个包
    ```


1. 打包并压缩

    ```
    tar -czvf abc.tar.gz a b c
    ```

1. 解包

    ```
    tar -xvf abc.tar  //直接将里面的文件解到当前文件夹
    ```

    ```
     tar -xvf abc.tar -C folder1  //解到指定的文件夹folder1, folder1必须存在
    ```

1. 解包并解压

    ```
    tar -xzvf abc.tar.gz  //直接将里面的文件解包并解压到到当前文件夹
    ```

    ```
    tar -xzvf abc.tar.gz -C t2
    ```

1. 不解压下查看内容

    ```
    tar -tf abc.tar
    ```

1. 关于参数-C

    tar -cvf file2.tar -C /home/usr2 file2

    该命令中的-C dir参数，将tar的工作目录从当前目录改为/home/usr2，将file2文件（不带绝对路径）压缩到file2.tar中。注意：-C dir参数的作用在于改变工作目录，其有效期为该命令中下一次-C dir参数之前。

    使用tar的-C dir参数，同样可以做到在当前目录/home/usr1下将文件解压缩到其他目录，例如：

    ```
    >$ tar -xvf file2.tar -C /home/usr2
    ```
    而tar不用-C dir参数时是无法做到的：

    ```
    $ tar -xvf file2.tar /home/usr2
    tar: /tmp/file: Not found in archive
    tar: Error exit delayed from previous errors
    ```