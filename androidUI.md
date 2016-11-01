

1.
 <ProgressBar
            style="?android:attr/progressBarStyle"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_gravity="center"
            android:indeterminateDuration="1000"
            android:indeterminateDrawable="@drawable/progress_bg"
            android:indeterminate="false" />


2.
mDialog = new XDialog(context, R.style.Dialog);
		mDialog.setCancelable(cancelable);
		mDialog.setCanceledOnTouchOutside(cancelable);
		mDialog.setMessage(msg);
		mDialog.show();

3.
        <View style="@style/Divider" />                    
What's style???
    <style name="Divider">
        <item name="android:layout_width">match_parent</item>
        <item name="android:layout_height">1px</item>
        <item name="android:background">@color/divider</item>
    </style>


4.shape属于drable??
<shape xmlns:android="http://schemas.android.com/apk/res/android" >
    <corners android:radius="5dp" />
    <solid android:color="@android:color/white" />
</shape>


5.设置背景
圆角的长方形
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@drawable/dialog_corner_black_trans"
    android:orientation="horizontal" >

dialog_corner_black_trans.xml:
<shape xmlns:android="http://schemas.android.com/apk/res/android" >
    <corners android:radius="5dp" />
    <solid android:color="#9F000000" />
</shape>

6.颜色表示
colors.xml:
<resources>
    <color name="e_wallet_text">#ffc7c7</color>
使用:
android:text="提示"
android:textColor="@color/e_wallet_text"    

7. textview仿按钮
利用background实现按和不按的不同效果
 <cn.stlc.app.view.XTextView
    ...
   android:background="@drawable/dialog_corner_bottom"
   
dialog_corner_bottom.xml如下:
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true"><shape>
            <corners android:bottomLeftRadius="5dp" android:bottomRightRadius="5dp" />
            <solid android:color="#c8c8c8" />
        </shape></item>
    <item android:state_selected="true"><shape>
            <corners android:bottomLeftRadius="5dp" android:bottomRightRadius="5dp" />
            <solid android:color="#c8c8c8" />
        </shape></item>
    <item><shape>
            <corners android:bottomLeftRadius="5dp" android:bottomRightRadius="5dp" />
            <solid android:color="@color/white" />
        </shape></item>
</selector>