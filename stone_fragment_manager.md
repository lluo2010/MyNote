# Stone fragment manager note


## 主页面的fragment管理

---

主页面上有四个fragment, 在用到时先判断是否已经有了(add了),如果没有add,如果有就直接在show这个fragment.
当然显示任何fragment前都实现把其他fragment隐藏掉的, 否则会出现好几个fragment都显示在一起的场景.

主要代码在MainActivity中, 相关代码判断如下:

```
FragmentTransaction transaction = fragmentManager.beginTransaction();
hideAllFragment(transaction);           //先把所有的fragment给隐藏了，然后在根据id显示出来
if (mRecommendFragment == null) {
    mRecommendFragment = new RecommendFragment();
    transaction.add(R.id.content_frame, mRecommendFragment);        //add fragment
} else {
    transaction.show(mRecommendFragment);                           //show
}
```


## 二级页面的fragment管理

---

二级页面的fragment都在CommonActivity里管理.

CommonActivity里有一个存放BaseFragment的Stack.

```
private Stack<BaseFragment> ftStack = new Stack<>();
```

### fragment的显示

当要求新建一个fragment时, 先通过FragmentTransaction往Activity里add一个fragment, 然后往Stack里存入该fragment, 之后遍历Stack,将其他的fragment全部隐藏了.

相应代码如下:

```
public BaseFragment addFragment(int id, BaseFragment fragment, boolean isAnim){
	if (id == 0) {
		id = R.id.content_frame;
	}

	fragment = super.addFragment(id, fragment, isAnim);         //添加显示fragment
	ftStack.push(fragment);
	HANDLER.postDelayed(new HideRunnable(fragment), DELAY);     //隐藏所有不需要显示的fragment.
	return fragment;
}
...
...

private BaseFragment bt;
...

/**
* 这个Runnable去隐藏所有不显示的fragment
*/
public HideRunnable(BaseFragment bf) {
	this.bt = bf;
}

@Override
public void run() {
	FragmentTransaction transaction = fragmentManager.beginTransaction();
	for (int i = 0; i < ftStack.size(); i++) {
		Fragment ft = ftStack.get(i);
		if (ft != bt) {
			if (!ft.isHidden())
				transaction.hide(ft);
		} else {
			if (ft.isHidden())
				transaction.show(ft);
		}
	}
	try {
		transaction.commit();
	} catch (Exception e) {
		if (BuildConfig.DEBUG)
			e.printStackTrace();
	}
}

```


### Fragment回退

CommonActivity重写回退事件onBackPressed():

1. 先从fragment的堆栈stack里获取最上面的fragment, 如果该onBackPressed()返回true, 则什么也不做,等于没有点回退.
1. 如果目前该fragment是最后一个fragment,则关闭Activity.
1. 如果还有其他fragment, 则将该fragment从堆栈中移除, 然后post一个HideRunnable去隐藏其他不该显示的fragment.

对应代码片段如下:

```
@Override
public void onBackPressed() {
	if (ftStack.peek().onBackPressed()) {
		return;
	}
	if (ftStack.size() > 1) {
		ftStack.pop().onHiddenChanged(true);
	} else {
		if (!ftStack.isEmpty()) {
			ftStack.peek().onHiddenChanged(true);
		}
		finish();
		return;
	}
	HANDLER.postDelayed(new HideRunnabl(ftStack.peek()), DELAY);
	super.onBackPressed();
	onDestroyFragment();
}
```



Parcelable创建?????????????