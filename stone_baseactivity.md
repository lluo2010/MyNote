# Stone BaseActivity note

BaseActivity从FragmentActivity派生,是其他Activity的基类. 它重写了onBackPressed(),对手势事件进行监听. 提供在Activity里对fragment操作(add/remove/repalce)的操作接口.

## 具体功能

---

1. 提供重新定义的接口setContentView(),实现在Activity里再加一些header view.

1. 提供屏幕左滑右滑的事件接口onLeftFling,onRightFling, 子类通过重写实现比如右滑退出当前页的动作.   
    这些都是通过重新实现GestureDetector.SimpleOnGestureListener的onFling,然后将dispatchTouchEvent的事件塞给mDetector.onTouchEvent(ev)实现的.

    具体代码如下:

    ```
    private GestureDetector mDetector;
    ...

    protected void onLeftFling() {
    }

    protected void onRightFling() {
    }
    @Override
    public boolean dispatchTouchEvent(@NonNull MotionEvent ev) {
        mDetector.onTouchEvent(ev);
        return super.dispatchTouchEvent(ev);
    }
    ...
    ...
    mDetector = new GestureDetector(this, new SimpleOnGestureListener() {              
        @Override
        public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX, float velocityY) {
            if (e1==null||e2==null){
                return super.onFling(e1, e2, velocityX, velocityY);
            }
            float path = Math.abs(e2.getRawX() - e1.getRawX());
            if (e1.getRawX() < WIDTH && path > SCREEN_WIDTH / 2) {// 右滑
                onRightFling();
            } else if (e1.getRawX() > SCREEN_WIDTH - WIDTH && path > SCREEN_WIDTH / 2) {
                onLeftFling();
            }
            return super.onFling(e1, e2, velocityX, velocityY);
        }
    });
    ```

1. 重写onBackPressed, 当2秒内连续按两次就退出App.

    ```    
        @Override
        public void onBackPressed() {
            if (isLock) {
                long currentTime = System.currentTimeMillis();
                if ((currentTime - touchTime) >= 2000) {
                    UIUtils.show("再按一次退出应用");
                    touchTime = currentTime;
                } else {
                    StoneApp.exit();
                }
            } else {
                super.onBackPressed();
            }
        }
    ```

1. 提供了往Activity里add/remove/replace fragment的方法  

    比如:

    ```
        protected BaseFragment addFragment(int id, BaseFragment fragment, boolean isAnim) {
            setRightVisibility(false);
            fragment.setTAG("");
            initData(fragment.getArguments());
            try {
                FragmentTransaction transaction = getFragmentTransaction(isAnim);
                transaction.add(id, fragment, fragment.getTAG()).addToBackStack(fragment.getTAG()).commit();
            } catch (Exception e) {
                BasicUtil.printStackTraceInDebug(e);
                FragmentTransaction transaction = getFragmentTransaction(isAnim);
                transaction.add(id, fragment, fragment.getTAG()).addToBackStack(fragment.getTAG()).commitAllowingStateLoss();
            }
            return fragment;
        }

        public BaseFragment removeFragment(BaseFragment fragment) {
            try {
                getSupportFragmentManager().beginTransaction().remove(fragment).commitAllowingStateLoss();
            } catch (Exception e) {
    //			e.printStackTrace();
            }
            return fragment;
        }

        public BaseFragment replaceFragment(int id, BaseFragment fragment) {
            fragment.setTAG("");
            initData(fragment.getArguments());
            FragmentTransaction transaction = getFragmentTransaction(false);
            transaction.replace(id, fragment, fragment.getTAG()).commitAllowingStateLoss();
            return fragment;
        }
    ```