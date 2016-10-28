##目录
- [Day13](#day13)
    - [1、病毒](#病毒)
	- [2、手机杀毒界面](#手机杀毒界面)
	- [3、扫描操作(重点:动画)](#扫描操作)
	- [4、获取签名信息(重点:指定信息的标签)](#获取签名信息)
	- [5、手机杀毒](#手机杀毒)
	- [6、缓存清理框架(重点!!!!!!)](#缓存清理框架)
	- [7、缓存清理扫描操作](#缓存清理扫描操作)
	- [8、获取缓存(重点:反射+源码查看)](#获取缓存)
	- [9、展示缓存应用](#展示缓存应用)
	- [10、清理缓存](#清理缓存)
	- [11、异常捕获(重点)](#异常捕获)
	- [12、代码混淆(重点)](#代码混淆)
	- [13、广告](#广告)

<h2 id="day13">Day13</h2>
<h2 id="病毒">1、病毒</h2>

	病毒 : 一段带有恶意行为的计算机程序
	恶搞 : 炫耀技术,熊猫烧香,损人不利己
	灰鸽子 : 盗号,qq,游戏账号
	肉机 : 通过一段程序控制的电脑
	
	杀毒软件历史
		1.黑名单  : 将已有的病毒保存到数据库中,卡巴斯基,江民,瑞星,金山,问题:不能及时发现病毒,蜜罐,云查杀
		2.白名单  : 360  把查杀过的应用发送数据库中

	手机端:黑名单

<h2 id="手机杀毒界面">2、手机杀毒界面</h2>

	layer-list : 图层,帧布局framlayout,可以将图片一层叠一层展示,布局文件中最下面的控件,显示的时候在最上面

	1.布局
			<RelativeLayout 
	        android:layout_width="match_parent"
	        android:layout_height="wrap_content"
	        >
	        <ImageView 
	            android:id="@+id/iv_antivirus_anitimageview"
	            android:layout_width="80dp"
	            android:layout_height="80dp"
	            android:src="@drawable/ic_scanner_malware"
	            android:layout_margin="10dp"
	            />
	        <ImageView 
	            android:id="@+id/iv_antivirus_scanner"
	            android:layout_width="80dp"
	            android:layout_height="80dp"
	            android:src="@drawable/act_scanning_03"
	            android:layout_margin="10dp"
	            />
	        <TextView 
	            android:id="@+id/tv_antivirus_text"
	            android:layout_width="match_parent"
	            android:layout_height="wrap_content"
	            android:text="正在扫描..."
	            android:layout_toRightOf="@id/iv_antivirus_anitimageview"
	            android:layout_marginTop="25dp"
	            android:textSize="18sp"
	            android:layout_marginRight="15dp"
	            />
			<!-- progress : 设置进度条当前进度 -->
	        <ProgressBar
	            android:id="@+id/pb_antivirus_progressbar"
	            style="?android:attr/progressBarStyleHorizontal"
	            android:layout_width="match_parent"
	            android:layout_height="wrap_content"
	            android:layout_below="@id/tv_antivirus_text"
	            android:layout_toRightOf="@id/iv_antivirus_anitimageview"
	            android:layout_marginTop="5dp"
	            android:layout_marginRight="15dp"
	            android:progressDrawable="@drawable/progerssbar_antivirus_bg"
	            android:progress="30"
	            />
	
	    </RelativeLayout>
	2.图层  res->drawable -> xxx.xml
		<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
		    <item android:id="@android:id/background">
		      <bitmap android:src="@drawable/security_progress_bg"
		        android:gravity="center" />
		    </item>
		    <item android:id="@android:id/secondaryProgress">
		      <bitmap android:src="@drawable/security_progress"
		        android:gravity="center" />
		    </item>
		    <item android:id="@android:id/progress">
		      <bitmap android:src="@drawable/security_progress"
		        android:gravity="center" />
		    </item>
		</layer-list>
	3.更改样式,progressbar的属性
		android:progressDrawable="@drawable/progerssbar_antivirus_bg"

<h2 id="扫描操作">3、扫描操作(重点:动画)</h2>

		//设置旋转动画
		//fromDegrees : 旋转开始的角度
		//toDegrees : 结束的角度
		//后面四个:是以自身变化还是以父控件变化
		//Animation.RELATIVE_TO_SELF : 以自身旋转
		//Animation.RELATIVE_TO_PARENT : 以父控件旋转
		RotateAnimation rotateAnimation = new RotateAnimation(0, 360, Animation.RELATIVE_TO_SELF, 0.5f, Animation.RELATIVE_TO_SELF, 0.5f);
		rotateAnimation.setDuration(1000);//持续时间
		rotateAnimation.setRepeatCount(Animation.INFINITE);//设置旋转的次数,INFINITE:一直旋转
		
		//设置动画插补器
		LinearInterpolator linearInterpolator = new LinearInterpolator();
		rotateAnimation.setInterpolator(linearInterpolator);
		iv_antivirus_scanner.startAnimation(rotateAnimation);

	展示软件列表  1.listview   2.linearlayout添加textview
		
    /**
	 * 扫描程序
	 */
	private void scanner() {
		//1.获取包的管理者
		final PackageManager pm = getPackageManager();
		tv_antivirus_text.setText("正在初始化64核杀毒引擎....");
		new Thread(){
			public void run() {
				//延迟一秒中扫描程序
				SystemClock.sleep(100);
				//2.获取所有安装应用程序信息
				List<PackageInfo> installedPackages = pm.getInstalledPackages(0);
				
				//3.1设置进度条最大进度
				pb_antivirus_progressbar.setMax(installedPackages.size());
				//3.2设置当前进度
				int count=0;
				for (PackageInfo packageInfo : installedPackages) {
					SystemClock.sleep(100);
					//3.设置进度条的最大进度和当前进度
					count++;
					pb_antivirus_progressbar.setProgress(count);
					
					//4.设置扫描显示软件名称
					final String name = packageInfo.applicationInfo.loadLabel(pm).toString();
					//在ui线程(主线程)进行操作
					runOnUiThread(new Runnable() {//封装了handler
						
						@Override
						public void run() {
							tv_antivirus_text.setText("正在扫描:"+name);
							
							//6.展示扫描软件的名称信息
							TextView textView = new TextView(getApplicationContext());
							textView.setTextColor(Color.BLACK);
							textView.setText(name);
							//textview添加到线性布局中
							//ll_antivirus_safeapks.addView(textView);
							ll_antivirus_safeapks.addView(textView, 0);//index:将textview添加到线性布局的哪个位置
						}
					});
				};
				//5.扫描完成,显示扫描完成信息,同时旋转动画停止
				runOnUiThread(new Runnable() {
					
					@Override
					public void run() {
						//设置显示信息以及停止动画
						tv_antivirus_text.setText("扫描完成,未发现病毒");
						iv_antivirus_scanner.clearAnimation();//移出动画
					}
				});
			};
		}.start();
	}

<h2 id="获取签名信息">4、获取签名信息(重点:指定信息的标签)</h2>

	1.指定获取签名信息标签
		List<PackageInfo> installedPackages = pm.getInstalledPackages(PackageManager.GET_SIGNATURES);
	2.获取签名信息
		//7.获取应用程序的签名,并进行md5加密
		Signature[] signatures= packageInfo.signatures;//获取应用程序的签名信息,获取签名的数组
		String charsString = signatures[0].toCharsString();
		//对签名信息进行MD5加密
		String signature = MD5Util.passwordMD5(charsString);
		System.out.println(name+"  : "+signature);

<h2 id="手机杀毒">5、手机杀毒</h2>

	1.将病毒库导入到手机中,参考splashactivity中拷贝数据库操作,增加方法传递参数
	2.创建查询数据库操作
			//查询Md5值是否在数据库中
			public class AntiVirusDao {
				/**
				 * 查询Md5值是否在病毒数据库中
				 * @param context
				 * @param md5
				 * @return
				 */
				public static boolean queryAntiVirus(Context context,String md5){
					boolean ishave = false;
					//1.获取数据库的路径
					File file = new File(context.getFilesDir(), "antivirus.db");
					SQLiteDatabase database = SQLiteDatabase.openDatabase(file.getAbsolutePath(), null, SQLiteDatabase.OPEN_READONLY);
					Cursor cursor = database.query("datable", null, "md5=?", new String[]{md5}, null, null, null);
					if (cursor.moveToNext()) {
						ishave = true;
					}
					cursor.close();
					database.close();
					return ishave;
				}
			}
	3.使用
		//8.查询MD5值是否在病毒数据库中
		boolean b = AntiVirusDao.queryAntiVirus(getApplicationContext(), signature);
	4.根据查询值,设置病毒标示
		//6.展示扫描软件的名称信息
		TextView textView = new TextView(getApplicationContext());
		//9.根据返回的值将病毒标示出来
		if (b) {
			//有病毒
			textView.setTextColor(Color.RED);
			//将病毒的包名添加到list集合
			list.add(packageInfo.packageName);
		}else{
			//没有病毒
			textView.setTextColor(Color.BLACK);
		}
		textView.setText(name);
	5.对查询到病毒进行处理
						//10.根据是否有病毒进行显示操作
						if (list.size() > 0) {
							//有病毒
							tv_antivirus_text.setText("扫描完成,发现"+list.size()+"个病毒");
							//卸载病毒应用
							AlertDialog.Builder builder = new Builder(AntivirusActivity.this);
							builder.setTitle("提醒!发现"+list.size()+"个病毒");
							builder.setIcon(R.drawable.ic_launcher);
							builder.setMessage("是否卸载病毒应用!");
							builder.setPositiveButton("卸载", new DialogInterface.OnClickListener() {
								
								@Override
								public void onClick(DialogInterface dialog, int which) {
									//卸载操作
									for (String packagname : list) {
										Intent intent = new Intent();
										intent.setAction("android.intent.action.DELETE");
										intent.addCategory("android.intent.category.DEFAULT");
										intent.setData(Uri.parse("package:"+packagname));
										startActivity(intent);
									}
									dialog.dismiss();
								}
							});
							builder.setNegativeButton("取消", null);
							builder.show();
						}else{
							//设置显示信息以及停止动画
							tv_antivirus_text.setText("扫描完成,未发现病毒");
						}

<h2 id="缓存清理框架">6、缓存清理框架(重点!!!!!!)</h2>

	1.将activity改为继承fragmentActivity
		//如果要对fragment进行管理操作,activity必须继承fragmentActivity
		public class ClearCacheActivity extends FragmentActivity {
		}
	2.设置布局文件
		!-- fragment替换的控件 -->
	    <FrameLayout 
	        android:id="@+id/fram_clearcache_fragment"
	        android:layout_width="match_parent"
	        android:layout_height="match_parent"
	        android:layout_weight="1"
	        ></FrameLayout>
	   <LinearLayout 
	       android:layout_width="match_parent"
	       android:layout_height="wrap_content"
	       >
	    
	    <Button 
	        android:layout_width="0dp"
	        android:layout_weight="1"
	        android:layout_height="wrap_content"
	        android:text="缓存清理"
	        android:background="@drawable/selector_contact_button"
	        android:onClick="cache"
	        />
	    
	    <Button 
	        android:layout_width="0dp"
	        android:layout_weight="1"
	        android:layout_height="wrap_content"
	        android:text="SD卡清理"
	         android:background="@drawable/selector_contact_button"
	         android:onClick="sd"
	        />
	    </LinearLayout>
	3.创建fragment
		public class CacheFragment extends Fragment {
			//初始化操作
			@Override
			public void onCreate(Bundle savedInstanceState) {
				// TODO Auto-generated method stub
				super.onCreate(savedInstanceState);
			}
			//设置fragment的布局
			@Override
			public View onCreateView(LayoutInflater inflater, ViewGroup container,
					Bundle savedInstanceState) {
				//参数1:布局文件
				//参数2:容器
				//参数3:自动挂载  ,一律false
				View view= inflater.inflate(R.layout.fragment_cache, container, false);
				return view;
			}
			//设置填充显示数据
			@Override
			public void onActivityCreated(Bundle savedInstanceState) {
				// TODO Auto-generated method stub
				super.onActivityCreated(savedInstanceState);
			}
			
		}
	4.创建fragment布局文件,线性布局,背景是红色
	5.在actiivty中添加fragment
		@Override
		protected void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.activity_clearcache);
			
			//挂载fragment
			//1.获取fragment
			cacheFragment = new CacheFragment();
			sdFragment = new SDFragment();
			//2.加载fragment
			//2.1获取fragment的管理者
			supportFragmentManager = getSupportFragmentManager();
			//2.2开启事务
			FragmentTransaction beginTransaction = supportFragmentManager.beginTransaction();
			//2.3添加fragment操作
			//数据不会重新刷新
			//参数1:fragment要替换的控件的id
			//参数2:要替换fragment
			beginTransaction.add(R.id.fram_clearcache_fragment, cacheFragment);
			//界面展示以最后一个添加的为准
			beginTransaction.add(R.id.fram_clearcache_fragment, sdFragment);
			//隐藏fragment操作
			beginTransaction.hide(sdFragment);
			//也可以实现替换操作,数据会重新刷新
			//beginTransaction.replace(arg0, arg1)
			//2.4提交事务
			beginTransaction.commit();
		}
	6.隐藏显示fragment操作
		//缓存清理
		public void cache(View v){
			//隐藏sdfragment,显示cacheFragment
			//1.获取事务
			FragmentTransaction beginTransaction = supportFragmentManager.beginTransaction();
			//2.隐藏显示操作
			beginTransaction.hide(sdFragment);//隐藏fragmnt
			beginTransaction.show(cacheFragment);//显示fragment操作
			//3.提交
			beginTransaction.commit();
		}

<h2 id="缓存清理扫描操作">7、缓存清理扫描操作</h2>

	/**
	 * 扫描
	 */
	private void scanner() {
		//1.获取包的管理者
		//getActivity() : 获取fragment所在的activity
		final PackageManager pm = getActivity().getPackageManager();
		tv_cachefragment_text.setText("正在初始化128核扫描引擎.....");
		new Thread(){
			public void run() {
				SystemClock.sleep(100);
				List<PackageInfo> installedPackages = pm.getInstalledPackages(0);
				//设置进度条最大进度
				pb_cachefragment_progressbar.setMax(installedPackages.size());
				int count=0;
				for (PackageInfo packageInfo : installedPackages) {
					SystemClock.sleep(100);
					//设置进度条最大进度和当前进度
					count++;
					pb_cachefragment_progressbar.setProgress(count);
					
					//设置扫描显示的应用的名称
					final String name = packageInfo.applicationInfo.loadLabel(pm).toString();
					if (getActivity() != null) {
						getActivity().runOnUiThread(new Runnable() {
							
							@Override
							public void run() {
								tv_cachefragment_text.setText("正在扫描:"+name);
							}
						});	
					}
				}
				if (getActivity() != null) {
					getActivity().runOnUiThread(new Runnable() {
						
						@Override
						public void run() {
							tv_cachefragment_text.setVisibility(View.GONE);
							pb_cachefragment_progressbar.setVisibility(View.GONE);
						}
					});
				}
			};
		}.start();
	}

<h2 id="获取缓存">8、获取缓存(重点:反射+源码查看)</h2>

	1.反射操作
					//反射获取缓存
					try {
						Class<?> loadClass = getActivity().getClass().getClassLoader().loadClass("android.content.pm.PackageManager");
						Method method = loadClass.getDeclaredMethod("getPackageSizeInfo", String.class,IPackageStatsObserver.class);
						//receiver : 类的实例,隐藏参数,方法不是静态的必须指定
						method.invoke(pm, packageInfo.packageName,mStatsObserver);
					} catch (Exception e) {
						// TODO Auto-generated catch block
						e.printStackTrace();
					}
	2.远程服务aidl
		//获取缓存大小
		IPackageStatsObserver.Stub mStatsObserver = new IPackageStatsObserver.Stub() {
	        public void onGetStatsCompleted(PackageStats stats, boolean succeeded) {
	        	long cachesize = stats.cacheSize;//缓存大小
	        	long codesize = stats.codeSize;//应用程序的大小
	        	long datasize = stats.dataSize;//数据大小
	        	
	        	String cache = Formatter.formatFileSize(getActivity(), cachesize);
	        	String code = Formatter.formatFileSize(getActivity(), codesize);
	        	String data = Formatter.formatFileSize(getActivity(), datasize);
	        	
	        	System.out.println(stats.packageName+"cachesize:"+cache +" codesize:"+code+" datasize:"+data);
	        }
		};
	3.权限
		<uses-permission android:name="android.permission.GET_PACKAGE_SIZE" />

<h2 id="展示缓存应用">9、展示缓存应用</h2>

	1.使用listview进行展示
		@Override
		public View getView(int position, View convertView, ViewGroup parent) {
			
			View cachView;
			ViewHolder viewHolder;
			if (convertView == null) {
				 cachView = View.inflate(getActivity(), R.layout.item_cache, null);
				 viewHolder = new ViewHolder();
				 viewHolder.iv_itemcache_icon = (ImageView) cachView.findViewById(R.id.iv_itemcache_icon);
				 viewHolder.tv_itemcache_name = (TextView) cachView.findViewById(R.id.tv_itemcache_name);
				 viewHolder.tv_itemcache_size = (TextView) cachView.findViewById(R.id.tv_itemcache_size);
				 cachView.setTag(viewHolder);
			}else{
				cachView = convertView;
				viewHolder = (ViewHolder) cachView.getTag();
			}
			//设置显示数据
			CachInfo cachInfo = list.get(position);
			//根据包名获取application信息
			try {
				ApplicationInfo applicationInfo = pm.getApplicationInfo(cachInfo.getPackageName(), 0);
				Drawable icon = applicationInfo.loadIcon(pm);
				String name = applicationInfo.loadLabel(pm).toString();
				//设置显示
				 viewHolder.iv_itemcache_icon.setImageDrawable(icon);
				 viewHolder.tv_itemcache_name.setText(name);
				 viewHolder.tv_itemcache_size.setText(cachInfo.getCachesize());
			} catch (NameNotFoundException e) {
				e.printStackTrace();
			}
			return cachView;
		}

<h2 id="清理缓存">10、清理缓存</h2>

	android中当前程序是不能清理其他程序缓存

	1.进入详情界面进行清理
		lv_cachefragment_caches.setOnItemClickListener(new OnItemClickListener() {

			@Override
			public void onItemClick(AdapterView<?> parent, View view,
					int position, long id) {
				//跳转到详情页面
				Intent intent = new Intent();
				intent.setAction("android.settings.APPLICATION_DETAILS_SETTINGS");
				intent.setData(Uri.parse("package:"+list.get(position).getPackageName()));
				startActivity(intent);
			}
		});
	2.通过反射清理所有应用程序的缓存
		//清理缓存
		btn_cachefragment_clear.setOnClickListener(new OnClickListener() {
									
			@Override
			public void onClick(View v) {
											
					//真正的实现清理缓存
					try {
						Class<?> loadClass = getActivity().getClass().getClassLoader().loadClass("android.content.pm.PackageManager");
						//Long.class  Long     TYPE  long
						Method method = loadClass.getDeclaredMethod("freeStorageAndNotify", Long.TYPE,IPackageDataObserver.class);
						method.invoke(pm, Long.MAX_VALUE,new MyIPackageDataObserver());
					} catch (Exception e) {
						// TODO Auto-generated catch block
						e.printStackTrace();
					}
					
					//清理缓存
					list.clear();
					//更新界面
					myadapter.notifyDataSetChanged();
					//隐藏button按钮
					btn_cachefragment_clear.setVisibility(View.GONE);
				}
		});

<h2 id="异常捕获">11、异常捕获(重点)</h2>

	//异常捕获
	//Application : 当前的应用程序,所有的应用最先的执行都是applicaiton
	public class MyApplication extends Application {

		@Override
		public void onCreate() {
			super.onCreate();
			System.out.println("applcation启动了......");
			Thread.currentThread().setUncaughtExceptionHandler(new MyUncaughtExceptionHandler());
		}
		private class MyUncaughtExceptionHandler implements UncaughtExceptionHandler{
			//系统中由未捕获的异常的时候调用
			//Throwable : Error和Exception的父类
			@Override
			public void uncaughtException(Thread thread, Throwable ex) {
				System.out.println("哥捕获异常了......");
				ex.printStackTrace();
				try {
					//将捕获到异常,保存到SD卡中
					ex.printStackTrace(new PrintStream(new File("/mnt/sdcard/log.txt")));
				} catch (FileNotFoundException e) {
					e.printStackTrace();
				}
				//myPid() : 获取当前应用程序的进程id
				//自己把自己杀死
				android.os.Process.killProcess(android.os.Process.myPid());
			}
		}
	}

	需要将清单文件中的application改为我们自定义的application
		<application
        	android:name=".MyApplication"

<h2 id="代码混淆">12、代码混淆(重点)</h2>

	1.将sdk\tools\proguard\proguard-android.txt拷贝到工程的根目录下
	2.在工程的project.properties文件中将proguard.config=proguard-android.txt:proguard-project.txt代码放开

<h2 id="广告">13、广告</h2>

	1.app特别火,广告厂商找你
	2.广告平台   sdk   广告厂商 -> 广告平台 -> 开发者
		1000条  1毛   
		300000下载量    60000活跃量  玩半个小时,1分钟3-5条广告
		60000*30*3 * 30/1000/10
	
	有米
	开发必用
	id:4de79868e98eae74
	秘钥:26bf4111c161c2fc
    
<img src="http://oe53dpmqz.bkt.clouddn.com/20161028001.png" width = "300" height = "500" alt=”广告" align=center />

