#智能管家
##1. 项目架构部署
	- Package
	- Application
	- Activity
	- Drawable
	- Values
	- UtilTools
	- StaticClass

###1.1 Package
	
	- adapter
	- application：项目的统一入口
	- entity
	- fragment
	- service
	- ui
	- utils：工具类
	- view： activity

###1.2 application/BaseApplication：

```java
public class BaseApplication extends Application {
    // 创建
    @Override
    public void onCreate() {
        super.onCreate();
    }
}
```

AndroidManifest.xml中application进行注册

```java
android:name=".application.BaseApplication"
```
###1.3 ui/BaseActivity

```java

/**
 * 主要做的事情：
 * 1. 统一的属性
 * 2. 统一的接口
 * 3. 统一的方法
 */
public class BaseActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
    // 显示返回键
        getSupportActionBar().setDisplayHomeAsUpEnabled(true);
    }
    // 菜单栏操作

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()){
            case android.R.id.home:
                finish();
                break;
        }
        return super.onOptionsItemSelected(item);
    }
}

```

###1.4 utils/StaticClass & utils/UtilTools
		


###1.5 view/

###1.6 Drawable:

	- res/drawable
	- res/drawable-hdpi
	- res/drawable-mdpi
	- res/drawable-xhdpi
	- res/drawable-xxhdpi
	- res/drawable-xxxhdpi

###1.7 res/values-zh & res/values-en
		复制values里的string.xml会自动识别的

##2. 首页框架-TabLayout布局搭建
Md风格控件之一-实现选项卡

```java
compile 'com.android.support:design:24.2.0'
```
Tablayout + 四个fragment + ViewPager
实现选项卡滑动

Tablayout的样式：
	
	1. 标题颜色
	2. 下划线
	3. 默认字体
	4. 选中字体颜色

###2.1 加载远程库：
app\build.gradle

```
dependencies {
    compile 'com.android.support:appcompat-v7:25.1.1'
    testCompile 'junit:junit:4.12'
    //Tablayout
    compile 'com.android.support:design:24.2.0'
}
```
加入后点击同步		
###2.2 编写Tablayout和viewpager

引入命名空间：
```
xmlns:android="http://schemas.android.com/apk/res-auto"
```

在线性布局中放置 Tablayout 和 ViewPager

声明控件及数据：
```java
private Tablayout mTabLayout;
private ViewPager mViewPager;


//Title
private List<String> mTitle;
//Fragment
private List<Fragment> mFragment;
//fab_setting
private FloatingActionButton mfab_seeting;

```
initdata()方法填充两个ArrayList，标题和fragment
```java
 private void initData() {
        mTitle = new ArrayList<>();
        mTitle.add("服务管家");
        mTitle.add("微信精选");
        mTitle.add("美女社区");
        mTitle.add("个人中心");

        mFragment = new ArrayList<>();
        mFragment.add(new ButlerFragment());
        mFragment.add(new WeChatFragment());
        mFragment.add(new GrilFragment());
        mFragment.add(new UserFragment());

    }
```
initView()方法：
找到TabLayout布局和ViewPager布局
设置Viewpager预加载和适配器
设置标题
然后绑定。
找到fab_setting设置点击事件。
为viewPager设置滑动监听
```java
 //初始化view
    private void initView() {
        mTablayout = (TabLayout) findViewById(R.id.mTabLayout);
        mViewPager = (ViewPager) findViewById(R.id.mViewPager);
        mfab_seeting = (FloatingActionButton) findViewById(R.id.fab_setting);
        //默认隐藏
        mfab_seeting.setVisibility(View.GONE);
        //设置点击监听器
        mfab_seeting.setOnClickListener(this);
        //预加载
        mViewPager.setOffscreenPageLimit(mFragment.size());
        //设置适配器
        mViewPager.setAdapter(new FragmentPagerAdapter(getSupportFragmentManager()) {
            //选中的fragment
            @Override
            public Fragment getItem(int position) {
                return mFragment.get(position);
            }

            //返回item的个数
            @Override
            public int getCount() {
                return mFragment.size();
            }

            //设置标题

            @Override
            public CharSequence getPageTitle(int position) {
                return mTitle.get(position);
            }
        });
        //viewpager设置滑动监听器
        mViewPager.addOnPageChangeListener(new ViewPager.OnPageChangeListener() {
            @Override
            public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {

            }

            @Override
            public void onPageSelected(int position) {
                if(position ==0){
                    mfab_seeting.setVisibility(View.GONE);
                }else{
                    mfab_seeting.setVisibility(View.VISIBLE);
                }
            }

            @Override
            public void onPageScrollStateChanged(int state) {

            }
        });
        //绑定
        mTablayout.setupWithViewPager(mViewPager);
    }

```
滑动监听：
```java
@Override
    public void onClick(View v) {
        switch (v.getId()){
            case R.id.fab_setting:
                startActivity(new Intent(this, SettingActivity.class));
        }
    }
}

```
