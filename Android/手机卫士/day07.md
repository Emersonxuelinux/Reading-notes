###目录
- [Day07](#day07)
    - [1、监听文本变化](#监听文本变化)
    - [2、抖动的效果](#抖动的效果)
    - [3、振动的效果](#振动的效果)
    - [4、监听电话状态(重点:服务,telephoneManger)](#监听电话状态)
    - [5、自定义toast(重点:看源码,layoutParams)](#自定义toast)
    - [6、服务开启(重点:动态获取正在运行服务)](#服务开启)
    - [7、自定义toast样式](#自定义toast样式)
    - [8、外拨电话显示号码归属地(重点:代码注册广播接受者)](#外拨电话显示号码归属地)
    - [9、归属地提示框风格(重点:单选框)](#归属地提示框风格)
    - [10、设置toast位置(重点)](#设置toast位置)
    - [11、归属地提示框位置界面](#归属地提示框位置界面)
    - [12、随着手指移动而移动的操作(重点)](#随着手指移动而移动的操作)
 
<h2 id="day07">Day07</h2>
<h2 id="监听文本变化">1、监听文本变化</h2>

        //监听输入框文本变化
        et_address_queryphone.addTextChangedListener(new TextWatcher() {
            //当文本变化完成的的时候调用
            @Override
            public void onTextChanged(CharSequence s, int start, int before, int count) {
                //1.获取输入框输入的内容
                String phone = s.toString();
                //2.根据号码查询号码归属地
                String queryAddress = AddressDao.queryAddress(phone, getApplicationContext());
                //3.判断查询的号码归属地是否为空
                if (!TextUtils.isEmpty(queryAddress)) {
                    //将查询的号码归属地设置给textveiw显示
                    tv_address_queryaddress.setText(queryAddress);
                }
            }
            //当文本变化之前调用
            @Override
            public void beforeTextChanged(CharSequence s, int start, int count,
                    int after) {
                // TODO Auto-generated method stub
                
            }
            //文本变化之后调用
            @Override
            public void afterTextChanged(Editable s) {
                // TODO Auto-generated method stub
                
            }
        });

<h2 id="抖动的效果">2、抖动的效果</h2>

    借鉴别人代码的技巧
        1.根据布局文件中的文本,去找相应布局文件
        2.根据布局文件去找java类
        3.在java类根据效果触发事件,去找相应的实现代码
    1.res -> anim -> shake.xml
        <!-- interpolator : 动画插补器 -->
        <translate xmlns:android="http://schemas.android.com/apk/res/android"
            android:duration="1000"
            android:fromXDelta="0"
            android:interpolator="@anim/cycle_7"
            android:toXDelta="10" />
    2.res -> anim -> cycle_7.xml
        <!-- cycleInterpolator : 循环插补器   cycles : 执行的次数 -->
        <cycleInterpolator xmlns:android="http://schemas.android.com/apk/res/android"
            android:cycles="7" />
    3.使用
            //实现抖动的效果
            Animation shake = AnimationUtils.loadAnimation(this, R.anim.shake);
            /*shake.setInterpolator(new Interpolator() {
                
                @Override
                public float getInterpolation(float x) {
                    return 0;//根据x的值获取y的值  y=x*x  y=x-k
                }
            });*/
            et_address_queryphone.startAnimation(shake);//开启动画

<h2 id="振动的效果">3、振动的效果</h2>

        //1.获取振动的管理者
        Vibrator vibrator = (Vibrator) getSystemService(VIBRATOR_SERVICE);
        //2.振动的效果
        //vibrator.vibrate(Long.MAX_VALUE);//milliseconds : 振动的持续时间,毫秒值,但是在国产定制系统只会振动一次,比如小米
        //pattern : 振动平率
        //repeat : 是否重复振动,-1不重复,非-1重复,如果是非-1表示从振动平率的那个元素开始振动
        vibrator.vibrate(new long[]{20l,10l,20l,10l}, -1);

    权限
        <uses-permission android:name="android.permission.VIBRATE"/>

<h2 id="监听电话状态">4、监听电话状态(重点:服务,telephoneManger)</h2>

    1.创建一个服务
        public class AddressService extends Service {

    2.通过telephoneManager监听电话的状态
        //监听电话状态
        //1.获取电话的管理者
        telephonyManager = (TelephonyManager) getSystemService(TELEPHONY_SERVICE);
        //2.监听电话的状态
        myPhoneStateListener = new MyPhoneStateListener();
        //listener : 电话状态的回调监听
        //events : 监听电话的事件
        //LISTEN_NONE : 不做任务监听操作
        //LISTEN_CALL_STATE : 监听电话状态
        telephonyManager.listen(myPhoneStateListener, PhoneStateListener.LISTEN_CALL_STATE);
    3.创建phonesatelistener
        private class MyPhoneStateListener extends PhoneStateListener{
            //监听电话状态的回调方法
            //state : 电话的状态
            //incomingNumber :　来电电话
            @Override
            public void onCallStateChanged(int state, String incomingNumber) {
                switch (state) {
                case TelephonyManager.CALL_STATE_IDLE://空闲 状态,挂断状态
                    break;
                case TelephonyManager.CALL_STATE_RINGING:;//响铃的状态
                    //查询号码归属地并显示
                    String queryAddress = AddressDao.queryAddress(incomingNumber, getApplicationContext());
                    //判断查询归属地是否为空
                    if (!TextUtils.isEmpty(queryAddress)) {
                        //显示号码归属地
                        Toast.makeText(getApplicationContext(), queryAddress, 0).show();
                    }
                    break;
                case TelephonyManager.CALL_STATE_OFFHOOK://通话的状态
                    break;
                }
                super.onCallStateChanged(state, incomingNumber);
            }
        }
    4.服务关闭的时候,取消监听
        @Override
        public void onDestroy() {
            //当服务关闭的时候,取消监听操作
            telephonyManager.listen(myPhoneStateListener, PhoneStateListener.LISTEN_NONE);
            super.onDestroy();
        }

<h2 id="自定义toast">5、自定义toast(重点:看源码,layoutParams)</h2>

    抄写系统源码实现
    1.显示toast
        /**
         * 显示toast
         */
        public void showToast(String queryAddress) {
            //1.获取windowmanager
            windowManager = (WindowManager) getSystemService(WINDOW_SERVICE);
            
            textView = new TextView(getApplicationContext());
            textView.setText(queryAddress);
            textView.setTextSize(100);
            textView.setTextColor(Color.RED);
            
            //3.设置toast的属性
            //layoutparams是toast的属性,控件要添加到那个父控件中,父控件就要使用那个父控件的属性,表示控件的属性规则符合父控件的属性规则
            WindowManager.LayoutParams params = new WindowManager.LayoutParams();
            params.height = WindowManager.LayoutParams.WRAP_CONTENT;//高度包裹内容
            params.width = WindowManager.LayoutParams.WRAP_CONTENT; //宽度包裹内容
            params.flags = WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE  //没有焦点
                    | WindowManager.LayoutParams.FLAG_NOT_TOUCHABLE  // 不可触摸
                    | WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON; // 保持当前屏幕
            params.format = PixelFormat.TRANSLUCENT; // 透明
            params.type = WindowManager.LayoutParams.TYPE_TOAST; // 执行toast的类型
            
            //2.将view对象添加到windowManager中
            //params : layoutparams  控件的属性
            //将params属性设置给view对象,并添加到windowManager中
            windowManager.addView(textView, params);
        }
    2.隐藏toast的方法
        /**
         * 隐藏toast
         */
        public void hideToast(){
            if (windowManager != null && textView!= null) {
                windowManager.removeView(textView);//移出控件
                windowManager= null;
                textView=null;
            }
        }

<h2 id="服务开启">6、服务开启(重点:动态获取正在运行服务)</h2>

    alt+shift+m : 将选中的代码抽取到方法中

    1.在SettingActivity中增加显示号码归属地的条目
    2.给条目设置点击事件
        sv_setting_address.setOnClickListener(new OnClickListener() {
            
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(SettingActivity.this,AddressService.class);
                //根据checkbox的状态设置描述信息的状态
                //isChecked() : 之前的状态
                if (sv_setting_address.isChecked()) {
                    //关闭提示更新
                    stopService(intent);
                    //更新checkbox的状态
                    sv_setting_address.setChecked(false);
                }else{
                    //打开提示更新
                    startService(intent);
                    sv_setting_address.setChecked(true);
                }
            }
        });
    2.回显操作了,因为我们可以在设置中心中手动关闭服务,所以要动态的去获取服务是否开启
        a.创建工具类,动态获取服务是否开启
            /**
             * 动态获取服务是否开启
             * @return
             */
            public static boolean isRunningService(String className,Context context){
                //进程的管理者,活动的管理者
                ActivityManager activityManager = (ActivityManager) context.getSystemService(Context.ACTIVITY_SERVICE);
                //获取正在运行的服务
                List<RunningServiceInfo> runningServices = activityManager.getRunningServices(1000);//maxNum 返回正在运行的服务的上限个数,最多返回多少个服务
                //遍历集合
                for (RunningServiceInfo runningServiceInfo : runningServices) {
                    //获取控件的标示
                    ComponentName service = runningServiceInfo.service;
                    //获取正在运行的服务的全类名
                    String className2 = service.getClassName();
                    //将获取到的正在运行的服务的全类名和传递过来的服务的全类名比较,一直表示服务正在运行  返回true,不一致表示服务没有运行  返回false
                    if (className.equals(className2)) {
                        return true;
                    }
                }
                return false;
            }
        b.使用
            //回显操作
            //动态的获取服务是否开启
            if (AdressUtils.isRunningService("com.itheima.mobliesafe75.service.AddressService", getApplicationContext())) {
                //开启服务
                sv_setting_address.setChecked(true);
            }else{
                //关闭服务
                sv_setting_address.setChecked(false);
            }
    3.当最小化应用的时候,从设置中心关闭服务,再次打开应用,发现条目没有进行更新的操作,解决:在onstart中调用动态获取服务是否开启的操作
        //activity可见的时候调用
        @Override
        protected void onStart() {
            super.onStart();
            address();
        }

<h2 id="自定义toast样式">7、自定义toast样式</h2>

    1.创建布局文件设置样式
        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:orientation="horizontal" 
            android:gravity="center_vertical"
            android:background="@drawable/call_locate_blue"
            >
            <ImageView 
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:src="@drawable/ic_launcher"
                />
            <TextView 
                android:id="@+id/tv_toastcustom_address"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="北京移动"
                android:textSize="18sp"
                android:textColor="#000000"
                />
        </LinearLayout>
    2.将布局文件转化成view对象,并添加到windowmanager
        //将布局文件转化成view对象
        view = View.inflate(getApplicationContext(), R.layout.toast_custom, null);
        //初始化控件
        //view.findViewById表示去toast_custom找控件
        TextView tv_toastcustom_address = (TextView) view.findViewById(R.id.tv_toastcustom_address);
        tv_toastcustom_address.setText(queryAddress);

        //将params属性设置给view对象,并添加到windowManager中
        windowManager.addView(view, params);
        

<h2 id="外拨电话显示号码归属地">8、外拨电话显示号码归属地(重点:代码注册广播接受者)</h2>

    广播接受者有两种注册方式
        1.清单文件中注册
            只要程序安装,就会一直生效,不能注销广播接受者,不受程序员控制
        2.代码注册
            只有程序运行,才会生效,杀死进程,就会失效,可以通过代码注销广播接受者,受程序员控制

    代码注册广播接受者步骤
        1.在服务中创建广播接受者
            /**
             * 外拨电话的广播接受者
             * @author Administrator
             *
             */
            private class MyOutGoingCallReceiver extends BroadcastReceiver{
        
                @Override
                public void onReceive(Context context, Intent intent) {
                    
                }
            }
        2.设置接受的广播事件
            //1.设置广播接受者
            myOutGoingCallReceiver = new MyOutGoingCallReceiver();
            //2.设置接受的广播事件
            IntentFilter intentFilter = new IntentFilter();
            intentFilter.addAction("android.intent.action.NEW_OUTGOING_CALL");//设置接受的广播事件
        3.注册广播接受者
            //3.注册广播接受者
            registerReceiver(myOutGoingCallReceiver, intentFilter);
        4.注销广播接受者
            //注销外拨电话广播接受者
            unregisterReceiver(myOutGoingCallReceiver);
        5.在广播接受者的onReceive中进行操作
            @Override
            public void onReceive(Context context, Intent intent) {
                //查询外拨电话的号码归属地
                //1.获取外拨电话
                String phone = getResultData();
                //2.查询号码归属地
                String queryAddress = AddressDao.queryAddress(phone, getApplicationContext());
                //3.判断号码归属地是否为空
                if (!TextUtils.isEmpty(queryAddress)) {
                    //显示toast
                    showToast(queryAddress);
                }
            }

<h2 id="归属地提示框风格">9、归属地提示框风格(重点:单选框)</h2>

    1.创建settingClickview及布局文件,拷贝settingview,将checkbox及自定义属性全部去掉
    2.使用
        /**
         * 设置归属地提示框风格
         */
        private void changedbg() {
            final String[] items={"半透明","活力橙","卫士蓝","金属灰","苹果绿"};
            //设置标题和描述信息
            scv_setting_changedbg.setTitle("归属地提示框风格");
            //根据保存的选中的选项的索引值设置自定义组合控件描述信息回显操作
            scv_setting_changedbg.setDes(items[sp.getInt("which", 0)]);
            //设置自定义组合控件的点击事件
            scv_setting_changedbg.setOnClickListener(new OnClickListener() {
                
                @Override
                public void onClick(View v) {
                    //弹出单选对话框
                    AlertDialog.Builder builder = new Builder(SettingActivity.this);
                    //设置图标
                    builder.setIcon(R.drawable.ic_launcher);
                    //设置标题
                    builder.setTitle("归属地提示框风格");
                    //设置单选框
                    //items : 选项的文本的数组
                    //checkedItem : 选中的选项
                    //listener : 点击事件
                    //设置单选框选中选项的回显操作
                    builder.setSingleChoiceItems(items, sp.getInt("which", 0), new DialogInterface.OnClickListener(){
                        //which : 选中的选项索引值
                        @Override
                        public void onClick(DialogInterface dialog, int which) {
                            
                            Editor edit = sp.edit();
                            edit.putInt("which", which);
                            edit.commit();
                            //1.设置自定义组合控件描述信息文本
                            scv_setting_changedbg.setDes(items[which]);//根据选中选项索引值从items数组中获取出相应文本,设置给描述信息控件
                            //2.隐藏对话框
                            dialog.dismiss();
                        }
                    });
                    //设置取消按钮
                    builder.setNegativeButton("取消", null);//当点击按钮只是需要进行隐藏对话框的操作的话,参数2可以写null,表示隐藏对话框
                    builder.show();
                }
            });
        }
    3.修改toast背景风格
        //根据归属地提示框风格中设置的风格索引值设置toast显示的背景风格
        view.setBackgroundResource(bgcolor[sp.getInt("which", 0)]);

<h2 id="设置toast位置">10、设置toast位置(重点)</h2>

        //设置toast位置
        //效果冲突,以默认的效果为主
        params.gravity = Gravity.LEFT | Gravity.TOP;
        
        params.x = 120;//不是坐标,表示的距离边框的距离,根据gravity来设置的,如果gravity是left表示距离左边框的距离,如果是right表示距离有边框的距离
        params.y = 100;//跟x的含义

<h2 id="归属地提示框位置界面">11、归属地提示框位置界面</h2>

    1.在设置中心添加归属地提示框风格相似条目
    2.设置标题,描述信息和点击事件
        /**
         * 归属地提示框位置
         */
        private void location() {
            scv_setting_location.setTitle("归属地提示框位置");
            scv_setting_location.setDes("设置归属地提示框的显示位置");
            scv_setting_location.setOnClickListener(new OnClickListener() {
                
                @Override
                public void onClick(View v) {
                    //跳转到设置位置的界面
                    Intent intent = new Intent(SettingActivity.this,DragViewActivity.class);
                    startActivity(intent);
                }
            });
        }
    3.设置dragviewActivity的界面
        a.在清单文件中设置activity的theme属性
            <activity android:name=".DragViewActivity"
                android:theme="@android:style/Theme.Translucent.NoTitleBar"
            />
        b.设置布局文件中的父控件的背景
             android:background="#aa000000"
        c.设置相应的控件
            <LinearLayout
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:background="@drawable/call_locate_orange"
                android:gravity="center_vertical"
                android:orientation="horizontal" >
        
                <ImageView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:src="@drawable/ic_launcher" />
        
                <TextView
                    android:id="@+id/tv_toastcustom_address"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="双击居中"
                    android:textColor="#ffffff"
                    android:textSize="18sp" />
            </LinearLayout>
            
            <TextView 
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:text="按住提示框拖到任意位置\n按手机返回键立即生效"
                android:textColor="#000000"
                android:layout_alignParentBottom="true"
                android:textSize="18sp"
                android:gravity="center_horizontal"
                android:background="@drawable/call_locate_blue"
                android:padding="10dp"
                />

<h2 id="随着手指移动而移动的操作">12、随着手指移动而移动的操作(重点)</h2>

    给控件设置触摸监听事件
        /**
         * 设置触摸监听
         */
        private void setTouch() {
            ll_dragview_toast.setOnTouchListener(new OnTouchListener() {
                private int startX;
                private int startY;
    
                //v : 当前的控件
                //event : 控件执行的事件
                @Override
                public boolean onTouch(View v, MotionEvent event) {
                    //event.getAction() : 获取控制的执行的事件
                    switch (event.getAction()) {
                    case MotionEvent.ACTION_DOWN:
                        //按下的事件
                        System.out.println("按下了....");
                        //1.按下控件,记录开始的x和y的坐标
                        startX = (int) event.getRawX();
                        startY = (int) event.getRawY();
                        break;
                    case MotionEvent.ACTION_MOVE:
                        //移动的事件
                        System.out.println("移动了....");
                        //2.移动到新的位置记录新的位置的x和y的坐标
                        int newX = (int) event.getRawX();
                        int newY = (int) event.getRawY();
                        //3.计算移动的偏移量
                        int dX = newX-startX;
                        int dY = newY-startY;
                        //4.移动相应的偏移量,重新绘制控件
                        //获取的时候原控件距离父控件左边和顶部的距离
                        int l = ll_dragview_toast.getLeft();
                        int t = ll_dragview_toast.getTop();
                        //获取新的控件的距离父控件左边和顶部的距离
                        l+=dX;
                        t+=dY;
                        int r = l+ll_dragview_toast.getWidth();
                        int b = t+ll_dragview_toast.getHeight();
                        ll_dragview_toast.layout(l, t, r, b);//重新绘制控件
                        //5.更新开始的坐标
                        startX=newX;
                        startY=newY;
                        break;
                    case MotionEvent.ACTION_UP:
                        //抬起的事件
                        System.out.println("抬起了....");
                        break;
                    }
                    //True if the listener has consumed the event, false otherwise.
                    //true:事件消费了,执行了,false:表示事件被拦截了
                    return true;
                }
            });
        }
