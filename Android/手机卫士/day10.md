##目录

- [Day10](#day10)
    - [1、系统程序和用户程序拆分](#系统程序和用户程序拆分)
    - [2、浮动显示程序个数](#浮动显示程序个数)
    - [3、popuwindow操作(重点)](#popuwindow操作)
    - [4、popuwindow动画(重点)](#popuwindow动画)
    - [5、设置控件点击事件](#设置控件点击事件)
    - [6、卸载](#卸载)
    - [7、启动](#启动)
    - [8、详情](#详情)
    - [9、分享(重点)](#分享)
    - [10、获取可用空间](#获取可用空间)
    - [11、获取系统所有进程信息(重点)](#获取系统所有进程信息)
    - [12、进程管理界面(重点:思路)](#进程管理界面)
    - [13、checkboxBug解决(重点)](#checkboxBug解决)
    - [14、全选和取消](#全选和取消)
    - [15、清理进程(重点:五个进程)](#清理进程)
    - [16、清理细节处理](#清理细节处理)
    - []

<h2 id="day10">Day10</h2>
<h2 id="系统程序和用户程序拆分">1、系统程序和用户程序拆分</h2>

    1.将集合拆分,分别保存
            @Override
            public void doinBack() {
                list = AppEngine.getAppInfos(getApplicationContext());
                userappinfo = new ArrayList<AppInfo>();
                systemappinfo = new ArrayList<AppInfo>();
                for (AppInfo appinfo : list) {
                    //将数据分别存放到用户程序集合和系统程序集合中
                    if (appinfo.isUser()) {
                        userappinfo.add(appinfo);
                    }else{
                        systemappinfo.add(appinfo);
                    }
                }
            }
    2.修改adapter的getcount返回值
            @Override
            public int getCount() {
                // TODO Auto-generated method stub
                //list.size() = userappinfo.size()+systemappinfo.size()
                return userappinfo.size()+systemappinfo.size()+2;
            }
    3.在getview中增加条目,并获取数据
        增加条目
            if (position == 0) {
                //添加用户程序(...个)textview
                TextView textView = new TextView(getApplicationContext());
                textView.setBackgroundColor(Color.GRAY);
                textView.setTextColor(Color.WHITE);
                textView.setText("用户程序("+userappinfo.size()+")");
                return textView;
            }else if(position == userappinfo.size()+1){
                //添加系统程序(....个)textview
                TextView textView = new TextView(getApplicationContext());
                textView.setBackgroundColor(Color.GRAY);
                textView.setTextColor(Color.WHITE);
                textView.setText("系统程序("+systemappinfo.size()+")");
                return textView;
            }
        获取数据
            //1.获取应用程序的信息
            AppInfo appInfo;
            //数据就要从userappinfo和systemappinfo中获取
            if (position <= userappinfo.size()) {
                //用户程序
                appInfo = userappinfo.get(position-1);
            }else{
                //系统程序
                appInfo = systemappinfo.get(position - userappinfo.size() - 2);
            }
            
        判断缓存复用
            if (convertView != null && convertView instanceof RelativeLayout) {
                view = convertView;
                viewHolder = (ViewHolder) view.getTag();
            }else{

<h2 id="浮动显示程序个数">2、浮动显示程序个数</h2>

    /**
     * listview滑动监听事件
     */
    private void listviewOnscroll() {
        lv_softmanager_application.setOnScrollListener(new OnScrollListener() {
            //滑动状态改变的时候调用
            @Override
            public void onScrollStateChanged(AbsListView view, int scrollState) {
                // TODO Auto-generated method stub
                
            }
            //滑动的时候调用
            //view : listview
            //firstVisibleItem : 界面第一个显示条目
            //visibleItemCount : 显示条目总个数
            //totalItemCount : 条目的总个数
            @Override
            public void onScroll(AbsListView view, int firstVisibleItem,
                    int visibleItemCount, int totalItemCount) {
                //为null的原因:listview在初始化的时候就会调用onScroll方法
                if (userappinfo != null && systemappinfo != null) {
                    if (firstVisibleItem >= userappinfo.size()+1) {
                        tv_softmanager_userorsystem.setText("系统程序("+systemappinfo.size()+")");  
                    }else{
                        tv_softmanager_userorsystem.setText("用户程序("+userappinfo.size()+")");    
                    }
                }
            }
        });
    }

<h2 id="popuwindow操作">3、popuwindow操作(重点)</h2>

    /**
     * 条目点击事件
     */
    private void listviewItemClick() {
        lv_softmanager_application.setOnItemClickListener(new OnItemClickListener() {

            //view : 条目的view对象
            @Override
            public void onItemClick(AdapterView<?> parent, View view,
                    int position, long id) {
                //弹出气泡
                //1.屏蔽用户程序和系统程序(...个)弹出气泡
                if (position == 0 || position == userappinfo.size()+1) {
                    return;
                }
                //2.获取条目所对应的应用程序的信息
                //数据就要从userappinfo和systemappinfo中获取
                if (position <= userappinfo.size()) {
                    //用户程序
                    appInfo = userappinfo.get(position-1);
                }else{
                    //系统程序
                    appInfo = systemappinfo.get(position - userappinfo.size() - 2);
                }
                //5.弹出新的气泡之前,删除旧 的气泡
                hidePopuwindow();
                //3.弹出气泡
                /*TextView contentView = new TextView(getApplicationContext());
                contentView.setText("我是popuwindow的textview控件");
                contentView.setBackgroundColor(Color.RED);*/
                View contentView = View.inflate(getApplicationContext(), R.layout.popu_window, null);
                //contentView : 显示view对象
                //width,height : view宽高
                popupWindow = new PopupWindow(contentView, LayoutParams.WRAP_CONTENT, LayoutParams.WRAP_CONTENT);
                //4.获取条目的位置,让气泡显示在相应的条目
                int[] location = new int[2];//保存x和y坐标的数组
                view.getLocationInWindow(location);//获取条目x和y的坐标,同时保存到int[]
                //获取x和y的坐标
                int x = location[0];
                int y = location[1];
                //parent : 要挂载在那个控件上
                //gravity,x,y : 控制popuwindow显示的位置
                popupWindow.showAtLocation(parent, Gravity.LEFT | Gravity.TOP, x+50, y);
            }

        });
    }

    隐藏气泡
        /**
         * 隐藏气泡
         */
        private void hidePopuwindow() {
            if (popupWindow != null) {
                popupWindow.dismiss();//隐藏气泡
                popupWindow = null;
            }
        }

<h2 id="popuwindow动画">4、popuwindow动画(重点)</h2>

    动画要想执行,执行的控件必须有背景,动画都是基于背景来进行一些计算,没有背景动画是无法执行,popuwindow默认是没有设置背景

    //6.设置动画
                //缩放动画
                //前四个 :　控制控件由没有变到有   动画 0:没有    1:整个控件
                //后四个:控制控件是按照自身还是父控件进行变化
                //RELATIVE_TO_SELF : 以自身变化
                //RELATIVE_TO_PARENT : 以父控件变化
                ScaleAnimation scaleAnimation = new ScaleAnimation(0, 1, 0, 1, Animation.RELATIVE_TO_SELF, 0, Animation.RELATIVE_TO_SELF, 0.5f);
                scaleAnimation.setDuration(500);
                
                //渐变动画
                AlphaAnimation alphaAnimation = new AlphaAnimation(0.4f, 1.0f);//由半透明变成不透明
                alphaAnimation.setDuration(500);
                
                //组合动画
                //shareInterpolator : 是否使用相同的动画插补器  true:共享    false:各自使用各自的
                AnimationSet animationSet = new AnimationSet(true);
                //添加动画
                animationSet.addAnimation(scaleAnimation);
                animationSet.addAnimation(alphaAnimation);
                //执行动画
                contentView.startAnimation(animationSet);
    给popuwindow设置背景
        popupWindow.setBackgroundDrawable(new ColorDrawable(Color.TRANSPARENT));

<h2 id="设置控件点击事件">5、设置控件点击事件</h2>

    1.初始化控件
                //初始化控件
                LinearLayout ll_popuwindow_uninstall = (LinearLayout) contentView.findViewById(R.id.ll_popuwindow_uninstall);
                LinearLayout ll_popuwindow_start = (LinearLayout) contentView.findViewById(R.id.ll_popuwindow_start);
                LinearLayout ll_popuwindow_share = (LinearLayout) contentView.findViewById(R.id.ll_popuwindow_share);
                LinearLayout ll_popuwindow_detail = (LinearLayout) contentView.findViewById(R.id.ll_popuwindow_detail);
    2.activity实现点击事件接口,并实现相应的点击事件方法,同时设置控件点击事件
            public class SoftMangaerActivity extends Activity implements OnClickListener {
        
                //给控件设置点击事件
                ll_popuwindow_uninstall.setOnClickListener(SoftMangaerActivity.this);
                ll_popuwindow_start.setOnClickListener(SoftMangaerActivity.this);
                ll_popuwindow_share.setOnClickListener(SoftMangaerActivity.this);
                ll_popuwindow_detail.setOnClickListener(SoftMangaerActivity.this);
            
            @Override
            public void onClick(View v) {
                //判断点击是按个按钮
                //getId() : 获取点击按钮的id
                switch (v.getId()) {
                case R.id.ll_popuwindow_uninstall:
                    System.out.println("卸载");
                    break;
                case R.id.ll_popuwindow_start:
                    System.out.println("启动");
                    break;
                case R.id.ll_popuwindow_share:
                    System.out.println("分享");
                    break;
                case R.id.ll_popuwindow_detail:
                    System.out.println("详情");
                    break;
                }
            }

<h2 id="卸载">6、卸载</h2>

        /**
         * 卸载
         */
        private void uninstall() {
            /**
             * <intent-filter>
                    <action android:name="android.intent.action.VIEW" />
                    <action android:name="android.intent.action.DELETE" />
                    <category android:name="android.intent.category.DEFAULT" />
                    <data android:scheme="package" />
                </intent-filter>
             */
            //判断是否是系统程序
            if (appInfo.isUser()) {
                //判断是否是我们自己的应用,是不能卸载
                if (!appInfo.getPackagName().equals(getPackageName())) {
                    Intent intent = new Intent();
                    intent.setAction("android.intent.action.DELETE");
                    intent.addCategory("android.intent.category.DEFAULT");
                    intent.setData(Uri.parse("package:"+appInfo.getPackagName()));//tel:110
                    startActivityForResult(intent,0);
                }else{
                    Toast.makeText(getApplicationContext(), "文明社会,杜绝自杀", 0).show();
                }
            }else{
                Toast.makeText(getApplicationContext(), "要想卸载系统程序,请root先", 0).show();
            }
        }
        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            super.onActivityResult(requestCode, resultCode, data);
            fillData();
        }

<h2 id="启动">7、启动</h2>

    /**
     * 启动
     */
    private void start() {
        PackageManager pm = getPackageManager();
        //获取应用程序的启动意图
        Intent intent = pm.getLaunchIntentForPackage(appInfo.getPackagName());
        if (intent!=null) {
            startActivity(intent);
        }else{
            Toast.makeText(getApplicationContext(), "系统核心程序,无法启动", 0).show();
        }
    }

<h2 id="详情">8、详情</h2>
    
    通过系统log查看跳转操作
    
    /**
     *详情
     */
    private void detail() {
        /**
         *  Intent 
            { 
            act=android.settings.APPLICATION_DETAILS_SETTINGS    action
            dat=package:com.example.android.apis   data
            cmp=com.android.settings/.applications.InstalledAppDetails 
            } from pid 228
         */
        Intent intent = new Intent();
        intent.setAction("android.settings.APPLICATION_DETAILS_SETTINGS");
        intent.setData(Uri.parse("package:"+appInfo.getPackagName()));
        startActivity(intent);
    }

<h2 id="分享">9、分享(重点)</h2>

    1.分享
        /**
         *  Intent 
            { 
                act=android.intent.action.SEND 
                typ=text/plain 
                flg=0x3000000 
                cmp=com.android.mms/.ui.ComposeMessageActivity (has extras)   intent中包含信息
            } from pid 228
         */
        Intent intent = new Intent();
        intent.setAction("android.intent.action.SEND");
        intent.setType("text/plain");
        intent.putExtra(Intent.EXTRA_TEXT, "发现一个很牛x软件"+appInfo.getName()+",下载地址:www.baidu.com,自己去搜");
        startActivity(intent);
    2.如果是应用能够进行分享操作
        a.在activity中添加意图
            <!-- 表名应用程序能够接受分享信息 -->
            <intent-filter>
               <action android:name="android.intent.action.SEND" />
               <category android:name="android.intent.category.DEFAULT" />
               <data android:mimeType="text/plain" />
           </intent-filter>
        b.接受分享信息
            Intent intent = getIntent();
            String stringExtra = intent.getStringExtra(Intent.EXTRA_TEXT);
            if (!TextUtils.isEmpty(stringExtra)) {
                iv_share.setText(stringExtra);
            }

    shareSDK

<h2 id="获取可用空间">10、获取可用空间</h2>

    1.apputil
        /**
         * 获取SD卡可用空间
         */
        public static long getAvailableSD(){
             //获取SD卡路径
             File path = Environment.getExternalStorageDirectory();
             //硬盘的API操作
             StatFs stat = new StatFs(path.getPath());
             long blockSize = stat.getBlockSize();//获取每块的大小
             long totalBlocks = stat.getBlockCount();//获取总块数
             long availableBlocks = stat.getAvailableBlocks();//获取可用的块数
             return availableBlocks*blockSize;
        }
        /**
         *获取内存可用空间
         * @return
         */
        public static long getAvailableROM(){
            //获取内存路径
            File path = Environment.getDataDirectory();
            //硬盘的API操作
            StatFs stat = new StatFs(path.getPath());
            long blockSize = stat.getBlockSize();//获取每块的大小
            long totalBlocks = stat.getBlockCount();//获取总块数
            long availableBlocks = stat.getAvailableBlocks();//获取可用的块数
            return availableBlocks*blockSize;
        }
    2.使用
        //获取可用内存,获取都是kb
        long availableSD = AppUtil.getAvailableSD();
        long availableROM = AppUtil.getAvailableROM();
        //数据转化
        String sdsize = Formatter.formatFileSize(getApplicationContext(), availableSD);
        String romsize = Formatter.formatFileSize(getApplicationContext(), availableROM);
        //设置显示
        tv_softmanager_sd.setText("SD卡可用:"+sdsize);
        tv_softmanager_rom.setText("内存可用:"+romsize);

<h2 id="获取系统所有进程信息">11、获取系统所有进程信息(重点)</h2>

    /**
     * 获取系统中所有进程信息
     * @return
     */
    public static List<TaskInfo> getTaskAllInfo(Context context){
        List<TaskInfo> list = new ArrayList<TaskInfo>();
        //1.进程的管理者
        ActivityManager activityManager = (ActivityManager) context.getSystemService(Context.ACTIVITY_SERVICE);
        PackageManager pm = context.getPackageManager();
        //2.获取所有正在运行的进程信息
        List<RunningAppProcessInfo> runningAppProcesses = activityManager.getRunningAppProcesses();
        //遍历集合
        for (RunningAppProcessInfo runningAppProcessInfo : runningAppProcesses) {
            TaskInfo taskInfo = new TaskInfo();
            //3.获取相应的信息
            //获取进程的名称,获取包名
            String packagName = runningAppProcessInfo.processName;
            taskInfo.setPackageName(packagName);
            //获取进程所占的内存空间,int[] pids : 输入几个进程的pid,就会返回几个进程所占的空间
            MemoryInfo[] memoryInfo = activityManager.getProcessMemoryInfo(new int[]{runningAppProcessInfo.pid});
            int totalPss = memoryInfo[0].getTotalPss();
            long ramSize = totalPss*1024;
            taskInfo.setRamSize(ramSize);
            try {
                //获取application信息
                //packageName : 包名     flags:指定信息标签
                ApplicationInfo applicationInfo = pm.getApplicationInfo(packagName, 0);
                //获取图标
                Drawable icon = applicationInfo.loadIcon(pm);
                taskInfo.setIcon(icon);
                //获取名称
                String name = applicationInfo.loadLabel(pm).toString();
                taskInfo.setName(name);
                //获取程序的所有标签信息,是否是系统程序是以标签的形式展示
                int flags = applicationInfo.flags;
                boolean isUser;
                //判断是否是用户程序
                if ((applicationInfo.FLAG_SYSTEM & flags) == applicationInfo.FLAG_SYSTEM) {
                    //系统程序
                    isUser = false;
                }else{
                    //用户程序
                    isUser = true;
                }
                //保存信息
                taskInfo.setUser(isUser);
            } catch (NameNotFoundException e) {
                e.printStackTrace();
            }
            //taskinfo添加到集合
            list.add(taskInfo);
        }
        return list;
    }

<h2 id="进程管理界面">12、进程管理界面(重点:思路)</h2>

    按照软件管理思路一步步复制

<h2 id="checkboxBug解决">13、checkboxBug解决(重点)</h2>

    问题:checkbox的状态会跟着控件一起复用,
    解决:所以将状态保存到bean类中,根据bean中保存的状态在getview中进行动态设置
    1.将状态设置到bean类中
        //checkbox是否被选中
        private boolean isChecked = false;
    2.在getview中进行动态判断设置
            //因为checkbox的状态会跟着一起复用,所以一般要动态修改的控件的状态,不会跟着去复用,而是将状态保存到bean对象,在每次复用使用控件的时候
            //根据每个条目对应的bean对象保存的状态,来设置控件显示的相应的状态
            if (taskinfo.isChecked()) {
                viewHolder.cb_itemtaskmanager_ischecked.setChecked(true);
            }else{
                viewHolder.cb_itemtaskmanager_ischecked.setChecked(false);
            }
    3.屏蔽checkbox的获取焦点和点击事件
        <CheckBox 
            android:id="@+id/cb_itemtaskmanager_ischecked"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_alignParentRight="true"
            android:layout_centerVertical="true"
            android:layout_marginRight="15dp"
            android:focusable="false"
            android:clickable="false"
            />
    4.屏蔽之后,发现点击条目无法更改checkbox的状态,所以设置条目点击事件进行更改
        /**
         * listview条目点击事件
         */
        private void listviewItemClick() {
            lv_taskmanager_processes.setOnItemClickListener(new OnItemClickListener() {
                //view : 条目的view对象
                @Override
                public void onItemClick(AdapterView<?> parent, View view,
                        int position, long id) {
                    //动态改变checkbox状态
                    //1.屏蔽用户程序和系统程序(...个)弹出气泡
                    if (position == 0 || position == userappinfo.size()+1) {
                        return;
                    }
                    //2.获取条目所对应的应用程序的信息
                    //数据就要从userappinfo和systemappinfo中获取
                    if (position <= userappinfo.size()) {
                        //用户程序
                        taskInfo = userappinfo.get(position-1);
                    }else{
                        //系统程序
                        taskInfo = systemappinfo.get(position - userappinfo.size() - 2);
                    }
                    //3.根据之前保存的checkbox的状态设置点击之后的状态,原先选中,点击之后不选中
                    if (taskInfo.isChecked()) {
                        taskInfo.setChecked(false);
                    }else{
                        taskInfo.setChecked(true);
                    }
                    //4.更新界面
                    //myadapter.notifyDataSetChanged();
                    //只更新点击的条目
                    ViewHolder viewHolder = (ViewHolder) view.getTag();
                    viewHolder.cb_itemtaskmanager_ischecked.setChecked(taskInfo.isChecked());
                }
            });
        }
    5.单独更新单个条目
        因为控件是保存在ViewHolder控件容器中,而容器又是在listview条目展示的时候通过view.settag和view对象进行绑定了,那我们就可以在条目点击事件进行使用处理,因为在条目点击事件中有view参数,这个参数代表就是点击条目的view对象,通过view.gettag获取到和view对象绑定的容器,从容器中获取控件,设置控件的状态了

<h2 id="全选和取消">14、全选和取消</h2>

        /**
         * 全选
         * @param v
         */
        public void all(View v){
            //用户进程
            for (int i = 0; i < userappinfo.size(); i++) {
                userappinfo.get(i).setChecked(true);
            }
            //系统进程
            for (int i = 0; i < systemappinfo.size(); i++) {
                systemappinfo.get(i).setChecked(true);
            }
            //更新界面
            myadapter.notifyDataSetChanged();
        }
        /**
         * 取消
         * @param v
         */
        public void cancel(View v){
            //用户进程
            for (int i = 0; i < userappinfo.size(); i++) {
                userappinfo.get(i).setChecked(false);
            }
            //系统进程
            for (int i = 0; i < systemappinfo.size(); i++) {
                systemappinfo.get(i).setChecked(false);
            }
            //更新界面
            myadapter.notifyDataSetChanged();
        }

<h2 id="清理进程">15、清理进程(重点:五个进程)</h2>

    android进程
        1.前台进程  正在操作的进程,正在运行进程,不可杀死
        2.可见进程  没有焦点,但是可见的进程
        3.服务进程  包含服务的进程,当内存不足的时候,会被杀掉,当内存充足的时候,重新服务进程
        4.后台进程  点击home键,最小化
        5.空进程    退出,为了下次能够快速启动

    后台进程和空进程是可以通过代码杀死

    /**
     * 清理
     * @param v
     */
    public void clear(View v){
        //1.获取进程的管理者
        ActivityManager am = (ActivityManager) getSystemService(ACTIVITY_SERVICE);
        //保存杀死进程信息的集合
        List<TaskInfo> deleteTaskInfos = new ArrayList<TaskInfo>();
        
        for (int i = 0; i < userappinfo.size(); i++) {
            if (userappinfo.get(i).isChecked()) {
                //杀死进程
                //packageName : 进程的包名
                //杀死后台进程
                am.killBackgroundProcesses(userappinfo.get(i).getPackageName());
                deleteTaskInfos.add(userappinfo.get(i));//将杀死的进程信息保存的集合中
            }
        }
        //系统进程
        for (int i = 0; i < systemappinfo.size(); i++) {
            if (systemappinfo.get(i).isChecked()) {
                //杀死进程
                //packageName : 进程的包名
                //杀死后台进程
                am.killBackgroundProcesses(systemappinfo.get(i).getPackageName());
                deleteTaskInfos.add(systemappinfo.get(i));//将杀死的进程信息保存的集合中
            }
        }
        //遍历deleteTaskInfos,分别从userappinfo和systemappinfo中删除deleteTaskInfos中的数据
        for (TaskInfo taskInfo : deleteTaskInfos) {
            if (taskInfo.isUser()) {
                userappinfo.remove(taskInfo);
            }else{
                systemappinfo.remove(taskInfo);
            }
        }
        //为下次清理进程做准备
        deleteTaskInfos.clear();
        deleteTaskInfos=null;
        //更新界面
        myadapter.notifyDataSetChanged();
    }

    权限
    <uses-permission android:name="android.permission.KILL_BACKGROUND_PROCESSES"/>

<h2 id="清理细节处理">16、清理细节处理</h2>

    1.设置清理显示
        long memory=0;
        //遍历deleteTaskInfos,分别从userappinfo和systemappinfo中删除deleteTaskInfos中的数据
        for (TaskInfo taskInfo : deleteTaskInfos) {
            if (taskInfo.isUser()) {
                userappinfo.remove(taskInfo);
            }else{
                systemappinfo.remove(taskInfo);
            }
            memory+=taskInfo.getRamSize();
        }
        //数据转化
        String deletesize = Formatter.formatFileSize(getApplicationContext(), memory);
        Toast.makeText(getApplicationContext(), "共清理"+deleteTaskInfos.size()+"个进程,释放"+deletesize+"内存空间", 0).show();
    2.设置当前应用不能选中
        在条目中处理
                //3.根据之前保存的checkbox的状态设置点击之后的状态,原先选中,点击之后不选中
                if (taskInfo.isChecked()) {
                    taskInfo.setChecked(false);
                }else{
                    //如果是当前应用不能设置成true
                    if (!taskInfo.getPackageName().equals(getPackageName())) {
                        taskInfo.setChecked(true);
                    }
                }
        在全选中处理
            //用户进程
            for (int i = 0; i < userappinfo.size(); i++) {
                if (!userappinfo.get(i).getPackageName().equals(getPackageName())) {
                    userappinfo.get(i).setChecked(true);
                }
            }
    3.设置当前应用的checkbox隐藏
        //判断如果是我们的应用程序,就把checkbox隐藏,不是的话显示,在getview中有if必须有else
            if (taskinfo.getPackageName().equals(getPackageName())) {
                viewHolder.cb_itemtaskmanager_ischecked.setVisibility(View.INVISIBLE);
            }else{
                viewHolder.cb_itemtaskmanager_ischecked.setVisibility(View.VISIBLE);
            }
