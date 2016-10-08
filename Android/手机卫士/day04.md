###目录

- [Day04](#day04)
    - [1、界面切换动画效果(重点)](#界面切换动画效果)
    - [2、手势识别器(重点)](#手势识别器)
    - [3、第四个界面设置完成操作](#第四个界面设置完成操作)
    - [4、shape资源(重点)](#shape资源)
    - [5、绑定SIM卡](#绑定SIM卡)
    - [6、监听手机重启(重点:广播接受者)](#监听手机重启)
    - [7、保存安全号码](#保存安全号码)
    - [8、获取联系人操作(重点)](#获取联系人操作)
    - [9、获取联系人测试](#获取联系人测试)
    - [10、选择联系人界面](#选择联系人界面)
    - [11、选择联系人界面设置安全号码(重点:两个activity之间的数据传递)](#选择联系人界面设置安全号码)
    - [12、细节处理(重点:耗时操作,注解初始化控件)](#细节处理)
    
<h2 id="day04">Day04</h2>

<h2 id="界面切换动画效果">1、界面切换动画效果(重点)</h2>

    平移动画 
    1.res -> anim -> xxx.xml   (translate)
        <translate
            xmlns:android="http://schemas.android.com/apk/res/android"
            android:fromXDelta="100%"
            android:toXDelta="0"
            android:duration="500"
            >
            <!-- fromXDelta : 表示从x轴哪里开始移动 
                 toXDelta : 移动到哪里
                 duration : 持续时间
            -->
        
        </translate>
    2.调用
        //执行平移动画
        //执行界面切换动画的操作,是在startActivity或者finish之后执行
        //enterAnim : 新的界面进入的动画
        //exitAnim : 旧的界面退出的动画
        overridePendingTransition(R.anim.setup_enter_next, R.anim.setup_exit_next);

<h2 id="手势识别器">2、手势识别器(重点)</h2>

    private GestureDetector gestureDetector;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        //1.获取手势识别器
        //要想让手势是识别器生效,必须将手势识别器注册到屏幕的触摸事件中
        gestureDetector = new GestureDetector(this, new MyOnGestureListener());
    }
    //界面的触摸事件
    @Override
    public boolean onTouchEvent(MotionEvent event) {
        gestureDetector.onTouchEvent(event);
        return super.onTouchEvent(event);
    }
    //base simple
    private class MyOnGestureListener extends SimpleOnGestureListener{
        //e1 : 按下的事件,保存有按下的坐标
        //e2 : 抬起的事件,保存有抬起的坐标
        //velocityX : velocity 速度    在x轴上移动的速率
        @Override
        public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
                float velocityY) {
            //得到按下的x坐标
            float startX = e1.getRawX();
            //得到抬起的x坐标
            float endX = e2.getRawX();
            //得到按下的Y坐标
            float startY = e1.getRawY();
            //得到抬起的y坐标
            float endY = e2.getRawY();
            //判断是否是斜滑
            if ((Math.abs(startY-endY)) > 50) {
                Toast.makeText(getApplicationContext(), "你小子又乱滑了,别闹了....", 0).show();
                return false;
            }
            //下一步
            if ((startX-endX) > 100) {
                next_activity();
            }
            //上一步
            if ((endX-startX) > 100) {
                pre_activity();
            }
            //true if the event is consumed, else false
            //true : 事件执行     false:拦截事件,事件不执行
            return true;
        }
        
    }

<h2 id="第四个界面设置完成操作">3、第四个界面设置完成操作</h2>

    @Override
    public void next_activity() {
        //保存用户第一次进入手机防盗模块设置向导的状态,frist
        Editor edit = sp.edit();
        edit.putBoolean("first", false);
        edit.commit();
        // 跳转到手机防盗页面
        Intent intent = new Intent(this,LostfindActivity.class);
        startActivity(intent);
        finish();
    }

<h2 id="shape资源">4、shape资源(重点)</h2>

    1.res -> drawable -> xxx.xml  (shape)
        <shape xmlns:android="http://schemas.android.com/apk/res/android" 
            android:shape="rectangle"
            >
            <!-- shape : 设置图片的行状态      rectangle : 矩形     oval:椭圆    line:线    ring:环形-->
            <!-- corners : 弧度 -->
            <corners android:radius="10dp"/>
            <!-- solid : 颜色 -->
            <solid android:color="#ff0000"/>
            <!-- stroke :　虚线　　　 dashWidth : 点的宽度   dashGap : 点之间的距离
            <stroke android:width="3dp" android:color="#00ff00" android:dashWidth="3dp" android:dashGap="5dp"/>
            -->
            <!-- gradient : 渐变      angle : 渐变的角度-->
            <gradient android:startColor="#ff0000" android:centerColor="#00ff00" android:endColor="#0000ff"/>
        </shape>
    2.调用
        android:background="@drawable/shape_drawable"

<h2 id="绑定SIM卡">5、绑定SIM卡</h2>

        //设置回显操作
        //根据保存的SIM卡去初始化控件状态,有保存SIM卡号就是绑定SIM卡,如果没有就是没有绑定SIM卡
        if (TextUtils.isEmpty(sp.getString("sim", ""))) {
            //没有绑定
            sv_setup2_sim.setChecked(false);
        }else{
            //绑定SIM卡
            sv_setup2_sim.setChecked(true);
        }
        sv_setup2_sim.setOnClickListener(new OnClickListener() {
            
            @Override
            public void onClick(View v) {
                Editor edit = sp.edit();
                //绑定SIM卡
                //根据checkbox的状态设置描述信息的状态
                //isChecked() : 获取之前checkbox的状态
                if (sv_setup2_sim.isChecked()) {
                    //解绑
                    edit.putString("sim", "");
                    sv_setup2_sim.setChecked(false);
                }else{
                    //绑定SIM卡
                    //获取SIM卡号
                    //电话的管理者
                    TelephonyManager tel = (TelephonyManager) getSystemService(TELEPHONY_SERVICE);
                    //tel.getLine1Number();//获取SIM卡绑定的电话号码    line1:双卡双待.在中国不太适用,运营商一般不会将SIM卡和手机号码绑定
                    String sim = tel.getSimSerialNumber();//获取SIM卡序列号,唯一标示
                    //保存SIM卡号
                    edit.putString("sim", sim);
                    sv_setup2_sim.setChecked(true);
                }
                edit.commit();
            }
        });

<h2 id="监听手机重启">6、监听手机重启(重点:广播接受者)</h2>

    1.监听手机重启
        a.创建一个广播接受者,接受者手机重启的广播事件
            public class BootCompleteReceiver extends BroadcastReceiver {

                @Override
                public void onReceive(Context context, Intent intent) {
                    System.out.println("手机重启了......");
                }
            
            }
        b.清单文件中配置
            <receiver android:name="com.itheima.mobliesafe75.receiver.BootCompleteReceiver">
                <!-- priority : 广播接受者的优先级,值越大优先级越高,越先接收到广播 -->
                <intent-filter 
                    android:priority="1000">
                    <action android:name="android.intent.action.BOOT_COMPLETED"/>
                </intent-filter>
            </receiver>
        c.权限
            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
    2.发送短信
        a.发送短信
            //发送报警短信
            //短信的管理者
            SmsManager smsManager = SmsManager.getDefault();
            //destinationAddress : 收件人
            //scAddress :　短信中心的地址　　一般null
            //text : 短信的内容
            //sentIntent : 是否发送成功
            //deliveryIntent : 短信的协议  一般null
            smsManager.sendTextMessage("5556", null, "da ge wo bei dao le,help me!!!!", null, null);
        b.权限
            <uses-permission android:name="android.permission.SEND_SMS"/>
    3.检查SIM卡是否发生变化
        //1.获取保存的SIM卡号
        String sp_sim = sp.getString("sim", "");
        //2.再次获取本地SIM卡号
        TelephonyManager tel = (TelephonyManager)context.getSystemService(Context.TELEPHONY_SERVICE);
        //tel.getLine1Number();//获取SIM卡绑定的电话号码    line1:双卡双待.在中国不太适用,运营商一般不会将SIM卡和手机号码绑定
        String sim = tel.getSimSerialNumber();//获取SIM卡序列号,唯一标示
        //3.判断两个SIM卡号是否为空
        if (!TextUtils.isEmpty(sp_sim) && !TextUtils.isEmpty(sim)) {
            //4.判断两个SIM卡是否一致,如果一致就不发送报警短信,不一致发送报警短信
            if (!sp_sim.equals(sim)) {
                //发送报警短信
                //短信的管理者
                SmsManager smsManager = SmsManager.getDefault();
                //destinationAddress : 收件人
                //scAddress :　短信中心的地址　　一般null
                //text : 短信的内容
                //sentIntent : 是否发送成功
                //deliveryIntent : 短信的协议  一般null
                smsManager.sendTextMessage("5556", null, "da ge wo bei dao le,help me!!!!", null, null);
            }
        }

<h2 id="保存安全号码">7、保存安全号码</h2>

    1.保存安全号码
        //保存输入的安全号
        //1.获取输入的安全号码
        String safenum = et_setup3_safenum.getText().toString().trim();
        //2.判断号码是否为空
        if (TextUtils.isEmpty(safenum)) {
            Toast.makeText(getApplicationContext(), "请输入安全号码", 0).show();
            return;
        }
        //3.保存输入的安全号码
        Editor edit = sp.edit();
        edit.putString("safenum", safenum);
        edit.commit();
    2.回显操作
        //回显操作
        et_setup3_safenum.setText(sp.getString("safenum", ""));

<h2 id="获取联系人操作">8、获取联系人操作(重点)</h2>

    /**
     * 获取系统联系人
     * @return
     */
    public static List<HashMap<String, String>> getAllContactInfo(Context context){
        ArrayList<HashMap<String, String>> list = new ArrayList<HashMap<String,String>>();
        //1.获取内容解析者
        ContentResolver resolver = context.getContentResolver();
        //2.获取内容提供者的地址:com.android.contacts    www.baidu.com/jdk
        //raw_contacts表的地址 :raw_contacts
        //view_data表的地址 : data
        //3.生成查询地址
        Uri raw_uri = Uri.parse("content://com.android.contacts/raw_contacts");//http://
        Uri date_uri = Uri.parse("content://com.android.contacts/data");
        //4.查询操作,先查询raw_contacts,查询contact_id
        //projection : 查询的字段
        Cursor cursor = resolver.query(raw_uri, new String[]{"contact_id"}, null, null, null);
        //5.解析cursor
        while(cursor.moveToNext()){
            //6.获取查询的数据
            String contact_id = cursor.getString(0);
            //cursor.getString(cursor.getColumnIndex("contact_id"));//getColumnIndex : 查询字段在cursor中索引值,一般都是用在查询字段比较多的时候
            //7.根据contact_id查询view_data表中的数据
            //selection : 查询条件
            //selectionArgs :查询条件的参数
            //sortOrder : 排序
            Cursor c = resolver.query(date_uri, new String[]{"data1","mimetype"}, "raw_contact_id=?", new String[]{contact_id}, null);
            HashMap<String, String> map = new HashMap<String, String>();
            //8.解析c
            while(c.moveToNext()){
                //9.获取数据
                String data1 = c.getString(0);
                String mimetype = c.getString(1);
                //10.根据类型去判断获取的data1数据并保存
                if (mimetype.equals("vnd.android.cursor.item/phone_v2")) {
                    //电话
                    map.put("phone", data1);
                }else if(mimetype.equals("vnd.android.cursor.item/name")){
                    //姓名
                    map.put("name", data1);
                }
            }
            //11.添加到集合中数据
            list.add(map);
            //12.关闭cursor
            c.close();
        }
        //12.关闭cursor
        cursor.close();
        return list;
    }

    权限:<uses-permission android:name="android.permission.READ_CONTACTS"/>

<h2 id="获取联系人测试">9、获取联系人测试</h2>

    1.创建一个测试工程
    2.解决contact_id为null
        if (!TextUtils.isEmpty(contact_id)) {//null   ""
            .......     
        }
    

<h2 id="选择联系人界面">10、选择联系人界面</h2>

    1.listview使用
         <ListView 
            android:id="@+id/lv_contact_contacts"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            ></ListView>
    2.代码中使用
        lv_contact_contacts = (ListView) findViewById(R.id.lv_contact_contacts);
        lv_contact_contacts.setAdapter(new Myadapter());

    adapter

        // 设置条目的样式
        @Override
        public View getView(int position, View convertView, ViewGroup parent) {
            View view = View.inflate(getApplicationContext(), R.layout.item_contact, null);
            //初始化控件
            //view.findViewById 是从item_contact找控件,findViewById是从activity_contacts找控件
            TextView tv_itemcontact_name = (TextView) view.findViewById(R.id.tv_itemcontact_name);
            TextView tv_itemcontact_phone = (TextView) view.findViewById(R.id.tv_itemcontact_phone);
            //设置控件的值
            tv_itemcontact_name.setText(list.get(position).get("name"));//根据条目的位置从list集合中获取相应的数据
            tv_itemcontact_phone.setText(list.get(position).get("phone"));
            return view;
        }

<h2 id="选择联系人界面设置安全号码">11、选择联系人界面设置安全号码(重点:两个activity之间的数据传递)</h2>

    两个activity之间的数据传递
        1.在setup3Activity中使用startActivityForResult(intent, 0);进行跳转
            /**
             * 选择联系人按钮
             * @param v
             */
            public void selectContacts(View v){
                //跳转到选择联系人界面
                Intent intent = new Intent(this,ContactActivity.class);
                //当现在的activity退出的时候,会调用之前activity的onActivityResult方法
                startActivityForResult(intent, 0);
            }
        同时重写setup3Activity的onActivityResult方法
            @Override
            protected void onActivityResult(int requestCode, int resultCode, Intent data) {
                super.onActivityResult(requestCode, resultCode, data);
                
            }
        2.在contactActivity中给listview设置条目点击事件,并传递数据
            lv_contact_contacts.setOnItemClickListener(new OnItemClickListener() {

                @Override
                public void onItemClick(AdapterView<?> parent, View view,
                        int position, long id) {
                    //将点击联系人的号码传递给设置安全号码界面
                    Intent intent = new Intent();
                    intent.putExtra("num", list.get(position).get("phone"));
                    //将数据传递给设置安全号码界面
                    //设置结果的方法,会将结果传递给调用当前activity的activity
                    setResult(RESULT_OK, intent);
                    //移出界面
                    finish();
                }
            });
        3.在setup3Activity的onActivityResult方法中接受数据
            @Override
            protected void onActivityResult(int requestCode, int resultCode, Intent data) {
                super.onActivityResult(requestCode, resultCode, data);
                if (data!=null) {
                    //接受选择联系人界面传递过来的数据,null.方法      参数为null
                    String num = data.getStringExtra("num");
                    //将获取到的号码,设置给安全号码输入框
                    et_setup3_safenum.setText(num);
                }
            }

<h2 id="细节处理">12、细节处理(重点:耗时操作,注解初始化控件)</h2>

    耗时操作的处理
    1.将获取联系人的操作放到子线程中执行
        private Handler handler = new Handler(){
            public void handleMessage(android.os.Message msg) {
                lv_contact_contacts.setAdapter(new Myadapter());
                
            };
        };

        new Thread(){
            public void run() {
                //获取联系人
                list = ContactEngine.getAllContactInfo(getApplicationContext());
                //获取完联系人的时候给handler发送一个消息,在handler中去setadapter
                handler.sendEmptyMessage(0);
            };
        }.start();
    2.解决界面加载数据时,空白页面展示问题,使用进度条
        使用注解的方式初始化控件
            a.在类的成员变量出去声明控件,并加上注解
                @ViewInject(R.id.lv_contact_contacts)
                private ListView lv_contact_contacts;
            b.在oncreate方法中初始化操作
                @Override
                protected void onCreate(Bundle savedInstanceState) {
                    super.onCreate(savedInstanceState);
                    setContentView(R.layout.activity_contacts);
                    ViewUtils.inject(this);
        处理空白界面问题,使用进度条
            //在加载数据之前显示进度条
            loading.setVisibility(View.VISIBLE);
            new Thread(){}
            
            private Handler handler = new Handler(){
                public void handleMessage(android.os.Message msg) {
                    lv_contact_contacts.setAdapter(new Myadapter());
                    //数据显示完成,隐藏进度条
                    loading.setVisibility(View.INVISIBLE);
                };
            };
