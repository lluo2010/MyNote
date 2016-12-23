
# Android RecyclerView note


## 简介

---

>The RecyclerView is a new ViewGroup that is prepared to render any adapter-based view in a similar way. It is supossed to be the successor of ListView and GridView, and it can be found in the latest support-v7 version.

通常，我们写 ListView 适配器，都是首先继承 BaseAdapter，实现四个抽象方法，创建一个静态 ViewHolder ， getView() 方法中判断 convertView 是否为空，创建还是获取 viewholder 对象。

而RecyclerView也是类似的步骤，首先继承RecyclerView.Adapter<RecyclerView.ViewHolder>类，实现三个抽象方法，创建一个静态的 ViewHolder。

ListView中convertView是复用的，在RecyclerView中，是把ViewHolder作为缓存的单位.


## 使用

---

1. 库的引入

    build.gridle 文件中加入:

    ```
    compile 'com.android.support:recyclerview-v7:24.0.0'
    ```

1. 创建对象

    ```
    RecyclerView recyclerview = (RecyclerView) findViewById(R.id.recyclerview);
    ```

1. 设置显示规则

    ```
    recyclerview.setLayoutManager(new LinearLayoutManager( this, LinearLayoutManager.VERTICAL, false));
    ```

    RecyclerView 将所有的显示规则交给一个叫 LayoutManager 的类去完成了。

    LayoutManager是一个抽象类，系统已经为我们提供了三个默认的实现类，分别是 LinearLayoutManager、GridLayoutManager、 StaggeredGridLayoutManager，分别是，线性显示、网格显示、瀑布流显示。当然也可以通过继承这些类来扩展实现自己的 LayougManager。

1. 实现RecyclerView.Adapter和RecyclerVie.ViewHolder  
    Holder必须从RecyclerVie.ViewHolder派生, Adapter必须从RecyclerView.Adapter<RecyclerView.ViewHolder>派生.

    ```
    private class CrimeHolder extends RecyclerVie.ViewHolder {
        ...
    }

    private class CrimeAdapter extends RecyclerView.Adapter<CrimeHolder> {
        @Override
        public CrimeHolder onCreateViewHolder(ViewGroup parent, int pos) {
            View view = LayoutInflater.from(parent.getContext())
                    .inflate(R.layout.list_item_crime, parent, false);
            return new CrimeHolder(view);
        
        @Override
        public void onBindViewHolder(CrimeHolder holder, int pos) {
            Crime crime = mCrimes.get(pos);
            holder.bindCrime(crime);
        
        @Override
        public int getItemCount() {
            return mCrimes.size();
        }
    } 
    ```


1. 设置适配器

    ```
    recyclerview.setAdapter(adapter);
    ```
    不同于ListView的适配器, Recyclerview的适配器需要从RecyclerView.Adapter<RecyclerView.ViewHolder>继承.


## 关于Adapter-RecyclerView.Adapter<RecyclerView.ViewHolder>

---

>Adapters provide a binding from an app-specific data set to views that are displayed within a RecyclerView.

Adapter将指定的数据与视图进行了绑定,类似于Controller的角色.

### 几个主要的方法如下:

方法|说明
-|:-
abstract VH	onCreateViewHolder(ViewGroup parent, int viewType)| Called when RecyclerView needs a new RecyclerView.ViewHolder of the given type to represent an item.
abstract void	onBindViewHolder(VH holder, int position)| Called by RecyclerView to display the data at the specified position.
void	onBindViewHolder(VH holder, int position, List<Object> payloads)| Called by RecyclerView to display the data at the specified position.
abstract int	getItemCount()| Returns the total number of items in the data set held by the adapter.
long	getItemId(int position)| Return the stable ID for the item at position.The default implementation of this method returns NO_ID.
int	getItemViewType(int position)| Return the view type of the item at position for the purposes of view recycling.The default implementation of this method returns 0, making the assumption of a single view type for the adapter


getItemCount(),onCreateViewHolder(xx),onBindViewHolder(xxx)必须实现.


