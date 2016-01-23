# TestCoordinatorLayout
CoordinatorLayout使用方法

作为Android的控件, CoordinatorLayout已经加入最新的HelloWorld项目中, 也是Material风格的重要组件, 协调(Coordinate)其他组件, 实现联动. 那么让我们来看看这个动画效果怎么用吧? 

###1. 准备

首先新建HelloWorld项目.

在项目的build.gradle文件中, 引入头像控件库和CardView库, 在本例中会使用.
```
    compile 'de.hdodenhof:circleimageview:1.3.0'
    compile 'com.android.support:cardview-v7:23.1.0'
    compile 'com.jakewharton:butterknife:7.0.1'
```

###2. 页面
在``activity_main.xml``中, 保留CoordinatorLayout和AppBarLayout, 重新编写页面.
> ``android:fitsSystemWindows="true"`` 这句也需要去掉, 本例需要保留最上面的状态栏(status bar), 这个属性会导致重叠, 默认false.

activity_main.xml代码
``` xml
<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true"
    tools:context=".MainActivity">

    <android.support.design.widget.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:theme="@style/AppTheme.AppBarOverlay">

    </android.support.design.widget.AppBarLayout>

</android.support.design.widget.CoordinatorLayout>
```

修改主题颜色, 粉色符合风格
```
    <color name="colorPrimary">#FFCCCC</color>
    <color name="colorPrimaryDark">#FF00FF</color>
```

在AppBarLayout中, 添加CollapsingToolbarLayout控件, CollapsingToolbar会滚动消失, 被ToolBar替换, 实现滚动动画.
``` xml
        <android.support.design.widget.CollapsingToolbarLayout
            android:layout_width="match_parent"
            android:layout_height="450dp"
            app:layout_scrollFlags="scroll|exitUntilCollapsed|snap">

        </android.support.design.widget.CollapsingToolbarLayout>
```
> ``app:layout_scrollFlags``的属性, scroll滑动, exitUntilCollapsed退出到最小, snap自动滑动动画, 定义CollapsingToolbarLayout的滑动属性.

CollapsingToolbarLayout划分两部分, 一部分大图, 一部分文字.
```
            <ImageView
                android:layout_width="match_parent"
                android:layout_height="300dp"
                android:scaleType="centerCrop"
                android:src="@drawable/large"
                app:layout_collapseMode="parallax" />

            <FrameLayout
                android:layout_width="match_parent"
                android:layout_height="150dp"
                android:layout_gravity="bottom|center_horizontal"
                android:background="@color/colorPrimary"
                app:layout_collapseMode="parallax">

                <LinearLayout
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:orientation="vertical"
                    android:layout_gravity="center">

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_gravity="center_horizontal"
                        android:gravity="bottom|center"
                        android:text="@string/person_name"
                        android:textColor="@android:color/white"
                        android:textSize="30sp" />

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_gravity="center_horizontal"
                        android:layout_marginTop="4dp"
                        android:text="@string/person_title"
                        android:textColor="@android:color/white"/>
                </LinearLayout>
            </FrameLayout>
```

> ``app:layout_collapseMode="parallax"``折叠时的视差效果, 自动滑动.

