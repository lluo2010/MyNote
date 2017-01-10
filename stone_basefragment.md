# Stone BaseFragment note


## 介绍

---

BaseFragment是所有fragment的础类, 对fragment进行了封装, 大致功能有下面几项:

1. 对fragment进行封装, 对各个生命周期相关的函数进行了重写.
1. 提供几个可以重载的方法方便子类对各生命周期该做的事情进行抽象(比如初始化变量,提供fragment视图, 初始化控件,获取数据).
1. 实现了加载时loading页面的显示.
1. 提供启动fragment的方法,实现页面结束后返回结果的功能.
1. 善后工作(task释放,rxjava subscription注销).



## 主要的几个函数

---

1. onCreate:

    ```
    public void onCreate(Bundle savedInstanceState) {
        ...
        initData();		/////////
        ...
    }
    ```


1. onCreateView: 

    ```
    public final View onCreateVie(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        ...
        View v = onCreateFragmentView(inflater, container, savedInstanceState);
        ...
        ViewGroup viewGroup = getOutSideViewGroup(inflater,container);  /////////
        if (viewGroup!=null){
            viewGroup.addView(view);
            return viewGroup;
        }else{
            return view;
        }
    }
    ```

1. onActivityCreated()

    ```
    public void onActivityCreate(Bundle savedInstanceState) {
        View view = getView();
        if (view != null) {
            view.setClickable(true);
            onInitView(view);   //////////////
        }
        super.onActivityCreated(savedInstanceState);
        mIsActivityCreated = true;
        onHiddenChanged(false);
        getData();             ////////////
    }
    ```


1. onDestroy()

    在这里取消任务,释放资源. 那些task, rxjava的subscription的善后工作都是这里做的.

1. startFragment
    启动fragment, 不需要返回, 定义如下:

    ```
    public final void startFragment(BaseFragment f);
    public final void startFragment(Intent intent) {
    ```

1. startFragmentForResult

    启动fragment, 页面结束后需要把结果返回,定义如下:

    ```
    public final void startFragmentForResult(Intent intent, int requestCode);
    public final void startFragmentForResult(BaseFragment fragment, int requestCode);
    ```

    当页面结束后, 回调onFragmentResult()被调用,函数参数包含了返回值.

其中:

- initData()

    initData在onCreate()中调用,在initData()中获取外部传进来的数据, 子类重写onInitData()对各种变量做初始化.

    ```
    private void initData(){
        Bundle bundle = getArguments();
        if (bundle == null) {
            bundle = new Bundle();
        }
        mRefreshTag = bundle.getString(REFRESH_TAG);
        mRequestCode = bundle.getInt(REQUEST_CODE);
        mRefreshTag = mRefreshTag == null ? "" : mRefreshTag;
        onInitData(bundle);     //////////////
        mTitle = bundle.getString(IntentExtra.TITLE);
    }
    ```

- onCreateFragmentView()

    onCreateFragmentView()在onCreateView中被调用,子类通过重写该方法来返回自己的view, 一般采用inflater.inflate(R.layout.fragment_buy, container, false)返回View.

- onInitView()

    onInitView()在onActivityCreated()中被调用, 子类重写它做一些获取子类控件及初始化控件的事情.

- getData()

    getData()在onActivityCreate()中被创建, 用来获取数据, 内部调用onGetData(), 一般子类通过重写onGetData()然后在onGetData里面获取数据.

    ```
    public final void getData() {
        onGetData();
    }
    ```

总结:

函数|说明
-|-
initData|在onCreate()中调用,获取Fragment外部传进来的数据, 子类重写onInitData()对各种变量做初始化.
onCreateFragmentView|在onCreateView中被调用,子类通过重写该方法来返回自己的view
onInitView|onActivityCreated()中被调用, 子类重写它做一些获取子类控件及初始化控件的事情.
getData|onActivityCreate()中被创建, 用来获取数据, 内部调用onGetData(), 一般子类通过重写onGetData()然后在onGetData里面获取数据.


## 子类重写的几个函数

---

1. onInitData()
    初始化变量, 定义如下:

    ```
    protected void onInitData(Bundle bundle);
    ```

1. onCreateFragmentView

    返回View, 指定子类的视图, 定义如下:

    ```
    protected abstract View onCreateFragmentView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState);
    ```

1. onInitView

    主要是获取控件及初始化控件的事情. 定义如下:
    ```
    protected void onInitView(View view);
    ```
1. onGetData
    获取数据,一般从网络获取,定义如下:

    ```
	protected void onGetData();
    ```