1. onCreateViewHolder  

    在onCreateViewHolder()中通过inflate获取view, 然后创建ViewHolder.

    改方法只有在需要创建view时才调用,如果是复用的不会被调用.

1. onBindViewHolder  

    在改方法中, 绑定数据到ViewHolder里面的控件的内容和状态.  

    在onBindViewHolder()中更新ViewHolder中空间的内容和状态.等于RecyclerView的Adpater实现了ListView中getView的功能:判断convertview是否为空,如果为空,赋值,从中获取到各个控件并保存在ViewHolder中,如果不为空,直接拿到ViewHolder.然后对ViewHolder中的控件的内容和状态进行更新.

### 例子

```
public class MyRecyclerAdapter extends RecyclerView.Adapter<MyRecyclerAdapter.ViewHolder> {
    private List<ViewModel> items;
    private int itemLayout;

    public MyRecyclerAdapter(List<ViewModel> items, int itemLayout) {
        this.items = items;
        this.itemLayout = itemLayout;
    }

    @Override public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View v = LayoutInflater.from(parent.getContext()).inflate(itemLayout, parent, false);
        return new ViewHolder(v);
    }
 
    @Override public void onBindViewHolder(ViewHolder holder, int position) {
        ViewModel item = items.get(position);
        holder.text.setText(item.getText());
        holder.image.setImageBitmap(null);
        Picasso.with(holder.image.getContext()).cancelRequest(holder.image);
        Picasso.with(holder.image.getContext()).load(item.getImage()).into(holder.image);
        holder.itemView.setTag(item);
    }

    @Override public int getItemCount() {
        return items.size();
    }
 
    public static class ViewHolder extends RecyclerView.ViewHolder {
        public ImageView image;
        public TextView text;
 
        public ViewHolder(View itemView) {
            super(itemView);
            image = (ImageView) itemView.findViewById(R.id.image);
            text = (TextView) itemView.findViewById(R.id.text);
        }
    }
}

```

### 生命周期相关的方法

方法|说明
-|:-
void	onAttachedToRecyclerView(RecyclerView recyclerView)| Called by RecyclerView when it starts observing this Adapter.
void	onDetachedFromRecyclerView(RecyclerView recyclerView)| Called by RecyclerView when it stops observing this Adapter.
void	onViewRecycled(VH holder)| Called when a view created by this adapter has been recycled.View被回收了.

还有一些通知相关的方法, 在下面"关于通知"中有介绍.



## 关于通知

---

ListView 每次修改了数据源后，都要调用它的Adapter的notifyDataSetChanged()刷新每项item，RecyclerView除了调用notifyDataSetChanged()刷新每项更新外, 还支持局部刷新, 如下:

方法|:说明
-|-
final void	notifyDataSetChanged()| Notify any registered observers that the data set has changed.
final void	notifyItemChanged(int position, Object payload)| Notify any registered observers that the item at position has changed with an optional payload object.
final void	notifyItemChanged(int position)| Notify any registered observers that the item at position has changed.
final void	notifyItemInserted(int position)| Notify any registered observers that the item reflected at position has been newly inserted.
final void	notifyItemMoved(int fromPosition, int toPosition)| Notify any registered observers that the item reflected at fromPosition has been moved to toPosition.
final void	notifyItemRangeChanged(int positionStart, int itemCount, Object payload)| Notify any registered observers that the itemCount items starting at position positionStart have changed.
final void	notifyItemRangeChanged(int positionStart, int itemCount)| Notify any registered observers that the itemCount items starting at position positionStart have changed.
final void	notifyItemRangeInserted(int positionStart, int itemCount)| Notify any registered observers that the currently reflected itemCount items starting at positionStart have been newly inserted.
final void	notifyItemRangeRemoved(int positionStart, int itemCount)| Notify any registered observers that the itemCount items previously located at positionStart have been removed from the data set.
final void	notifyItemRemoved(int position)|Notify any registered observers that the item previously located at position has been removed from the data set.

上面即可以刷新指定的某一目, 也可以指定的某个范围的几项, 也可以某一项或者几项被insert,remove.