效果
![CollapsingToolbarLayout](http://upload-images.jianshu.io/upload_images/749674-b7f1ad96c90bdd61.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

添加NestedScrollView, 文字的滚动视图.
```
    <android.support.v4.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="none"
        app:behavior_overlapTop="30dp"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">

        <android.support.v7.widget.CardView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_margin="8dp"
            app:cardElevation="8dp"
            app:contentPadding="16dp">

            <TextView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:lineSpacingExtra="8dp"
                android:text="@string/person_intro"/>

        </android.support.v7.widget.CardView>
    </android.support.v4.widget.NestedScrollView>
```
> ``app:behavior_overlapTop="30dp"``, The amount that the scrolling view should overlap the bottom of any AppBarLayout. 使ScrollView压在AppBarLayout上面一段长度.

添加ToolBar, 滚动结束的效果图.
```
    <android.support.v7.widget.Toolbar
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize"
        android:background="@color/colorPrimary"
        app:layout_anchor="@id/main_fl_title"
        app:theme="@style/ThemeOverlay.AppCompat.Dark">

        <LinearLayout
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:orientation="horizontal">

            <Space
                android:layout_width="@dimen/image_final_width"
                android:layout_height="@dimen/image_final_width" />

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="match_parent"
                android:gravity="center_vertical"
                android:text="@string/person_name"
                android:textColor="@android:color/white"
                android:textSize="20sp"/>

        </LinearLayout>

    </android.support.v7.widget.Toolbar>
```

> ToolBar是工具栏, 滑动完成时, 在最上部显示. ``Space``预留头像的位置.

圆形头像CircleImageView显示(使用第三方库)
```
    <de.hdodenhof.circleimageview.CircleImageView
        android:layout_width="120dp"
        android:layout_height="120dp"
        android:layout_gravity="center"
        android:src="@drawable/small"
        app:border_color="@android:color/white"
        app:border_width="2dp"
        app:layout_behavior=".AvatarImageBehavior" />
```
> 头像控件的滑动行为是自定义的类, 具体行为参考注释. 判断起始和终止位置, child和dependency视图联动, 这里是图像视图和Toolbar联动.

```
/**
 * 图片控件位置动画
 *
 * @author wangchenlong
 */
@SuppressWarnings("unused")
public class AvatarImageBehavior extends CoordinatorLayout.Behavior<CircleImageView> {

    private final static float MIN_AVATAR_PERCENTAGE_SIZE = 0.3f;
    private final static int EXTRA_FINAL_AVATAR_PADDING = 80;

    private int mStartYPosition; // 起始的Y轴位置
    private int mFinalYPosition; // 结束的Y轴位置
    private int mStartHeight; // 开始的图片高度
    private int mFinalHeight; // 结束的图片高度
    private int mStartXPosition; // 起始的X轴高度
    private int mFinalXPosition; // 结束的X轴高度
    private float mStartToolbarPosition; // Toolbar的起始位置

    private final Context mContext;
    private float mAvatarMaxSize;

    public AvatarImageBehavior(Context context, AttributeSet attrs) {
        mContext = context;
        init();
    }

    private void init() {
        bindDimensions();
    }

    private void bindDimensions() {
        mAvatarMaxSize = mContext.getResources().getDimension(R.dimen.image_width);
    }


    @Override
    public boolean layoutDependsOn(CoordinatorLayout parent, CircleImageView child, View dependency) {
        // 依赖Toolbar控件
        return dependency instanceof Toolbar;
    }

    @Override
    public boolean onDependentViewChanged(CoordinatorLayout parent, CircleImageView child, View dependency) {

        // 初始化属性
        shouldInitProperties(child, dependency);

        // 最大滑动距离: 起始位置-状态栏高度
        final int maxScrollDistance = (int) (mStartToolbarPosition - getStatusBarHeight());

        // 滑动的百分比
        float expandedPercentageFactor = dependency.getY() / maxScrollDistance;

        // Y轴距离
        float distanceYToSubtract = ((mStartYPosition - mFinalYPosition)
                * (1f - expandedPercentageFactor)) + (child.getHeight() / 2);

        // X轴距离
        float distanceXToSubtract = ((mStartXPosition - mFinalXPosition)
                * (1f - expandedPercentageFactor)) + (child.getWidth() / 2);

        // 高度减小
        float heightToSubtract = ((mStartHeight - mFinalHeight) * (1f - expandedPercentageFactor));

        // 图片位置
        child.setY(mStartYPosition - distanceYToSubtract);
        child.setX(mStartXPosition - distanceXToSubtract);

        // 图片大小
        CoordinatorLayout.LayoutParams lp = (CoordinatorLayout.LayoutParams) child.getLayoutParams();
        lp.width = (int) (mStartHeight - heightToSubtract);
        lp.height = (int) (mStartHeight - heightToSubtract);
        child.setLayoutParams(lp);

        return true;
    }

    /**
     * 初始化动画值
     *
     * @param child      图片控件
     * @param dependency ToolBar
     */
    private void shouldInitProperties(CircleImageView child, View dependency) {

        // 图片控件中心
        if (mStartYPosition == 0)
            mStartYPosition = (int) (child.getY() + (child.getHeight() / 2));

        // Toolbar中心
        if (mFinalYPosition == 0)
            mFinalYPosition = (dependency.getHeight() / 2);

        // 图片高度
        if (mStartHeight == 0)
            mStartHeight = child.getHeight();

        // Toolbar缩略图高度
        if (mFinalHeight == 0)
            mFinalHeight = mContext.getResources().getDimensionPixelOffset(R.dimen.image_final_width);

        // 图片控件水平中心
        if (mStartXPosition == 0)
            mStartXPosition = (int) (child.getX() + (child.getWidth() / 2));

        // 边缘+缩略图宽度的一半
        if (mFinalXPosition == 0)
            mFinalXPosition = mContext.getResources().getDimensionPixelOffset(R.dimen.abc_action_bar_content_inset_material) + (mFinalHeight / 2);

        // Toolbar的起始位置
        if (mStartToolbarPosition == 0)
            mStartToolbarPosition = dependency.getY() + (dependency.getHeight() / 2);
    }

    // 获取状态栏高度
    public int getStatusBarHeight() {
        int result = 0;
        int resourceId = mContext.getResources().getIdentifier("status_bar_height", "dimen", "android");

        if (resourceId > 0) {
            result = mContext.getResources().getDimensionPixelSize(resourceId);
        }
        return result;
    }
}
```

添加需要使用的id, ``activity_main.xml``如下
```
<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="false"
    tools:context=".MainActivity">

    <android.support.design.widget.AppBarLayout
        android:id="@+id/main_abl_app_bar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar">

        <android.support.design.widget.CollapsingToolbarLayout
            android:layout_width="match_parent"
            android:layout_height="450dp"
            app:layout_scrollFlags="scroll|exitUntilCollapsed|snap">

            <ImageView
                android:id="@+id/main_iv_placeholder"
                android:layout_width="match_parent"
                android:layout_height="300dp"
                android:scaleType="centerCrop"
                android:src="@drawable/large"
                app:layout_collapseMode="parallax" />

            <FrameLayout
                android:id="@+id/main_fl_title"
                android:layout_width="match_parent"
                android:layout_height="150dp"
                android:layout_gravity="bottom|center_horizontal"
                android:background="@color/colorPrimary"
                app:layout_collapseMode="parallax">

                <LinearLayout
                    android:id="@+id/main_ll_title_container"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_gravity="center"
                    android:orientation="vertical">

                    <TextView
                        android:layout_marginTop="@dimen/title_margin"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_gravity="center_horizontal"
                        android:gravity="bottom|center"
                        android:text="@string/person_name"
                        android:textColor="@android:color/white"
                        android:textSize="30sp"/>

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_gravity="center_horizontal"
                        android:layout_marginTop="4dp"
                        android:text="@string/person_title"
                        android:textColor="@android:color/white" />
                </LinearLayout>
            </FrameLayout>
        </android.support.design.widget.CollapsingToolbarLayout>
    </android.support.design.widget.AppBarLayout>

    <android.support.v4.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="none"
        app:behavior_overlapTop="30dp"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">

        <android.support.v7.widget.CardView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_margin="8dp"
            app:cardElevation="8dp"
            app:contentPadding="16dp">

            <TextView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:lineSpacingExtra="8dp"
                android:text="@string/person_intro" />

        </android.support.v7.widget.CardView>

    </android.support.v4.widget.NestedScrollView>

    <android.support.v7.widget.Toolbar
        android:id="@+id/main_tb_toolbar"
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize"
        android:background="@color/colorPrimary"
        app:layout_anchor="@id/main_fl_title"
        app:theme="@style/ThemeOverlay.AppCompat.Dark">

        <LinearLayout
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:orientation="horizontal">

            <Space
                android:layout_width="@dimen/image_final_width"
                android:layout_height="@dimen/image_final_width" />

            <TextView
                android:id="@+id/main_tv_title"
                android:layout_width="wrap_content"
                android:layout_height="match_parent"
                android:layout_marginLeft="8dp"
                android:gravity="center_vertical"
                android:text="@string/person_name"
                android:textColor="@android:color/white"
                android:textSize="20sp"
                android:visibility="invisible"/>

        </LinearLayout>

    </android.support.v7.widget.Toolbar>

    <de.hdodenhof.circleimageview.CircleImageView
        android:layout_width="@dimen/image_width"
        android:layout_height="@dimen/image_width"
        android:layout_gravity="center"
        android:src="@drawable/small"
        app:border_color="@android:color/white"
        app:border_width="2dp"
        app:layout_behavior=".AvatarImageBehavior" />

</android.support.design.widget.CoordinatorLayout>
```

效果
![页面](http://upload-images.jianshu.io/upload_images/749674-71735cefe17a4966.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 3. 逻辑
在onCreate里面设置滑动逻辑, 设置两个动画: 监听AppBar的滑动, 处理Toolbar和Title的显示; 自动滑动效果.
```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ButterKnife.bind(this);

        mTbToolbar.setTitle("");

        // AppBar的监听
        mAblAppBar.addOnOffsetChangedListener(new AppBarLayout.OnOffsetChangedListener() {
            @Override
            public void onOffsetChanged(AppBarLayout appBarLayout, int verticalOffset) {
                int maxScroll = appBarLayout.getTotalScrollRange();
                float percentage = (float) Math.abs(verticalOffset) / (float) maxScroll;
                handleAlphaOnTitle(percentage);
                handleToolbarTitleVisibility(percentage);
            }
        });

        initParallaxValues(); // 自动滑动效果
    }
```

根据滑动百分比, 设置Title和Toolbar的显示与消失, 使用Alpha动画.
```
    // 处理ToolBar的显示
    private void handleToolbarTitleVisibility(float percentage) {
        if (percentage >= PERCENTAGE_TO_SHOW_TITLE_AT_TOOLBAR) {
            if (!mIsTheTitleVisible) {
                startAlphaAnimation(mTvToolbarTitle, ALPHA_ANIMATIONS_DURATION, View.VISIBLE);
                mIsTheTitleVisible = true;
            }
        } else {
            if (mIsTheTitleVisible) {
                startAlphaAnimation(mTvToolbarTitle, ALPHA_ANIMATIONS_DURATION, View.INVISIBLE);
                mIsTheTitleVisible = false;
            }
        }
    }

    // 控制Title的显示
    private void handleAlphaOnTitle(float percentage) {
        if (percentage >= PERCENTAGE_TO_HIDE_TITLE_DETAILS) {
            if (mIsTheTitleContainerVisible) {
                startAlphaAnimation(mLlTitleContainer, ALPHA_ANIMATIONS_DURATION, View.INVISIBLE);
                mIsTheTitleContainerVisible = false;
            }
        } else {
            if (!mIsTheTitleContainerVisible) {
                startAlphaAnimation(mLlTitleContainer, ALPHA_ANIMATIONS_DURATION, View.VISIBLE);
                mIsTheTitleContainerVisible = true;
            }
        }
    }

    // 设置渐变的动画
    public static void startAlphaAnimation(View v, long duration, int visibility) {
        AlphaAnimation alphaAnimation = (visibility == View.VISIBLE)
                ? new AlphaAnimation(0f, 1f)
                : new AlphaAnimation(1f, 0f);

        alphaAnimation.setDuration(duration);
        alphaAnimation.setFillAfter(true);
        v.startAnimation(alphaAnimation);
    }
```

自动滑动动画, 当到一定比例时展开或关闭.
```
    // 设置自动滑动的动画效果
    private void initParallaxValues() {
        CollapsingToolbarLayout.LayoutParams petDetailsLp =
                (CollapsingToolbarLayout.LayoutParams) mIvPlaceholder.getLayoutParams();

        CollapsingToolbarLayout.LayoutParams petBackgroundLp =
                (CollapsingToolbarLayout.LayoutParams) mFlTitleContainer.getLayoutParams();

        petDetailsLp.setParallaxMultiplier(0.9f);
        petBackgroundLp.setParallaxMultiplier(0.3f);

        mIvPlaceholder.setLayoutParams(petDetailsLp);
        mFlTitleContainer.setLayoutParams(petBackgroundLp);
    }
```

最终逻辑
```
public class MainActivity extends AppCompatActivity {

    // 控制ToolBar的变量
    private static final float PERCENTAGE_TO_SHOW_TITLE_AT_TOOLBAR = 0.9f;
    private static final float PERCENTAGE_TO_HIDE_TITLE_DETAILS = 0.3f;

    private static final int ALPHA_ANIMATIONS_DURATION = 200;

    private boolean mIsTheTitleVisible = false;
    private boolean mIsTheTitleContainerVisible = true;

    @Bind(R.id.main_iv_placeholder)
    ImageView mIvPlaceholder; // 大图片

    @Bind(R.id.main_ll_title_container)
    LinearLayout mLlTitleContainer; // Title的LinearLayout

    @Bind(R.id.main_fl_title)
    FrameLayout mFlTitleContainer; // Title的FrameLayout

    @Bind(R.id.main_abl_app_bar)
    AppBarLayout mAblAppBar; // 整个可以滑动的AppBar

    @Bind(R.id.main_tv_toolbar_title)
    TextView mTvToolbarTitle; // 标题栏Title

    @Bind(R.id.main_tb_toolbar)
    Toolbar mTbToolbar; // 工具栏

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ButterKnife.bind(this);

        mTbToolbar.setTitle("");

        // AppBar的监听
        mAblAppBar.addOnOffsetChangedListener(new AppBarLayout.OnOffsetChangedListener() {
            @Override
            public void onOffsetChanged(AppBarLayout appBarLayout, int verticalOffset) {
                int maxScroll = appBarLayout.getTotalScrollRange();
                float percentage = (float) Math.abs(verticalOffset) / (float) maxScroll;
                handleAlphaOnTitle(percentage);
                handleToolbarTitleVisibility(percentage);
            }
        });

        initParallaxValues(); // 自动滑动效果
    }

    // 设置自动滑动的动画效果
    private void initParallaxValues() {
        CollapsingToolbarLayout.LayoutParams petDetailsLp =
                (CollapsingToolbarLayout.LayoutParams) mIvPlaceholder.getLayoutParams();

        CollapsingToolbarLayout.LayoutParams petBackgroundLp =
                (CollapsingToolbarLayout.LayoutParams) mFlTitleContainer.getLayoutParams();

        petDetailsLp.setParallaxMultiplier(0.9f);
        petBackgroundLp.setParallaxMultiplier(0.3f);

        mIvPlaceholder.setLayoutParams(petDetailsLp);
        mFlTitleContainer.setLayoutParams(petBackgroundLp);
    }

    // 处理ToolBar的显示
    private void handleToolbarTitleVisibility(float percentage) {
        if (percentage >= PERCENTAGE_TO_SHOW_TITLE_AT_TOOLBAR) {
            if (!mIsTheTitleVisible) {
                startAlphaAnimation(mTvToolbarTitle, ALPHA_ANIMATIONS_DURATION, View.VISIBLE);
                mIsTheTitleVisible = true;
            }
        } else {
            if (mIsTheTitleVisible) {
                startAlphaAnimation(mTvToolbarTitle, ALPHA_ANIMATIONS_DURATION, View.INVISIBLE);
                mIsTheTitleVisible = false;
            }
        }
    }

    // 控制Title的显示
    private void handleAlphaOnTitle(float percentage) {
        if (percentage >= PERCENTAGE_TO_HIDE_TITLE_DETAILS) {
            if (mIsTheTitleContainerVisible) {
                startAlphaAnimation(mLlTitleContainer, ALPHA_ANIMATIONS_DURATION, View.INVISIBLE);
                mIsTheTitleContainerVisible = false;
            }
        } else {
            if (!mIsTheTitleContainerVisible) {
                startAlphaAnimation(mLlTitleContainer, ALPHA_ANIMATIONS_DURATION, View.VISIBLE);
                mIsTheTitleContainerVisible = true;
            }
        }
    }

    // 设置渐变的动画
    public static void startAlphaAnimation(View v, long duration, int visibility) {
        AlphaAnimation alphaAnimation = (visibility == View.VISIBLE)
                ? new AlphaAnimation(0f, 1f)
                : new AlphaAnimation(1f, 0f);

        alphaAnimation.setDuration(duration);
        alphaAnimation.setFillAfter(true);
        v.startAnimation(alphaAnimation);
    }
}
```

[Github下载地址](https://github.com/SpikeKing/TestCoordinatorLayout.git)

这样联动效果就已经显示出来了, 在AppBar中Toolbar和Title之间的关系, 符合Material的风格, 给用户更多的体验. 既然已经集成到HelloWorld中, 说明Android是极力推荐使用CoordinatorLayout这种风格的, 那我们就Enjoy it吧.
