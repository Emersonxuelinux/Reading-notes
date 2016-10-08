###目录
- [Day05](#day05)
    - [1、异步加载框架(重点:面试必问)](#异步加载框架)
    - [2、修改进度条样式](#修改进度条样式)
    - [3、防盗保护是否开启](#防盗保护是否开启)
    - [4、设置手机防盗页面安全号码和防盗保护状态](#设置手机防盗页面安全号码和防盗保护状态)
    - [5、接受指令操作(重点:广播接受者)](#接受指令操作)
    - [6、定位方式](#定位方式)

<h2 id="day05">Day05</h2>
<h2 id="异步加载框架">1、异步加载框架(重点:面试必问)</h2>

    handler机制

    1.创建异步加载框架
        public abstract class MyAsycnTaks {
            private Handler handler = new Handler(){
                public void handleMessage(android.os.Message msg) {
                    postTask();
                };
            };
            /**
             * 在子线程之前执行的方法
             */
            public abstract void preTask();
            /**
             * 在子线程之中执行的方法
             */
            public abstract void doinBack();
            /**
             * 在子线程之后执行的方法
             */
            public abstract void postTask();
            /**
             * 执行
             */
            public void execute(){
                preTask();
                new Thread(){
                    public void run() {
                        doinBack();
                        handler.sendEmptyMessage(0);
                    };
                }.start();
            }
        }
    2.使用
        //异步加载框架
        new MyAsycnTaks() {
            
            @Override
            public void preTask() {
                //在加载数据之前显示进度条,在子线程之前执行的操作
                loading.setVisibility(View.VISIBLE);
            }
            
            @Override
            public void postTask() {
                //在子线程之后执行操作
                lv_contact_contacts.setAdapter(new Myadapter());
                //数据显示完成,隐藏进度条
                loading.setVisibility(View.INVISIBLE);
            }
            
            @Override
            public void doinBack() {
                //获取联系人,在子线程之中执行的操作
                list = ContactEngine.getAllContactInfo(getApplicationContext());
            }
        }.execute();

<h2 id="修改进度条样式">2、修改进度条样式</h2>

    android:indeterminateOnly : 是否显示进度条   true:不显示   false:显示

    1.res->drawable -> xxx.xml
        <animated-rotate xmlns:android="http://schemas.android.com/apk/res/android"
            android:drawable="@drawable/shenmabg"
            android:pivotX="50%"
            android:pivotY="50%"
        />
    2.使用,indeterminateDrawable属性
        <ProgressBar
            android:id="@+id/loading"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" 
            android:layout_gravity="center"
            android:indeterminateDrawable="@drawable/progressbar_drawable"
            />

<h2 id="防盗保护是否开启">3、防盗保护是否开启</h2>

    1.checkbox设置点击事件
        //根据保存的用户状态进行回显操作
        if (sp.getBoolean("protected", false)) {
            //开始防盗保护
            cb_setup4_protected.setText("您已经开启了防盗保护");
            cb_setup4_protected.setChecked(true);//必须要写
        }else{
            //关闭防盗保护
            cb_setup4_protected.setText("您还没有开启防盗保护");
            cb_setup4_protected.setChecked(false);//必须要写
        }
        //设置checkbox点击事件
        //当checkbox状态改变的时候调用
        cb_setup4_protected.setOnCheckedChangeListener(new OnCheckedChangeListener() {
            //CompoundButton : checkbox
            //isChecked :　改变之后的值,点击之后的值
            @Override
            public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
                Editor edit = sp.edit();
                //根据checkbox的状态设置checkbox的信息
                if (isChecked) {
                    //开始防盗保护
                    cb_setup4_protected.setText("您已经开启了防盗保护");
                    cb_setup4_protected.setChecked(true);//程序严谨性
                    //保存用户选中的状态
                    edit.putBoolean("protected", true);
                }else{
                    //关闭防盗保护
                    cb_setup4_protected.setText("您还没有开启防盗保护");
                    cb_setup4_protected.setChecked(false);//程序严谨性
                    edit.putBoolean("protected", false);
                }
                edit.commit();
            }
        });
    2.根据保存的防盗保护是否开启的状态,判断是否应该监听手机重启检测SIM卡是否变化发送报警短信的操作,在bootCompleteReceiver
        if (sp.getBoolean("protected", false)) {
            //检查SIM卡是否发生变化
            .......
        }

<h2 id="设置手机防盗页面安全号码和防盗保护状态">4、设置手机防盗页面安全号码和防盗保护状态</h2>

            TextView tv_lostfind_safenum = (TextView) findViewById(R.id.tv_lostfind_safenum);
            ImageView tv_lostfind_protected = (ImageView) findViewById(R.id.tv_lostfind_protected);
            //根据保存的安全号码和防盗保护状态进行设置
            //设置安全号码
            tv_lostfind_safenum.setText(sp.getString("safenum", ""));
            //设置防盗保护是否开启状态
            //获取保存的防盗保护状态
            boolean b = sp.getBoolean("protected", false);
            //根据获取防盗保护状态设置相应显示图片
            if (b) {
                //开启防盗保护
                tv_lostfind_protected.setImageResource(R.drawable.lock);
            }else{
                //关闭防盗保护
                tv_lostfind_protected.setImageResource(R.drawable.unlock);
            }

<h2 id="接受指令操作">5、接受指令操作(重点:广播接受者)</h2>

    1.服务器端推送一个指令,客户端接受者指令,叫做消息推送,心跳连接/长连接(费流量,费电),第三方消息推送sdk,极光推送,百度推送,xmpp,局限性:依赖于网络
    2.接受解析短信的操作,解析短信的内容,如果是指令的话,就执行相应的操作,局限性:费钱

    1.创建一个广播接受者
        public class SmsReceiver extends BroadcastReceiver {
    2.清单文件配置
        <!-- 要想实现拦截操作,优先级必须大于0,小于0是系统先接受到短信,大于0是我们先接受到短信 -->
        <receiver android:name="com.itheima.mobliesafe75.receiver.SmsReceiver">
            <intent-filter android:priority="1000">
                <action android:name="android.provider.Telephony.SMS_RECEIVED"/>
            </intent-filter>
        </receiver>
    3.接受解析短信
        @Override
        public void onReceive(Context context, Intent intent) {
            //接受解析短信
            //70汉字一条短信,71汉字两条短信
            Object[] objs = (Object[]) intent.getExtras().get("pdus");
            for(Object obj:objs){
                //解析成SmsMessage
                SmsMessage smsMessage = SmsMessage.createFromPdu((byte[]) obj);
                String body = smsMessage.getMessageBody();//获取短信的内容
                String sender = smsMessage.getOriginatingAddress();//获取发件人
                System.out.println("发件人:"+sender+"  短信内容:"+body);
                //真机测试,加发件人判断
                //判断短信是哪个指令
                if ("#*location*#".equals(body)) {
                    //GPS追踪
                    System.out.println("GPS追踪");
                    //拦截短信
                    abortBroadcast();//拦截操作,原生android系统,国产深度定制系统中屏蔽,比如小米
                }else if("#*alarm*#".equals(body)){
                    //播放报警音乐
                    System.out.println("播放报警音乐");
                    abortBroadcast();//拦截操作,原生android系统,国产深度定制系统中屏蔽,比如小米
                }else if("#*wipedata*#".equals(body)){
                    //远程删除数据
                    System.out.println("远程删除数据");
                    abortBroadcast();//拦截操作,原生android系统,国产深度定制系统中屏蔽,比如小米
                }else if("#*lockscreen*#".equals(body)){
                    //远程锁屏
                    System.out.println("远程锁屏");
                    abortBroadcast();//拦截操作,原生android系统,国产深度定制系统中屏蔽,比如小米
                }
                
            }
            }
        }

<h2 id="定位方式">6、定位方式</h2>

    gps一种定位方式
    1.wifi定位,IP地址,根据你的IP地址获取你的地理位置,精确度不是特别高了
    2.基站定位,基站就是为电话服务,信号的强弱决定了你离基站的距离,精确度比较高,几十米--几公里,精确度取决于基站的个数
    
    wifi定位和基站定位局限性:不能定位海拔
    3.gps定位,gps定位卫星进行定位,使用最少卫星实现全球定位,去和gps定位卫星进行通讯来获取定位坐标,通过光波进行通讯,必须得到空旷地方才能进行定位,连接至少需要一分钟,耗电,精确度特别高,不需要联网,联网:agps技术,通过联网来修正获取的坐标,特别准确的

    百度定位sdk   gps
    高德  sdk
