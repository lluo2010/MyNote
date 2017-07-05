# Python matplotlib study note


1. 折线图

plt.plot(x, data, color = 'r')

x为x坐标值，data为y坐标值


1. 柱状图

plt.bar(x, data, alpha = .5, color = 'g')
x为x坐标值，data为y坐标值





1. 直方图：

    ```
    import numpy as np
    import matplotlib.pyplot as plt

    ...
    plt.hist(z, bins=500, normed=True)//直接画

    or:

    fig = plt.figure()
    ax = fig.add_subplot(111)
    ax.hist(df['Age'], bins=7)
    plt.title('Age distribution')   //整个图title
    plt.xlabel('Age')   //x轴描述
    plt.ylabel('Employee')  //y轴描述
    plt.show()
    ```

1. 堆积图stacked bar


N = len(trains)         #-- Number of trains
ind = np.arange(N)      # the x locations for the groups
width = 0.35            # the width of the bars: can also be len(x) sequence
...
p1 = plt.bar(ind, ideal, width, color='green')
p2 = plt.bar(ind, correct, width, color='yellow', bottom=ideal)
p3 = plt.bar(ind, deficient, width, color='red', bottom=correct)
p4 = plt.bar(ind, without, width, color="grey", bottom=deficient)



1. pie


    ```
    # Pie chart, where the slices will be ordered and plotted counter-clockwise:
    labels = 'Frogs', 'Hogs', 'Dogs', 'Logs'
    sizes = [25, 30, 45, 100]
    explode = (0, 0.1, 0, 0)  # only "explode" the 2nd slice (i.e. 'Hogs')
    fig1, ax1 = plt.subplots()
    ax1.pie(sizes, explode=explode, labels=labels, autopct='%1.1f%%', shadow=True, startangle=90)
    ax1.axis('equal')  # Equal aspect ratio ensures that pie is drawn as a circle.
    plt.show()
    ```

pie(x, explode=None, labels=None, colors=None, autopct=None, pctdistance=0.6,
        shadow=False, labeldistance=1.1, startangle=None, radius=None) 
参数:
x       (每一块)的比例，如果sum(x) > 1会使用sum(x)归一化
labels  (每一块)饼图外侧显示的说明文字
explode (每一块)离开中心距离
startangle  起始绘制角度,默认图是从x轴正方向逆时针画起,如设定=90则从y轴正方向画起
shadow  是否阴影
labeldistance label绘制位置,相对于半径的比例, 如<1则绘制在饼图内侧
autopct 控制饼图内百分比设置,可以使用format字符串或者format function
        '%1.1f'指小数点前后位数(没有用空格补齐)
pctdistance 类似于labeldistance,指定autopct的位置刻度
radius  控制饼图半径




##  怎么画多个图？·



## Referece

1. [matplotlib.pyplot**](https://matplotlib.org/api/pyplot_api.html)
1. [matplotlib-绘制精美的图表**](http://old.sebug.net/paper/books/scipydoc/matplotlib_intro.html)
1. [matplotlib绘图实例：pyplot、pylab模块及作图参数**](http://blog.csdn.net/pipisorry/article/details/40005163)
1. [matplotlib overview](https://matplotlib.org/contents.html)
1. [绘图: Python matplotlib简介](http://www.cnblogs.com/vamei/archive/2012/09/17/2689798.html)



快捷H5-标准版-php.rar




plt.pie(x_list, labels=label_list, autopct='%1.1f%%')



matplotlib-绘制精美的图表
http://old.sebug.net/paper/books/scipydoc/matplotlib_intro.html