1. onFragmentResult()
如果fragment是通过startFragmentForResult()启动的, 在fragment关闭后, onFragmentResult被调用, 参数里包含了回调的返回值.

    定义如下:

    ```
    protected void onFragmentResult(int resultCode, int requestCode, Bundle bundle);
    ```


总结:

函数|说明
-|-
onInitData()|初始化变量
onCreateFragmentView|返回View, 指定子类的视图.
onInitView|主要是获取控件及初始化控件的事情.
onGetData|获取数据,一般从网络获取.
onFragmentResult()|如果fragment是通过startFragmentForResult()启动的, 在fragment关闭后, onFragmentResult被调用, 参数里包含了回调的返回值.



## fragment的启动

---

### 启动

一般在BaseFragment里使用如下代码去启动一个fragment:

```
Intent intent  = new Intent(mContext, WebPowerfulFragment.class);
intent.putExtra(IntentExtra.TITLE, "服务协议");
intent.putExtra(IntentExtra.DATA, Constants.PROTOCOL);
this.startFragment(intent);
```

startFragment(intent)的实现如下, 传入的是fragment的class,通过Class.newInstance直接构造出fragment,然后通过setArguments()将参数传进去. 最后用另一个startFragment(f);

```
public final void startFragment(Intent intent) {
	if (intent == null) {
		throw new NullPointerException("intent");
	}
	String clazz = intent.getComponent().getClassName();
	try {
		BaseFragment bf = (BaseFragment) Class.forName(clazz).newInstance();
		bf.setArguments(intent.getExtras());
		if (intent.getExtras() != null && intent.getExtras().containsKey(IntentExtra.TAG)) {
			bf.setTAG(intent.getExtras().getString(IntentExtra.TAG));
		}
		startFragment(bf);
	} catch (Exception e) {
		e.printStackTrace();
	}
}
```

startFragment(fragment)调用startFragmentForResult:

```
	public final void startFragment(BaseFragment f) {
		startFragmentForResult(f, -1);
	}
```

### 传参

startFragmentForResult(Intent intent, int requestCode)实现如下, 

```
public final void startFragmentForResult(Intent intent, int requestCode) {
		if (intent == null) {
			throw new NullPointerException("intent");
		}
		String clazz = intent.getComponent().getClassName();
		try {
			BaseFragment bf = (BaseFragment) Class.forName(clazz).newInstance();
			bf.setArguments(intent.getExtras());
			if (intent.getExtras().containsKey(IntentExtra.TAG)) {
				bf.setTAG(intent.getExtras().getString(IntentExtra.TAG));
			}
			startFragmentForResult(bf, requestCode);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}

public final void startFragmentForResult(BaseFragment fragment, int requestCode) {
	boolean anim = true;
	if (fragment != null && requestCode > 0) {
		Bundle arguments = fragment.getArguments();
		if (arguments == null) {
			arguments = new Bundle();
		}
		arguments.putString(REFRESH_TAG, getTAG());
		arguments.putInt(REQUEST_CODE, requestCode);
		anim = arguments.getBoolean(IntentExtra.ANIM, true);
		fragment.setArguments(arguments);
	}
	mActivity.addFragment(0, fragment, anim);
}

```
TAG作为fragment的参数传递, 这个TAG默认为fragment的class的simpleName,在后面fragment结束要传回结果是作为tag去FragmentManager里查找对应的fragment.

也就是说,当一个fragment结束时,通过TAG,在FragmentManager里找到启动它的fragment,然后调用该fragment的onFragmentResult,将参数传回去, 参数包含requestCode,以及setResult()设置的Bundle.

fragment启动及传参总结:

1. 外部传进要启动的fragment的class name, 然后找到对应的Class, 调用Class.newInstance(),创建相应的fragment. 
1. 使用setArgument设置参数, 将TAG也作为参数传进来,表示的是上个页面的TAG, 在本页面关闭时通过Tag在FragmentManager里找到上个fragment, 然后调用onFragmentResult()方法将参数传回去.
1. 返回值是调用setResult(int resultCode, Bundle bundle)设置的.



## BaseActionbarFragment

----

BaseActionbarFragment从BaseFragment派生,在BaseFragment的基础上, 实现了ActionBar, 提供了几个actionBar的控件接口.

它重写了getOutSideViewGroup(),返回包含ActionBar的ViewGroup, BaseFragment根据这个返回将原来的View加到这个ViewGroup里, 从而使Fragment有ActionBar.


```
public ViewGroup getOutSideViewGroup(LayoutInflater inflater, ViewGroup container) {
    LinearLayout groupView = (LinearLayout) inflater.inflate(R.layout.base_fragment_container, container, false);
    initActionBar(groupView);
    return groupView;
}
```