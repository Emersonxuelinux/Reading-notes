###目录
- [Day09](#day09)
    - [1、查询全部数据](#查询全部数据)
    - [2、通讯卫士界面展示](#通讯卫士界面展示)
    - [3、listview复用缓存(重点)](#listview复用缓存)
    - [4、删除黑名单号码](#删除黑名单号码)
    - [5、添加黑名单(重点 : RadioGroup)](#添加黑名单)
    - [6、分批加载(重点)](#分批加载)
    - [7、短信拦截](#短信拦截)
    - [8、电话拦截(建议:看看反射)](#电话拦截)
    - [9、删除通话记录(重点:内容观察者)](#删除通话记录)
    - [10、获取系统应用程序信息(重点)](#获取系统应用程序信息)
    - [11、软件管理界面](#软件管理界面)

<h2 id="day09">Day09</h2>
<h2 id="查询全部数据">1、查询全部数据</h2>
    
    /**
     * 查询全部黑名单号码
     */
    public List<BlackNumInfo> queryAllBlackNum(){
        List<BlackNumInfo> list = new ArrayList<BlackNumInfo>();
        //1.获取数据库
        SQLiteDatabase database = blackNumOpenHlper.getReadableDatabase();
        //2.查询操作
        Cursor cursor = database.query(BlackNumOpenHlper.DB_NAME, new String[]{"blacknum","mode"}, null, null, null, null, null);
        //3.解析cursor
        while(cursor.moveToNext()){
            //获取查询出来的数据]
            String blacknum = cursor.getString(0);
            int mode = cursor.getInt(1);
            BlackNumInfo blackNumInfo = new BlackNumInfo(blacknum, mode);
            list.add(blackNumInfo);
        }
        //4.关闭数据库
        cursor.close();
        database.close();
        return list;
    }

<h2 id="通讯卫士界面展示">2、通讯卫士界面展示</h2>

    1.通过异步加载获取数据
        /**
         * 加载数据
         */
        private void fillData() {
            new MyAsycnTaks() {
    
                @Override
                public void preTask() {
                    loading.setVisibility(View.VISIBLE);
                }
                
                @Override
                public void postTask() {
                    lv_callsmssafe_blacknums.setAdapter(new MyAdapter());
                    loading.setVisibility(View.INVISIBLE);
                }
                
                @Override
                public void doinBack() {
                    list = blackNumDao.queryAllBlackNum();
                }
            }.execute();
        }
    2.通过listview展示数据
        //设置条目显示的样式
        @Override
        public View getView(int position, View convertView, ViewGroup parent) {
            //根据条目的位置获取对应的bean对象
            BlackNumInfo blackNumInfo = list.get(position);
            View view = View.inflate(getApplicationContext(), R.layout.item_callsmssafe, null);
            TextView tv_itemcallsmssafe_blacknum = (TextView) view.findViewById(R.id.tv_itemcallsmssafe_blacknum);
            TextView tv_itemcallsmssafe_mode = (TextView) view.findViewById(R.id.tv_itemcallsmssafe_mode);
            ImageView iv_itemcallsmssafe_delete = (ImageView) view.findViewById(R.id.iv_itemcallsmssafe_delete);
            
            //设置显示数据
            tv_itemcallsmssafe_blacknum.setText(blackNumInfo.getBlacknum());
            int mode = blackNumInfo.getMode();
            switch (mode) {
            case BlackNumDao.CALL:
                tv_itemcallsmssafe_mode.setText("电话拦截");
                break;
            case BlackNumDao.SMS:
                tv_itemcallsmssafe_mode.setText("短信拦截");
                break;
            case BlackNumDao.ALL:
                tv_itemcallsmssafe_mode.setText("全部拦截");
                break;
            }
            return view;
        }

<h2 id="listview复用缓存">3、listview复用缓存(重点)</h2>

    1.在activity创建存放控件的容器
        /**
         * 存放控件的容器
         * @author Administrator
         *
         */
        class ViewHolder{
            TextView tv_itemcallsmssafe_blacknum,tv_itemcallsmssafe_mode;
            ImageView iv_itemcallsmssafe_delete;
        }
    2.在创建条目view对象的时候,将控件保存到控件的容器中,并将容器绑定到view对象中
            ViewHolder viewHolder;
            if (convertView == null) {
                view = View.inflate(getApplicationContext(), R.layout.item_callsmssafe, null);
                //创建控件的容器
                viewHolder = new ViewHolder();
                //把控件存放到容器中
                viewHolder.tv_itemcallsmssafe_blacknum = (TextView) view.findViewById(R.id.tv_itemcallsmssafe_blacknum);
                viewHolder.tv_itemcallsmssafe_mode = (TextView) view.findViewById(R.id.tv_itemcallsmssafe_mode);
                viewHolder.iv_itemcallsmssafe_delete = (ImageView) view.findViewById(R.id.iv_itemcallsmssafe_delete);
                //将容器和view对象绑定在一起
                view.setTag(viewHolder);
    3.复用缓存的时候,将控件的容器从复用的view对象中拿出来
            }else{
                view = convertView;
                //从view对象中得到控件的容器
                viewHolder = (ViewHolder) view.getTag();
            }
    4.使用,可以直接使用复用的控件的容器中存放已经findviewbyid过的控件,就是在使用已已经findviewbyid过的控件
            //设置显示数据
            viewHolder.tv_itemcallsmssafe_blacknum.setText(blackNumInfo.getBlacknum());

<h2 id="删除黑名单号码">4、删除黑名单号码</h2>

            /**
             * 删除黑名单
             */
            viewHolder.iv_itemcallsmssafe_delete.setOnClickListener(new OnClickListener() {
                
                @Override
                public void onClick(View v) {
                    //删除黑名单操作
                    AlertDialog.Builder builder = new Builder(CallSmsSafeActivity.this);
                    builder.setMessage("您确认要删除黑名单号码:"+blackNumInfo.getBlacknum()+"?");
                    //设置确定和取消按钮
                    builder.setPositiveButton("确定", new DialogInterface.OnClickListener(){

                        @Override
                        public void onClick(DialogInterface dialog, int which) {
                            //删除黑名单操作
                            //1.删除数据库中的黑名单号码
                            blackNumDao.deleteBlackNum(blackNumInfo.getBlacknum());
                            //2.删除界面中已经显示黑名单号码
                            //2.1从存放有所有数据的list集合中删除相应的数据
                            list.remove(position);//删除条目对应位置的相应的数据
                            //2.2更新界面
                            myAdapter.notifyDataSetChanged();//更新界面
                            //3.隐藏对话框
                            dialog.dismiss();
                        }
                    });
                    builder.setNegativeButton("取消", null);
                    builder.show();
                }
            });

<h2 id="添加黑名单">5、添加黑名单(重点 : RadioGroup)</h2>

    1.添加黑名单操作
        /**
         * 添加黑名单点击事件
         * @param v
         */
        public void addBlackNum(View v){
            //弹出对话框,让用户去添加黑名单
            AlertDialog.Builder builder = new Builder(this);
            View view = View.inflate(getApplicationContext(), R.layout.dialog_add_blacknum, null);
            //初始化控件,执行添加操作
            final EditText et_addblacknum_blacknum = (EditText) view.findViewById(R.id.et_addblacknum_blacknum);
            final RadioGroup rg_addblacknum_modes = (RadioGroup) view.findViewById(R.id.rg_addblacknum_modes);
            Button btn_ok = (Button) view.findViewById(R.id.btn_ok);
            Button btn_cancle = (Button) view.findViewById(R.id.btn_cancle);
            
            //设置按钮的点击事件
            btn_ok.setOnClickListener(new OnClickListener() {
                
                @Override
                public void onClick(View v) {
                    //添加黑名单号码操作
                    //1.获取输入的黑名单号码
                    String blacknum = et_addblacknum_blacknum.getText().toString().trim();
                    //2.判断获取的内容是否为空
                    if (TextUtils.isEmpty(blacknum)) {
                        Toast.makeText(getApplicationContext(), "请输入黑名单号码", 0).show();
                        return;
                    }
                    //3.获取拦截模式
                    int mode = -1;
                    int radioButtonId = rg_addblacknum_modes.getCheckedRadioButtonId();//获取选中的RadioButton的id
                    switch (radioButtonId) {
                    case R.id.rb_addblacknum_tel:
                        //电话拦截
                        mode = BlackNumDao.CALL;
                        break;
                    case R.id.rb_addblacknum_sms:
                        //短信拦截
                        mode = BlackNumDao.SMS;
                        break;
                    case R.id.rb_addblacknum_all:
                        //全部拦截
                        mode = BlackNumDao.ALL;
                        break;
                    }
                    //4.添加黑名单
                    //1.添加到数据库
                    blackNumDao.addBlackNum(blacknum, mode);
                    //2.添加到界面显示
                    //2.1添加到list集合中
                    //list.add(new BlackNumInfo(blacknum, mode));
                    list.add(0, new BlackNumInfo(blacknum, mode));//location : 参数2要添加到位置,参数2:添加数据
                    //2.2更新界面
                    myAdapter.notifyDataSetChanged();
                    //隐藏对话哭给你
                    dialog.dismiss();
                }
            });
            //设置取消按钮的点击事件
            btn_cancle.setOnClickListener(new OnClickListener() {
                
                @Override
                public void onClick(View v) {
                    //隐藏对话框
                    dialog.dismiss();
                }
            });
            
            builder.setView(view);
            //builder.show();
            dialog = builder.create();
            dialog.show();
        }
    2.修改查询数据的方式为倒序查询
        Cursor cursor = database.query(BlackNumOpenHlper.DB_NAME, new String[]{"blacknum","mode"}, null, null, null, null, "_id desc");//desc倒序查询,asc:正序查询,默认正序查询

<h2 id="分批加载">6、分批加载(重点)</h2>

    select blacknum from info order by _id desc limit 20 offset 20
    limit : 查询的总个数  offset :查询的其实位置
    select blacknum from info order by _id desc limit 0,20
    前面:查询的起始位置   后面:查询的总个数

    1.创建加载部分数据的方法
        /**
         * 查询部分数据
         * 查询20条数据
         * MaxNum : 查询的总个数
         * startindex : 查询的起始位置
         * @return
         */
        public List<BlackNumInfo> getPartBlackNum(int MaxNum,int startindex){
            List<BlackNumInfo> list = new ArrayList<BlackNumInfo>();
            //1.获取数据库
            SQLiteDatabase database = blackNumOpenHlper.getReadableDatabase();
            //2.查询操作
            //Cursor cursor = database.query(BlackNumOpenHlper.DB_NAME, new String[]{"blacknum","mode"}, null, null, null, null, "_id desc");//desc倒序查询,asc:正序查询,默认正序查询
            Cursor cursor = database.rawQuery("select blacknum,mode from info order by _id desc limit ? offset ?", new String[]{MaxNum+"",startindex+""});
            //3.解析cursor
            while(cursor.moveToNext()){
                //获取查询出来的数据]
                String blacknum = cursor.getString(0);
                int mode = cursor.getInt(1);
                BlackNumInfo blackNumInfo = new BlackNumInfo(blacknum, mode);
                list.add(blackNumInfo);
            }
            //4.关闭数据库
            cursor.close();
            database.close();
            return list;
        }
    2.更改加载数据方式
        @Override
            public void postTask() {
                if (myAdapter == null) {
                    myAdapter = new MyAdapter();
                    lv_callsmssafe_blacknums.setAdapter(myAdapter);
                }else{
                    myAdapter.notifyDataSetChanged();
                }
                loading.setVisibility(View.INVISIBLE);
            }
            
            @Override
            public void doinBack() {
                if (list == null) {
                    //1.2.3    4.5.6
                    list = blackNumDao.getPartBlackNum(MAXNUM,startIndex);
                }else{
                    //addAll : 将一个集合整合到另一个集合
                    //A [1.2.3] B[4.5.6]
                    //A.addAll(B)  A [1.2.3.4.5.6]
                    list.addAll(blackNumDao.getPartBlackNum(MAXNUM,startIndex));
                }
            }   
    3.实现滑动加载更多操作
            //listview滑动监听事件
            lv_callsmssafe_blacknums.setOnScrollListener(new OnScrollListener() {
                //当滑动状态改变的时候调用的方法
                //view : listview
                //scrollState : 滑动状态
                //SCROLL_STATE_IDLE : 空闲的状态
                //SCROLL_STATE_TOUCH_SCROLL : 缓慢滑动的状态
                //SCROLL_STATE_FLING : 快速滑动
                @Override
                public void onScrollStateChanged(AbsListView view, int scrollState) {
                    //当listview静止的时候判断界面显示的最后一个条目是否是查询数据的最后一个条目,是加载下一波数据,不是用户进行其他操作
                    if (scrollState == OnScrollListener.SCROLL_STATE_IDLE) {
                        //获取界面显示最后一个条目
                        int position = lv_callsmssafe_blacknums.getLastVisiblePosition();//获取界面显示最后一个条目,返回的时候条目的位置
                        //判断是否是查询数据的最后一个数据  20   0-19
                        if (position == list.size()-1) {
                            //加载下一波数据
                            //更新查询的其实位置   0-19    20-39
                            startIndex+=MAXNUM;
                            fillData();
                        }
                    }
                }
                //当滑动的时候调用的方法
                @Override
                public void onScroll(AbsListView view, int firstVisibleItem,
                        int visibleItemCount, int totalItemCount) {
                    // TODO Auto-generated method stub
                    
                }
            });

<h2 id="短信拦截">7、短信拦截</h2>

    1.创建一个服务,在其中去实现拦截功能
    2.在设置中心中模仿显示号码归属地添加一个条目,拷贝代码修改
    3.在服务中注册广播接受者
        a.创建广播接受者
            private class SmsReceiver extends BroadcastReceiver{

                @Override
                public void onReceive(Context context, Intent intent) {
                    
                }
            }
        b.注册广播接受者
            //代码注册短信到来的广播接受者
            //1.创建广播接受者
            smsReceiver = new SmsReceiver();
            //2.设置监听广播事件
            IntentFilter intentFilter = new IntentFilter();
            //广播接受者的最高优先级Integer.MAX_VALUE,优先级相同,代码注册的广播接受者先接受广播事件
            intentFilter.setPriority(1000);
            intentFilter.addAction("android.provider.Telephony.SMS_RECEIVED");
            //3.注册广播接受者
            registerReceiver(smsReceiver, intentFilter);
        c.注销广播接受者
            @Override
            public void onDestroy() {
                super.onDestroy();
                unregisterReceiver(smsReceiver);
            }
        d.实现操作
                @Override
                public void onReceive(Context context, Intent intent) {
                    System.out.println("代码注册广播接受者接受短信");
                    //接受解析短信的操作
                    Object[] objs = (Object[]) intent.getExtras().get("pdus");
                    for(Object obj:objs){
                        //解析成SmsMessage
                        SmsMessage smsMessage = SmsMessage.createFromPdu((byte[]) obj);
                        String body = smsMessage.getMessageBody();//获取短信的内容
                        String sender = smsMessage.getOriginatingAddress();//获取发件人
                        //根据发件人号码,获取号码的拦截模式
                        int mode = blackNumDao.queryBlackNumMode(sender);
                        //判断是否是短信拦截或者是全部拦截
                        if (mode == BlackNumDao.SMS || mode == BlackNumDao.ALL) {
                            abortBroadcast();//拦截广播事件,拦截短信操作
                        }
                    }
                }

<h2 id="电话拦截">8、电话拦截(建议:看看反射)</h2>

    1.通过telePhoneManager监听电话的状态
        private class MyPhoneStateListener extends PhoneStateListener{
            @Override
            public void onCallStateChanged(int state, String incomingNumber) {
                super.onCallStateChanged(state, incomingNumber);
                //如果是响铃状态,检测拦截模式是否是电话拦截,是挂断
                if (state == TelephonyManager.CALL_STATE_RINGING) {
                    //获取拦截模式
                    int mode = blackNumDao.queryBlackNumMode(incomingNumber);
                    if (mode == BlackNumDao.CALL || mode == BlackNumDao.ALL) {
                        //挂断电话 1.5
                        endCall();
                    }
                }
            }
        }
    2.通过反射实现挂断电话
        /**
         * 挂断电话
         */
        public void endCall() {
            
            //通过反射进行实现
            try {
                //1.通过类加载器加载相应类的class文件
                //Class<?> forName = Class.forName("android.os.ServiceManager");
                Class<?> loadClass = BlackNumService.class.getClassLoader().loadClass("android.os.ServiceManager");
                //2.获取类中相应的方法
                //name : 方法名
                //parameterTypes : 参数类型
                Method method = loadClass.getDeclaredMethod("getService", String.class);
                //3.执行方法,获取返回值
                //receiver : 类的实例
                //args : 具体的参数
                IBinder invoke = (IBinder) method.invoke(null, Context.TELEPHONY_SERVICE);
                //aidl
                ITelephony iTelephony = ITelephony.Stub.asInterface(invoke);
                //挂断电话
                iTelephony.endCall();
            } catch (Exception e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            }
        }
    3.权限
        <uses-permission android:name="android.permission.CALL_PHONE"/>

<h2 id="删除通话记录">9、删除通话记录(重点:内容观察者)</h2>

                    //删除通话记录
                    //1.获取内容解析者
                    final ContentResolver resolver = getContentResolver();
                    //2.获取内容提供者地址  call_log   calls表的地址:calls
                    //3.获取执行操作路径
                    final Uri uri = Uri.parse("content://call_log/calls");
                    //4.删除操作
                    //resolver.delete(uri, "number=?", new String[]{incomingNumber});
                    //通过内容观察者观察内容提供者内容,如果变化,就去执行删除操作
                    //notifyForDescendents : 匹配规则,true : 精确匹配   false:模糊匹配
                    resolver.registerContentObserver(uri, true, new ContentObserver(new Handler()) {
                        //内容提供者内容变化的时候调用
                        @Override
                        public void onChange(boolean selfChange) {
                            super.onChange(selfChange);
                            //删除通话记录
                            resolver.delete(uri, "number=?", new String[]{incomingNumber});
                            //注销内容观察者
                            resolver.unregisterContentObserver(this);
                        }
                    });

<h2 id="获取系统应用程序信息">10、获取系统应用程序信息(重点)</h2>

    /**
     * 获取系统中所有应用程序的信息
     */
    public static List<AppInfo> getAppInfos(Context context){
        List<AppInfo> list = new ArrayList<AppInfo>();
        //获取应用程序信息
        //包的管理者
        PackageManager pm = context.getPackageManager();
        //获取系统中安装到所有软件信息
        List<PackageInfo> installedPackages = pm.getInstalledPackages(0);
        for (PackageInfo packageInfo : installedPackages) {
            //获取包名
            String packageName = packageInfo.packageName;
            //获取版本号
            String versionName = packageInfo.versionName;
            //获取application
            ApplicationInfo applicationInfo = packageInfo.applicationInfo;
            //获取应用程序的图标
            Drawable icon = applicationInfo.loadIcon(pm);
            //获取应用程序的名称
            String name = applicationInfo.loadLabel(pm).toString();
            //是否是用户程序
            //获取应用程序中相关信息,是否是系统程序和是否安装到SD卡
            boolean isUser;
            int flags = applicationInfo.flags;
            if ((applicationInfo.FLAG_SYSTEM & flags) == applicationInfo.FLAG_SYSTEM) {
                //系统程序
                isUser = false;
            }else{
                //用户程序
                isUser = true;
            }
            //是否安装到SD卡
            boolean isSD;
            if ((applicationInfo.FLAG_EXTERNAL_STORAGE & flags) == applicationInfo.FLAG_EXTERNAL_STORAGE) {
                //安装到了SD卡
                isSD = true;
            }else{
                //安装到手机中
                isSD = false;
            }
            //添加到bean中
            AppInfo appInfo = new AppInfo(name, icon, packageName, versionName, isSD, isUser);
            //将bean存放到list集合
            list.add(appInfo);
        }
        return list;
    }

<h2 id="软件管理界面">11、软件管理界面</h2>

    1.使用异步加载框架,加载数据
        /**
         * 加载数据
         */
        private void fillData() {
            new MyAsycnTaks() {
                
                @Override
                public void preTask() {
                    loading.setVisibility(View.VISIBLE);
                }
                
                @Override
                public void postTask() {
                    lv_softmanager_application.setAdapter(new Myadapter());
                    loading.setVisibility(View.INVISIBLE);
                }
                
                @Override
                public void doinBack() {
                    list = AppEngine.getAppInfos(getApplicationContext());
                }
            }.execute();
        }
    2.通过listview展示数据
        @Override
        public View getView(int position, View convertView, ViewGroup parent) {
            View view;
            ViewHolder viewHolder;
            if (convertView != null ) {
                view = convertView;
                viewHolder = (ViewHolder) view.getTag();
            }else{
                view = View.inflate(getApplicationContext(), R.layout.item_softmanager, null);
                viewHolder = new ViewHolder();
                viewHolder.iv_itemsoftmanage_icon = (ImageView) view.findViewById(R.id.iv_itemsoftmanage_icon);
                viewHolder.tv_softmanager_name = (TextView) view.findViewById(R.id.tv_softmanager_name);
                viewHolder.tv_softmanager_issd = (TextView) view.findViewById(R.id.tv_softmanager_issd);
                viewHolder.tv_softmanager_version = (TextView) view.findViewById(R.id.tv_softmanager_version);
                //将viewholer和view对象绑定
                view.setTag(viewHolder);
            }
            //1.获取应用程序的信息
            AppInfo appInfo = list.get(position);
            //设置显示数据
            viewHolder.iv_itemsoftmanage_icon.setImageDrawable(appInfo.getIcon());
            viewHolder.tv_softmanager_name.setText(appInfo.getName());
            boolean sd = appInfo.isSD();
            if (sd) {
                viewHolder.tv_softmanager_issd.setText("SD卡");
            }else{
                viewHolder.tv_softmanager_issd.setText("手机内存");
            }
            viewHolder.tv_softmanager_version.setText(appInfo.getVersionName());
            return view;
        }


        static class ViewHolder{
            ImageView iv_itemsoftmanage_icon;
            TextView tv_softmanager_name,tv_softmanager_issd,tv_softmanager_version;
        }
