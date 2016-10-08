###目录
- [Day01](#day01)
    - [1、svn使用](#svn)
    - [2、splash界面作用](#splash界面作用)
    - [3、代码包结构划分方式](#代码包结构划分方式)
    - [4、spalsh界面搭建(重点)](#spalsh界面搭建(重点))
    - [5、获取版本号(重点)](#获取版本号(重点))
    - [6、连接服务器](#连接服务器)
    - [7、数据封装形式(重点)](#数据封装形式(重点))
    - [8、解析json数据(重点)](#解析json数据(重点))
    - [9、弹出对话框](#弹出对话框)
    - [10、对话框细节的处理](#对话框细节的处理)
    - [11、下载操作(重点)](#下载操作(重点))
    - [12、安装新的apk(重点)](#安装新的apk(重点))
    - [13、异常处理](#异常处理)
    - [14、两种上下文的区别](#两种上下文的区别)
    - [15、打包签名(重点)](#打包签名(重点))


<h2 id="day01">Day1</h2>
<h2 id="svn">1、SVN</h2>

    svn checkout
    svn update
    svn commit
    svn add

    student 
    123
    
    http://192.168.20.87/svn/Mobliesafe75

<h2 id="splash界面作用">2、splash界面作用</h2>

    1 卖弄 logo界面 展示次数 品牌效应 
    2 项目初始化  数据库copy    
    3 提醒用户更新版本
    4 广告的展示

    小步快跑  一个月做出beta版app,抢占市场,一个星期更新一个版本,10版本积攒人气,11版本盈利

<h2 id="代码包结构划分方式">3、代码包结构划分方式</h2>

    目的:方便后期维护和更新的项目

    1.按照代码类型划分
        
    2.按照业务类型划分
        业务模块比较独立
        黑马  办公软件   www.itheima.com
        教学模块  com.itheima.teach
        招生模块     com.itheima.student
        财务模块     com.itheima.money

<h2 id="spalsh界面搭建(重点)">4、spalsh界面搭建(重点)</h2>

    1.textview的阴影效果
        <!-- layout_centerInParent:在父控件的中间位置 
        shadow
        shadowColor : 设置阴影颜色
        shadowDx : 关于x轴的偏移量
        shadowRadius : 偏移的角度
        -->
    
        <TextView
            android:id="@+id/textView1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:text="版本号:1.0" 
            android:shadowColor="#ffff00"
            android:shadowDx="1"
            android:shadowDy="1"
            android:shadowRadius="2"
            />
    2.去除标题栏
        1.在values目录的styles.xml文件中增加属性
            <!-- Application theme. -->
            <style name="AppTheme" parent="AppBaseTheme">
                <!-- 去除标题栏 -->
                <item name="android:windowNoTitle">true</item>
                <!-- All customizations that are NOT specific to a particular API-level can go here. -->
            </style>
        2.在清单文件中的application中设置theme属性是我们工程的AppTheme
             android:theme="@style/AppTheme"

<h2 id="获取版本号(重点)">5、获取版本号(重点)</h2>

    id : 驼峰式命名:   控件类型_控件的所在位置_控件表示的逻辑内容

    /**
     * 获取当前应用程序的版本号
     * @return
     */
    private String getVersionName(){
        //包的管理者,获取清单文件中的所有信息
        PackageManager pm = getPackageManager();
        try {
            //根据包名获取清单文件中的信息,其实就是返回一个保存有清单文件信息的javabean
            //packageName :应用程序的包名
            //flags : 指定信息的标签,0:获取基础的信息,比如包名,版本号,要想获取权限等等信息,必须通过标签来指定,才能去获取
            //GET_PERMISSIONS : 标签的含义:处理获取基础信息之外,还会额外获取权限的信息
            //getPackageName() : 获取当前应用程序的包名
            PackageInfo packageInfo = pm.getPackageInfo(getPackageName(), 0);
            //获取版本号名称
            String versionName = packageInfo.versionName;
            return versionName;
        } catch (NameNotFoundException e) {
            //包名找不到的异常
            e.printStackTrace();
        }
        return null;
    }

<h2 id="连接服务器">6、连接服务器</h2>

    /**
     * 提醒用户更新版本
     */
    private void update() {
        //1.连接服务器,查看是否有最新版本,  联网操作,耗时操作,4.0以后不允许在主线程中执行的,放到子线程中执行
        new Thread(){
            public void run() {
                try {
                    //1.1连接服务器
                    //1.1.1设置连接路径
                    //spec:连接路径
                    URL url = new URL("xxxxx");
                    //1.1.2获取连接操作
                    HttpURLConnection conn = (HttpURLConnection) url.openConnection();//http协议,httpClient
                    //1.1.3设置超时时间
                    conn.setConnectTimeout(5000);//设置连接超时时间
                    //conn.setReadTimeout(5000);//设置读取超时时间
                    //1.1.4设置请求方式
                    conn.setRequestMethod("GET");//post
                    //1.1.5获取服务器返回的状态码,200,404,500
                    int responseCode = conn.getResponseCode();
                    if (responseCode == 200) {
                        //连接成功
                    }else{
                        //连接失败
                    }
                } catch (MalformedURLException e) {
                    e.printStackTrace();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            };
        }.start();
    }

    权限:<uses-permission android:name="android.permission.INTERNET"/>

<h2 id="数据封装形式(重点)">7、数据封装形式(重点)</h2>

    1.xml
    2.json
    json封装形式:{"code":"2.0","apkurl":"xxxxx","des":"新版本上线了,快来下载吧!!!!"}

    json : utf-8无bom格式  Notepad++
           editplus : utf-8 (选择)   utf-8+bom

<h2 id="解析json数据(重点)">8、解析json数据(重点)</h2>

    1.获取服务器返回的流信息
        //获取服务器返回的流信息
        InputStream in = conn.getInputStream();
    2.将流信息转化成字符串
        public class StreamUtil {
            /**
             * 将流信息转化成字符串
             * @return
             * @throws IOException 
             */
            public static String parserStreamUtil(InputStream in) throws IOException{
                //字符流,读取流
                BufferedReader br = new BufferedReader(new InputStreamReader(in));
                //写入流
                StringWriter sw  = new StringWriter();
                //读写操作
                //数据缓冲区
                String str = null;
                while((str = br.readLine()) !=null){
                    //写入操作
                    sw.write(str);
                }
                //关流
                sw.close();
                br.close();
                return sw.toString();
            }
        }
    3.解析json数据
        //将获取到的流信息转化成字符串
        String json = StreamUtil.parserStreamUtil(in);
        //解析json数据
        JSONObject jsonObject = new JSONObject(json);
    4.获取数据
        //获取数据
        code = jsonObject.getString("code");
        apkurl = jsonObject.getString("apkurl");
        des = jsonObject.getString("des");

<h2 id="弹出对话框">9、弹出对话框</h2>

    1.因为在子线程不能更新主线程ui所以给handler发送一个,在handler中弹出对话框
        private Handler handler = new Handler(){
            public void handleMessage(android.os.Message msg) {
                switch (msg.what) {
                case MSG_UPDATE_DIALOG:
                    //弹出对话框
                    showdialog();
                    break;
                }
            };
        };
    2.弹出对话框
        /**
         * 弹出对话框
         */
        protected void showdialog() {
            AlertDialog.Builder builder = new Builder(this);
            //设置对话框不能消失
            builder.setCancelable(false);
            //设置对话框的标题
            builder.setTitle("新版本:"+code);
            //设置对话框的图标
            builder.setIcon(R.drawable.ic_launcher);
            //设置对话框的描述信息
            builder.setMessage(des);
            //设置升级取消按钮
            builder.setPositiveButton("升级", new DialogInterface.OnClickListener() {
                
                @Override
                public void onClick(DialogInterface dialog, int which) {
                    
                }
            });
            builder.setNegativeButton("取消", new DialogInterface.OnClickListener() {
                
                @Override
                public void onClick(DialogInterface dialog, int which) {
                    
                }
            });
            //显示对话框
            //builder.create().show();//两种方式效果一样
            builder.show();
        }

<h2 id="对话框细节的处理">10、对话框细节的处理</h2>

    1.实现了取消按钮的操作
        builder.setNegativeButton("取消", new DialogInterface.OnClickListener() {
            
            @Override
            public void onClick(DialogInterface dialog, int which) {
                //1.隐藏对话框
                dialog.dismiss();
                //2.跳转到主界面
                enterHome();
            }
        });
    2.处理了在跳转到主界面,从主界面点击返回键返回的时候,返回splash界面的问题,解决:在跳转的是将splash界面移出
        /**
         * 跳转到主界面
         */
        protected void enterHome() {
            Intent intent = new Intent(this,HomeActivity.class);
            startActivity(intent);
            //移出splash界面
            finish();
        }
    3.处理对话框延迟显示的问题
        //处理连接外网连接时间的问题
        //在连接成功之后在去获取一个时间
        int endTime = (int) System.currentTimeMillis();
        //比较两个时间的时间差,如果小于两秒,睡两秒,大于两秒,不睡
        int dTime = endTime-startTime;
        if (dTime<2000) {
            //睡两秒钟
            SystemClock.sleep(2000-dTime);//始终都是睡两秒钟的时间
        }

<h2 id="下载操作(重点)">11、下载操作(重点)</h2>

    /**
     * 3.下载最新版本
     */
    protected void download() {
        HttpUtils httpUtils = new HttpUtils();
        //判断SD卡是否挂载
        if (Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)) {
            //url : 新版本下载的路径 apkurl
            //target : 保存新版本的目录
            //callback : RequestCallBack
            httpUtils.download(apkurl, "/mnt/sdcard/mobliesafe75_2.apk", new RequestCallBack<File>() {
                //现在成功调用的方法
                @Override
                public void onSuccess(ResponseInfo<File> arg0) {
                    // TODO Auto-generated method stub
                    
                }
                //下载失败调用的方法
                @Override
                public void onFailure(HttpException arg0, String arg1) {
                    // TODO Auto-generated method stub
                    
                }
                //显示当前下载进度操作
                //total : 下载总进度
                //current : 下载的当前进度
                //isUploading : 是否支持断点续传
                @Override
                public void onLoading(long total, long current, boolean isUploading) {
                    super.onLoading(total, current, isUploading);
                    //设置显示下载进度的textview可见,同时设置相应的下载进度
                    tv_spalsh_plan.setVisibility(View.VISIBLE);//设置控件是否可见
                    tv_spalsh_plan.setText(current+"/"+total);//110/200
                }
                
            });
        }
    }
    
    注意问题:
        1.修改apkurl地址
        2.增加sd操作权限
            <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        3.判断sd是否挂载
            if (Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)) {}
        4.生成2.0版本的apk
            工程的bin目录下去找
            a.将清单文件中的版本号改成2.0
            b.重新运行
            c.拷贝bin目录下的文件

<h2 id="安装新的apk(重点)">12、安装新的apk(重点)</h2>

    /**
     * 4.安装最新的版本
     */
    protected void installAPK() {
        /**
         *  <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <data android:scheme="content" /> //content : 从内容提供者中获取数据  content://
                <data android:scheme="file" /> // file : 从文件中获取数据
                <data android:mimeType="application/vnd.android.package-archive" />
            </intent-filter>
         */
        Intent intent = new Intent();
        intent.setAction("android.intent.action.VIEW");
        intent.addCategory("android.intent.category.DEFAULT");
        //单独设置会相互覆盖
        /*intent.setType("application/vnd.android.package-archive");
        intent.setData(Uri.fromFile(new File("/mnt/sdcard/mobliesafe75_2.apk")));*/
        intent.setDataAndType(Uri.fromFile(new File("/mnt/sdcard/mobliesafe75_2.apk")), "application/vnd.android.package-archive");
        //在当前activity退出的时候,会调用之前的activity的onActivityResult方法
        //requestCode : 请求码,用来标示是从哪个activity跳转过来
        //ABC  a -> c    b-> c  ,c区分intent是从哪个activity传递过来的,这时候就要用到请求码
        startActivityForResult(intent, 0);
    }
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        enterHome();
    }

<h2 id="异常处理">13、异常处理</h2>

    根据不同的异常,采用不同方式处理,方便项目后期定位异常,便于解决维护异常

<h2 id="两种上下文的区别">14、两种上下文的区别</h2>

    1.getApplicationContext()  返回Context 
    2.activity.this   代表的就是当前的activity,继承context,父类当中有的方法子类中一定有,子类中有的方法父类中不一定有,在用getApplicationContext()一定能使用activity.this,但是能用activity.this不一定能使用getApplicationContext()

    android.view.WindowManager$BadTokenException: Unable to add window -- token null is not for an application  没有添加窗口,
    
    在使用对话框的时候,必须告诉对话框,要挂载在那个activity,也是告诉对话框应用在那个activity中显示

    在使用跟对话框相关的操作的时候必须使用activity.this不能使用getApplicationContext()

    getContext() : 返回Context ,一般用在单元测试中或者是自定义控件,其实和getApplicationContext()一样操作

<h2 id="打包签名(重点)">15、打包签名(重点)</h2>

    只有打包签名过的应用才能上传应用市场上去

    应用升级条件
        1.包名一致
        2.签名一致
    微信  com.weixin
    流氓程序  com.weixin

    注意:签名千万不能丢的,密码也不能忘,丢失用户
    解决办法:将原应用下架,重新打包签名上架,  应用市场会有一模一样的应用,  丢失一部分用户
 