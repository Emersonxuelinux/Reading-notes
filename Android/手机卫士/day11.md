##目录

- [Day11](#day11)
    - [1、运行进程个数以及剩余总内存(重点:sdk版本)](#运行进程个数以及剩余总内存)
    - [2、桌面小控件](#桌面小控件)
    - [3、桌面小控件生命周期](#桌面小控件生命周期)
    - [4、桌面小控件移植到手机卫士(重点:反编译+如何找资源控件)](#桌面小控件移植到手机卫士)
    - [5、更新widget中textview](#更新widget中textview)
    - [6、一键清理(重点:发送自定义广播+接受自定义广播+pendingIntente)](#一键清理)
    - [7、隐藏显示系统进程](#隐藏显示系统进程)
    - [8、锁屏和解锁的操作(重点)](#锁屏和解锁的操作)
    - [9、widgetBug解决](#widgetBug解决)
    - [10、屏幕适配(重点)](#屏幕适配)
    - [11、创建快捷方式(重点)](#创建快捷方式)
    - [12、监听用户代开应用程序(重点)](#监听用户代开应用程序)
    - [13、看门狗数据库操作(重点)](#看门狗数据库操作)

<h2 id="day11">Day11</h2>
<h2 id="运行进程个数以及剩余总内存">1、运行进程个数以及剩余总内存(重点:sdk版本)</h2>

    1.获取操作
        public class TaskUtil {
            /**
             * 获取正在运行的进程的个数
             * @return
             */
            public static int getProcessCount(Context context){
                ActivityManager am = (ActivityManager) context.getSystemService(Context.ACTIVITY_SERVICE);
                List<RunningAppProcessInfo> runningAppProcesses = am.getRunningAppProcesses();
                return runningAppProcesses.size();
            }
            /**
             * 获取剩余内存
             * @return
             */
            public static long getAvailableRam(Context context){
                ActivityManager am = (ActivityManager) context.getSystemService(Context.ACTIVITY_SERVICE);
                //获取内存的信息,保存到memoryinfo中
                MemoryInfo outInfo = new MemoryInfo();
                am.getMemoryInfo(outInfo);
                //获取空闲的内存
                //outInfo.availMem;
        //      //获取总的内存
        //      outInfo.totalMem;
                return outInfo.availMem;
            }
            
            /**
             * 获取总的内存
             * @return
             * @deprecated
             */
            public static long getTotalRam(Context context){
                ActivityManager am = (ActivityManager) context.getSystemService(Context.ACTIVITY_SERVICE);
                //获取内存的信息,保存到memoryinfo中
                MemoryInfo outInfo = new MemoryInfo();
                am.getMemoryInfo(outInfo);
                //获取空闲的内存
                //outInfo.availMem;
        //      //获取总的内存
        //      outInfo.totalMem;
                return outInfo.totalMem;//16版本之上才有,之下是没有的
            }
            /**
             * 兼容低版本
             * @return
             */
            public static long getTotalRam(){
                File file = new File("/proc/meminfo");
                StringBuilder sb = new StringBuilder();
                try {
                    //读取文件
                    BufferedReader br = new BufferedReader(new FileReader(file));
                    String readLine = br.readLine();
                    //获取数字
                    char[] charArray = readLine.toCharArray();
                    for (char c : charArray) {
                        if (c>='0' && c<='9') {
                            sb.append(c);
                        }
                    }
                    String string = sb.toString();
                    //转化成long
                    long parseLong = Long.parseLong(string);
                    return parseLong*1024;
                } catch (FileNotFoundException e) {
                    e.printStackTrace();
                } catch (IOException e) {
                    e.printStackTrace();
                }
                return 0;
            }
        }
    2.使用
        tv_taskmanager_processes = (TextView) findViewById(R.id.tv_taskmanager_processes);
        tv_taskmanager_freeandtotalram = (TextView) findViewById(R.id.tv_taskmanager_freeandtotalram);

        // 设置显示数据
        // 获取相应的数据
        // 获取运行的进程个数
        processCount = TaskUtil.getProcessCount(getApplicationContext());
        tv_taskmanager_processes.setText("运行中进程:\n" + processCount + "个");
        // 获取剩余,总内存'
        long availableRam = TaskUtil.getAvailableRam(getApplicationContext());
        // 数据转化
        String availaRam = Formatter.formatFileSize(getApplicationContext(),
                availableRam);
        // 获取总内存
        // 根据不同的sdk版去调用不同的方法
        // 1.获取当前的sdk版本
        int sdk = android.os.Build.VERSION.SDK_INT;
        long totalRam;
        if (sdk >= 16) {
            totalRam = TaskUtil.getTotalRam(getApplicationContext());
        } else {
            totalRam = TaskUtil.getTotalRam();
        }
        // 数据转化
        String totRam = Formatter.formatFileSize(getApplicationContext(),
                totalRam);
        tv_taskmanager_freeandtotalram.setText("剩余/总内存:\n" + availaRam + "/"
                + totRam);
    3.清理操作
        // 更改运行中的进程个数以及剩余总内存
        processCount = processCount - deleteTaskInfos.size();
        tv_taskmanager_processes.setText("运行中进程:\n" + processCount + "个");

        // 更改剩余总内存,重新获取剩余总内存
        // 获取剩余,总内存'
        long availableRam = TaskUtil.getAvailableRam(getApplicationContext());
        // 数据转化
        String availaRam = Formatter.formatFileSize(getApplicationContext(),
                availableRam);
        // 获取总内存
        // 根据不同的sdk版去调用不同的方法
        // 1.获取当前的sdk版本
        int sdk = android.os.Build.VERSION.SDK_INT;
        long totalRam;
        if (sdk >= 16) {
            totalRam = TaskUtil.getTotalRam(getApplicationContext());
        } else {
            totalRam = TaskUtil.getTotalRam();
        }
        // 数据转化
        String totRam = Formatter.formatFileSize(getApplicationContext(),
                totalRam);
        tv_taskmanager_freeandtotalram.setText("剩余/总内存:\n" + availaRam + "/"
                + totRam);

<h2 id="桌面小控件">2、桌面小控件</h2>

    1.创建appwidgetprovider
        public class MyWidget extends AppWidgetProvider {

        }
    2.清单文件配置
         <receiver android:name="com.example.widgets.MyWidget" >
            <intent-filter>
                <action android:name="android.appwidget.action.APPWIDGET_UPDATE" />
            </intent-filter>

            <meta-data
                android:name="android.appwidget.provider"
                android:resource="@xml/example_appwidget_info" />
        </receiver>
    3.xml -> xxx.xml
        <appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"
            android:minWidth="275dp"
            android:minHeight="72dp"
            android:updatePeriodMillis="0"
            android:initialLayout="@layout/example_appwidget"
            android:resizeMode="horizontal|vertical"
            >
        </appwidget-provider>
        
        <!-- 
            minWidth,minHeight : 最小的宽高
            updatePeriodMillis : 更新时间,毫秒值,最短不能小于30分钟,设置0跟30分钟一个效果
            previewImage : 预览图片,不设置默认使用应用图标
            initialLayout :widget布局文件
            configure : 可选数据,创建widget之后会启动那个activity
            resizeMode : 设置widget显示尺寸规则,主要用来设置屏幕横竖屏切换保证widget布局不会变形
            widgetCategory : 设置widget显示在什么地方 ,home_screen : 桌面    keyguard:键盘
            initialKeyguardLayout : 设置锁屏的时候显示控件的布局
         -->
    4.带有@Remoteview注解控件是可以显示在桌面小控件上的

<h2 id="桌面小控件生命周期">3、桌面小控件生命周期</h2>

    第一次创建
        10-20 02:08:34.418: I/System.out(4570): onReceive
        10-20 02:08:34.418: I/System.out(4570): onEnabled
        10-20 02:08:34.418: I/System.out(4570): onReceive
        10-20 02:08:34.418: I/System.out(4570): onUpdate
        10-20 02:08:37.788: I/System.out(4570): onReceive
    第二次创建
        10-20 02:09:27.292: I/System.out(4570): onReceive
        10-20 02:09:27.292: I/System.out(4570): onUpdate
        10-20 02:09:31.439: I/System.out(4570): onReceive
    第三次创建
        10-20 02:09:56.408: I/System.out(4570): onReceive
        10-20 02:09:56.408: I/System.out(4570): onUpdate
        10-20 02:10:00.769: I/System.out(4570): onReceive
    第一次删除
        10-20 02:10:28.303: I/System.out(4570): onReceive
        10-20 02:10:28.303: I/System.out(4570): onDeleted
    第二次删除
        10-20 02:10:45.319: I/System.out(4570): onReceive
        10-20 02:10:45.319: I/System.out(4570): onDeleted
    删除最后一个
        10-20 02:11:09.370: I/System.out(4570): onReceive
        10-20 02:11:09.370: I/System.out(4570): onDeleted
        10-20 02:11:09.380: I/System.out(4570): onReceive
        10-20 02:11:09.380: I/System.out(4570): onDisabled

    1.第一次创建的时候调用onEnabled
    2.不管什么操作都会调用onReceive
    3.每次创建都会调用onUpdate
    4.每次删除都会调用onDeleted
    5.删除最后一个会调用onDisabled

<h2 id="桌面小控件移植到手机卫士">4、桌面小控件移植到手机卫士(重点:反编译+如何找资源控件)</h2>

    1.反编译金山卫士apk
    2.根据广播事件去清单文件中找widget
    3.根据元数据找到xml -> xxx.xml
    4.根据xxx.xml下initialLayout找到布局文件
    5.拷贝布局文件,根据错误进行修改

<h2 id="更新widget中textview">5、更新widget中textview</h2>

    1.创建服务
    2.在widget的onEnabled开启服务,在onDisabled关闭服务
    3.更新
        //widget的管理者
        appWidgetManager = AppWidgetManager.getInstance(this);

        /**
         * 更新widget
         */
        private void updateWidgets() {
            /*new Thread(){
                public void run() {
                    while(true){
                        SystemClock.sleep(2000);
                    }
                };
            }.start();*/
            //计数器
            Timer timer = new Timer();
            //执行操作
            //task : 要执行操作
            //when : 延迟的时间
            //period : 每次执行的间隔时间
            timer.schedule(new TimerTask() {
                
                @Override
                public void run() {
                    //更新操作
                    //获取组件的标示
                    ComponentName provider = new ComponentName(WidgetService.this, MyWidget.class);
                    //获取远程布局
                    //packageName : 应用的包名
                    //layoutId :widget布局文件
                    RemoteViews views = new RemoteViews(getPackageName(), R.layout.process_widget);
                    //远程布局不能通过findviewbyid获取初始化控件
                    //更新布局文件中相应控件的值
                    //viewId :更新控件的id
                    //text : 更新的内容
                    views.setTextViewText(R.id.process_count, "正在运行软件:"+TaskUtil.getProcessCount(WidgetService.this));
                    views.setTextViewText(R.id.process_memory, "可用内存:"+Formatter.formatFileSize(WidgetService.this, TaskUtil.getAvailableRam(WidgetService.this)));
                    //更新操作
                    appWidgetManager.updateAppWidget(provider, views);
                }
            }, 2000, 2000);
        }

<h2 id="一键清理">6、一键清理(重点:发送自定义广播+接受自定义广播+pendingIntente)</h2>

    1.在timer中进行操作
        //pendingIntent : 延迟意图  包含一个intent意图,当点击的才去执行这个意图,不点击就不执行意图
        views.setOnClickPendingIntent(R.id.btn_clear, pendingIntent);
    2.创建pendingIntent
        //通过发送一个广播去表示要执行清理操作,通过接受发送的广播执行清理操作
        //flags : 指定信息的标签
        PendingIntent pendingIntent = PendingIntent.getBroadcast(WidgetService.this, 0, intent, PendingIntent.FLAG_UPDATE_CURRENT);
    3.pedingintent中需要intent,创建intent,设置发送广播事件
        //按钮点击事件
        Intent intent = new Intent();
        intent.setAction("aa.bb.cc");//设置要发送的广播,aa.bb.cc:自定义的广播事件
        //sendBroadcast(intent);
    4.创建清理进程广播接受者接受广播
        a.创建广播接受者
            /**
             * 清理进程的广播接受者
             * @author Administrator
             *
             */
            private class WidgetReceiver extends BroadcastReceiver{
        
                @Override
                public void onReceive(Context context, Intent intent) {
                    //清理进程
                    killProcess();
                }
            }
        b.注册广播接受者
            //注册清理进程广播接受者
            //1.广播接受者
            widgetReceiver = new WidgetReceiver();
            //2.设置接受的广播事件
            IntentFilter intentFilter = new IntentFilter();
            intentFilter.addAction("aa.bb.cc");
            //3.注册广播接受者
            registerReceiver(widgetReceiver, intentFilter);
        c.注销广播接受者
            //注销清理进程的广播接受者
            if (widgetReceiver != null) {
                unregisterReceiver(widgetReceiver);
                widgetReceiver = null;
            }
        d.清理进程
            /**
             * 清理进程
             */
            public void killProcess() {
                ActivityManager am = (ActivityManager) getSystemService(ACTIVITY_SERVICE);
                //获取正在运行进程
                List<RunningAppProcessInfo> runningAppProcesses = am.getRunningAppProcesses();
                for (RunningAppProcessInfo runningAppProcessInfo : runningAppProcesses) {
                    //判断我们的应用进程不能被清理
                    if (!runningAppProcessInfo.processName.equals(getPackageName())) {
                        am.killBackgroundProcesses(runningAppProcessInfo.processName);
                    }
                }
            }
    5.关闭服务的,停止更新
        //停止更新widget
        if (timer != null) {
            timer.cancel();
            timer = null;
        }

<h2 id="隐藏显示系统进程">7、隐藏显示系统进程</h2>

    1.创建一个标示
        //是否显示系统进程的标示
        private boolean isshowSystem = true;
    2.在adapter的getCount方法根据标示设置显示的条目个数
        @Override
        public int getCount() {
            // TODO Auto-generated method stub
            // list.size() = userappinfo.size()+systemappinfo.size()
            return isshowSystem==true ? userappinfo.size() + 1 + systemappinfo.size() + 1 : userappinfo.size() + 1;
        }
    3.在设置的点击事件中更改标示
        /**
         * 设置
         * 
         * @param v
         */
        public void setting(View v) {
            //true 改为false  false改为true
            isshowSystem = !isshowSystem;
            //更新界面
            myadapter.notifyDataSetChanged();
        }

<h2 id="锁屏和解锁的操作">8、锁屏和解锁的操作(重点)</h2>

    android锁屏和解锁广播接受者比较特别,不能在清单文件中注册,比如通过代码去注册

    1.锁屏清理进程
        a.代码注册锁屏的广播接受者
            /**
             * 锁屏的广播接受者
             * @author Administrator
             *
             */
            private class ScreenOffReceiver extends BroadcastReceiver{
        
                @Override
                public void onReceive(Context context, Intent intent) {
                    System.out.println("锁屏了.....");
                    
                }
            }
        b.注册
            //注册锁屏的广播接受者
            screenOffReceiver = new ScreenOffReceiver();
            //设置接受广播事件
            IntentFilter screenoffIntentfilter = new IntentFilter();
            screenoffIntentfilter.addAction(Intent.ACTION_SCREEN_OFF);
            registerReceiver(screenOffReceiver, screenoffIntentfilter);
        c.注销
            if (screenOffReceiver != null) {
                unregisterReceiver(screenOffReceiver);
                screenOffReceiver = null;
            }
        d.锁屏清理进程
                @Override
                public void onReceive(Context context, Intent intent) {
                    System.out.println("锁屏了.....");
                    //清理进程
                    killProcess();
                }
    2.锁屏停止更新,解锁重新更新
        a.在锁屏中停止更新
            /**
             * 锁屏的广播接受者
             * @author Administrator
             *
             */
            private class ScreenOffReceiver extends BroadcastReceiver{
        
                @Override
                public void onReceive(Context context, Intent intent) {
                    System.out.println("锁屏了.....");
                    //清理进程
                    killProcess();
                    //停止更新
                    stopUpdates();
                }
            }
        b.代码注册解锁的广播接受者
            /**
             * 解锁的广播接受者
             */
            private class ScreenOnReceiver extends BroadcastReceiver{
                @Override
                public void onReceive(Context context, Intent intent) {
                }
            }
        c.注册解锁的广播接受者
            //注册解锁的广播接受者
            screenOnReceiver = new ScreenOnReceiver();
            IntentFilter screenOnIntentFilter = new IntentFilter();
            screenOnIntentFilter.addAction(Intent.ACTION_SCREEN_ON);
            registerReceiver(screenOnReceiver, screenOnIntentFilter);
        d.注销
            if (screenOnReceiver != null) {
                unregisterReceiver(screenOnReceiver);
                screenOnReceiver = null;
            }
        e.解锁重新进行更新
            /**
             * 解锁的广播接受者
             * @author Administrator
             */
            private class ScreenOnReceiver extends BroadcastReceiver{
                @Override
                public void onReceive(Context context, Intent intent) {
                    updateWidgets();
                }
            }

<h2 id="widgetBug解决">9、widgetBug解决</h2>

    将onEnabled开启服务的操作,移植onUpdate方法中,因为更新时间到了之后就会调用onUpdate方法

<h2 id="屏幕适配">10、屏幕适配(重点)</h2>

    1.dp -> px
        public class DensityUtil {
            /** 
             * 根据手机的分辨率从 dip 的单位 转成为 px(像素) 
             * @return 
             */  
            public static int dip2qx(Context context, float dpValue) {  
                final float scale = context.getResources().getDisplayMetrics().density;  //获取屏幕的密度
                return (int) (dpValue * scale + 0.5f); //+0.5f四舍五入   3.7  3   3.7+0.5 = 4.2   4
            }  
          
            /** 
             * 根据手机的分辨率从 px(像素) 的单位 转成为 dp 
             */  
            public static int px2dip(Context context, float pxValue) {  
                final float scale = context.getResources().getDisplayMetrics().density;  
                return (int) (pxValue / scale + 0.5f);  
            }  
        }
    2.ScrollView
        <ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:itheima="http://schemas.android.com/apk/res/com.itheima.mobliesafe75"
            android:layout_width="match_parent"
            android:layout_height="match_parent" >
            <!-- ScrollView : 只能有一个子控件 -->

<h2 id="创建快捷方式">11、创建快捷方式(重点)</h2>

    /**
     * 创建快捷方式
     */
    private void shortcut() {
        if (sp.getBoolean("firstshortcut", true)) {
            // 给桌面发送一个广播
            Intent intent = new Intent(
                    "com.android.launcher.action.INSTALL_SHORTCUT");
            // 设置属性
            // 设置快捷方式名称
            intent.putExtra(Intent.EXTRA_SHORTCUT_NAME, "手机卫士75");
            // 设置快捷方式的图标
            Bitmap value = BitmapFactory.decodeResource(getResources(),
                    R.drawable.ic_launcher);
            intent.putExtra(Intent.EXTRA_SHORTCUT_ICON, value);
            // 设置快捷方式执行的操作
            Intent intent2 = new Intent();
            intent2.setAction("com.itheima.mobliesafe75.home");
            intent2.addCategory("android.intent.category.DEFAULT");
            intent.putExtra(Intent.EXTRA_SHORTCUT_INTENT, intent2);
            sendBroadcast(intent);
            
            //保存已经创建快捷方式的状态
            Editor edit = sp.edit();
            edit.putBoolean("firstshortcut", false);
            edit.commit();
        }
    }

<h2 id="监听用户代开应用程序">12、监听用户代开应用程序(重点)</h2>

    时时刻刻监听某些操作行为  ,  watch dog   看门狗

        //时时刻刻监听用户打开的程序
        //activity都是存放在任务栈中的,一个应用只有一个任务栈
        final ActivityManager am = (ActivityManager) getSystemService(ACTIVITY_SERVICE);
        new Thread(){
            public void run() {
                while(true){
                    //监听操作
                    //监听用户打开了哪些任务栈,打开哪些应用
                    //获取正在运行的任务栈,如果任务栈运行,就表示应用打开过
                    //maxNum : 获取正在运行的任务栈的个数
                    //现在打开的应用的任务栈,永远在第一个,而之前(点击home最小化,没有退出)的应用的任务栈会依次往后推
                    List<RunningTaskInfo> runningTasks = am.getRunningTasks(1);
                    System.out.println("----------------------");
                    for (RunningTaskInfo runningTaskInfo : runningTasks) {
                        //获取任务栈,栈底的activity
                        ComponentName baseactivity = runningTaskInfo.baseActivity;
                        /*//获取任务栈栈顶的activity
                        runningTaskInfo.topActivity;*/
                        String packageName = baseactivity.getPackageName();
                        System.out.println(packageName);
                        if (packageName.equals("com.android.mms")) {
                            Intent intent = new Intent(WatchDogService.this,MainActivity.class);
                            //因为activity保存在任务栈中,而服务,广播没有保存在任务栈中的,所以在服务,广播中跳转activity必须给activity设置任务栈
                            intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                            startActivity(intent);
                        }
                    }
                    System.out.println("----------------------");
                    SystemClock.sleep(3000);
                }
            };
        }.start();

<h2 id="看门狗数据库操作">13、看门狗数据库操作(重点)</h2>

    1.创建数据库
            public class WatchDogOpenHelper extends SQLiteOpenHelper {

                    public static final String DB_NAME="info";
                    
                    public WatchDogOpenHelper(Context context) {
                        super(context, "watchdog.db", null, 1);
                    }
                
                    @Override
                    public void onCreate(SQLiteDatabase db) {
                        db.execSQL("create table "+DB_NAME+"(_id integer primary key autoincrement,packagename varchar(50))");
                    }
                
                    @Override
                    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
                
                    }
                
            }
    2.操作,拷贝blacknumdao,修改
        public class WatchDogDao {
                
                private WatchDogOpenHelper watchDogOpenHelper;
                private byte[] b = new byte[1024];
                //在构造函数中获取BlackNumOpenHlper
                public WatchDogDao(Context context){
                    watchDogOpenHelper = new WatchDogOpenHelper(context);
                }
                //增删改查
                //面试:我在同一时刻对数据库既进行读操作也进行写操作,怎么避免这个两个操作同时操作数据库,同步锁+将WatchDogOpenHelper设置成单例模式
                /**
                 * 添加应用程序包名
                 * @param blacknum
                 * @param mode
                 */
                public void addLockApp(String packageName){
            //      synchronized (b) {
                        //1.获取数据库
                        SQLiteDatabase database = watchDogOpenHelper.getWritableDatabase();
                        //2.添加操作
                        //ContentValues :　添加的数据
                        ContentValues values = new ContentValues();
                        values.put("packagename", packageName);
                        database.insert(WatchDogOpenHelper.DB_NAME, null, values);
                        //3.关闭数据库
                        database.close();
            //      }
                }
