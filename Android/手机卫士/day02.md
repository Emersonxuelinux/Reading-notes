###目录
- [Day02](#Day02)
    - [1、Gridview使用](#Gridview使用)
    - [2、Textview滚动效果](#Textview滚动效果)
    - [3、设置中心界面](#设置中心界面)
    - [4、自定义组合控件](#自定义组合控件)
    - [5、屏蔽自动更新](#屏蔽自动更新)
    
    
<h2 id="Day02">Day02</h2>

<h2 id="Gridview使用">1、Gridview使用</h2>
    
    1.布局文件中用法,其实跟listview相似
        <!-- numColumns : 设置每行显示的个数
            verticalSpacing : 设置每行之间的距离
        -->
        <GridView 
            android:id="@+id/gv_home_gridview"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:numColumns="3"
            android:verticalSpacing="10dp"
            android:layout_marginTop="20dp"
        ></GridView>
    2.代码中的用法,其实跟listview相似
        a.初始化控件
            gv_home_gridview = (GridView) findViewById(R.id.gv_home_gridview);
            gv_home_gridview.setAdapter(new Myadapter());
        b.adapter
            // 设置条目的样式
            @Override
            public View getView(int position, View convertView, ViewGroup parent) {
                //position代表是条目的位置,从0开始   0-8
                //将布局文件转化成view对象
                View view = View.inflate(getApplicationContext(), R.layout.item_home, null);
                //每个条目的样式都不一样,初始化控件,去设置控件的值
                //view.findViewById是从item_home布局文件中找控件,findViewById是从activity_home中找控件
                ImageView iv_itemhome_icon = (ImageView)view.findViewById(R.id.iv_itemhome_icon);
                TextView tv_itemhome_text = (TextView) view.findViewById(R.id.tv_itemhome_text);
                //设置控件的值
                iv_itemhome_icon.setImageResource(imageId[position]);//给imageview设置图片,根据条目的位置从图片数组中获取相应的图片
                tv_itemhome_text.setText(names[position]);
                return view;
            }


<h2 id="Textview滚动效果">2、Textview滚动效果</h2>

    1.第一种方式
        <!-- 
            singleLine : 一行显示
            ellipsize  
                none :省略后面文字
                start : 隐藏前面的文字
                middle : 隐藏中间的文字
                end : 隐藏后面的文字
                marquee : 滚动
            focusableInTouchMode : 触摸获取焦点
            TextView天生是没有点击事件和获取焦点的事件
            focusable : 是否获取焦点操作,true:可以  false:不可以
            marqueeRepeatLimit : 设置滚动次数,marquee_forever : -1  一直滚动
        -->
        <TextView 
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="手机卫士,真64杀毒引擎,超神速度,打开7次可以召唤神龙,辅助杀毒!!!"
            android:singleLine="true"
            android:ellipsize="marquee"
            android:focusableInTouchMode="true"
            android:focusable="true"
            android:marqueeRepeatLimit="marquee_forever"
            />
    2.第二种方式,自定义一个textview,让textview自动获取焦点
        a.创建自定义控件,继承textview
            public class HomeTextView extends TextView {
                //在代码中使用的调用
                public HomeTextView(Context context) {
                    super(context);
                }
                //在布局文件中使用的时候调用,比两个参数多了样式
                public HomeTextView(Context context, AttributeSet attrs, int defStyle) {
                    super(context, attrs, defStyle);
                    // TODO Auto-generated constructor stub
                }
                //在布局文件中使用的时候调用
                //布局文件中的控件都是可以用代码来表示
                //AttributeSet : 保存了控件在布局文件中的所有属性
                public HomeTextView(Context context, AttributeSet attrs) {
                    super(context, attrs);
                    // TODO Auto-generated constructor stub
                }
                //设置自定义的textview自动获取焦点
                //是否获取焦点
                @Override
                public boolean isFocused() {
                    //true:获取焦点,false:不获取焦点
                    return true;
                }
            }
        b.再布局文件中使用
            <com.itheima.mobliesafe75.ui.HomeTextView 
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:text="手机卫士,真64杀毒引擎,超神速度,打开7次可以召唤神龙,辅助杀毒!!!"
                android:singleLine="true"
                android:ellipsize="marquee"
                android:focusableInTouchMode="true"
                android:marqueeRepeatLimit="marquee_forever"
                />

<h2 id="设置中心界面">3、设置中心界面</h2>

    <RelativeLayout 
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        >
        <TextView 
            android:id="@+id/tv_setting_title"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="提示更新"
            android:layout_margin="5dp"
            android:textSize="18sp"
            />
        <!-- layout_below : 在某个控件的下方 
        layout_marginLeft : 距离左边距离
        -->
        <TextView 
            android:id="@+id/tv_setting_des"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="关闭提示更新"
            android:textSize="16sp"
            android:layout_below="@+id/tv_setting_title"
            android:layout_marginLeft="5dp"
            android:textColor="#aa000000"
            />

        <CheckBox
            android:id="@+id/checkBox1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_alignParentRight="true"
            android:layout_centerVertical="true"
            android:layout_marginRight="17dp"
            />
        <View 
            android:layout_width="match_parent"
            android:layout_height="0.5dp"
            android:background="#77000000"
            android:layout_below="@+id/tv_setting_des"
            android:layout_marginTop="5dp"
            />
        
    </RelativeLayout>

<h2 id="自定义组合控件">4、自定义组合控件</h2>

    1.创建自定义控件继承RelativeLayout
        public class SettingView extends RelativeLayout {
    2.创建一个init方法,并在每个构造函数中调用
        /**
         * 添加控件
         */
        private void init(){
            //添加布局文件
    //      TextView textView = new TextView(getContext());
    //      textView.setText("我是自定义组合控件的textview");
            //第一种方式
            //将布局文件转化成view对象
    //      View view = View.inflate(getContext(), R.layout.settingview, null);//爹有了,去找孩子,亲生
    //      //添加操作
    //      this.addView(view);//在自定义组合控件中添加一个textview
            //第二种方式
            //获取view对象,同时给veiw对象设置父控件,相当于先创建一个view对象,在把控件放到自定义控件中
            View.inflate(getContext(), R.layout.settingview, this);//孩子有了,去找爹,喜当爹
        }

<h2 id="屏蔽自动更新">5、屏蔽自动更新</h2>

    1.将自定义组合控件移植到手机卫士中
    2.为了方便修改自定义控件中的控件的值,所以在自定义控件中创建一些方法
        //需要添加一些方法,使程序员能方便的去改变自定义控件中的控件的值
        /**
         * 设置标题的方法
         */
        public void setTitle(String title){
            tv_setting_title.setText(title);
        }
        /**
         * 设置描述信息的方法
         */
        public void setDes(String des){
            tv_setting_des.setText(des);
        }
        /**
         * 设置checkbox状态
         */
        public void setChecked(boolean isChecked){
            //设置checkbox的状态
            cb_setting_update.setChecked(isChecked);
        }
        /**
         * 获取checkbox的状态
         */
        public boolean isChecked(){
            return cb_setting_update.isChecked();//获取checkbox的状态
        }
    3.到activity中去使用这些方法
        sv_setting_update = (SettingView) findViewById(R.id.sv_setting_update);
        //初始化自定义控件中各个控件的值
        sv_setting_update.setTitle("提示更新");
        sv_setting_update.setDes("打开提示更新");
        sv_setting_update.setChecked(true); 
        
        //设置自定义组合控件的点击事件
        //问题1:点击checkbox发现描述信息没有改变,原因:因为checkbox天生是有点击事件和获取焦点事件,当点击checkbox,这个checkbox就会执行他的点击事件
        //而不会执行条目的点击事件
        //问题2:没有保存用户点击操作
        sv_setting_update.setOnClickListener(new OnClickListener() {
            
            @Override
            public void onClick(View v) {
                //更改状态
                //根据checkbox之前的状态来改变checkbox的状态
                if (sv_setting_update.isChecked()) {
                    //关闭提示更新
                    sv_setting_update.setDes("关闭提示更新");
                    sv_setting_update.setChecked(false);
                    
                }else{
                    //打开提示更新
                    sv_setting_update.setDes("打开提示更新");
                    sv_setting_update.setChecked(true);
                    
                }
            }
        });
    4.解决两个问题
        问题1:点击checkbox发现描述信息没有改变,原因:因为checkbox天生是有点击事件和获取焦点事件,当点击checkbox,这个checkbox就会执行他的点击事件
             <!-- 
                focusable : 是否可以获取焦点,true:可以,false:不可以
                clickable : 是否可以点击    true:可以,false:不可以 -->
                <CheckBox
                    android:id="@+id/cb_setting_update"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_alignParentRight="true"
                    android:layout_centerVertical="true"
                    android:layout_marginRight="17dp" 
                    android:focusable="false"
                    android:clickable="false"
                    />
        问题2:没有保存用户点击操作
            //name : 保存信息的文件的名称
            //mode : 权限
            sp = getSharedPreferences("config", MODE_PRIVATE);
            sv_setting_update = (SettingView) findViewById(R.id.sv_setting_update);
            //初始化自定义控件中各个控件的值
            sv_setting_update.setTitle("提示更新");
            //defValue : 缺省的值
            if (sp.getBoolean("update", true)) {
                sv_setting_update.setDes("打开提示更新");
                sv_setting_update.setChecked(true); 
            }else{
                sv_setting_update.setDes("关闭提示更新");
                sv_setting_update.setChecked(false);    
            }
            //设置自定义组合控件的点击事件
            //问题1:点击checkbox发现描述信息没有改变,原因:因为checkbox天生是有点击事件和获取焦点事件,当点击checkbox,这个checkbox就会执行他的点击事件
            //而不会执行条目的点击事件
            //问题2:没有保存用户点击操作
            sv_setting_update.setOnClickListener(new OnClickListener() {
                
                @Override
                public void onClick(View v) {
                    Editor edit = sp.edit();
                    //更改状态
                    //根据checkbox之前的状态来改变checkbox的状态
                    if (sv_setting_update.isChecked()) {
                        //关闭提示更新
                        sv_setting_update.setDes("关闭提示更新");
                        sv_setting_update.setChecked(false);
                        //保存状态
                        edit.putBoolean("update", false);
                        //edit.apply();//保存到文件中,但是仅限于9版本之上,9版本之下保存到内存中的
                    }else{
                        //打开提示更新
                        sv_setting_update.setDes("打开提示更新");
                        sv_setting_update.setChecked(true);
                        //保存状态
                        edit.putBoolean("update", true);
                    }
                    edit.commit();
                }
            });
        }
    5.屏蔽自动更新
        if (sp.getBoolean("update", true)) {
           update();
       }else{
           //跳转到主界面
           //不能让主线程去睡两秒钟
           //主线程是有渲染界面的操作,主线程睡两秒钟就没有办法去渲染界面
           new Thread(){
               public void run() {
                   SystemClock.sleep(2000);
                   enterHome(); 
               };
           }.start();
       }

