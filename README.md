# Design新控件
谷歌在推出Android5.0的同时推出了全新的设计Material Design，谷歌为了给我们提供更加规范的MD设计风格的控件，在2015年IO大会上推出了Design支持包，Design常用的新控件有下面几种。
## 目录
* [**1.官方侧滑菜单DrawerLayout**](#1)
* [**2.导航栏NavigationView**](#2)
* [**3.AppbarLayout**](#3)
* [**4.CollapsingToolbarLayout**](#4)
* [**5.TextInputLayout**](#5)
* [**6.SnackBar**](#6)
* [**6.FloatingActionButton**](#7)
* [**7.CoordinatorLayout**](#8)
## 1
## 官方侧滑菜单DrawerLayout 

![imgs](https://github.com/bux-git/MeterialDesignStudy/raw/master/imges/drawerlayout01.gif)     

__一.概念__    
    DrawerLayout其实是一个布局控件，跟LinearLayout等控件是一种东西，但是drawerLayout带有滑动的功能。只要按照drawerLayout的规定布局方式写完布局，就能有侧滑的效果

__二.使用__    
DrawerLayout分为侧边菜单和主内容区两部分，侧边菜单可以根据手势展开与隐藏（drawerLayout自身特性），主内容区的内容可以随着菜单的点击而变化（这需要使用者自己实现）    
```
 <android.support.v4.widget.DrawerLayout
        android:id="@+id/dl_content"
        android:layout_width="match_parent"
        android:layout_height="match_parent">


        <android.support.v7.widget.RecyclerView
            android:id="@+id/rl_content"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="@android:color/holo_red_light">
        </android.support.v7.widget.RecyclerView>


        <android.support.design.widget.NavigationView
            android:id="@+id/ng_view"
            android:layout_width="200dp"
            android:layout_height="match_parent"
            android:layout_gravity="start"
            app:headerLayout="@layout/main_header_layout"
            app:menu="@menu/main_nav_menu">
        </android.support.design.widget.NavigationView>
    </android.support.v4.widget.DrawerLayout>
```
    其中：DrawerLayout最好为界面的根布局，官网是这样说的，否则可能会出现触摸事件被屏蔽的问题；
    主内容区的布局代码要放在侧滑菜单布局的前面, 因为 XML 顺序意味着按 z 序（层叠顺序）排序，并且抽屉式导航栏必须位于内容顶部；
    侧滑菜单部分的布局（NavigationView）必须设置layout_gravity属性，他表示侧滑菜单是在左边还是右边，而且如果不设置在打开关闭抽屉的时候会报错，
    设置了layout_gravity="start/left"的视图才会被认为是侧滑菜单
__三.DrawerLayout常用方法__  

    /**
      * Adds the specified listener to the list of listeners that will be notified of drawer events.
      *将指定的监听添加到DrawerLayout监听列表中
      * @param listener Listener to notify when drawer events occur.
      * @see #removeDrawerListener(DrawerListener)
    */
    addDrawerListener(@NonNull DrawerListener listener) 
    
    //移除对应的监听
    removeDrawerListener(DrawerListener)
    
    //关闭指定的抽屉视图
    closeDrawer()
    
    //打开指定的抽屉视图
    openDrawer()
    
    检查给定的抽屉视图当前是否处于打开状态。 *被认为是“开放”的抽屉必须已经处于完全可见的状态。检查部分可见性使用情况isDrawerVisible()
    isDrawerOpen()
    
    
           drawerLayout.addDrawerListener(new DrawerLayout.DrawerListener() {
                /**
                 * 当抽屉被滑动的时候调用月此方法
                 *
                 * @param drawerView
                 * @param slideOffset 表示滑动的幅度(0-1)
                 */
                @Override
                public void onDrawerSlide(View drawerView, float slideOffset) {
    
                }
    
                /**
                 * 当抽屉完全打开时调用
                 * @param drawerView
                 */
                @Override
                public void onDrawerOpened(View drawerView) {
    
                }
    
                /**
                 * 当抽屉完全关闭时调用
                 * @param drawerView
                 */
                @Override
                public void onDrawerClosed(View drawerView) {
    
                }
    
                /**
                 * 当抽屉运动状态改变的时候被调用
                 * 状态值分别为 STATE_IDLE  0 抽屉处于闲置状态。没有动画正在进行中。处于打开或者关闭状态
                 *  STATE_DRAGGING 1 表示用户当前正在拖动抽屉。
                 *  STATE_SETTLING 2 表示抽屉滑动到了关闭或者打开的位置
                 *  当用户滑动抽屉时 抽屉此时处于STATE_DRAGGING 滑动到刚好关闭或者打开后是STATE_SETTLING状态 随后状态会变为STATE_IDLE 闲置状态
                 * @param newState
                 */
                @Override
                public void onDrawerStateChanged(int newState) {
                    Log.d(TAG, "onDrawerStateChanged: "+newState);
                }
            });
    
__四.ActionBarDrawerToggle与ToolBar__    

    ActionBarDrawerToggle实现了DrawerListener，所以他能做DrawerListener可以做的任何事情，同时他还能将drawerLayout的展开和隐藏与Toolbar的app 图标关联起来，
    点击图标的时候还能展开或者隐藏菜单
    
    将抽屉滑动与Toolbar图标关联
    1. mToolbar = (Toolbar) findViewById(R.id.toolbar);
       setSupportActionBar(mToolbar);
    2.  
     初始化ActionBarDrawerToggle
     ActionBarDrawerToggle toggle = new ActionBarDrawerToggle(this, drawerLayout, mToolbar, R.string.open_string, R.string.close_string)
    
    3.将ActionBarDrawerToggle添加到DrawerLayout监听列表中
      drawerLayout.addDrawerListener(toggle);
      
    4.将 ActionBarDrawerToggle与Toolbar图标关联
      toggle.syncState();
      这样在抽屉打开关闭时 Toolbar图标将会变动，且点击图标时可以切换抽屉的打开关闭状态
      
      同时一下主题可以设置图标的颜色
        <style name="AppTheme">
              <item name="drawerArrowStyle">@style/DrawerArrowStyle</item>
          </style>
      
          <style name="DrawerArrowStyle" parent="Widget.AppCompat.DrawerArrowToggle">
              <item name="spinBars">true</item>
              <item name="color">@android:color/holo_red_light</item>
          </style>

## 2
## NavigationView

__一.概念__    
    NavigationView顾名思义是指导航菜单栏,一般配合DrawerLayout使用作为侧滑菜单栏  
__二.使用__    
    NavigationView需要接收几个必要的参数、一个用于显示头部的布局app:headerLayout="@layout/main_header_layout"（可选） 以及用于建立导向选项的菜单app:menu="@menu/main_nav_menu"，这些都设置完之后，你就只添加监听选中事件的listener就行了。   
    其中app:menu配置的是一个菜单文件    
    
    <menu xmlns:android="http://schemas.android.com/apk/res/android">
        <group android:id="@+id/group1"
               android:checkableBehavior="single">
            <item
                android:id="@+id/app_bar"
                android:icon="@android:drawable/ic_delete"
                android:title="AppbarActivity"></item>
        </group>
    
        <group android:id="@+id/group2"
               android:checkableBehavior="single">
            <item
                android:icon="@android:drawable/ic_menu_save"
                android:title="Start"></item>
        </group>
    <item android:id="@+id/sub1" android:title="sub item">
        <menu>
            <item
                android:icon="@android:drawable/ic_menu_save"
                android:title="Start"></item>
        </menu>
    </item>
    </menu>
__其中:__     
        __1.group表示分组 checkableBehavior用来设置item选中模式 有3个值 表示选中状态 single单选 all多选 none 默认   
           item可以表示一个子项 也可在其中加入menu添加子菜单来实现带有头部的分组效果  
         每个group 和menu子菜单都会在顶部产生移到横线__    
         
__2.设置item选中事件__
        
        
         mNavigationView.setNavigationItemSelectedListener(new NavigationView.OnNavigationItemSelectedListener() {
                    @Override
                    public boolean onNavigationItemSelected(@NonNull MenuItem item) {
                        item.setChecked(true);
                        drawerLayout.closeDrawer(Gravity.LEFT);
                        return true;
                    }
                });
                
__注意：这样只可以设置group中的item选中状态__   
__item子菜单设置选中状态__   

            1.首先设置子菜单item android:checkable="true"
            2.public boolean onNavigationItemSelected(@NonNull MenuItem item) {
                    item.setChecked(true);
                     if (mPreMenuItem != null) mPreMenuItem.setChecked(false);
                     item.setChecked(true);
                      drawerLayout.closeDrawers();
                      mPreMenuItem = item;
                      return true;
             }
         
__三.常用属性__

    app:itemIconTint="" 修改图标颜色
    app:itemBackground="" item背景颜色
    app:itemTextColor=""  item 文字颜色
## 3
#### AppBarLayout
![imgs](https://github.com/bux-git/MeterialDesignStudy/raw/master/imges/appbarlayout01.gif)  
__一.概念__    

    AppBarLayout继承自LinearLayout，布局方向为垂直方向。所以你可以把它当成垂直布局的LinearLayout来使用。
    AppBarLayout是在LinearLayou上加了一些材料设计的概念，它可以让你定制当某个可滚动View的滚动手势发生变化时，其内部的子View实现何种动作   
__解释:__  
__1.AppBarLayout内部子View可以和一个可滚动的View的滑动事件产生关联，从而使AppBarLayout内部的子View执行相关联的滑动动作，   
内部子View可以是Toolbar、任何View或者布局__   
>注意：AppbarLayout 严重依赖于CoordinatorLayout，必须用于CoordinatorLayout 的直接子View，如果你将AppbarLayout 放在其他的ViewGroup 里面，那么它的这些功能是无效的

__2.AppBarLayout如何与可滚动的View关联:__    

    <android.support.v4.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        
        app:layout_behavior="@string/appbar_scrolling_view_behavior">
       <!--将你的内容放在这里-->
    </android.support.v4.widget.NestedScrollView>

>属性：app:layout_behavior="@string/appbar_scrolling_view_behavior"是CoordinatorLayout的布局属性,它能够将此View与AppBarLayout关联，指定Behavior的，appbar_scrolling_view_behavior对应的类的名称是：android.support.design.widget.AppBarLayout$ScrollingViewBehavior

__3.AppBarLayout可以与那些View关联__   
>__3.1,根据概念首先这个View必须是可以滚动的如ScrollView RecyclerView等     
3.2,还有一个就是此View必须实现NestedScrollingChild接口__
        
    所以ScrollView ListView GridView是不能直接和AppBarLayout联合使用的，需要自己实现NestedScrollingChild接口后才行
    已经实现此接口的如：RecyclerView,NestedScrollView等

__4.AppBarLayout内部子View如何执行动作__
>__1.内部的子View通过在布局中加app:layout_scrollFlags设置执行的动作__   
>__layout_scrollFlagsyou 有如下几个值：__  

>>* __1、 scroll ,子View 添加layout_scrollFlags属性 的值scroll 时，这个View将会随着可滚动View（如：NestedScrollView,NestedScrollView 来代替可滚动的View ）一起滚动，就好像子View 是属于ScrollView的一部分一样。__

>>* __2、 enterAlways ,子View 添加layout_scrollFlags属性 的值有enterAlways 时, NestedScrollView 滑动时，子View将剥夺滑动事件先执行动作后 ，NestedScrollView 再滑动。
注意：要与scroll 搭配使用，否者是不能滑动的。__

>>* __3、 enterAlwaysCollapsed ,必须配合scroll|enterAlways 一起使用, enterAlwaysCollapsed 是对enterAlways 的补充，
NestedScrollView 向上滑动和Scroll，enterAlways效果一样 ，向下滑动时 滑动View先下滑到最小高度 minHeight（最小高度）指定的，然后让NestedScrollView滑动到顶点后，滑动View再继续下滑到最大值。__  
  
>>* __4、exitUntilCollapsed,必须配合scroll 当NestedScrollView向上滑动时，滑动View先响应滑动事件，滑动至最小高度，(也就是通过minHeight 设置的最小高度）后，就固定不动了，再把滑动事件交给 NestedScrollView 继续滑动。__   

>>* __5、snap,意思是：会给滑动view的滑动事件添加一个自动滚动属性，   
在滚动结束后，view滑动到部分可见时，如果隐藏区域比显示区域大则它将滚动离开屏幕，显示区域比影藏区域大，它将自动滚动到完全显示。必须配合scroll使用__     

__5.app:layout_scrollInterpolator属性指定滚动动画效果的插值器.__      
   
__6.AppBarLayout常用方法__
>* addOnOffsetChangedListener 当AppbarLayout 的偏移发生改变的时候回调，也就是子View滑动。

>* getTotalScrollRange 返回AppbarLayout 所有子View的滑动范围

>* removeOnOffsetChangedListener 移除监听器

>* setExpanded (boolean expanded, boolean animate)设置AppbarLayout 是展开状态还是折叠状态，animate 参数控制切换到新的状态时是否需要动画

>* setExpanded (boolean expanded) 设置AppbarLayout 是展开状态还是折叠状态,默认有动画
  
__特殊场景__
>禁止单独触摸AppBarLayout而滑动展开AppBarLayout

    CoordinatorLayout.LayoutParams params = (CoordinatorLayout.LayoutParams) appBarLayout.getLayoutParams();
     AppBarLayout.Behavior behavior = (AppBarLayout.Behavior) params.getBehavior();
     
     behavior.setDragCallback(new AppBarLayout.Behavior.DragCallback() {
         @Override
         public boolean canDrag(@NonNull AppBarLayout appBarLayout) {
             return false;
         }
     });
     通过总是返回 false ，您滚动 view 不会再由 ABL 控制。
     
     注︰之前调用此你应该检查， ViewCompat.isLaidOut(appBarLayout) ，否则为 params.getBehavior() 将返回 null。
## 4
## CollapsingToolbarLayout
`[images](https://github.com/bux-git/MeterialDesignStudy/raw/master/imges/collspasingtoolbarlayout01.gif1)`
__一.概念__    
CollapsingToolbarLayout是用来对Toolbar进行再次包装的ViewGroup，主要是用于实现折叠的App Bar效果。      
它需要作为AppBarLayout的直接子View，并且需要作为AppBarLayout的关联滑动View   

CollapsingToolbarLayout包含以下功能：  

* 1.Collapsing title(折叠标题) 当布局全部可见的时候，title 是最大的，当布局开始滑出屏幕，title 将变得越来越小，你可以通过setTitle（CharSequence） 来设置要显示的标题


    >注意：Toolbar 和CollapsingToolbarLayout 同时设置了title时，不会显示Toolbartitle而是显示CollapsingToolbarLayout 的title，如果要显示Toolbar 的title，你可一在代码中添加如下代码：collapsingToolbarLayout.setTitle("");
    
* 2.Content scrim(内容纱布) 当CollapsingToolbarLayout滑动到一个确定的阀值时将显示或者隐藏内容纱布，可以通过setContentScrim(Drawable)来设置纱布的图片。
 
 >提醒：纱布可以是图片也可以是颜色色值，如果要显示颜色，在xml 布局文件中用contentScrim属性添加，代码如下：
 app:contentScrim="@color/colorPrimary" 
 
* 3.Status bar scrim（状态栏纱布） 当CollapsingToolbarLayout滑动到一个确定的阀值时，状态栏显示或隐藏纱布，你可以通过setStatusBarScrim(Drawable)来设置纱布图片。
 
 >注意：同内容纱布一样，状态栏纱布可以是图片也可以是一个颜色值，如果要显示颜色值，在xml 中用statusBarScrim 属性指定。
 
* 4.Parallax scrolling children（有视差地滚动子View） 让CollapsingToolbarLayout 的子View 可以有视差的滚动，需要在xml中用 添加如下代码：
 
 >app:layout_collapseMode="parallax"
 
* 5.Pinned position children（固定子View的位置）子View可以固定在全局空间内，这对于实现了折叠并且允许通过滚动布局来固定Toolbar 这种情况非常有用。在xml 中将collapseMode设为pin，代码如下：
 
 > app:layout_collapseMode="pin"
 
* 其他属性： 

    >collapsedTitleGravity：折叠时Toolbar标题位置   
    expandedTitleGravity:展开时Toolbar标题位置     
    titleEnabled：滑动时,设置是否应该显示自己的标题。标题将根据滚动偏移而收缩和增长。默认为true

CollapsingToolbarLayout使用时  
>1.作为AppBarLayout的子View并设置滑动关联动作如: app:layout_scrollFlags="scroll|exitUntilCollapsed"  
2.根据需求设置自身标题等一些相关属性     
3.添加Toolbar和其他子View 

## 5
## TextInputLayout
![imgs](https://github.com/bux-git/MeterialDesignStudy/raw/master/imges/textinputlayout01.gif)
__1.概念__  
TextInputLayout 将EditText包裹起来能够辅助EditText实现一些如hint 以浮动标签的形式显示出来，同时可以通过setErrorEnabled(boolean)和setError(CharSequence)来显示错误信息等   
每一个TextInputLayout中只能有一个EditText
  
__XML属性&常用方法__  
  
* counterEnabled 对应方法 setCounterEnabled(boolean)

    >用于设置字符计数器的显示与隐藏，会在布局右下角显示输入字符的进度：1/10这样
    
* counterMaxLength 对应方法 setCounterMaxLength(int)
    
  >设置字符计数器的最大长度。（仅用于设置计数器最大值，并不影响文本实际能输入的最大长度）

* errorEnabled 对应方法 setErrorEnabled(boolean)
    
  >用于设置错误提示是否显示

* hint 对应方法 setHint(CharSequence)
    
  >设置输入框的提示语

* hintAnimationEnabled 对应方法 setHintAnimationEnabled(boolean)
    
  >开启或关闭hint浮动成标签的动画效果
  
* hint 对应方法 setHint(CharSequence)
    
  >设置输入框的提示语
  
* hintEnabled 对应方法 setHintEnabled(boolean)
    
  >开启或关闭hint浮动的功能，设为false的话就和之前的EditText一样，在输入文字后，提示语就消失了
  
* hintTextAppearance 对应方法 setHintTextAppearance(int)
    
  >设置hint的style，字体颜色，字体大小等，可引用系统自带的也可以自定义。若要使用请统一使用，以保证APP体验的统一性
  
__当文本输入类型为密码时，系统提供了一个开关来控制密码是否可见（默认为眼睛👁）。此为design包24.0.2新提供的功能。__

* passwordToggleEnabled 对应方法 setPasswordVisibilityToggleEnabled(boolean)
    
  >控制密码可见开关是否启用。设为false则该功能不启用，密码输入框右侧也没有控制密码可见与否的开关

* passwordToggleDrawable 对应方法 setPasswordVisibilityToggleDrawable(Drawable)
    
  >设置密码可见开关的图标。通常我们会在不同的情况下设定不同的图标，可通过自定义一个selector，根据“state_checked”属性来控制图标的切换
  
* passwordToggleTint 对应方法 setPasswordVisibilityToggleTintList(ColorStateList)
    
  >控制密码可见开关图标的颜色。在开启或关闭的状态下我们可以设定不同的颜色，可通过自定义一个color的selector，根据“state_checked”和“state_selected”属性来控制颜色的切换
  
* hintEnabled 对应方法 setHintEnabled(boolean)
    
  >开启或关闭hint浮动的功能，设为false的话就和之前的EditText一样，在输入文字后，提示语就消失了   
  
[TextInputLayout详解](https://zhuanlan.zhihu.com/p/22402340)  


## 6
## SnackBar
__一.概念__  

>Snackbar 是一种针对操作的轻量级反馈机制，常以一个小的弹出框的形式，出现在手机屏幕下方或者桌面左下方。它们出现在屏幕所有层的最上方，包括浮动操作按钮。
它们会在超时或者用户在屏幕其他地方触摸之后自动消失。Snackbar 可以在屏幕上滑动关闭。当它们出现时，不会阻碍用户在屏幕上的输入，并且也不支持输入。屏幕上同时最多只能现实一个 Snackbar。 

>综上所述:
>1.SnackBar可以自动消失，也可以手动取消   
2.SnackBar可以通过setAction（）来与用户进行交互   
3.通过CallBack我们可以获取SnackBar的状态   

__二.使用方法__  
>Snackbar用法：Snackbarmake(@NonNull View view, @NonNull CharSequence text,@Duration int duration).show();     
__View:__
>>SnackBar显示，它需要有一个View来承载SnackBar会遍历整个View Tree来找到一个合适的View承载SnackBar的View，     
    如果你想要实现上面的动画交互效果的话最好是在布局中包括CoordinatorLayout，假如你的布局中不包括CoordinatorLayout是不会有动画效果的      

>__text:__   
>>SnackBar显示文字

>__duration__
>>有三种状态：  
     Snackbar.LENGTH_SHORT// 短时间显示，然后自动取消   
     Snackbar.LENGTH_LONG// 长时间显示，然后自动取消    
     Snackbar.LENGTH_INDEFINITE// 不消失显示，除非手动取消  
     
     
>__make方法源码：__

    public static Snackbar make(@NonNull View view, @NonNull CharSequence text,
            @Duration int duration) {
            
        Snackbar snackbar = new Snackbar(findSuitableParent(view));
        snackbar.setText(text);
        snackbar.setDuration(duration);
        return snackbar;
        
    }

其实这里面的重点就是Snackbarsnackbar =newSnackbar(findSuitableParent(view));  
  我们可以看到我们传入的view经过了    
    findSuitableParent()方法的包装。  
    
__这个方法主要的作用是：__     

>1.当传入的View不为空时，如果我们在布局中发现了CoordinatorLayout布局，那么返回的View就是CoordinatorLayout；     

>2.如果没有CoordinatorLayout布局，我们就先找到一个id为android.R.id.content的FrameLayout（这个布局是最底层的根布局），    
    将View设置为该FrameLayout；   
    
>3.其他情况下就使用View的Parent布局一直到这个View不为空。    

                //获取实例
                Snackbar snackbar= Snackbar.make(mBtnShow,"测试",Snackbar.LENGTH_LONG);
                //设置右侧点击事件
                snackbar.setAction("编辑", new View.OnClickListener() {
                    @Override
                    public void onClick(View v) {
                        Toast.makeText(SnackBarActivity.this,"点击",Toast.LENGTH_SHORT).show();
                    }
                });
                //设置点击文字颜色
                snackbar.setActionTextColor(getResources().getColor(R.color.black));
                //设置SnackBar 背景颜色
                snackbar.getView().setBackgroundColor(getResources().getColor(R.color.colorAccent));
                //添加显示与消失监听
                snackbar.addCallback(new BaseTransientBottomBar.BaseCallback<Snackbar>() {
                    @Override
                    public void onDismissed(Snackbar transientBottomBar, int event) {
                        super.onDismissed(transientBottomBar, event);
                    }

                    @Override
                    public void onShown(Snackbar transientBottomBar) {
                        super.onShown(transientBottomBar);
                    }
                });
                //显示SnackBar
                snackbar.show();
               
[Snackbars 与 Toasts](http://wiki.jikexueyuan.com/project/material-design/components/snackbars-and-toasts.html)  
[SnackBar使用详解](http://blog.csdn.net/u013320868/article/details/51906896)    

## 7
## FloatingActionButton 
[浅谈FloatingActionButton(悬浮按钮)](http://www.cnblogs.com/xqxacm/p/5852783.html)    
[浮动操作按钮详解](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0718/3197.html)   

  
## 8
## CoordinatorLayout    
__一.概念__    
译为：协调者布局    
>1.首先可以理解Coordinatorlayout是一个FrameLayout升级版本 

>2.重要功能：CoordinatorLayout可以用来协调其子view之间动作的交互     
如：协调滑动控件和AppBarLayout之间的交互等等，  
CoordinatorLayout 实现子View之间的交互是靠Behavior来实现的        


__二.使用__    
CoordinatorLayout子View之间是如何协调的： 
>1.根据前面与：    
       3.AppbarLayout  
       4.CollapsingToolbarLayout  
       6.SnackBar  
       6.FloatingActionButton  
       等使用的情况，子View之间相互协调是通过CoordinatorLayout的布局属性app:layout_behavior来设置的   
       layout_behavior 属性定义了这个View如何和其他View互相交互的行为, 其值填写的是一个class的名字(全称带包名) 
       这个值指定的类必须是 CoordinatorLayout.Behavior<V> 的子类, 我们也可以自定义一个该类继承于它, 以此来写自己想要的交互效果.   
       
__三.Behavior__   
   
__1.概述__
>CoordinatorLayout的诸多功能全部依赖与CoordinatorLayout.Behavior来实现   
通过为CoordinatorLayout的直接子view设置一个Behavior，CoordinatorLayout会遍历一遍自己的直接子View，      
一个一个的调用子view中的Behavio就可以拦截touch events, window insets, measurement, layout, 和 nested scrolling等动作。      
Design Library大量利用了Behaviors来实现你所看到的功能

__2.Behavior的创建__
>2.1、创建behavior,需要继承 [CoordinatorLayout.Behavior](https://developer.android.google.cn/reference/android/support/design/widget/CoordinatorLayout.Behavior.html)
或其子View
>>  

    public class FollowUpDownBehavior extends CoordinatorLayout.Behavior {
    
      public FollowUpDownBehavior(Context context, AttributeSet attrs) {
          super(context, attrs);
  
        }
    }
>>这样就可以将这个Behavior设置给任何View,如果只想设置给某一些特定类型的View则可以传入泛型如:  

    FollowUpDownBehavior extends CoordinatorLayout.Behavior<Button>
__3.设置Behavior__    
设置Behavior一共有3种方式:  
>3.1 在代码中设置Behavior     
>>CoordinatorLayout.LayoutParam中可以存储Behavior布局属性,所以在代码中：

        FollowUpDownBehavior behavior = new FollowUpDownBehavior();
        CoordinatorLayout.LayoutParams layoutParams= (CoordinatorLayout.LayoutParams) view.getLayoutParams();
        view.setLayoutParams(layoutParams);

>3.2 在XML中设置        
>>在xml布局中直接设置属性值    

    <View android:layout_width="50dp"
          android:layout_height="20dp"
          android:background="@color/black"
          app:layout_behavior="com.dqr.www.meterialdesignstudy.coordinatorlayout.behavior.FollowUpDownBehavior"
          app:target="@id/moveView"></View>
>>在XML设置属性，初始化Behavior时，是使用的FollowUpDownBehavior(Context context, AttributeSet attrs)   
两个参数的构造函数,有传入AttributeSet所以可以自定义一些属性然后在xml中设置，Behavior中接收并使用，   
如app:target属性设置一个目标ID   


>3.3 在View类上添加默认的Behavior   
>>在自定义View时我们希望这个View自带一个Behavior，而不需要去另外设置，我们可以在自定义View类上设置    
注释:

    @CoordinatorLayout.DefaultBehavior(BtnTestBehavior.class)
    public class TempView extends View {
    }

__Behavior的功能__     

__1.拦截Touch Events__    
>CoordinatorLayout会在他的onInterceptTouchEvent()中将事件MotionEvent传递到子View的    
Behavior.onInterceptTouchEvent()中，让Behavior也可以拦截触摸事件,   
如果Behavior.onInterceptTouchEvent()返回true,则Behavior.onTouchEvent()将会收到   
后续触摸事件，而View将不会收到后续的触摸事件    

CoordinatorLayout的事件分发过程
>>首先ViewGroup/View 的事件分派, 事件分派是有两个过程的:  [深入理解CoordinatorLayout原文](http://www.jianshu.com/p/26439595ffef)    

>>捕获过程：从根元素到子元素依次调用onInterceptTouchEvent,检测是否有View要拦截触摸事件 
如果有View拦截了立即进入冒泡过程，否则一直传递到最末尾的元素再进入到冒泡过程.   

>>冒泡过程:从底层往上冒泡,一次调用onTouchEvent,如果有View消耗了事件,则不再继续向上传递，否则一直传递 
到根元素.

>CoordinatorLayout事件分发  
>>CoordinatorLayout在他的onInterceptTouchEvent中去遍历所有的子View,并调用子View的Behavior.onInterceptTouchEvent方法 
如果在Behavior.onInterceptTouchEvent方法中返回了true拦截了该事件，则该Behavior就可以在onTouchEvent    
中处理触摸事件，而这个Behavior对应的View将不会收到触摸时间 

>这样的设置可以使得处理例如手势的逻辑可以完全从具体的某个View解耦出来，可以给不同的View设置相同的   
Behavior来获得处理相同手势的功能，代码复用率极高.   

__2.子View之间的依赖__       
>Behaviors的强大之处在于在View之间建立依赖关系－当另一个View改变的时候，你的Behavior会得到一个callback，根据外部条件改变它的功能    

在 CoordinatorLayout.LayoutParams 中定义了一个View是否依赖( dependsOn ) 另一个View:

    boolean dependsOn(CoordinatorLayout parent, View child, View dependency) {
        return dependency == mAnchorDirectChild
                || (mBehavior != null && mBehavior.layoutDependsOn(parent, child, dependency));
    }


Behaviors依赖于View有两种形式:
>>1.在Behavior.layoutDependsOn()中返回true  
>>2.使用CoordinatorLayout的layout_anchor 属性之时。它和layout_anchorGravity 属性结合，可以让你有效的把两个View捆绑在一起。比如，你可以把一个FloatingActionButton锚定在一个AppBarLayout上，那么如果AppBarLayout滚动出屏幕，FloatingActionButton.Behavior将使用隐式的依赖去隐藏FAB    

>View之间关联之后，当依赖View被移除的时候，将会回调Behavior.onDependentViewRemoved() 
 当依赖的View发生变化的时候（比如：调整大小或者重置自己的position），得到回调 onDependentViewChanged()  
 我们可以在这2个方法中处理关于自身View的一些事情     
 >>这个把View绑定在一起的能力正是Design Library那些酷炫功能的工作原理   
 －以FloatingActionButton与Snackbar之间的交互为例。FAB的 Behavior依赖于被添加到CoordinatorLayout的Snackbar，.
 然后它使用onDependentViewChanged()  callback来将FAB向上移动，以避免和Snackbar重叠。

CoordinatorLayout的三个子View A B  C之间的依赖可以有如下几种:           
* 可以 多个View同时依赖同一个View: B C 同时依赖A     
* 被依赖的View可以继续依赖其他View A 依赖B  B 依赖C       
* 也可以单独依赖 A 依赖 B          
* 但是不能循环依赖 ：A 依赖B  B 依赖C C依赖A         



__3.嵌套滚动__  
NestedScrolling 是Android提供的一套父View和子View交互滑动机制.     
完成这样的交互 需要父View实现NestedScrollingParent接口,子View实现NestedScrollingChild接口      
同时系统也提供了2辅助类来帮助处理子View和父View交互的大部分逻辑：   
>NestedScrollingParent--->NestedScrollingParentHelper   
>NestedScrollingChild--->NestedScrollingChildHelper     

__NestedScrollingChild & NestedScrollingChildHelper__    

    public interface NestedScrollingChild {  
      
        /** 
         * 设置嵌套滑动是否可用 
         * 
         * @param enabled 
         */  
        public void setNestedScrollingEnabled(boolean enabled);  
      
        /** 
         * 嵌套滑动是否可用 
         * 
         * @return 
         */  
        public boolean isNestedScrollingEnabled();  
      
        /** 
         * 开启整个嵌套滑动流程,通知父View一起处理TouchEvent事件
         * 
         * @param axes 表示方向 有一下两种值 
         *             ViewCompat.SCROLL_AXIS_HORIZONTAL 水平方向滑动 
         *             ViewCompat.SCROLL_AXIS_VERTICAL 纵向滑动 
         * @return 父View是否支持嵌套滑动
         */  
        public boolean startNestedScroll(int axes);  
      
        /** 
         * 结束嵌套滑动流程
         */  
        public void stopNestedScroll();  
      
        /** 
         * 是否有父View 支持 嵌套滑动,  会一层层的往上寻找父View 
         * @return 
         */  
        public boolean hasNestedScrollingParent();  
      
        /** 
         * 向父view汇报滚动情况，包括子view消费的部分和子view没有消费的部分。
         * 如果父view接受了它的滚动参数，进行了部分消费，则这个函数返回true，否则为false。
         * 这个函数一般在子view处理scroll后调用。
           
         * @param dxConsumed x轴上 被父View消费的距离 
         * @param dyConsumed y轴上 被父View消费的距离 
         * @param dxUnconsumed x轴上 未被消费的距离 
         * @param dyUnconsumed y轴上 未被消费的距离 
         * @param offsetInWindow view 的移动距离 
         * @return 
         */  
        public boolean dispatchNestedScroll(int dxConsumed, int dyConsumed,  
                                            int dxUnconsumed, int dyUnconsumed, int[] offsetInWindow);  
      
        /** 
         * 在子View的onInterceptTouchEvent或者onTouch中(一般在MontionEvent.ACTION_MOVE事件里)，
         * 调用该方法将子View此次滑动的距离通知给父View做处理
         * @param dx x 轴上滑动的距离, 相对于上一次事件, 不是相对于 down事件的 那个距离 
         * @param dy y 轴上滑动的距离 
         * @param consumed 数组 输出参数,返回父View消耗掉的滑动长度
         *  如果传入不为null，cosumed[0]表示父View在X方向上消费掉的Scroll距离
         *  cosumes[1]表示父View在Y方向上消费掉的scroll距离
         *  如果这两个值不为0，则子view需要对滚动的量进行一些修正
         * @param offsetInWindow   支持嵌套滑动到额父View 消费 滑动事件后 导致 本 View 的移动距离 
         * @return 如果父View 接收了滚动参数，并进行了消费，则返回true 否则为false
         这个函数一般在子view处理scroll前调用
         */  
        public boolean dispatchNestedPreScroll(int dx, int dy, int[] consumed, int[] offsetInWindow);  
      
        /** 
         * 
         * @param velocityX x 轴上的滑动速度 
         * @param velocityY y 轴上的滑动速度 
         * @param consumed 是否被消费 
         * @return 
         */  
        public boolean dispatchNestedFling(float velocityX, float velocityY, boolean consumed);  
      
        /** 
         * 
         * @param velocityX x 轴上的滑动速度 
         * @param velocityY y 轴上的滑动速度 
         * @return 
         */  
        public boolean dispatchNestedPreFling(float velocityX, float velocityY);  
    } 
使用: 

    public class Child extends ViewGroup implements NestedScrollingChild {  
      
        private NestedScrollingChildHelper mNestedScrollingChildHelper;  
      
        public Child(Context context, AttributeSet attrs) {  
            super(context, attrs);  
            mNestedScrollingChildHelper = new NestedScrollingChildHelper(this);  
        }  
      
        @Override  
        public void setNestedScrollingEnabled(boolean enabled) {  
            mNestedScrollingChildHelper.setNestedScrollingEnabled(enabled);  
        }  
      
        @Override  
        public boolean isNestedScrollingEnabled() {  
            return mNestedScrollingChildHelper.isNestedScrollingEnabled();  
        }  
      
        @Override  
        public boolean startNestedScroll(int axes) {  
            return mNestedScrollingChildHelper.startNestedScroll(axes);  
        }  
      
        @Override  
        public void stopNestedScroll() {  
            mNestedScrollingChildHelper.stopNestedScroll();  
        }  
      
        @Override  
        public boolean hasNestedScrollingParent() {  
            return mNestedScrollingChildHelper.hasNestedScrollingParent();  
        }  
      
        @Override  
        public boolean dispatchNestedScroll(int dxConsumed, int dyConsumed, int dxUnconsumed, int dyUnconsumed, int[] offsetInWindow) {  
            return mNestedScrollingChildHelper.dispatchNestedScroll(dxConsumed, dyConsumed, dxUnconsumed, dyUnconsumed, offsetInWindow);  
        }  
      
        @Override  
        public boolean dispatchNestedPreScroll(int dx, int dy, int[] consumed, int[] offsetInWindow) {  
            return mNestedScrollingChildHelper.dispatchNestedPreScroll(dx, dy, consumed, offsetInWindow);  
        }  
      
        @Override  
        public boolean dispatchNestedFling(float velocityX, float velocityY, boolean consumed) {  
            return mNestedScrollingChildHelper.dispatchNestedFling(velocityX, velocityY, consumed);  
        }  
      
        @Override  
        public boolean dispatchNestedPreFling(float velocityX, float velocityY) {  
            return mNestedScrollingChildHelper.dispatchNestedPreFling(velocityX, velocityY);  
        }  
        
            /** 
             *重写这个方法或者onInterceptTouchEvent
             *在这里面开启嵌套滑动相关方法并处理相关逻辑
             *具体可以参考现有的RecyclerView NestedScrollView 等
             *
             */  
           @Override
            public boolean onTouchEvent(MotionEvent e) { 
                1.startNestedScroll(nestedScrollAxis);
                2.dispatchNestedPreScroll(dx, dy, mScrollConsumed, mScrollOffset);
                3.stopNestedScroll();
             
                return super.onTouchEvent(e);
            }
        
    }  
    
 __NestedScrollingParent & NestedScrollingParentHelper__    
     
     public interface NestedScrollingParent {
         /**
          * 当子view的调用NestedScrollingChild的方法startNestedScroll时,会调用该方法 
          * 该方法决定了当前控件是否能接收到其内部View(直接子View或者子View的子View)滑动时的参数
          * @param child ViewParent 的直接子View 该View 或者是他的子View实现了NestedScrollingChild
          * @param target 实现了NestedScrollingChild的View (在这里如果不涉及多层嵌套的话,child和target)
          * @param nestedScrollAxes 嵌套滚动的滚动方向 Flags consisting of {@link ViewCompat#SCROLL_AXIS_HORIZONTAL},
          *                         {@link ViewCompat#SCROLL_AXIS_VERTICAL} or both
          * @return 是否接受此次嵌套滑动
          */
         public boolean onStartNestedScroll(View child, View target, int nestedScrollAxes);
     
         /**
          * 
          *如果onStartNestedScroll方法返回true,之后就会调用该方法.它是让嵌套滚动在开始滚动之前,让布局容器(viewGroup)或者它的父类执行一些配置的初始化
          * @param child Direct child of this ViewParent containing target
          * @param target View that initiated the nested scroll
          * @param nestedScrollAxes Flags consisting of {@link ViewCompat#SCROLL_AXIS_HORIZONTAL},
          *                         {@link ViewCompat#SCROLL_AXIS_VERTICAL} or both
          * @see #onStartNestedScroll(View, View, int)
          * @see #onStopNestedScroll(View)
          */
         public void onNestedScrollAccepted(View child, View target, int nestedScrollAxes);
     
         /**
          * 当子view调用stopNestedScroll时会调用该方法,停止滚动
          * React to a nested scroll operation ending.
          *
          * <p>Perform cleanup after a nested scrolling operation.
          * This method will be called when a nested scroll stops, for example when a nested touch
          * scroll ends with a {@link MotionEvent#ACTION_UP} or {@link MotionEvent#ACTION_CANCEL} event.
          * Implementations of this method should always call their superclass's implementation of this
          * method if one is present.</p>
          *
          * @param target View that initiated the nested scroll
          */
         public void onStopNestedScroll(View target);
     
         /**
          * 当子view调用dispatchNestedScroll方法时,会调用该方法
          * @param target 实现了NestedScrollingChild的View
          * @param dxConsumed 表示target已经消费的x方向的距离
          * @param dyConsumed 表示target已经消费的y方向的距离
          * @param dxUnconsumed 表示x方向剩下的滑动距离 
          * @param dyUnconsumed 表示y方向剩下的滑动距离 
          */
         public void onNestedScroll(View target, int dxConsumed, int dyConsumed,
                 int dxUnconsumed, int dyUnconsumed);
     
         /**
          * 
          *consumed:表示父布局要消费的滚动距离,consumed[0]和consumed[1]分别表示父布局在x和y方向上消费的距离. 
           当子view调用dispatchNestedPreScroll方法是,会调用该方法
          * @param target 实现了NestedScrollingChild的View
          * @param dx 表示target本次滚动产生的x方向的滚动总距离 
          * @param dy 表示target本次滚动产生的y方向的滚动总距离 
          * @param consumed Output. The horizontal and vertical scroll distance consumed by this parent
          */
         public void onNestedPreScroll(View target, int dx, int dy, int[] consumed);
     
         /**
          * Request a fling from a nested scroll.
          *
          * <p>This method signifies that a nested scrolling child has detected suitable conditions
          * for a fling. Generally this means that a touch scroll has ended with a
          * {@link VelocityTracker velocity} in the direction of scrolling that meets or exceeds
          * the {@link ViewConfiguration#getScaledMinimumFlingVelocity() minimum fling velocity}
          * along a scrollable axis.</p>
          *
          * <p>If a nested scrolling child view would normally fling but it is at the edge of
          * its own content, it can use this method to delegate the fling to its nested scrolling
          * parent instead. The parent may optionally consume the fling or observe a child fling.</p>
          *
          * @param target View that initiated the nested scroll
          * @param velocityX Horizontal velocity in pixels per second
          * @param velocityY Vertical velocity in pixels per second
          * @param consumed true if the child consumed the fling, false otherwise
          * @return true if this parent consumed or otherwise reacted to the fling
          */
         public boolean onNestedFling(View target, float velocityX, float velocityY, boolean consumed);
     
         /**
          * React to a nested fling before the target view consumes it.
          *
          * <p>This method siginfies that a nested scrolling child has detected a fling with the given
          * velocity along each axis. Generally this means that a touch scroll has ended with a
          * {@link VelocityTracker velocity} in the direction of scrolling that meets or exceeds
          * the {@link ViewConfiguration#getScaledMinimumFlingVelocity() minimum fling velocity}
          * along a scrollable axis.</p>
          *
          * <p>If a nested scrolling parent is consuming motion as part of a
          * {@link #onNestedPreScroll(View, int, int, int[]) pre-scroll}, it may be appropriate for
          * it to also consume the pre-fling to complete that same motion. By returning
          * <code>true</code> from this method, the parent indicates that the child should not
          * fling its own internal content as well.</p>
          *
          * @param target View that initiated the nested scroll
          * @param velocityX Horizontal velocity in pixels per second
          * @param velocityY Vertical velocity in pixels per second
          * @return true if this parent consumed the fling ahead of the target view
          */
         public boolean onNestedPreFling(View target, float velocityX, float velocityY);
     
         /**
          * Return the current axes of nested scrolling for this NestedScrollingParent.
          *
          * <p>A NestedScrollingParent returning something other than {@link ViewCompat#SCROLL_AXIS_NONE}
          * is currently acting as a nested scrolling parent for one or more descendant views in
          * the hierarchy.</p>
          *
          * @return Flags indicating the current axes of nested scrolling
          * @see ViewCompat#SCROLL_AXIS_HORIZONTAL
          * @see ViewCompat#SCROLL_AXIS_VERTICAL
          * @see ViewCompat#SCROLL_AXIS_NONE
          */
         public int getNestedScrollAxes();
     }
     
 
 整个嵌套滑动的流程child对应parent:    
 
 子view  | 父view|
 --------- | --------|
 startNestedScroll  | onStartNestedScroll、onNestedScrollAccepted |
 dispatchNestedPreScroll  |onNestedPreScroll  |
 dispatchNestedScroll |onNestedScroll  |
 stopNestedScroll  | onStopNestedScroll |
  未完  

 	
 	
 	
 	
 	
   
   
[自定义Behavior的艺术探索-仿UC浏览器主页](http://www.jianshu.com/p/f7989a2a3ec2#)     
[Android嵌套滑动机制（NestedScrolling）](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0603/2990.html)  
__4.拦截Window Insets__   
__5.拦截Measurement 和 layout__    
[拦截一切的CoordinatorLayout Behavior](http://jcodecraeer.com/a/anzhuokaifa/androidkaifa/2016/0224/3991.html)

### 学习资料               
[Material Design之 AppbarLayout 开发实践总结](http://www.jianshu.com/p/ac56f11e7ce1)    
[玩转AppBarLayout，更酷炫的顶部栏](http://blog.csdn.net/huachao1001/article/details/51558835)    
[CoordinatorLayout, AppBarLayout, CollapsingToolbarLayout使用](http://www.cnblogs.com/mengdd/p/5641187.html)    
[深入理解CoordinatorLayout](http://www.jianshu.com/p/26439595ffef)    
[自定义Behavior的艺术探索-仿UC浏览器主页](http://www.jianshu.com/p/f7989a2a3ec2#)    
[一个神奇的控件——Android CoordinatorLayout与Behavior使用指南](http://www.jianshu.com/p/488283f74e69)    
[ CoordinatorLayout的使用如此简单](http://blog.csdn.net/huachao1001/article/details/51554608)    
[拦截一切的CoordinatorLayout Behavior](http://jcodecraeer.com/a/anzhuokaifa/androidkaifa/2016/0224/3991.html)


[回到顶部](#目录) 