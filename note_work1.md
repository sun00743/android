----mageView 转 Bitmap
Bitmap image = ((BitmapDrawable)imageView.getDrawable()).getBitmap();  

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
    获取屏幕分辨率利用 DisplayMetrics
    DisplayMetrics metrics = new DisplayMetrics();
    getWindowManager.getDefultDisplay.getMetrics;
    metrics.widthplex
    metrics.heightplex

----控件设置xml背景属性
<selector>
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

----自定义view
    1.想要padding生效，则要在ondraw方法中getpadding，然后在宽度中减去padding的值
    2.xml： res/values/attrs_xxxx.xml 
                <res...
                    <declare-styleable name="XXXX">
                        <attr name="" format="" />
                    </dec...
    3.format属性: reference  引用      @drawable/图片ID
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
    4.从xml资源中获取属性：
        TypeArray array = context.obtainStyledAttributes(attrs, R.styleable.XXXX);
        
----属性动画
    插值器
    1）AccelerateDecelerateInterpolator：先加速再减速。
    2）AccelerateInterpolator：一直加速。
    3）AnticipateInterpolator：先往后一下，再嗖的一声一往无前。
    4）AnticipateOvershootInterpolator：先往后一下，再一直往前超过终点，再往回收一下。
    5）BounceInterpolator：最后像个小球弹几下。
    6）CycleInterpolator：重复几次，感觉就是环形进度条那种，具体我还没试过。
    7）DecelerateInterpolator：一直减速。
    8）LinearInterpolator：线性，这个就是我们上面讲到的很均匀的了。
    9）OvershootInterpolator：到了终点之后，超过一点，再往回走。有个参数可以定义，超过的力度。
    
    valueAnimator中ofobject方法，要实现TypeEvaluator，复写evaluate方法
        fraction = startvalue/endvalue ， 随着duration （startvalue -> endvalue）  
     
----bezier曲线
    B(t) = P0*(1-t)^3 + 3*P1*t*(1-t)^2 + 3*P2*t^2*(1-t) + P3*t^3
    
----IPC 进程间通讯
    1. serializable：
        (1) 传递的对象类要实现serialiable接口
        (2) 网络传输并不安全
        (3) objectOutputstream / objectInputStream  
    2. parcelable：
        通过intent和binder传递
        (1) writeToparcel(Parcel dest, int flags) flags 几乎所有情况都为0
        (2) private User(Parcel in) 传递CREATOR加载器
        (3) public static final Parcelable.Creator<User> CREATOR = new P...Crea....;  反序列化由creator完成
    
    Binder：
        关于binder死亡代理：
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
            为binder设置死亡代理
            mService = IMessageBoxManager.Stub.asInterfacce(binder);
            binder.linkToDeath(mDeathRecipient, 0);
    
    Messenger(单应用)：
        底层AIDL 
        (1) 服务端:
        1. 创建一个handler(处理逻辑业务)来创建messenger对象：private final Messenger mess = new Messenger(MessengerHandler);
        	private static class MessengerHandler extend Handler(){.....} 
        2. onbind方法里 mess.getbinder(); 来绑定
        3. 回复信息：handler中通过 msg.replyTo 方法来获取messenger对象 (messenger client = msg.replyTo;)
        创建一个message.obtain(handler h,int what);通过messenger.send; (client.send(replymessage);)来传递bundle对象
        (2) 客户端：
        1. 创建serivceconnection 和messenger ，创建一个message.obtain(handler h,int what)通过messenger.send来传递bundle对象
        public void onServiceConnected(ComponentName name, IBinder service) {
			mService = new Messenger(service);
		2. 创建intent 和绑定service	bindService(intent, conn, Context.BIND_AUTO_CREATE);
		3. 接收服务器回复信息；也需要创建接受消息的handler和messenger对象：同服务端，在handler中msg.getData().get...(key);
	    接受信息。
	    4. 吧接收回复的这个messenger通过msg.replyTo = mGetReplyMessenger传递给服务端
	AIDL: 
		AIDL注意： 
			实现parcelable的类要在AIDL文件包中(在ＡＳ中这个类要放在gen目录下与aidl文件同一个包中)
			相互间传递参数要在非UI线程中操作
		AIDL支持数据类型:
			1. 基本数据类型
			2. String,charsquence
			3. List : Arraylist, ; map: hashmap
    		4. 实现parcelable接口的类和对象
    		5. AIDL接口
    	Service: 
    		1. 通过AIDL.stub()方法创建binder对象,在其中实现AIDL中的接口方法
    		2. onBinder方法中返回这个这个binder对象
    	Client:
    		1. 创建一个serviceConnection对象，将binder参数通过xxAIDL.stub().asInterface(binder)实例化连接
    		2. 创建 service指定action的intent，以通过bindservice来绑定AIDL服务。
    		3. 需要把监听回调接口传递到服务端的AIDL则需要在客户端创建一个xxaidl对象 = new xxAidl.stub()

----ListView : 
    1.索引接口：
        SectionIndex index = (SectionIndex)adapter;    
        
    2.onScrollListener的onScroll方法中，可以获取到当前的 firstvisibleItem，visibleItemCount, totalItemCount
    3.descendantFocusability属性，三种模式可分配焦点问题
    
    
----CopyOrWriteArrayList:
	支持并发读写, 并不是继承自ArrayList, Binder会按照List的规范去访问最后生成一个ArrayList,
----RemoteCallBackList:
	系统专门用于删除跨进程 Listener（接口？）的接口
    
	





