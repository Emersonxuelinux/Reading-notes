###目录
- [Day08](#day08)
    - [1、设置外拨电话和来电界面toast的位置](#设置外拨电话和来电界面toast的位置)
    - [2、位置回显操作(重点:layoutparams)](#位置回显操作)
    - [3、防止控件移出屏幕的操作](#防止控件移出屏幕的操作)
    - [4、触摸事件和点击的冲突问题](#触摸事件和点击的冲突问题)
    - [5、双击居中(重点)](#双击居中)
    - [6、细节的处理](#细节的处理)
    - [7、外拨电话和来电显示界面toast的拖动(重点:触摸监听事件)](#外拨电话和来电显示界面toast的拖动)
    - [8、小火箭](#小火箭)
    - [9、创建数据库(重点)](#创建数据库)
    - [10、添加黑名单(重点)](#添加黑名单)
    - [11、数据其他操作(重点)](#数据其他操作)
    - 

<h2 id="day08">Day08</h2>
<h2 id="设置外拨电话和来电界面toast的位置">1、设置外拨电话和来电界面toast的位置</h2>

    1.在设置归属地提示框位置界面中保存控件移动到的新的位置的坐标,在up事件中
        case MotionEvent.ACTION_UP:
                    //抬起的事件
                    System.out.println("抬起了....");
                    //保存控件的坐标,保存的是控件的坐标不是手指坐标
                    //获取控件的坐标
                    int x = ll_dragview_toast.getLeft();
                    int y = ll_dragview_toast.getTop();
                    
                    Editor edit = sp.edit();
                    edit.putInt("x", x);
                    edit.putInt("y", y);
                    edit.commit();
                    break;
    2.在addressservice中的showToast方法中,根据保存的坐标设置toast的位置
         params.x = sp.getInt("x", 100);//不是坐标,表示的距离边框的距离,根据gravity来设置的,如果gravity是left表示距离左边框的距离,如果是right表示距离有边框的距离
         params.y = sp.getInt("y", 100);//跟x的含义

<h2 id="位置回显操作">2、位置回显操作(重点:layoutparams)</h2>

    控制正常显示出来
        1.测量控件的宽高
        2.分配控件的位置   
        3.绘制控件

    回显操作
        //设置控件回显操作
        //1.获取保存的坐标
        int x = sp.getInt("x", 0);
        int y = sp.getInt("y", 0);
        System.out.println("x:"+x+" y:"+y);
        //2.重新绘制控件
        /*int width = ll_dragview_toast.getWidth();
        int height = ll_dragview_toast.getHeight();
        System.out.println("width:"+width+" height:"+height);
        ll_dragview_toast.layout(x, y, x+width, y+height);*/
        //在初始化控件之前重新设置控件的属性
        //2.1获取父控件的属性规则,父控件的layoutparams
        RelativeLayout.LayoutParams params = (LayoutParams) ll_dragview_toast.getLayoutParams();
        //2.2设置相应的属性
        //leftMargin : 距离父控件左边的距离,根据布局文件中控件中layout_marginLeft属性效果相似
        params.leftMargin = x;
        params.topMargin = y;
        //2.3给控件设置属性
        ll_dragview_toast.setLayoutParams(params);

<h2 id="防止控件移出屏幕的操作">3、防止控件移出屏幕的操作</h2>

    在move事件中,在绘制控件之前判断
        //在绘制控件之前,判断ltrb的值是否超出屏幕的大小,如果是就不在进行绘制控件的操作
        if (l < 0 || r > width || t < 0 || b > height - 25) {
            break;
        }

<h2 id="触摸事件和点击的冲突问题">4、触摸事件和点击的冲突问题</h2>

    点击事件 : 一组事件的集合,按下+抬起
    触摸事件 : 每个事件都是一个单独的事件,按下,移动,抬起
    
    如果只有触摸事件:return true表示事件执行
    如果点击事件和触摸事件共同存在,触摸事件的事件是会点击事件消费掉

    先执行触摸   然后执行其他事件
    
    点击 按下+抬起

    return false
    触摸 :  按下 执行完  ->  移动  执行完 -> 抬起 
    触摸+点击 :   按下(触摸) 拦截 - > 按下(点击) 执行 - > 抬起(触摸) 拦截 - > 抬起(点击) 执行 - > 输出(触摸) - > 输出(点击)

    return true
    
    触摸 : 按下 -> 移动 -> 抬起
    触摸+点击  按下(触摸) 执行 ->  移动(触摸) -> 抬起(触摸)

    
    如果只有触摸事件:return true;
    如果触摸+点击事件:触摸事件return false

<h2 id="双击居中">5、双击居中(重点)</h2>

    long[] mHits = new long[2];
    /**
     * 双击居中
     */
    private void setDoubleClick() {
        ll_dragview_toast.setOnClickListener(new OnClickListener() {
            
            @Override
            public void onClick(View v) {
                /**
                 *  src the source array to copy the content.   拷贝的原数组
                    srcPos the starting index of the content in src.  是从源数组那个位置开始拷贝
                    dst the destination array to copy the data into.  拷贝的目标数组
                    dstPos the starting index for the copied content in dst.  是从目标数组那个位置开始去写
                    length the number of elements to be copied.   拷贝的长度
                 */
                //拷贝数组操作
                System.arraycopy(mHits, 1, mHits, 0, mHits.length-1);
                mHits[mHits.length-1] = SystemClock.uptimeMillis();  // 将离开机的时间设置给数组的第二个元素,离开机时间 :毫秒值,手机休眠不算
                if (mHits[0] >= (SystemClock.uptimeMillis()-500)) {  // 判断是否多击操作
                    System.out.println("双击了...");
                    //双击居中
                    int l = (width - ll_dragview_toast.getWidth())/2;
                    int t = (height -25- ll_dragview_toast.getHeight())/2;
                    ll_dragview_toast.layout(l, t, l+ll_dragview_toast.getWidth(), t+ll_dragview_toast.getHeight());
                    //保存控件的坐标
                    Editor edit = sp.edit();
                    edit.putInt("x", l);
                    edit.putInt("y", t);
                    edit.commit();
                }
            }
        });
    }

<h2 id="细节的处理">6、细节的处理</h2>

    1.在布局文件中添加两个一模一样的textview,一个在上方,一个在下方
    2.在move事件根据控件距离顶部的距离判断隐藏显示
                    //判断textview的隐藏显示
                    int top = ll_dragview_toast.getTop();
                    if (top >= height/2) {
                        //隐藏下方显示上方
                        tv_dragview_bottom.setVisibility(View.INVISIBLE);
                        tv_dragview_top.setVisibility(View.VISIBLE);
                    }else{
                        //隐藏上方显示下方
                        tv_dragview_top.setVisibility(View.INVISIBLE);
                        tv_dragview_bottom.setVisibility(View.VISIBLE);
                    }
    3.在回显操作的时候也进行相应的判断
            if (y >= height/2) {
                //隐藏下方显示上方
                tv_dragview_bottom.setVisibility(View.INVISIBLE);
                tv_dragview_top.setVisibility(View.VISIBLE);
            }else{
                //隐藏上方显示下方
                tv_dragview_top.setVisibility(View.INVISIBLE);
                tv_dragview_bottom.setVisibility(View.VISIBLE);
            }

<h2 id="外拨电话和来电显示界面toast的拖动">7、外拨电话和来电显示界面toast的拖动(重点:触摸监听事件)</h2>

    1.给toast设置触摸监听事件
        /**
         * toast触摸监听事件
         */
        private void setTouch() {
            view.setOnTouchListener(new OnTouchListener() {
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
                        params.x+=dX;
                        params.y+=dY;
                        //控制控件的坐标不能移出外拨电话界面
                        if (params.x < 0) {
                            params.x = 0;
                        }
                        if (params.y < 0) {
                            params.y=0;
                        }
                        if (params.x > width-view.getWidth()) {
                            params.x = width-view.getWidth();
                        }
                        if (params.y > height - view.getHeight() - 25) {
                            params.y = height - view.getHeight() - 25;
                        }
                        
                        windowManager.updateViewLayout(view, params);//更新windowmanager中的控件
                        //5.更新开始的坐标
                        startX=newX;
                        startY=newY;
                        break;
                    case MotionEvent.ACTION_UP:
                        //抬起的事件
                        System.out.println("抬起了....");
                        //保存控件的坐标,保存的是控件的坐标不是手指坐标
                        //获取控件的坐标
                        int x = params.x;
                        int y = params.y;
                        
                        Editor edit = sp.edit();
                        edit.putInt("x", x);
                        edit.putInt("y", y);
                        edit.commit();
                        break;
                    }
                    //True if the listener has consumed the event, false otherwise.
                    //true:事件消费了,执行了,false:表示事件被拦截了
                    return false;
                }
            });
        }
    2.修改相应的属性
        params.flags = WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE // 没有焦点
                |
                // WindowManager.LayoutParams.FLAG_NOT_TOUCHABLE // 不可触摸
                // |
                WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON; // 保持当前屏幕
        params.format = PixelFormat.TRANSLUCENT; // 透明
        params.type = WindowManager.LayoutParams.TYPE_PRIORITY_PHONE; // 使用TYPE_PHONE//// 执行toast的类型,toast天生是没有获取见到和触摸事件
    3.权限
         <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>

<h2 id="小火箭">8、小火箭</h2>

    10-15 06:18:10.301: E/AndroidRuntime(15436): android.util.AndroidRuntimeException: Calling startActivity() from outside of an Activity  context requires the FLAG_ACTIVITY_NEW_TASK flag. Is this really what you want?  //没有给activity设置任务栈

    1.触摸监听事件
        /**
         * 小火箭设置触摸监听事件
         */
        private void setTouch() {
            view.setOnTouchListener(new OnTouchListener() {
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
                        params.x+=dX;
                        params.y+=dY;
                        //控制控件的坐标不能移出外拨电话界面
                        if (params.x < 0) {
                            params.x = 0;
                        }
                        if (params.y < 0) {
                            params.y=0;
                        }
                        if (params.x > width-view.getWidth()) {
                            params.x = width-view.getWidth();
                        }
                        if (params.y > height - view.getHeight() - 25) {
                            params.y = height - view.getHeight() - 25;
                        }
                        
                        windowManager.updateViewLayout(view, params);//更新windowmanager中的控件
                        //5.更新开始的坐标
                        startX=newX;
                        startY=newY;
                        break;
                    case MotionEvent.ACTION_UP:
                        //抬起的事件
                        System.out.println("抬起了....");
                        //发射小火箭
                        //判断小火箭是否在屏幕底部中间的位置,在就可以发送,不在不能发送
                        if (params.y > 290 && params.x > 100 && params.x < 200) {
                            sendRocket();
                            
                            //开启烟雾动画
                            Intent intent = new Intent(RocketService.this,BackGroundActivity.class);
                            //给跳转了activity设置任务栈
                            intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                            startActivity(intent);
                        }
                        break;
                    }
                    //True if the listener has consumed the event, false otherwise.
                    //true:事件消费了,执行了,false:表示事件被拦截了
                    return true;
                }
            });
        }
    2.帧动画
        a.res -> anim - > xxx.xml
            <animation-list xmlns:android="http://schemas.android.com/apk/res/android"
                android:oneshot="false"> <!-- oneshot : 是否执行一次    true:执行一次   false:循环执行 -->
                <item android:drawable="@drawable/desktop_rocket_launch_1" android:duration="200" />
                <item android:drawable="@drawable/desktop_rocket_launch_2" android:duration="200" />
            </animation-list>
        b.使用
            AnimationDrawable animationDrawable = (AnimationDrawable) iv_rocket.getBackground();
            animationDrawable.start();//开始动画
    3.渐变动画
        AlphaAnimation alphaAnimation = new AlphaAnimation(0, 1);
        alphaAnimation.setDuration(200);
        ImageView smoke_m = (ImageView) findViewById(R.id.smoke_m);
        ImageView smoke_t = (ImageView) findViewById(R.id.smoke_t);
        
        //执行动画
        smoke_m.startAnimation(alphaAnimation);
        smoke_t.startAnimation(alphaAnimation);

<h2 id="创建数据库">9、创建数据库(重点)</h2>

    1.创建一个类继承SQLiteOpenHelper
        //创建数据库
        public class BlackNumOpenHlper extends SQLiteOpenHelper {
            //方便后期我们在实现数据库操作的时候能方便去使用表名,同时也方便后期去修改表名
            private static final String DB_NAME="info";
            /**
             * context : 上下文
             * name :　数据库名称
             * factory：游标工厂
             * version : 数据库的版本号
             * @param context
             */
            public BlackNumOpenHlper(Context context) {
                super(context, "blacknum.db", null, 1);
            }
            //第一次创建数据库的调用,创建表结构
            @Override
            public void onCreate(SQLiteDatabase db) {
                //创建表结构   字段:   blacknum:黑名单号码     mode:拦截类型
                //参数:创建表结构sql语句
                db.execSQL("create table "+DB_NAME+"(_id integer primary key autoincrement,blacknum varchar(20),mode varchar(2))");
            }
            //当数据库版本发生变化的时候调用
            @Override
            public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        
            }
        
        }
    2.单元测试
        public void testBlackNumOpenHelper(){
            BlackNumOpenHlper blackNumOpenHlper = new BlackNumOpenHlper(getContext());//不能创建数据库
            blackNumOpenHlper.getReadableDatabase();//创建数据库
        }

<h2 id="添加黑名单">10、添加黑名单(重点)</h2>

    /**
     * 添加黑名单
     * @param blacknum
     * @param mode
     */
    public void addBlackNum(String blacknum,int mode){
        //1.获取数据库
        SQLiteDatabase database = blackNumOpenHlper.getWritableDatabase();
        //2.添加操作
        //ContentValues :　添加的数据
        ContentValues values = new ContentValues();
        values.put("blacknum", blacknum);
        values.put("mode", mode);
        database.insert(BlackNumOpenHlper.DB_NAME, null, values);
        //3.关闭数据库
        database.close();
    }

<h2 id="数据其他操作">11、数据其他操作(重点)</h2>

    1.更新
        /**
         * 更新黑名单的拦截模式
         */
        public void updateBlackNum(String blacknum,int mode){
            //1.获取数据库
            SQLiteDatabase database = blackNumOpenHlper.getWritableDatabase();
            //2.更新操作
            ContentValues values = new ContentValues();
            values.put("mode", mode);
            //table : 表名
            //values : 要更新数据
            //whereClause : 查询条件  where blacknum=blacknum
            //whereArgs : 查询条件的参数
            database.update(BlackNumOpenHlper.DB_NAME, values, "blacknum=?", new String[]{blacknum});
            //3.关闭数据库
            database.close();
        }
    2.查询
        /**
         * 通过黑名单号码,查询黑名单号码的拦截模式
         */
        public int queryBlackNumMode(String blacknum){
            int mode=-1;
            //1.获取数据库
            SQLiteDatabase database = blackNumOpenHlper.getReadableDatabase();
            //2.查询数据库
            //table : 表名
            //columns : 查询的字段  mode
            //selection : 查询条件  where xxxx = xxxx
            //selectionArgs : 查询条件的参数
            //groupBy : 分组
            //having : 去重
            //orderBy : 排序
            Cursor cursor = database.query(BlackNumOpenHlper.DB_NAME, new String[]{"mode"}, "blacknum=?", new String[]{blacknum}, null, null, null);
            //3.解析cursor
            if (cursor.moveToNext()) {
                //获取查询出来的数据
                mode = cursor.getInt(0);
            }
            //4.关闭数据库
            cursor.close();
            database.close();
            return mode;
        }
    3.删除
        /**
         * 根据黑名单号码,删除相应的数据
         * @param blacknum
         */
        public void deleteBlackNum(String blacknum){
            //1.获取数据库
            SQLiteDatabase database = blackNumOpenHlper.getWritableDatabase();
            //2.删除
            //table : 表名
            //whereClause : 查询的条件
            //whereArgs : 查询条件的参数
            database.delete(BlackNumOpenHlper.DB_NAME, "blacknum=?", new String[]{blacknum});
            //3.关闭数据库
            database.close();
        }