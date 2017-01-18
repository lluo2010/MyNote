# Stone MVP 相关

由下面几个类/接口组成:

1. 接口Presenter提供attachView和detachView方法.
1. BasePresenter是一个泛型类, 实现了Presenter, 它包含了一个泛型mvpView(View的接口). 它实现了attachView和detachView方法, presenter的view接口是在这里赋值的, 清理工作是在detachView做的.
1. MvpFragment是所有使用mvp模式的fragment的抽象基类, 它从BaseFragment派生, 有一个泛型mvpPresenter, 表示相应的Presenter, 有一个抽象方法createPresenter(),子类需要实现它用于指定创建怎样的presenter. 在它的onViewCreated()里, mvpPresenter被创建, 在onDestroyView里, mvpPresenter的detachView被调用.

BasePresenter是在MvpFragment的onViewCreated里被调用onViewCreated()创建的, 它的attachView是在自己的构造函数里被调用的, BasePresenter的detachView是在MvpFragment的onDestroyView被调用.

通过上面的过程,BasePresenter被创建, attachView被调用, 最后detachView被调用,Presenter该清理的资源被清理.

对应时序图如下:

![时序图](https://www.evernote.com/shard/s164/sh/2414963d-b239-44a5-940f-4f24d7c7ee59/600d42d911a7b862/res/db25b029-50c4-4179-b8e7-2a245cdf54c5.png?resizeSmall&width=832)

## Presenter

Presenter提供attachView和detachView两个接口:

- attachView主要是给Presenter绑上与View之间的接口,并不是绑上View.
- detachView里做一些资源释放,取消任务都工作.

```
public interface Presenter<V> {
    void attachView(V view);

    void detachView();
}
```

## BasePresenter\<V>

BasePresenter实现了Presenter, 它包含了一个泛型mvpView(View的接口). 主要做了如下的工作:

1. 在构造函数里调用了attachView
1. 实现了attachView, 初始化mvpView.
1. 实现了detachView, mvpView=null, 同时取消任务, 注销rxjava的subscription.

相关代码如下:

```
public class BasePresenter<V> implements Presenter<V>, IClean {
    protected V mvpView;
    protected CompositeSubscription mSubscriptions;

    public BasePresenter(V view) {
        mSubscriptions = new CompositeSubscription();
        attachView(view);
    }

    @Override
    public void attachView(V view) {
        this.mvpView = view;
    }

    @Override
    public void detachView() {
        this.mvpView = null;
        this.cancelTask();
        this.unSubscribeSubscriptions();
    }
```

具体的Presenter直接从BasePresenter派生, 如下:

```
public class InvestPresenter extends BasePresenter<InvestViewInterface> {
}
```


## MvpFragment

MvpFragment是所有使用mvp模式的fragment的抽象基类, 它从BaseFragment派生, 有一个泛型mvpPresenter, 表示相应的Presenter, 有一个抽象方法createPresenter(),子类需要实现它用于指定创建怎样的presenter.

在它的onViewCreated()里, mvpPresenter被创建, 在onDestroyView里, mvpPresenter的detachView被调用.

对应代码如下:

```
public  abstract class MvpFragment <P extends BasePresenter> extends BaseFragment {
    protected P mvpPresenter;

    @Override
    public void onViewCreated(View view, Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);
        mvpPresenter = createPresenter();
    }

    protected abstract P createPresenter();


    @Override
    public void onDestroyView() {
        super.onDestroyView();
        if (mvpPresenter != null) {
            mvpPresenter.detachView();
        }
    }
}
```

一般的fragment通过下面的方式实现MvpFragment:

```
public class BuySuccessFragment extends MvpFragment<BuyPresenter> {
    ...
        @Override
    protected BuyPresenter createPresenter() {
        return new BuyPresenter(this,mContext,mMoney,mAddRateTiket);
    }
    ...
}

```

关联类图如下:

![presenter](https://www.evernote.com/shard/s164/sh/2414963d-b239-44a5-940f-4f24d7c7ee59/600d42d911a7b862/res/03f99425-0a8c-426b-9bba-425f47c6a268.png?resizeSmall&width=832)
![mvpFragment](https://www.evernote.com/shard/s164/sh/2414963d-b239-44a5-940f-4f24d7c7ee59/600d42d911a7b862/res/8fed7e19-8af1-488f-8256-c7b671ebe641.png?resizeSmall&width=832)