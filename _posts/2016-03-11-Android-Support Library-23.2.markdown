---
layout:     post
title:      "Android Support Library 23.2"
subtitle:   " \"Support and Feature\""
date:       2016-03-11 17:04:00
author:     "Lam"
header-img: "img/post-google.jpg"
tags:
    - Android
    - 翻译
---


[原文链接](http://android-developers.blogspot.com/2016/02/android-support-library-232.html)  
第一次做文章翻译，若有错误请指正。  
  
#### Android Support Library 23.2  
当我们说起Android Support Library的时候，必须要认识到这不仅仅是一个巨大的库，而是一个能向后兼容的[一个完整的库](http://developer.android.com/tools/support-library/features.html)，并且能提供新的特性且不需要最新的系统平台支持。23.2版本在许多现有库存在的基础上增加了一些新的特性。  
  
[原文视频链接(YouTube)](https://youtu.be/7E2lNBM38IE?list=PLWz5rJ2EKKc9e0d55YHgJFHXNZbGHEXJX)  
  
#### Support Vector Drawables and Animated Vector Drawables  
[Vector drawables](https://www.youtube.com/watch?v=wlFVIIstKmA)能让你定义在XML里的矢量图片替换掉大量的png图片资源，在此之前只有Lollipop以及更高的版本才支持的功能，现在只需要通过两个新增的支持库**support-vector-drawable**和**animated-vector-drawable**就能分别实现VectorDrawable（矢量图片）和AnimatedVectorDrawable（矢量图片动画）两项功能。  

[Android Studio 1.4](http://android-developers.blogspot.com/2015/09/android-studio-14.html)引进了[在编译期间生成png图片](https://www.youtube.com/watch?v=8e3I-PYJNHg)来实现对矢量图片的支持。关闭这项功能（从而得到这个库真正的优点和节省空间），你只需要在**build.gradle**文件里面添加 **vectorDrawables.useSupportLibrary = true**  
  
    // Gradle Plugin 2.0+  
    android {  
    	defaultConfig {  
     		vectorDrawables.useSupportLibrary = true  
    	}  
    }  
  
  在Gradle2.0及以上的插件你像这样去写，但如果是Gradle1.5的话你需要这样  
    
    // Gradle Plugin 1.5  
    android {  
    	defaultConfig {  
    		generatedDensities = []  
		}  

		// This is handled for you by the 2.0+ Gradle Plugin  
		aaptOptions {  
			additionalParameters "--no-version-vectors"  
		}  
	}  
  
VectorDrawableCompat最低支持到API 7，AnimatedVectorDrawableCompat最低支持到API 11。主要是因为在Android加载图片的时候，不是所有支持图片id(例如定义在XML文件里面)的地方都能支持适量图片。值得庆幸的是[AppCompat](http://developer.android.com/tools/support-library/features.html)已经增加了许多特性让你更加轻松地去使用新的矢量图片。  
  
首先，当我们在[ImageView](http://developer.android.com/reference/android/widget/ImageView.html)(或者是子类例如[ImageButton](http://developer.android.com/reference/android/widget/ImageButton.html)和[FloatingActionButton](http://developer.android.com/reference/android/support/design/widget/FloatingActionButton.html))，你可以使用新的属性**app:srcCompat**去引用矢量图片（也可以使用[android:src](http://developer.android.com/reference/android/widget/ImageView.html#attr_android:src)去引用其他图片）：
  
	<ImageView  
		android:layout_width="wrap_content"  
		android:layout_height="wrap_content"  
		app:srcCompat="@drawable/ic_add" />  
  
如果你需要在程序运行时改变图片，你可以使用像以前一样的方法[setImageResource()](http://developer.android.com/reference/android/widget/ImageView.html?#setImageResource\(int\))。**使用AppCompat和app:srcCompat是将矢量图片整合到你的app中最简单的方法。**  
  
你会发现在Lollipop中已经不再使用app:srcCompat去引用矢量图片。然而，当矢量图片被另外的drawable容器（例如[StateListDrawable](http://developer.android.com/reference/android/graphics/drawable/StateListDrawable.html)，[InsetDrawable](http://developer.android.com/reference/android/graphics/drawable/InsetDrawable.html)，[LayerDrawable](http://developer.android.com/reference/android/graphics/drawable/LayerDrawable.html)，[LevelListDrawable](http://developer.android.com/reference/android/graphics/drawable/LevelListDrawable.html) 和 [RotateDrawable](http://developer.android.com/reference/android/graphics/drawable/RotateDrawable.html)）所引用时，AppCompat是可以加载这些矢量图片的。正是因为这样，你可以在一些无法使用矢量图片的地方比如[TextView](http://developer.android.com/reference/android/widget/TextView.html)的[android:drawableLeft](http://developer.android.com/reference/android/widget/TextView.html?#attr_android:drawableLeft)通过间接地的方式来使用矢量图片。  
  
#### AppCompat DayNight theme  
AppCompatshif使你的app支持使用矢量图片已经是一个大变化了，但在这个版本里面还增加了一个新的主题：**Theme.AppCompat.DayNight**  
![image](https://3.bp.blogspot.com/-PCq6in0WXBs/Vsyp7EVfSsI/AAAAAAAACmQ/fMHWVrVibf0/s640/image05.png)![image](https://1.bp.blogspot.com/-Ru64P9N2S_M/VsyqB1Fw5gI/AAAAAAAACmU/dYegY5HFn58/s640/image01.png)  
在API 14之前，DayNight主题以及继承的DayNight.NoActionBar，DayNight.DarkActionBar，DayNight.Dialog等，亮度都是相同的。但是对于API 14及以后的设备，使得你的app很轻松的去切换Light和Dark主题，主要是由‘night’来决定是否从Light主题切换到Dark主题。  
  
默认的，是否为‘night’主题是根据系统值（[UiModeManager.getNightMode()](http://developer.android.com/reference/android/app/UiModeManager.html?#getNightMode\(\))）匹配的，但是你可以通过重写[AppCompatDelegate](http://developer.android.com/reference/android/support/v7/app/AppCompatDelegate.html)中的方法来改变这个值。你可以使用静态方法**AppCompatDelegate.setDefaultNightMode()**在整个app中去设置这个默认值（除非进程重启）或者是通过调用[getDelegate()](http://developer.android.com/reference/android/support/v7/app/AppCompatActivity.html?#getDelegate\(\))获取一个**AppCompatDelegate**对象，然后调用**setLocalNightMode()**方法来设置当前的Activity或者Dialog主题。  
  
当你在使用**AppCompatDelegate.MODE_NIGHT_AUTO**的时候，你手机的时间和你最后定位到的位置（如果你开启了定位权限）会被自动的用于切换白天或者黑夜主题，而**MODE_NIGHT_NO**和**MODE_NIGHT_YES**则分别用户设置是否从不或者是一直使用黑夜主题。  
  
当你在使用DayNight主题的时候并且硬编码了一些颜色值会很容易导致一些文字或者图标变得不可读，所以临界值的测试是必须的。如果你的主题使用的是标准的[TextAppearance.AppCompat](http://developer.android.com/reference/android/support/v7/appcompat/R.style.html#TextAppearance_AppCompat)样式（例如[android:textColorPrimary](http://developer.android.com/reference/android/R.attr.html#textColorPrimary)）来设置到你的文字或者颜色中去的话，你会发现他们已经自动更新了。  
  
尽管如此，如果你想要去为你的黑夜模式自定义一些资源文件时，AppCompat会重新加载[黑夜主题的资源文件夹](http://developer.android.com/guide/topics/resources/providing-resources.html#NightQualifier)，使得自定义每一个资源文件成为可能。请考虑使用标准的色彩方案或者是遵循AppCompat所建议的色彩方案，从而让你的app更加轻松的支持这个新的主题。  
  
#### Design Support Library: Bottom Sheets  
[Design Support Library](http://android-developers.blogspot.com/2015/05/android-design-support-library.html)提供了许多[material design](https://www.google.com/design/spec/material-design)样式。这个版本让开发整更加轻松的在他们的app里添加[bottom sheets](https://www.google.com/design/spec/components/bottom-sheets.html)。  
![image](https://4.bp.blogspot.com/-tHhmGm8q1Qs/VsyqSo_IBDI/AAAAAAAACmY/EWy2HbMmGYg/s640/image06.png)  
在[CoordinatorLayout](http://developer.android.com/reference/android/support/design/widget/CoordinatorLayout.html)的子View中增加**BottomSheetBehavior**属性（**app:layout_behavior="android.support.design.widget.BottomSheetBehavior"**），你会自动获取到相应的五个状态之间的转换：  
  
- STATE_COLLAPSED：折叠状态，是默认的，并且是只显示沿着底部布局的一部分。其高度可以通过**app:behavior_peekHeight**属性来控制（默认是0）。
- STATE_DRAGGING：中间状态，当用户正在向上或者向下拖动sheet。
- STATE_SETTLING：设置状态，当sheet被打开并且运行到最终的位置。
- STATE_EXPANDED：展开状态，当sheet被完全打开，且整个sheet显示出来（仅当sheet的高度小于CoordinatorLayout的高度时）或者是整个CoordinatorLayout被充满。
- STATE_HIDDEN：不可见状态（可以通过**app:behavior_hideable**属性来控制），开启后允许用户乡下滑动sheet直到完全关闭。  
  
如果的sheet中存在可滑动的视图，请确保他们能支持嵌套滑动（例如[NestedScrollView](http://developer.android.com/reference/android/support/v4/widget/NestedScrollView.html)，[RecyclerView](http://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)或者API 21+以后的ListView/ScrollView）。  
  
如果你想监听他们的回调状态，你可以设置一个BottomSheetCallback：  

	// The View with the BottomSheetBehavior  
	View bottomSheet = coordinatorLayout.findViewById(R.id.bottom_sheet);  
	BottomSheetBehavior behavior = BottomSheetBehavior.from(bottomSheet);  
	behavior.setBottomSheetCallback(new BottomSheetCallback() {  
		@Override  
		public void onStateChanged(@NonNull View bottomSheet, int newState) {  
		// React to state change  
		}  
		
		@Override  
		public void onSlide(@NonNull View bottomSheet, float slideOffset) {  
		// React to dragging events  
		}  
	});  
	  
这个版本不仅提供了BottomSheetBehavior获取[sheet的回调状态](https://www.google.com/design/spec/components/bottom-sheets.html#bottom-sheets-persistent-bottom-sheets)，而且还提供了BottomSheetDialog和BottomSheetDialogFragment用于覆盖[sheet](https://www.google.com/design/spec/components/bottom-sheets.html#bottom-sheets-modal-bottom-sheets)的使用场景，你只需要简单的替换成[AppCompatDialog](http://developer.android.com/reference/android/support/v7/app/AppCompatDialog.html)或者[AppCompatDialogFragment](http://developer.android.com/reference/android/support/v7/app/AppCompatDialogFragment.html)，就能达到你的dialog使用sheet的主题的效果。  
  
#### Support v4: MediaBrowserServiceCompat  
  
[Support v4](http://developer.android.com/tools/support-library/features.html#v4)为许多支持库提供了基础服务并且支撑了许多新版本框架的特性（例如[number of unique features](http://stackoverflow.com/a/22740525/1676363)）。  
  
在此之前层发布过一个媒体播放的基础类[MediaSessionCompat](https://www.youtube.com/watch?v=FBC1FgWe5X4)，在这个版本的基础上添加了MediaBrowserServiceCompat和MediaBrowserCompat，这个解决方案让支持的版本扩展到API 4以后。通过提供的这个标准的接口，使得[Android汽车](http://developer.android.com/training/auto/audio/index.html)或者是Android Wear上的媒体交互与Service和UI界面的连接更加方便。 
 
#### RecyclerView  

[RecyclerView](http://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)提供了先进灵活的[创建列表和网格](http://developer.android.com/training/material/lists-cards.html#RecyclerView)的基础方式并且支持[animations](https://www.youtube.com/watch?v=imsr8NrIAMs)。这个版本中的LayoutManager API带来了一个十分令人兴奋的心特性：**auto-measurement**！这使得RecyclerView可以根据自身内容的多少来决定自身的大小。这意味着之前的不可用的属性，例如使用WRAP_CONTENT来控制RecyclerView的大小，现在可以使用了。同时你也会发现在LayoutManagers构建的视图如今都支持auto-measurement了。  

由于这样的改变，请确认你之前的item布局属性，之前所忽略的布局属性（例如滑动方向里的MATCH_PARENT），现在他会全部展示。  

如果你自定义了一个LayoutManager但并不是基于LayoutManagers构建的（这是可选的API），你可以调用**setAutoMeasureEnabled(true)**去做一些细微的变化，在Javadoc里有介绍。  

值得注意的是，尽管RecyclerView可以设置子布局的动画，但是不会改变自身的位置区域。如果你想要RecyclerView的位置跟随动画变化而变化，请参考[Transition APIs](http://developer.android.com/training/transitions/overview.html)。  

#### Custom Tabs  

Custom Tabs可以让你的app在保持原有样子的情况下无缝过渡到web内容。伴随着这个版本的发布，你现在可以在底栏旁边增加一个对web内容操作的按钮。  
![image](https://1.bp.blogspot.com/-z_TM7Ch8fE0/VsyqZz2okNI/AAAAAAAACmc/3HT9_R_IhYU/s1600/image04.png)  

伴随这新的方法**addToolbarItem()**，现在你可以在底栏增加五个动作（MAX_TOOLBAR_ITEMS）并且可以在会话被创建的时候就更新他们。跟之前的方法[setToolbarColor()](https://developer.android.com/reference/android/support/customtabs/CustomTabsIntent.Builder.html#setToolbarColor\(int\))类似，你也会发现一个方法**setSecondaryToolbarColor()**来自定义底栏的颜色。  

#### Leanback for Android TV  

Leanback for Android TV提供给你一个工具让你更加方便的[将你的app适配到Android TV里去](https://www.youtube.com/watch?v=yT4ADuZGEVY)。在这个版本里[GuidedStepFragment](http://developer.android.com/reference/android/support/v17/leanback/app/GuidedStepFragment.html)得到了显著的改进。  
 
![image](https://4.bp.blogspot.com/-YmvEulQtB5o/VsyqlGkmHXI/AAAAAAAACmg/DZxERRItP0Q/s640/image02.png)  

最明显的变化应该是引入了两列动作按钮（通过重写onCreateButtonActions()或者调用setButtonActions()）。这使得更容易的得到自己想要的动作而不需要滑动整一个列表（[GuidedAction](http://developer.android.com/reference/android/support/v17/leanback/widget/GuidedAction.html)）。  

提到GuidedAction，有一些新的特性支持更多的输入，包括可编辑的描述（调用descriptionEditable()方法），下拉动作的子动作（subActions()方法），还有就是GuidedDatePickerAction。  

![image](https://4.bp.blogspot.com/-FahHAG7DavY/VszPhjBLnzI/AAAAAAAACm0/i7OXfxFI3-Q/s640/tv_combined_image.png)  

这些组件能让你更加方便的去知道用户的真是想法。  

#### Available Now  

23.2版本的Android Support Library现在已经可以通过你的SDK Manager或者是Android Studio 获取到。现在可以开始是用所有的新特性以及修复一些额外的bug。跟以往一样，可以在[b.android.com](https://code.google.com/p/android/issues/entry)上面提交bug报告或者是在[Android Development Google+ community](https://plus.google.com/communities/105153134372062985968)里联系其他的开发者。  


---  

thanks for reading.  
by lambun at 2016/2/29