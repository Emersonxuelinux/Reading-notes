###目录
- [Day03](#day03)
    - [1、自定义属性](#自定义属性)
    - [2、设置密码对话框布局(重点)](#设置密码对话框布局)
    - [3、设置密码操作](#设置密码操作)
    - [4、输入密码对话框](#输入密码对话框)
    - [5、兼容低版本](#兼容低版本)
    - [6、MD5加密(重点)](#MD5加密)
    - [7、隐藏显示密码效果](#隐藏显示密码效果)
    - [8、界面跳转逻辑(重点:逻辑)](#界面跳转逻辑)
    - [9、设置向导第一个界面](#设置向导第一个界面)
    - [10、状态选择器(重点)](#状态选择器)
    - [11、设置向导第二界面抽取样式(重点:抽取样式)](#设置向导第二界面抽取样式)
    - [12、第三个界面&.9图片(重点:.9图片)](#第三个界面&.9图片)
    - [13、第四个界面](#第四个界面)
    - [14、抽取操作(重点!!!!!!)](#抽取操作)

<h2 id="day03">Day3</h2>
<h2 id="自定义属性">1、自定义属性</h2>

    1.在values->attrs.xml
        <resources>
            <declare-styleable name="com.itheima.mobliesafe75.ui.SettingView">
                <attr name="title" format="string" /><!-- name:属性的名称,format:类型 -->
                <attr name="des_on" format="string" />
                <attr name="des_off" format="string" />
            </declare-styleable>
        </resources>
    2.在布局中使用
        a.命名空间
             xmlns:itheima="http://schemas.android.com/apk/res/com.itheima.mobliesafe75"
        b.控件中使用
            <com.itheima.mobliesafe75.ui.SettingView
                android:id="@+id/sv_setting_update"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                itheima:title="提示更新"
                itheima:des_on="打开提示更新"
                itheima:des_off="关闭提示更新"
        ></com.itheima.mobliesafe75.ui.SettingView>
    3.代码中使用
        String title = attrs.getAttributeValue("http://schemas.android.com/apk/res/com.itheima.mobliesafe75", "title");
        des_on = attrs.getAttributeValue("http://schemas.android.com/apk/res/com.itheima.mobliesafe75", "des_on");
        des_off = attrs.getAttributeValue("http://schemas.android.com/apk/res/com.itheima.mobliesafe75", "des_off");
        //给自定义组合控件的控件设置相应的值
        //初始化控件的值
        tv_setting_title.setText(title);
        if (isChecked()) {
            tv_setting_des.setText(des_on);
        }else{
            tv_setting_des.setText(des_off);
        }
    
        
        /**
         * 设置checkbox状态
         */
        public void setChecked(boolean isChecked){
            //设置checkbox的状态
            cb_setting_update.setChecked(isChecked);
            //其实就是把sv_setting_update.setDes("打开提示更新");封装到了setChecked方法中
            if (isChecked()) {
                tv_setting_des.setText(des_on);
            }else{
                tv_setting_des.setText(des_off);
            }
        }
    4.修改settingactivity中的操作
        //sv_setting_update.setTitle("提示更新");
        //defValue : 缺省的值
        if (sp.getBoolean("update", true)) {
            //sv_setting_update.setDes("打开提示更新");
            sv_setting_update.setChecked(true); 
        }else{
            //sv_setting_update.setDes("关闭提示更新");
            sv_setting_update.setChecked(false);    
        }

<h2 id="设置密码对话框布局">2、设置密码对话框布局(重点)</h2>

    /**
     * 设置密码对话框
     */
    protected void showSetPassWordDialog() {
        AlertDialog.Builder builder = new Builder(this);
        //设置对话框不能消息
        builder.setCancelable(false);
        //将布局文件转化成view对象
        View view = View.inflate(getApplicationContext(), R.layout.dialog_setpassword, null);
        EditText et_setpassword_password = (EditText) view.findViewById(R.id.et_setpassword_password);
        EditText et_setpassword_confrim = (EditText) view.findViewById(R.id.et_setpassword_confrim);
        Button btn_ok = (Button) view.findViewById(R.id.btn_ok);
        Button btn_cancle = (Button) view.findViewById(R.id.btn_cancle);
        //设置确定,取消按钮的点击事件
        btn_cancle.setOnClickListener(new OnClickListener() {
            
            @Override
            public void onClick(View v) {
                //隐藏对话框
                dialog.dismiss();
            }
        });
        builder.setView(view);
        //显示对话框
        //builder.show();
        dialog = builder.create();
        dialog.show();
        
    }

<h2 id="设置密码操作">3、设置密码操作</h2>

    btn_ok.setOnClickListener(new OnClickListener() {
            
            @Override
            public void onClick(View v) {
                //设置密码
                //1.获取密码输入框输入的内容
                String password = et_setpassword_password.getText().toString().trim();
                //2.判断密码是否为空
                if (TextUtils.isEmpty(password)) {//null :没有内存     "":有内存地址但是没有内容
                    Toast.makeText(getApplicationContext(), "请输入密码", 0).show();
                    return;
                }
                //3.获取确认密码
                String confrim_password = et_setpassword_confrim.getText().toString().trim();
                //4.判断两次密码是否一致
                if (password.equals(confrim_password)) {
                    //保存密码,sp
                    Editor edit = sp.edit();
                    edit.putString("password", password);
                    edit.commit();
                    //隐藏对话框
                    dialog.dismiss();
                    //提醒用户
                    Toast.makeText(getApplicationContext(), "密码设置成功", 0).show();
                }else{
                    Toast.makeText(getApplicationContext(), "两次密码输入不一致", 0).show();
                }
            }
        });

<h2 id="输入密码对话框">4、输入密码对话框</h2>

        /**
         * 输入密码对话框
         */
        protected void showEnterPasswordDialog() {
            //第一步:复制布局
            AlertDialog.Builder builder = new Builder(this);
            //设置对话框不能消息
            builder.setCancelable(false);
            //将布局文件转化成view对象
            View view = View.inflate(getApplicationContext(), R.layout.dialog_enterpassword, null);
            //第三步:复制初始化控件及功能实现
            final EditText et_setpassword_password = (EditText) view.findViewById(R.id.et_setpassword_password);
            Button btn_ok = (Button) view.findViewById(R.id.btn_ok);
            Button btn_cancle = (Button) view.findViewById(R.id.btn_cancle);
            btn_ok.setOnClickListener(new OnClickListener() {
                
                @Override
                public void onClick(View v) {
                    //判断密码输入是否正确
                    //1.获取输入的密码
                    String password = et_setpassword_password.getText().toString().trim();
                    //2.判断密码是否为空
                    if (TextUtils.isEmpty(password)) {
                        Toast.makeText(getApplicationContext(), "请输入密码", 0).show();
                        return;
                    }
                    //3.获取保存的密码
                    String sp_password = sp.getString("password", "");
                    //4.判断两个密码是否一致
                    if (password.equals(sp_password)) {
                        //跳转到到手机防盗界面
                        //隐藏对话框
                        dialog.dismiss();
                        //提醒用户
                        Toast.makeText(getApplicationContext(), "密码正确", 0).show();
                    }else{
                        Toast.makeText(getApplicationContext(), "密码错误", 0).show();
                    }
                }
            });
            btn_cancle.setOnClickListener(new OnClickListener() {
                
                @Override
                public void onClick(View v) {
                    //隐藏对话框
                    dialog.dismiss();
                }
            });
            //第二步:复制显示
            builder.setView(view);
            //显示对话框
            //builder.show();
            dialog = builder.create();
            dialog.show();
        }

<h2 id="兼容低版本">5、兼容低版本</h2>

    根据具体显示风格,去进行修改

<h2 id="MD5加密">6、MD5加密(重点)</h2>

    md5加密:明文转化成密文之后,密文是不能转化成明文

    /**
     * MD5加密
     * @return
     */
    public static String passwordMD5(String password){
        StringBuilder sb = new StringBuilder();
        try {
            //1.获取数据摘要器
            //arg0 : 加密的方式
            MessageDigest messageDigest = MessageDigest.getInstance("MD5");
            //2.将一个byte数组进行加密,返回的是一个加密过的byte数组,二进制的哈希计算,md5加密的第一步
            byte[] digest = messageDigest.digest(password.getBytes());
            //3.遍历byte数组
            for (int i = 0; i < digest.length; i++) {
                //4.MD5加密
                //byte值    -128-127
                int result = digest[i] & 0xff;
                //将得到int类型转化成16进制字符串
                //String hexString = Integer.toHexString(result)+1;//不规则加密,加盐
                String hexString = Integer.toHexString(result);
                if (hexString.length() < 2) {
//                  System.out.print("0");
                    sb.append("0");
                }
                //System.out.println(hexString);
                //e10adc3949ba59abbe56e057f20f883e
                sb.append(hexString);
            }
            return sb.toString();
        } catch (NoSuchAlgorithmException e) {
            //找不到加密方式的异常
            e.printStackTrace();
        }
        return null;
    }

<h2 id="隐藏显示密码效果">7、隐藏显示密码效果</h2>

    1.添加隐藏显示按钮
        <LinearLayout 
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal"
            >
            <!-- layout_weight : 设置渲染优先级别,值越大优先级越低,优先级低的后渲染 
            inputType : 设置输入框输入类型
            -->
        <EditText
            android:id="@+id/et_setpassword_password"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:ems="10"
            android:inputType="textPassword" 
            android:textColor="#000000"
            android:hint="请输入密码"
            >
        </EditText>
        <ImageView 
            android:id="@+id/iv_enterpassword_hide"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:src="@drawable/btn_circle_pressed"
            android:layout_gravity="center_vertical"
            />
        </LinearLayout>
    2.代码中进行操作
        int count=0;

        ImageView iv_enterpassword_hide = (ImageView) view.findViewById(R.id.iv_enterpassword_hide);
        iv_enterpassword_hide.setOnClickListener(new OnClickListener() {
            
            @Override
            public void onClick(View v) {
                //隐藏显示密码
                if (count%2 == 0) {
                    //显示密码
                    et_setpassword_password.setInputType(0);
                }else{
                    //隐藏密码
                    et_setpassword_password.setInputType(129);//代码设置输入框输入类型
                }
                count++;
            }
        });

<h2 id="界面跳转逻辑">8、界面跳转逻辑(重点:逻辑)</h2>

    private SharedPreferences sp;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        sp = getSharedPreferences("config", MODE_PRIVATE);
        //分为两部分1.显示设置过的手机防盗功能,2.设置手机防盗功能
        //判断用户是否是第一次进入手机防盗模块,是,跳转到设置向导界面,不是,跳转防盗功能显示界面
        if (sp.getBoolean("first", true)) {
            //第一次进入,跳转到手机防盗设置向导界面
            Intent intent = new Intent(this,SetUp1Activity.class);
            startActivity(intent);
            //移出lostfindActivity
            finish();
        }else{
            //手机防盗显示界面
            setContentView(R.layout.activity_lostfind);
        }
    }

<h2 id="设置向导第一个界面">9、设置向导第一个界面</h2>

        <!-- layout_margin : 距离上下左右的距离 -->
        <TextView 
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="您的手机防盗卫士:"
            android:layout_margin="5dp"
            android:textSize="18sp"
            />
        <!-- drawableLeft : 是在文本的左边设置一张图片 
        @android:drawable/star_big_on : 调用系统的图片,使用系统图片可以节省我们应用的体积
        -->
        <TextView 
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="SIM卡变更报警"
            android:textSize="18sp"
            android:drawableLeft="@android:drawable/star_big_on"
            android:gravity="center_vertical"
            android:layout_marginLeft="5dp"
            />
        <TextView 
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="GPS追踪"
            android:textSize="18sp"
            android:drawableLeft="@android:drawable/star_big_on"
            android:gravity="center_vertical"
            android:layout_marginLeft="5dp"
            />
        <TextView 
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="远程数据销毁"
            android:textSize="18sp"
            android:drawableLeft="@android:drawable/star_big_on"
            android:gravity="center_vertical"
            android:layout_marginLeft="5dp"
            />
        <TextView 
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="远程锁屏"
            android:textSize="18sp"
            android:drawableLeft="@android:drawable/star_big_on"
            android:gravity="center_vertical"
            android:layout_marginLeft="5dp"
            />
        <LinearLayout 
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:gravity="center_horizontal"
            >
            <ImageView 
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:src="@android:drawable/presence_online"
                />
            <ImageView 
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:src="@android:drawable/presence_invisible"
                />
            <ImageView 
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:src="@android:drawable/presence_invisible"
                />
            <ImageView 
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:src="@android:drawable/presence_invisible"
                />
        </LinearLayout>
        
        <RelativeLayout 
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            >
            <!-- layout_centerInParent : 在父控件的中间位置 -->
            <ImageView 
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:src="@drawable/setup1"
                android:layout_centerInParent="true"
                />
            <!-- layout_alignParentRight : 在父控件的右方 
            layout_alignParentBottom : 在父控件的下方
            -->
            <Button 
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="下一步"
                android:layout_alignParentRight="true"
                android:layout_alignParentBottom="true"
                />
        </RelativeLayout>

<h2 id="状态选择器">10、状态选择器(重点)</h2>

    状态选择器:特殊图片,根据不同的状态显示不同的图片,比如按下,抬起
    1.res->drawable -> button.xml
        <selector xmlns:android="http://schemas.android.com/apk/res/android">
            <item android:state_pressed="true"
                  android:drawable="@drawable/function_greenbutton_pressed" /> <!-- pressed :按下的时候显示图片-->
            <item android:drawable="@drawable/function_greenbutton_normal" /> <!-- default :默认显示图片-->
        </selector>
    2.使用
        android:background="@drawable/button"

<h2 id="设置向导第二界面抽取样式">11、设置向导第二界面抽取样式(重点:抽取样式)</h2>

    1.在styles.xml文件中
        <style name="next">
            <item name="android:layout_width">wrap_content</item>
            <item name="android:layout_height">wrap_content</item>
            <item name="android:text">下一步</item>
            <item name="android:layout_alignParentRight">true</item>
            <item name="android:layout_alignParentBottom">true</item>
            <item name="android:background">@drawable/button</item>
            <item name="android:drawableRight">@drawable/next</item>
            <item name="android:padding">5dp</item>
            <item name="android:onClick">next</item>
        </style>
    2.使用
        <Button 
           style="@style/next"
         />

<h2 id="第三个界面&.9图片">12、第三个界面&.9图片(重点:.9图片)</h2>

    .9图片:为了防止图片拉伸变形

    sdk\tools\draw9patch.bat

<h2 id="第四个界面">13、第四个界面</h2>

    <!-- layout_alignParentRight : 在父控件的右方 
        layout_alignParentBottom : 在父控件的下方
        padding : 距离控件上下左右内边框的距离
        要想将样式文件中的属性的值覆盖,在控件中使用相同的属性,并设置值就可以
        -->
    <Button 
       style="@style/next"
       android:text="设置完成"
       android:drawableRight="@null"
     />

<h2 id="抽取操作">14、抽取操作(重点!!!!!!)</h2>

    第一个抽取
        1.创建父类
            public abstract class SetUpBaseActivity extends Activity {
        2.将子类中的上一步,下一步按钮点击事件抽取到父类中
            //按钮点击事件的操作
            //将每个界面中的上一步,下一步按钮操作,抽取到父类中
            public void pre(View v){
                pre_activity();
            }
            public void next(View v){
                next_activity();
            }
        3.因为父类不知道子类上一步,下一步具体实现,所以创建两个抽象方法,让子类去实现,根据自己的特性去实现响应的操作
            //因为父类不知道子类上一步,下一步具体的执行操作代码,所以要创建一个抽象方法,让子类实现这个抽象方法,根据自己的特性去实现相应的操作
            //下一步的操作
            public abstract void next_activity();
            //上一步的操作
            public abstract void pre_activity();
        4.子类继承父类,实现抽象方法,根据自己的特性去进行相应的实现
            public class SetUp2Activity extends SetUpBaseActivity {
    
                @Override
                protected void onCreate(Bundle savedInstanceState) {
                    super.onCreate(savedInstanceState);
                    setContentView(R.layout.activity_setup2);
                }
            
                @Override
                public void next_activity() {
                    // 跳转到第三个界面
                    Intent intent = new Intent(this, SetUp3Activity.class);
                    startActivity(intent);
                    finish();
                }
            
                @Override
                public void pre_activity() {
                    // 跳转到第一个界面
                    Intent intent = new Intent(this, SetUp1Activity.class);
                    startActivity(intent);
                    finish();
                }
            }
    第二个抽取:因为在进行第二个界面的时候,点击返回键会直接回到主界面,因为每个界面中都有返回键操作,所以向上抽取到父类进行统一的处理,子类就不去单独处理返回键的操作
        //监听手机物理按钮的点击事件
        //keyCode :　物理按钮的标示
        //event : 按键的处理事件
        @Override
        public boolean onKeyDown(int keyCode, KeyEvent event) {
            //判断keycode是否是返回键的标示
            if (keyCode == KeyEvent.KEYCODE_BACK) {
                //true:是可以屏蔽按键的事件
                //return true;
                pre_activity();
            }
            return super.onKeyDown(keyCode, event);
        }