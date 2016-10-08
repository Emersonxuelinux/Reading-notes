###目录
- [Day06](#day06)
    - [1、定位的具体代码](#定位的具体代码)
    - [2、火星坐标](#火星坐标)
    - [3、定位操作移植到手机卫士(重点:服务器service)](#定位操作移植到手机卫士)
    - [4、播放报警音乐(重点)](#播放报警音乐)
    - [5、超级管理员权限](#超级管理员权限)
    - [6、远程删除数据&远程锁屏的功能](#远程删除数据&远程锁屏的功能)
    - [7、查询号码归属地之数据库优化](#查询号码归属地之数据库优化)
    - [8、拷贝数据库(重点)](#拷贝数据库)
    - [9、打开数据库,查询号码归属地(重点)](#打开数据库,查询号码归属地)
    - [10、查询号码归属地界面](#查询号码归属地界面)
    - [11、查询号码归属地细节处理(重点:逻辑)](#查询号码归属地细节处理)


<h2 id="day06">Day06</h2>
<h2 id="定位的具体代码">1、定位的具体代码</h2>

    android.permission.ACCESS_MOCK_LOCATION : 模拟位置的权限,模拟器中必须加的,真机可加可不加
    android.permission.ACCESS_FINE_LOCATION : 精确位置的权限,真机必须添加
    android.permission.ACCESS_COARSE_LOCATION : 大概位置的权限,真机必须添加

    passive :　被动，基站定位
    gps : gps定位
    
    定位的步骤
    1.获取位置的管理者
        //1.获取位置的管理者
        locationManager = (LocationManager) getSystemService(LOCATION_SERVICE);
    2.获取定位方式
        //2.获取定位方式
        //2.1获取所有的定位方式
        //enabledOnly : true : 返回所有可用的定位方式
        List<String> providers = locationManager.getProviders(true);
        for (String string : providers) {
            System.out.println(string);
        }
        //2.2获取最佳的定位方式
        Criteria criteria = new Criteria();
        criteria.setAltitudeRequired(true);//设置是否可以定位海拔,true:可以定位海拔,一定返回gps定位
        //criteria : 设置定位的属性,决定使用什么定位方式的
        //enabledOnly : true : 定位可用的就返回
        String bestProvider = locationManager.getBestProvider(criteria, true);
        System.out.println("最佳的定位方式:"+bestProvider);
    3.定位操作
        a.定位
            //provider : 定位方式
            //minTime : 定位的最小时间间隔
            //minDistance : 定位的最小距离间隔
            //listener : LocationListener
            locationManager.requestLocationUpdates(bestProvider, 0, 0, myLocationListener);
        b.LocationListener
            private class MyLocationListener implements LocationListener{
                //当定位位置改变的时候调用
                //location : 当前的位置
                @Override
                public void onLocationChanged(Location location) {
                    double latitude = location.getLatitude();//获取纬度,平行
                    double longitude = location.getLongitude();//获取经度
                    textview.setText("longitude:"+longitude+"   latitude:"+latitude);
                }
                //当定位状态改变的时候调用
                @Override
                public void onStatusChanged(String provider, int status, Bundle extras) {
                    // TODO Auto-generated method stub
                    
                }
                //当定位可用的时候调用
                @Override
                public void onProviderEnabled(String provider) {
                    // TODO Auto-generated method stub
                    
                }
                //当定位不可用的时候调用
                @Override
                public void onProviderDisabled(String provider) {
                    // TODO Auto-generated method stub
                    
                }
                
            }
    4.关闭gps定位
        @Override
        protected void onDestroy() {
            super.onDestroy();
            //关闭gps定位,高版本中已经不能这么做了,高版本中规定关闭和开启gps必须交由用户自己去实现
            locationManager.removeUpdates(myLocationListener);
        }

<h2 id="火星坐标">2、火星坐标</h2>

    116.29028943
    40.04306343

    火星坐标
    116.2963848402697
    40.04433174242044

<h2 id="定位操作移植到手机卫士">3、定位操作移植到手机卫士(重点:服务器service)</h2>

    1.创建一个服务GPSService
        public class GPSService extends Service {
        }
    2.清单文件配置
        <service android:name="com.itheima.mobliesafe75.service.GPSService"></service>
    3.将定位的操作移植到服务中
        复制到定位的代码到服务中
    4.开启服务
        //开启一个服务
        Intent intent_gps = new Intent(context,GPSService.class);
        context.startService(intent_gps);
    5.定位成功之后,发送短信,并且关闭服务
        //当定位位置改变的时候调用
        //location : 当前的位置
        @Override
        public void onLocationChanged(Location location) {
            double latitude = location.getLatitude();//获取纬度,平行
            double longitude = location.getLongitude();//获取经度
            //给安全号码发送一个包含经纬度坐标的短信
            SmsManager smsManager = SmsManager.getDefault();
            smsManager.sendTextMessage(sp.getString("safenum", "5556"), null, "longitude:"+longitude+"  latitude:"+latitude, null, null);
            //停止服务
            stopSelf();
        }

<h2 id="播放报警音乐">4、播放报警音乐(重点)</h2>

        1.播放报警音乐
                
                //在播放报警音乐之前,将系统音量设置成最大
                //声音的管理者
                AudioManager audioManager = (AudioManager) context.getSystemService(Context.AUDIO_SERVICE);
                //设置系统音量的大小
                //streamType : 声音的类型
                //index : 声音的大小   0最小    15最大
                //flags : 指定信息的标签
                //getStreamMaxVolume : 获取系统最大音量,streamType:声音的类型
                audioManager.setStreamVolume(AudioManager.STREAM_MUSIC, audioManager.getStreamMaxVolume(AudioManager.STREAM_MUSIC), 0);
                
                //判断是否在播放报警音乐
                if (mediaPlayer!=null) {
                    mediaPlayer.release();//释放资源
                }
                mediaPlayer = MediaPlayer.create(context, R.raw.ylzs);
                //mediaPlayer.setVolume(1.0f, 1.0f);//设置最大音量,音量比例
                //mediaPlayer.setLooping(true);
                mediaPlayer.start();
        2.解决同时播放两首报警音乐问题
                //广播接受者在每接收到一个广播事件,重新new广播接受者
                private static MediaPlayer mediaPlayer;

<h2 id="超级管理员权限">5、超级管理员权限</h2>

    Caused by: java.lang.SecurityException: No active admin owned by uid 10047 for policy #3//缺少超级管理员权限

    root:Linux系统中的最高权限,android是以liunx内核进行开发,root权限也是android的最高权限
    超级管理员:只是能做一些比较危险的事情,比如锁屏,删除数据,root也可以做,但是root权限能做的事情,超级管理员就不一定能做
    
    步骤
    1.创建创建一类继承DeviceAdminReceiver
        public class Admin extends DeviceAdminReceiver {

        }
    2.清单文件配置
         <receiver
            android:name="com.example.locknow.Admin"
            android:description="@string/sample_device_admin_description"
            android:label="@string/sample_device_admin"
            android:permission="android.permission.BIND_DEVICE_ADMIN" >
            <meta-data
                android:name="android.app.device_admin"
                android:resource="@xml/device_admin_sample" />

            <intent-filter>
                <action android:name="android.app.action.DEVICE_ADMIN_ENABLED" />
            </intent-filter>
        </receiver>
    3.在res->xml->device_admin_sample.xml,同时修改描述信息,标题等信息
        <device-admin xmlns:android="http://schemas.android.com/apk/res/android">
          <uses-policies>
            <limit-password />
            <watch-login />
            <reset-password />
            <force-lock />
            <wipe-data />
            <expire-password />
            <encrypted-storage />
            <disable-camera />
          </uses-policies>
        </device-admin>
    4.激活  setting -> security -> device admin...
    5.代码激活超级管理员
        public void register(View v){
             //代码激活超级管理员
             //设置激活超级管理员
             Intent intent = new Intent(DevicePolicyManager.ACTION_ADD_DEVICE_ADMIN);
             //设置激活那个超级管理员
             //mDeviceAdminSample : 超级管理员的标示
             intent.putExtra(DevicePolicyManager.EXTRA_DEVICE_ADMIN, componentName);
             //设置描述信息
             intent.putExtra(DevicePolicyManager.EXTRA_ADD_EXPLANATION,"一键锁屏,非常好用");
             startActivity(intent);
        }
    6.代码注销超级管理员
        public void delete(View v){
            //注销超级管理员
            //判断超级管理员是否激活
            if (devicePolicyManager.isAdminActive(componentName)) {
                //注销超级管理员
                devicePolicyManager.removeActiveAdmin(componentName);
            }
        }
    7.锁屏操作
        public void locknow(View v){
            //锁屏的操作
            devicePolicyManager.lockNow();//锁屏
        }

<h2 id="远程删除数据&远程锁屏的功能">6、远程删除数据&远程锁屏的功能</h2>

    1.将超级管理员操作移植到手机卫士中
    2.远程删除数据
        if (devicePolicyManager.isAdminActive(componentName)) {
            devicePolicyManager.wipeData(0);//远程删除数据
        }
    3.远程锁屏
        //判断超级管理员是否激活
        if (devicePolicyManager.isAdminActive(componentName)) {
            devicePolicyManager.lockNow();
        }

<h2 id="查询号码归属地之数据库优化">7、查询号码归属地之数据库优化</h2>

    通过号码去查询这个号码属于哪个地区   13333333333  河北秦皇岛  02年  200万  
    号码
        第一位:1
        第二位:34578
        第三位:0-9
    号码前三位:可以确定运营商    131联通   133电信    134移动
    往后4位:可以确定号码归属地
    一个号码的前7位可以确定一个号码归属地
    
    select location,areacode from mob_location group by location,areacode

    select outkey from data1 where id=1300001
    select location from data2 where id=496

    select location from data2 where id=(select outkey from data1 where id=1300001)

<h2 id="拷贝数据库">8、拷贝数据库(重点)</h2>

    /**
     * 拷贝数据库
     */
    private void copyDb() {
        File file = new File(getFilesDir(), "address.db");
        //判断文件是否存在
        if (!file.exists()) {
            //从assets目录中将数据库读取出来
            //1.获取assets的管理者
            AssetManager am = getAssets();
            InputStream in = null;
            FileOutputStream out = null;
            try {
                //2.读取数据库
                in = am.open("address.db");
                //写入流
                //getCacheDir : 获取缓存的路径
                //getFilesDir : 获取文件的路径
                out = new FileOutputStream(file);
                //3.读写操作
                //设置缓冲区
                byte[] b = new byte[1024];
                int len = -1;
                while((len=in.read(b)) != -1){
                    out.write(b, 0, len);
                }
            } catch (IOException e) {
                e.printStackTrace();
            }finally{
//              in.close();
//              out.close();
                IOUtils.closeQuietly(in);
                IOUtils.closeQuietly(out);
            }
        }
    }

<h2 id="打开数据库,查询号码归属地">9、打开数据库,查询号码归属地(重点)</h2>

    /**
     * 打开数据库,查询号码归属地
     * @return
     */
    public static String queryAddress(String num,Context context){
        String location = "";
        //1.获取数据库的路径
        File file = new File(context.getFilesDir(), "address.db");
        //2.打开数据库
        //getAbsolutePath : 获取文件的绝对路径
        //factory : 游标工厂
        SQLiteDatabase database = SQLiteDatabase.openDatabase(file.getAbsolutePath(), null, SQLiteDatabase.OPEN_READONLY);
        //3.查询号码归属地
        //sql : sql语句
        //selectionArgs :查询条件的参数
        //substring : 包含头不包含尾
        Cursor cursor = database.rawQuery("select location from data2 where id=(select outkey from data1 where id=?)", new String[]{num.substring(0, 7)});
        //4.解析cursor
        //因为每个号码对应一个号码归属地,所以查询出来的是一个号码归属地,没有必要用whlie,用if就可以了
        if (cursor.moveToNext()) {
            location = cursor.getString(0);
        }
        //5.关闭数据库
        cursor.close();
        database.close();
        return location;
    }

<h2 id="查询号码归属地界面">10、查询号码归属地界面</h2>

    1.创建高级工具activity,在其中增加按钮,点击进入查询号码归属地activity
    2.创建查询号码归属地的activity,在其中增加号码输入框,查询按钮,归属地显示textview
    3.查询操作
        /**
         * 查询号码归属地操作
         * @param v
         */
        public void query(View v){
            //1.获取输入的号码
            String phone = et_address_queryphone.getText().toString().trim();
            //2.判断号码是否为空
            if (TextUtils.isEmpty(phone)) {
                Toast.makeText(getApplicationContext(), "请输入要查询号码", 0).show();
                return;
            }
            //3.根据号码查询号码归属地
            String queryAddress = AddressDao.queryAddress(phone, getApplicationContext());
            //4.判断查询的号码归属地是否为空
            if (!TextUtils.isEmpty(queryAddress)) {
                tv_address_queryaddress.setText(queryAddress);
            }
        }

<h2 id="查询号码归属地细节处理">11、查询号码归属地细节处理(重点:逻辑)</h2>

        //使用正则表达式进行判断
        //^1[34578]\d{9}$
        //身份证   ^[0-9]{17}[0-9x]$
        //前六位:出生地   往后8位:出生年月日  剩下4位:前两位:出生编号   剩下2位:前一位:性别  奇数男  偶数女    最后一位:前17位的校验   x
        if (num.matches("^1[34578]\\d{9}$")) {
            Cursor cursor = database.rawQuery("select location from data2 where id=(select outkey from data1 where id=?)", new String[]{num.substring(0, 7)});
            //4.解析cursor
            //因为每个号码对应一个号码归属地,所以查询出来的是一个号码归属地,没有必要用whlie,用if就可以了
            if (cursor.moveToNext()) {
                location = cursor.getString(0);
            }
            //5.关闭数据库
            cursor.close();
        }else{
            //对特殊电话做出来
            switch (num.length()) {
            case 3://110  120  119  911
                location = "特殊电话";
                break;
            case 4://5554   5556
                location = "虚拟电话";
                break;
            case 5://10086  10010  10000
                location ="客服电话";
                break;
            case 7://座机,本地电话
            case 8:
                location="本地电话";
                break;

            default:// 010 1234567   10位    010 12345678  11位     0372  12345678  12位
                //长途电话
                if (num.length() >= 10  &&  num.startsWith("0")) {
                    //根据区号查询号码归属
                    //1.获取号码的区号
                    //3位,4位
                    //3位
                    String result = num.substring(1, 3);//010   10
                    //2.根据区号查询号码归属地
                    Cursor cursor = database.rawQuery("select location from data2 where area=?", new String[]{result});
                    //3.解析cursor
                    if (cursor.moveToNext()) {
                        location = cursor.getString(0);
                        //截取数据
                        location = location.substring(0, location.length()-2);
                        cursor.close();
                    }else{
                        //3位没有查询到,直接查询4位
                        //获取4位的区号
                        result = num.substring(1, 4);//0372    372
                        cursor = database.rawQuery("select location from data2 where area=?", new String[]{result});
                        if (cursor.moveToNext()) {
                            location = cursor.getString(0);
                            location = location.substring(0, location.length()-2);
                            cursor.close();
                        }
                    }
                }
                break;
            }
