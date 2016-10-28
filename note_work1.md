
- [自定义View](#zidingyiView)

----mageView 转 Bitmap:

	1.Bitmap image = ((BitmapDrawable)imageView.getDrawable()).getBitmap();  
----获得根视图

	getWindow().getDecorView().findviewById(android.R.id.content);
	getWindow().getDecorView()就是根视图了，当然用Activity.getWindow.getDecorView()来表达意思更为明确
----内存溢出

	Android 默认给每个APP只分配16M的内存
	
	thread 不销毁也会内存泄露
	bitmap 用完recyle掉
	static 修饰的context，尽量用弱引用context
	Cursor 用完也要close掉，不然会内存泄露
----java 中的引用

    强引用 垃圾回收器不会回收，java 默认引用都是强引用
    软引用 SoftReference   在内存不够时，垃圾回收器会考虑回收
    弱引用 WeakReference   在内存不够时，垃圾回收器会优先回收
    虚引用 PhantomReference   在内存不够时，垃圾回收器会最优先回收
    
	LruCache<K,V> (least recentlly use 最少最近引用)
		一个hashMap       
----尺寸适配

    dp = px/设备密度
    values -> dimens.xml / values-1280*720 -> dimens.xml 分别设置不同的dp 
----获得视图尺寸

	int heightMeasureSpec = View.MeasureSpec.makeMeasureSpec(1000, View.MeasureSpec.AT_MOST);
    //传递了size，mode 两个参数
    size：需要测量的视图的大小
    moed：测量的模式
        UNSPECIFIED：没有任何约束，孩子视图可以有任何想要的大小
        EXACTLTY：有一个确切的大小
		AT_MOST：孩子视图可以达到的最大值
----popWindow

    popWindow.showAsDropDown(view) 在view的下方展开popwindow;
    popWindow可以设置宽度
----List 转 数组

    String[] packageArr = (String[])packageList.toArray(new String[packageList.size()]);
    String[] array = (String[])List.toArray(new String[List.size()]);
----设置activity没有标题

    requestWindowFeature(Window.FEATURE_NO_TITLE);
----设备屏幕密度

    density = px/dp;
    float d = context.getResourse().getDisplayMetrics.denstiy;
    获取屏幕分辨率利用 DisplayMetrics
    DisplayMetrics metrics = new DisplayMetrics();
    getWindowManager.getDefultDisplay.getMetrics;
    metrics.widthplex
    metrics.heightplex
----控件设置xml背景属性

	<selector  ...>
	<item name = "state:pressed = "true"  ?">
    	<shape ..... >
        	<solid color = ""/>
		<croners radio = "" />	 
	 	others .......
    	</shape ...> 
	</item>
	<ite.....
	</selector>
----girdview

    Xml属性：
        android:columnWidth         设置列的宽度。   关联的方法为：setColumnWidth(int)
        android:horizontalSpacing   两列之间的间距。 关联方法：setHorizontalSpacing(int)
        android:numColumns          列数。           关联方法：setNumColumns(int)
        android:stretchMode         缩放模式。       关联方法：setStretchMode(int)
        android:verticalSpacing     两行之间的间距。 关联方法：setVerticalSpacing(int)
<p id="zidingyiView" />
##自定义view
[系列note](https://github.com/GcsSloop/AndroidNote/blob/master/CustomView/README.md)

1. 想要padding生效，则要在ondraw方法中getpadding，然后在宽度中减去padding的值
2. xml属性资源文件： res/values/attrs_xxxx.xml ; 动画文件：res/anim/anim_name_xml ;

                <res...
                    <declare-styleable name="XXXX">
                        <attr name="" format="" />
                </dec...
3. format属性:
    
    			  reference  引用      @drawable/图片ID
                  string     字符串    
                  color      颜色      #00FF00
                  dimension  尺寸值    25dp/px 
                  integer    整形
                  float      浮点型    102.2
                  fraction   百分比    50%
                  <attr name="orientation">    枚举  
                          <enum name="horizontal" value="0" />
                          <enum name="vertical" value="1" />
                  </attr>    
                  <attr name="windowSoftInputMode">   位或运算
                      <flag name = "stateUnspecified" value = "0" />
                      <flag name = "stateUnchanged" value = "1" />
4. 从xml资源中获取属性：
    
        TypeArray array = context.obtainStyledAttributes(attrs, R.styleable.XXXX);

##属性动画    
####插值器
1. AccelerateDecelerateInterpolator：先加速再减速。
2. AccelerateInterpolator：一直加速。
3. AnticipateInterpolator：先往后一下，再嗖的一声一往无前。
4. AnticipateOvershootInterpolator：先往后一下，再一直往前超过终点，再往回收一下。
5. BounceInterpolator：最后像个小球弹几下。
6. CycleInterpolator：重复几次，感觉就是环形进度条那种，具体我还没试过。
7. DecelerateInterpolator：一直减速。
8. LinearInterpolator：线性，这个就是我们上面讲到的很均匀的了。
9. OvershootInterpolator：到了终点之后，超过一点，再往回走。有个参数可以定义，超过的力度。

valueAnimator中ofobject方法，要实现TypeEvaluator，复写evaluate方法</br>
fraction = startvalue/endvalue ， 随着duration （startvalue -> endvalue)   
####bezier曲线
&nbsp;&nbsp;B(t) = P0*(1-t)^3 + 3*P1*t*(1-t)^2 + 3*P2*t^2*(1-t) + P3*t^3   
&nbsp;&nbsp;[具体图以及typeEvaluator示例](http://user.qzone.qq.com/327400482/2)   		
##IPC 进程间通讯
####serializable：
1. 传递的对象类要实现serialiable接口。
2. 网络传输并不安全。
3. objectOutputstream / objectInputStream。

####parcelable：
通过intent和binder传递

1. writeToparcel(Parcel dest, int flags) flags 几乎所有情况都为0
2. private User(Parcel in) 传递CREATOR加载器
3. public static final Parcelable.Creator<User> CREATOR = new P...Crea....;  反序列化由creator完成

####Messenger(单应用)：
底层AIDL 
#####服务端:
1. 创建一个handler(处理逻辑业务)来创建messenger对象：

		private final Messenger mess = new Messenger(MessengerHandler);
    	private static class MessengerHandler extend Handler(){.....} 
2. onbind方法里 mess.getbinder(); 来绑定
3. 回复信息：handler中通过 msg.replyTo 方法来获取messenger对象(messenger client = msg.replyTo;) <br>创建一个message.obtain(handler h,int what);<br>通过messenger.send; (client.send(replymessage);)来传递bundle对象
        
#####客户端：
1. 创建serivceconnection 和messenger ，创建一个message.obtain(handler h,int what)通过messenger.send来传递bundle对象
    
	public void onServiceConnected(ComponentName name, IBinder service) {
		mService = new Messenger(service);
2. 创建intent 和绑定service	bindService(intent, conn, Context.BIND_AUTO_CREATE);
3. 接收服务器回复信息；也需要创建接受消息的handler和messenger对象：同服务端，在handler中msg.getData().get...(key);接受信息。
4. 把接收回复的这个messenger通过msg.replyTo = mGetReplyMessenger传递给服务端

####AIDL: 
#####AIDL注意： 
实现parcelable的类要在AIDL文件包中(在ＡＳ中这个类要放在gen目录下与aidl文件同一个包中)
相互间传递参数要在非UI线程中操作
#####AIDL支持数据类型:
1. 基本数据类型
2. String,charsquence
3. List : Arraylist, ; map: hashmap
4. 实现parcelable接口的类和对象
5. AIDL接口

#####Service: 
1. 通过AIDL.stub()方法创建binder对象,在其中实现AIDL中的接口方法
2. onBinder方法中返回这个这个binder对象

#####Client:
1. 创建一个serviceConnection对象，将binder参数通过xxAIDL.stub().asInterface(binde r)实例化连接
2. 创建 service指定action的intent，以通过bindservice来绑定AIDL服务(这是隐式意图，可以通过intent.setPackage()来变为显示意图)。
3. 需要把监听回调接口传递到服务端的AIDL则需要在客户端创建一个xxaidl对象 = new xxAidl.stub()

#### Binder：
[Binder IPC机制的好处](https://www.zhihu.com/question/39440766/answer/89210950)

一个跨进程通讯对象基类，轻量级，高性能，安全，use as a token，在context内部的内部实现并且有高层组件运行。
#####关于binder死亡代理：
客户端声明DeathRecipient对象  
    
    private IBinder.DeathRecipient mDeathRecipient = new IBinder.Death.Recipient(){
        @override
        public void binderDied(){
            if(mBookManager(客户端创建的aidl对象) == null){
                return;
            }
            mBookManager.asBinder().unlinkToDeath(mDeathRecipient, 0);
            mBookManager = null;
            //重新绑定service
            binderservicer(intent, connection, Context.BIND_AUTO_CREATE);
        }
    }
在客户端绑定远程服务后(在serviceConnection中)为binder设置死亡代理  
	
	mBookManager = IMessageBoxManager.Stub.asInterfacce(IBinder);
    IBinder.linkToDeath(mDeathRecipient, 0);
####Binder链接池:
#####Service:  
1. 创建需要的aidl接口，并完成实现类。
2. 创建binder连接池的aidl接口Ibinderpool。
3. 创建bindpoolService，返回Ibinderpool的实现bindpoolImp。  

#####Client:  
(线程中，在bindpool中设置死亡代理)  
1. 创建binder连接池bindpool来绑定bindpoolService，创建querybinder方法来提供对应的binder。  
2. MainActivity利用querybinder获取到对应的binder，通过aidl实现类的asInstance(Ibinder)方法实例化链接  
3. 然后AIDL对象就可以调用方法完成通信。  

####ContentProvider:
#####Manifest:
1. 声明provider的authority，例如：

		android:authorities="com.contentprovider.demo.book.provider"
2. 为了安全起见，声明provider的permission（别忘了在自己的应用的Manifest文件中指定permission）。 例如：
		
		<uses-permission android:name="com.demo.PROVIDER"/>
		android:permission="com.demo.PROVIDER"
3. 为provider新开一个进程。
        android:process=":provider"

#####provider:
1. 
2. UriMatcher 用来将path和code进行关联

		UriMatcher matcher = new UriMatcher(UriMatcher.NO_MATCH);
		matcher.add(authority(com.con......), path("book"), code(0));
3. i d q u 注意线程同步
4. 

####其他注意：
#####CopyOrWriteArrayList:<br>
支持并发读写, 并不是继承自ArrayList, Binder会按照List的规范去访问最后生成一个ArrayList,
#####RemoteCallBackList:<br>
系统专门用于删除跨进程 Listener（接口？）的接口
#####DbHelper:
    	private static final String SQL_CREATE_MAIN = "CREATE TABLE " +
    			"main " +                       // Table's name
    			"(" +                           // The columns in the table
    			" _ID INTEGER PRIMARY KEY, " +
    			" WORD TEXT"
    			" FREQUENCY INTEGER " +
	 			" LOCALE TEXT )";
		Activity:
 			1. Uri: uri.parse("content://authority/path")
			2. contentResolver.query(uri,....);   
##ListView : 
1.索引接口：

        SectionIndex index = (SectionIndex)adapter;    
2.onScrollListener的onScroll方法中，可以获取到当前的 firstvisibleItem，visibleItemCount, totalItemCount
3.descendantFocusability属性，三种模式可分配焦点问题
    
##Socket:
通过指定端口和ip来标识网络中的进程
####client:
		……
		public void run{
			……
			Socket clientSocket = new socket("127.0.0.1",8688);
			//br接收服务器消息
			BufferReader br = new BufferReader(new InputStreamReader(clientSocket.getInputStream()));
			String msg = br.readline();
			if(msg != null){
			//handler通知更新ui
			}
		
			//os向服务器发送消息
			//PrintWriter mPrintWriter = new PrintWriter(new BufferedWriter(
            	            new OutputStreamWriter(socket.getOutputStream())),true);
        	//mPrintWriter.println("msg from client");
			OutPutStream os = clientSocket.getOutPutStream;
			os.write(("something").getType("gbk"));
			……
		}
		
	
##HTTP:
####Post:
		//发送json请求
		//首先设置requestProperty
		httpUrlConnection.setRequestProperty("Content-Type","application/json,charset=utf-8");
		httpUrlConnection.setRequestProperty("Accept","application/json");
		httpUrlConnection.setRequestMethod("post");
		//把doOutput设置为true
		httpUrlConnection.setDoOutput(true);
		//发送String类型的json数据
		DataOutPutStream dos = new DataOutPutStream(httpUrlConnection.getOutPutStream());
		dos.writeBytes(jsonString);
		//关闭流
		dos.flush();
		dos.close();
		……
		//接收回复
		if(httpUrlConnection.getResponseCode() == 200){
            BufferedReader reader = new BufferedReader(InputStreamReader(httpUrlConnection.getInputStream()));
            String response = reader.readLine();
            ……
        }





