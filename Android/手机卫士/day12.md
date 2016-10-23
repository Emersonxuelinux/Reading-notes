##目录
- [Day12](#day12)
    - [1、加锁解锁](#加锁解锁)
    - [2、监听用户打开程序](#监听用户打开程序)
    - [3、解锁界面优化](#解锁界面优化)
    - [4、解锁操作(重点:发送广播)](#解锁操作)
    - [5、两个bug解决方案(重点:启动模式)](#两个bug解决方案)
    - [6、数据库优化(重点:内容观察者)](#数据库优化)
    - [7、获取短信](#获取短信)
    - [8、备份短信(重点:创建xml文件)](#备份短信)
    - [9、备份效果(重点:progressdialog+回调函数)](#备份效果)
    - [10、短信还原](#短信还原)
    - [11、可扩展的listview](#可扩展的listview)
    - [12、抽屉效果(重点)](#抽屉效果)

<h2 id="day12">Day12</h2>
<h2 id="加锁解锁">1、加锁解锁</h2>

    1.在item的布局文件中增加imageview
    2.在getview中判断是否加锁
            //判断应用程序是加锁还是解锁
            if (watchDogDao.queryLockApp(appInfo.getPackagName())) {
                //加锁
                viewHolder.iv_itemsoftmanager_islock.setImageResource(R.drawable.lock);
            }else{
                //解锁
                viewHolder.iv_itemsoftmanager_islock.setImageResource(R.drawable.unlock);
            }
    3.使用长按点击事件实现加锁解锁
        /**
         * 长按点击事件
         */
        private void listviewItemLongClick() {
            lv_softmanager_application.setOnItemLongClickListener(new OnItemLongClickListener() {
    
                @Override
                public boolean onItemLongClick(AdapterView<?> parent, View view,
                        int position, long id) {
                    System.out.println("长按点击事件");
                    //true if the callback consumed the long click, false otherwise
                    //true:表示执行  false:拦截
                    //加锁解锁的操作
                    //屏蔽用户和系统程序(..个)不能加锁解锁操作
                    if (position == 0 || position == userappinfo.size()+1) {
                        return true;
                    }
                    //获取数据
                    if (position <= userappinfo.size()) {
                        //用户程序
                        appInfo = userappinfo.get(position-1);
                    }else{
                        //系统程序
                        appInfo = systemappinfo.get(position - userappinfo.size() - 2);
                    }
                    //加锁解锁
                    ViewHolder viewHolder = (ViewHolder) view.getTag();
                    //判断应用有没有加锁,有的解锁,没有的加锁
                    if (watchDogDao.queryLockApp(appInfo.getPackagName())) {
                        //解锁操作
                        watchDogDao.deleteLockApp(appInfo.getPackagName());
                        viewHolder.iv_itemsoftmanager_islock.setImageResource(R.drawable.unlock);
                    }else{
                        //加锁操作
                        watchDogDao.addLockApp(appInfo.getPackagName());
                        viewHolder.iv_itemsoftmanager_islock.setImageResource(R.drawable.lock);
                    }
                    //myadapter.notifyDataSetChanged();
                    return true;
                }
            });
        }

<h2 id="监听用户打开程序">2、监听用户打开程序</h2>

    1.创创建解锁界面
    2.在服务中,监听用户打开程序的操作中,通过查询数据库的方式判断打开的应用程序是否加锁
                //通过查询数据库,如果数据库中有,弹出解锁界面,没有不作处理
                        if (watchDogDao.queryLockApp(packageName)) {
                            //弹出解锁界面
                            Intent intent = new Intent(WatchDogService.this,WatchDogActivity.class);
                            intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                            startActivity(intent);
                        }
    3.在解锁界面点击返回键,重复弹出解锁界面
        @Override
        public boolean onKeyDown(int keyCode, KeyEvent event) {
            if (keyCode == KeyEvent.KEYCODE_BACK) {
                /**
                 * Starting: Intent { 
                   act=android.intent.action.MAIN 
                   cat=[android.intent.category.HOME
                   ] cmp=com.android.launcher/com.android.launcher2.Launcher } from pid 208
                 */
                //跳转到主界面
                Intent intent = new Intent();
                intent.setAction("android.intent.action.MAIN");
                intent.addCategory("android.intent.category.HOME");
                startActivity(intent);
                finish();
            }
            return super.onKeyDown(keyCode, event);
        }
    4.不能给当前应用程序加锁
                //判断如果是当前应用程序,就不要加锁
                    if (!appInfo.getPackagName().equals(getPackageName())) {
                        watchDogDao.addLockApp(appInfo.getPackagName());
                        viewHolder.iv_itemsoftmanager_islock.setImageResource(R.drawable.lock);
                    }else{
                        Toast.makeText(getApplicationContext(), "当前的应用程序不能加锁", 0).show();
                    }

<h2 id="解锁界面优化">3、解锁界面优化</h2>
 
    1.在服务跳转activity的操作中将加锁应用程序的包名传递给解锁界面
            Intent intent = new Intent(WatchDogService.this,WatchDogActivity.class);
            intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
            intent.putExtra("packageName", packageName);
            startActivity(intent);
    2.在解锁界面获取传递的数据,并通过包名获取图片和名称
        //接受获取数据
        Intent intent = getIntent();
        packagename = intent.getStringExtra("packageName");
        //设置显示加锁的应用程序的图片和名称
        PackageManager pm = getPackageManager();
        try {
            ApplicationInfo applicationInfo = pm.getApplicationInfo(packagename, 0);
            Drawable icon = applicationInfo.loadIcon(pm);
            String name = applicationInfo.loadLabel(pm).toString();
            //设置显示
            iv_watchdog_icon.setImageDrawable(icon);
            tv_watchdog_name.setText(name);
        } catch (NameNotFoundException e) {
            e.printStackTrace();
        }
    3.解决,点击home最小,打开其他加锁程序,显示上一个加锁程序的图片和名称的bug
        @Override
        protected void onStop() {
            super.onStop();
            finish();
        }

<h2 id="解锁操作">4、解锁操作(重点:发送广播)</h2>

    1.通过发送广播的方式,去给服务发送解锁信息
        if ("123".equals(password)) {
            //解锁
            //一般通过广播的形式将信息发送给服务
            Intent intent = new Intent();
            intent.setAction("com.itheima.mobliesafe75.unlock");//自定义发送广播事件
            intent.putExtra("packagename", packagename);
            sendBroadcast(intent);
            finish();
        }else{
            Toast.makeText(getApplicationContext(), "密码错误", 0).show();
        }
    2.在服务中,通过注册广播的形式实现接受信息并进行解锁操作
        注册广播接受者
            //注册解锁的广播接受者
            //1.广播接受者
            unlockCurrentReceiver = new UnlockCurrentReceiver();
            //2.设置接受的广播事件
            IntentFilter intentFilter = new IntentFilter();
            intentFilter.addAction("com.itheima.mobliesafe75.unlock");
        接受信息
            private class UnlockCurrentReceiver extends BroadcastReceiver{
    
                @Override
                public void onReceive(Context context, Intent intent) {
                    //解锁操作
                    unlockcurrentPackagname = intent.getStringExtra("packagename");
                }
            }
        解锁操作
            //通过查询数据库,如果数据库中有,弹出解锁界面,没有不作处理
            if (watchDogDao.queryLockApp(packageName)) {
                if (!packageName.equals(unlockcurrentPackagname)) {
                    //弹出解锁界面
                    Intent intent = new Intent(WatchDogService.this,WatchDogActivity.class);
                    intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                    intent.putExtra("packageName", packageName);
                    startActivity(intent);
                }
            }
        
    3.加锁操作,锁屏之后重新加锁
        a.注册锁屏的广播接受者
            //注册锁屏的广播接受者
            screenOffReceiver = new ScreenOffReceiver();
            IntentFilter screenOffIntentFilter = new IntentFilter();
            screenOffIntentFilter.addAction(Intent.ACTION_SCREEN_OFF);
            registerReceiver(screenOffReceiver, screenOffIntentFilter);
        b.在锁屏的广播接受者中将包名表示设置为null,表示加锁操作
            private class  ScreenOffReceiver extends BroadcastReceiver{

                @Override
                public void onReceive(Context context, Intent intent) {
                    //加锁的操作
                    unlockcurrentPackagname = null;
                }
            }

<h2 id="两个bug解决方案">5、两个bug解决方案(重点:启动模式)</h2>

    1.启动模式
        <activity android:name=".WatchDogActivity"
            android:launchMode="singleInstance"
            ></activity>
        <!-- 
            standard : 标准的启动模式
            singleTop : 如果activity在栈顶,直接使用
            singleTask : 如果任务栈中有activity的,之前的activity删除,将activity置顶
            singleInstance : 将activity单独存放一个任务栈中
         -->
    2.从最近列表中删除手机卫士应用
        android:excludeFromRecents="true"
        excludeFromRecents : 是否在最近列表中显示    true:不显示      false:显示,只有设置这个属性的activity运行了,这个属性才会生效

<h2 id="数据库优化">6、数据库优化(重点:内容观察者)</h2>

    1.当数据库发生变化,通知内容观察者进行更新数据,在数据库操作中,比如添加,删除
            //通知内容观察者数据库变化了
            ContentResolver contentResolver = context.getContentResolver();
            //因为是我们自己的数据发生变化了,所以我们要自定义一个uri进行操作
            Uri uri = Uri.parse("content://com.itheima.mobliesafe75.lock.changed");
            //通知内容观察者数据发生变化了
            contentResolver.notifyChange(uri, null);
    2.使用内容观察者进行数据更新操作
                //先将数据库中的数据,查询存放到内存,然后再把数据从内存中获取出来进行操作
                
                //当数据库变化的时候重新更新内存中的数据,当数据库变化的时候通知内容观察者数据库变化了,然后在内容观察者中去更新最新的数据
                Uri uri = Uri.parse("content://com.itheima.mobliesafe75.lock.changed");
                //notifyForDescendents:匹配规则,true:精确匹配  false:模糊匹配
                getContentResolver().registerContentObserver(uri, true, new ContentObserver(null) {
                    public void onChange(boolean selfChange) {
                        //更新数据
                        list = watchDogDao.querAllLockApp();
                    };
                });
    3.使用内存中的数据,使用list集合中的数据
                        //判断list集合中是否包含包名
                        boolean b = list.contains(packageName);
                        //通过查询数据库,如果数据库中有,弹出解锁界面,没有不作处理
                        if (b) {
                            if (!packageName.equals(unlockcurrentPackagname)) {
                                //弹出解锁界面
                                Intent intent = new Intent(WatchDogService.this,WatchDogActivity.class);
                                intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                                intent.putExtra("packageName", packageName);
                                startActivity(intent);
                            }
                        }

<h2 id="获取短信">7、获取短信</h2>

    /**
     * 获取短信
     */
    public static void getAllSMS(Context context){
        //1.获取内容解析者
        ContentResolver resolver = context.getContentResolver();
        //2.获取内容提供者地址   sms,sms表的地址:null  不写
        //3.获取查询路径
        Uri uri = Uri.parse("content://sms");
        //4.查询操作
        //projection : 查询的字段
        //selection : 查询的条件
        //selectionArgs : 查询条件的参数
        //sortOrder : 排序
        Cursor cursor = resolver.query(uri, new String[]{"address","date","type","body"}, null, null, null);
        //5.解析cursor
        while(cursor.moveToNext()){
            String address = cursor.getString(0);
            String date = cursor.getString(1);
            String type = cursor.getString(2);
            String body = cursor.getString(3);
            System.out.println("address:"+address+"   date:"+date+"  type:"+type+"  body:"+body);
        }
    }

    权限
     <uses-permission android:name="android.permission.READ_SMS"/>
     <uses-permission android:name="android.permission.WRITE_SMS"/>

<h2 id="备份短信">8、备份短信(重点:创建xml文件)</h2>

    1.数据库
    2.文件   xml

    /**
     * 获取短信
     */
    public static void getAllSMS(Context context){
        //1.获取短信
        //1.1获取内容解析者
        ContentResolver resolver = context.getContentResolver();
        //1.2获取内容提供者地址   sms,sms表的地址:null  不写
        //1.3获取查询路径
        Uri uri = Uri.parse("content://sms");
        //1.4.查询操作
        //projection : 查询的字段
        //selection : 查询的条件
        //selectionArgs : 查询条件的参数
        //sortOrder : 排序
        Cursor cursor = resolver.query(uri, new String[]{"address","date","type","body"}, null, null, null);
        
        
        //2.备份短信
        //2.1获取xml序列器
        XmlSerializer xmlSerializer = Xml.newSerializer();
        try {
            //2.2设置xml文件保存的路径
            //os : 保存的位置
            //encoding : 编码格式
            xmlSerializer.setOutput(new FileOutputStream(new File("/mnt/sdcard/backupsms.xml")), "utf-8");
            //2.3设置头信息
            //standalone : 是否独立保存
            xmlSerializer.startDocument("utf-8", true);
            //2.4设置根标签
            xmlSerializer.startTag(null, "smss");
            //1.5.解析cursor
            while(cursor.moveToNext()){
                //2.5设置短信的标签
                xmlSerializer.startTag(null, "sms");
                //2.6设置文本内容的标签
                xmlSerializer.startTag(null, "address");
                String address = cursor.getString(0);
                //2.7设置文本内容
                xmlSerializer.text(address);
                xmlSerializer.endTag(null, "address");
                
                xmlSerializer.startTag(null, "date");
                String date = cursor.getString(1);
                xmlSerializer.text(date);
                xmlSerializer.endTag(null, "date");
                
                xmlSerializer.startTag(null, "type");
                String type = cursor.getString(2);
                xmlSerializer.text(type);
                xmlSerializer.endTag(null, "type");
                
                xmlSerializer.startTag(null, "body");
                String body = cursor.getString(3);
                xmlSerializer.text(body);
                xmlSerializer.endTag(null, "body");
                
                xmlSerializer.endTag(null, "sms");
                System.out.println("address:"+address+"   date:"+date+"  type:"+type+"  body:"+body);
                
            }
            xmlSerializer.endTag(null, "smss");
            xmlSerializer.endDocument();
            //2.8将数据刷新到文件中
            xmlSerializer.flush();
        } catch (Exception e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } 
    }

<h2 id="备份效果">9、备份效果(重点:progressdialog+回调函数)</h2>

    1.progerssdialog
        //progressdialog是可以在子线程中更新
        progressDialog = new ProgressDialog(this);
        progressDialog.setCancelable(false);//不能取消
        progressDialog.setProgressStyle(ProgressDialog.STYLE_HORIZONTAL);
        /*progressDialog.setMax(max);//设置最大进度
        progressDialog.setProgress(value);//设置当前进度
        */progressDialog.show();
    2.使用progerssbar
        progerssbar.setMax(max);//设置最大进度
        progerssbar.setProgress(value)

    回调函数
        1.创建刷子
            public interface ShowProgress{
                //设置最大进度
                public void setMax(int max);
                //设置当前进度
                public void setProgress(int progerss);
            }
        2.媳妇儿给你刷子
            public static void getAllSMS(Context context,ShowProgress showProgress){
        
                //设置最大进度
                int count = cursor.getCount();//获取短信的个数
                showProgress.setMax(count);


                //设置当前进度
                progress++;
                showProgress.setProgress(progress);
            }
        3.我们接受刷子,进行刷牙,刷鞋,刷马桶.....
                SMSEngine.getAllSMS(getApplicationContext(),new ShowProgress() {
                    
                    @Override
                    public void setProgress(int progerss) {
                        //progressDialog.setProgress(progerss);
                        pb_atools_sms.setProgress(progerss);
                    }
                    
                    @Override
                    public void setMax(int max) {
                        //progressDialog.setMax(max);
                        pb_atools_sms.setMax(max);
                    }
                }); 

<h2 id="短信还原">10、短信还原</h2>

    /**
     * 短信还原
     * @param v
     */
    public void restoresms(View v){
        //解析xml
        //XmlPullParser xmlPullParser = Xml.newPullParser();
        //插入短信
        ContentResolver resolver = getContentResolver();
        Uri uri = Uri.parse("content://sms");
        ContentValues values = new ContentValues();
        values.put("address", "95588");
        values.put("date", System.currentTimeMillis());
        values.put("type", 1);
        values.put("body", "zhuan zhang le $10000000000000000000");
        resolver.insert(uri, values);
    }

<h2 id="可扩展的listview">11、可扩展的listview</h2>

    1.布局文件中
        <ExpandableListView 
        android:id="@+id/exlitview"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        ></ExpandableListView>
    2.使用
        exlitview = (ExpandableListView) findViewById(R.id.exlitview);
        exlitview.setAdapter(new MyAdapter());

        private class MyAdapter extends BaseExpandableListAdapter{
                //获取组的个数
                @Override
                public int getGroupCount() {
                    // TODO Auto-generated method stub
                    return 8;
                }
                //获取每组孩子的个数
                @Override
                public int getChildrenCount(int groupPosition) {
                    // TODO Auto-generated method stub
                    return groupPosition+1;
                }
                //设置组对应的相应的信息
                @Override
                public Object getGroup(int groupPosition) {
                    // TODO Auto-generated method stub
                    return null;
                }
                //设置每组孩子对应的信息
                //groupPosition ：组的位置
                //childPosition : 孩子的位置
                @Override
                public Object getChild(int groupPosition, int childPosition) {
                    // TODO Auto-generated method stub
                    return null;
                }
                //获取组的id
                @Override
                public long getGroupId(int groupPosition) {
                    // TODO Auto-generated method stub
                    return 0;
                }
                //获取每组孩子的id
                @Override
                public long getChildId(int groupPosition, int childPosition) {
                    // TODO Auto-generated method stub
                    return 0;
                }
                //获取id是否稳定,有没有设置id,有设置,返回true,没有设置返回false
                @Override
                public boolean hasStableIds() {
                    // TODO Auto-generated method stub
                    return false;
                }
                //设置组的样式
                @Override
                public View getGroupView(int groupPosition, boolean isExpanded,
                        View convertView, ViewGroup parent) {
                    TextView textView = new TextView(getApplicationContext());
                    textView.setText("         我是第"+groupPosition+"组");
                    textView.setTextColor(Color.RED);
                    return textView;
                }
                //设置每组孩子样式
                @Override
                public View getChildView(int groupPosition, int childPosition,
                        boolean isLastChild, View convertView, ViewGroup parent) {
                    
                    TextView textView = new TextView(getApplicationContext());
                    textView.setText("我是第"+groupPosition+"组的第"+childPosition+"个孩子");
                    textView.setTextColor(Color.GREEN);
                    return textView;
                }
                //设置孩子是否能够被点击,true:可以   false:不可以
                @Override
                public boolean isChildSelectable(int groupPosition, int childPosition) {
                    // TODO Auto-generated method stub
                    return false;
                }
                
            }

<h2 id="抽屉效果">12、抽屉效果(重点)</h2>

    slidingmenu  第三方法框架

    1.SlidingDrawer
        <SlidingDrawer 
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:handle="@+id/handle"
            android:content="@+id/content"
            android:orientation="vertical"
            >
            <LinearLayout 
                 android:id="@+id/handle"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                >
                <ImageView 
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:src="@drawable/shenmabg"
                />
            </LinearLayout>
            <LinearLayout 
                android:id="@+id/content"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:background="#ff0000"
                ></LinearLayout>
        </SlidingDrawer>
        
    2.android.support.v4.widget.DrawerLayout
        <android.support.v4.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical" 
            android:id="@+id/dl"
            >
        
           <RelativeLayout 
               android:layout_width="match_parent"
               android:layout_height="match_parent"
               >
            <TextView
                android:id="@+id/textView1"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:text="流量统计" 
                android:background="#8866ff00"
                android:gravity="center_horizontal"
                android:textSize="22sp"
                android:paddingTop="5dp"
                android:paddingBottom="5dp"
                />
            </RelativeLayout>
            <!-- layout_gravity : 在布局中决定控件存在位置 -->
            <LinearLayout 
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:layout_gravity="right"
                android:background="#ff0000"
                ></LinearLayout>
            <LinearLayout 
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:layout_gravity="left"
                android:background="#0000ff"
                ></LinearLayout>
        
    

    代码中使用
        DrawerLayout dl = (DrawerLayout) findViewById(R.id.dl);
        //dl.openDrawer(Gravity.RIGHT);//表示默认打开哪个方向布局
        dl.openDrawer(Gravity.LEFT);