如果我们需要在Adapter中添加删除一些数据, 则需要在Adapter中添加add,remove等方法,然后调用notifyItemInserted(),notifyItemRemoved()通知.

如:

```
public void add(ViewModel item, int position) {
    items.add(position, item);
    notifyItemInserted(position);
}
 
public void remove(ViewModel item) {
    int position = items.indexOf(item);
    items.remove(position);
    notifyItemRemoved(position);
}
```



## ViewHolder

---

作为缓存的单位, 需要从RecyclerView.ViewHolder派生, 里面缓存了视图目的各个子元素.

一般如下:

```
private class CrimeHolder extends RecyclerVie.ViewHolder {
        private final CheckBox mSolvedCheckBox;
        private Crime mCrime;

        public CrimeHolder(View itemView) {
            super(itemView);

            mSolvedCheckBox = (CheckBox) itemView
                .findViewById(R.id.crime_list_item_solvedCheckBox);
        }

        public void bindCrime(Crime crime) {
            mCrime = crime;
            mSolvedCheckBox.setChecked(crime.isSolved());
        }
    }
```



## LayoutManager

---

>This is the class that will decide in which part of the screen the views are placed. But that’s only one of its many responsibilities. It must be able to manage scrolling and recycling among others.

todo...XXX


## ItemAnimator

---

>ItemAnimator will animate ViewGroup modifications that are notified to adapter. Basically it will automatically animate adding and removing items. That’s not an easy class either, and we find a DefaultItemAnimator that works quite well.

todo...XXX



## 完整例子

---

```
View v = inflater.inflate(R.layout.fragment_recyclerview, parent, false);
mRecyclerView = (RecyclerView) v.findViewById(R.id.recycler_view);
mRecyclerView.setLayoutManager(new LinearLayoutManager(getActivity()));
mCrimes = CrimeLab.get(getActivity()).getCrimes();
mRecyclerView.setAdapter(new CrimeAdapter());

...

private class CrimeHolder extends RecyclerVie.ViewHolder {
     private final CheckBox mSolvedCheckBox;
     private Crime mCrime;
 
     public CrimeHolder(View itemView) {
         super(itemView);
 
         mSolvedCheckBox = (CheckBox) itemView
             .findViewById(R.id.crime_list_item_solvedCheckBox);
     }
 
     public void bindCrime(Crime crime) {
         mCrime = crime;
         mSolvedCheckBox.setChecked(crime.isSolved());
     }
 }


private class CrimeAdapter 
         extends RecyclerView.Adapter<CrimeHolder> {
     @Override
     public CrimeHolder onCreateViewHolder(ViewGroup parent, int pos) {
         View view = LayoutInflater.from(parent.getContext())
                 .inflate(R.layout.list_item_crime, parent, false);
         return new CrimeHolder(view);
     
     @Override
     public void onBindViewHolder(CrimeHolder holder, int pos) {
         Crime crime = mCrimes.get(pos);
         holder.bindCrime(crime);
     
     @Override
     public int getItemCount() {
         return mCrimes.size();
     }
} 
```



## Reference

---

1. [RecyclerView 和 ListView 使用对比分析](https://www.diycode.cc/topics/221)
1. [深入浅出 RecyclerView](https://gold.xitu.io/entry/5782da55a633bd005b208657)
1. [android RecycleView Adapter简单封装](http://jishu.y5y.com.cn/xiangzhihong8/article/details/52965533)
1. [RecyclerView Part 1: Fundamentals For ListView Experts](https://www.bignerdranch.com/blog/recyclerview-part-1-fundamentals-for-listview-experts/)
1. [RecyclerView in Android: The basics](https://antonioleiva.com/recyclerview/)
1. [Android 优雅的为RecyclerView添加HeaderView和FooterView](http://blog.csdn.net/lmj623565791/article/details/51854533)
1. [RecyclerView添加Header的正确方式](http://blog.csdn.net/qibin0506/article/details/49716795)


LayoutParameters????