hao ## 基础知识

### 1生命周期

Activity的生命周期图如下：

![activity_lifecycle](Images/activity_lifecycle.png)



常规情况不再废话，这里先讨论一个很重要的点：

----------


我们都知道，在系统内存不足的情况下，android会回收一些内存使得前台进程顺利运行，那么，android有两种选择：

1.杀死低优先级的App进程

2.回收当前App的后台Activity

问题在于，它会选择哪种方法，答案是：**只能选第一种**

这个问题在官方文档中一直没有明确的叙述，但是在网络上一直有争论，参见如下

[https://code.google.com/p/android/issues/detail?id=21552](https://code.google.com/p/android/issues/detail?id=21552 "Android issue")

[http://stackoverflow.com/questions/7536988/android-app-out-of-memory-issues-tried-everything-and-still-at-a-loss](http://stackoverflow.com/questions/7536988/android-app-out-of-memory-issues-tried-everything-and-still-at-a-loss)

这里有两个链接，第一个是android的issue，这里并没有得到android官方的明确答复，第二个是stackoverflow上的问题，回答者是android的开发人员，他的答案可信度比较高。

从官方的文档以及上图也可以窥见一二，

1.上图描述的被杀死的情况，一直用词是App process，这一点佐证了开发人员的回答

2.官方在Activity生命周期onStop的描述中建议，我们可以在onStop中注销一些广播接收者，试想一下，如果在onPause执行后，有可能Activity被单独回收，而不是杀死整个进程，那么必然会导致广播接收者没有注销，从而造成内存泄漏。

如上面两点中我们可以推论，android是不会回收某个应用的某些Activity而不杀死整个进程的，如果内存不够用，**只会有如下两种可能发生**：

**1.杀死并回收整个低优先级的进程**

**2.oom**

----------

明确了这一点，我们再来看如下问题


----------


从图中和官方文档中可以看出，Activity生命周期的注意点如下：

- onPause/onStop 之后，有可能因为更高优先级的App需要内存，导致当前App进程被杀
- App进程被杀死的时候，在极端情况下有可能不会调用Activity的onDestroy(),至于什么情况，官方没有明确说明

有了如上两个前提条件，我们再来讨论每个生命周期该注意什么该干什么：

####onCreate

整个生命周期只执行一次，所以，需要整体持有的在这里做，如findviewbyid

####onStart

可见时执行，可以在这里注册广播接收者，监听引发ui变化的因素

####onResume

可以在这里获取独占资源，例如camera，music播放，这些东西A应用获取了，B应用无法获取,为什么要在onResume不在onStart，因为当A启动B，并且释放掉资源占用后，而B的Activity是半透明的，那么返回A的时候，不会走A的onStart，这会导致没有去获取独占资源。

####onPause

这里值得注意，在A启动B之后，会怎么执行？根据官方文档的叙述有如下：

1. A.onpause
2. B.onCreate
2. B.onStart
3. B.onResume
4. A.onStop

这个顺序是一切的关键点所在，android这样设计是为了保证B的启动速度，那么自然，在A的onPause中，不会执行太长的任务，在ActivityStack中，规定了该方法的执行时间不能超过500毫秒：
    
    // How long we wait until giving up on the last activity to pause.
    
    //This is short because it directly impacts the responsiveness of startingthe
    
    // next activity.
    
    static final int PAUSE_TIMEOUT = 500; // 定义在ActivityStack.java中

所以在onPause中，我们应该释放一些紧要的独占资源，例如onResume中获取的那些。不要做太长的工作，可以保存一些对app来说至关重要的数据，毕竟onpause之后，app可能被杀死，onStop是不保证被执行的。

如果当前activity启动了一个dialog，该dialog的context是当前activity，那么当前activity不会调用onPause方法。

####onStop

这个方法不用多说，首先回收对应的onStart中申请的资源，注册的广播，其次，它可以做比onPause稍微繁重的工作，存贮app需要的数据

####onDestroy

该方法可能用户主要finish，也可能被系统杀死时调用（系统杀死时，不一定调用），要区分这两种情况，可以通过isFinishing方法来判断。
在横屏竖屏转换的时候，也可能调用。

### 2异常关闭与恢复


如前所诉，Activity有可能被系统异常杀死，那么如和在异常关闭后恢复内容。这里有两个方法需要了解：

1.onSaveInstanceState

2.onRestoreInstanceState

方法一用来保存数据，方法二用于恢复数据。

**onSaveInstanceState**

这个方法的基本用法不再详细说明，键值对保存即可。下面说几个注意点：

1. 调用情况：是否调用这个方法，取决于该activity切换到后台时，是否有可能被**意外**杀死。情况分为:
	1. home到桌面，这种情况下，app进入后台，有可能因为前台进程需要内存而被杀死
	2. 打开其他Activity(当前应用的/其他应用的,无论是否透明)，这种情况下，也可能因为前台app需要内存而被杀死
	3. 按下多任务按钮，这种情况下，有可能选择其他app，从而被杀死
		1. 点击多任务，随后，依然选择当前应用，生命周期如下：
			1. onPause
			2. onResume
		2. 点击多任务，选择其他应用。onSaveInstanceState会在当前应用的onStop之前调用，假设当前应用为A,其他应用为B,生命周期如下（无法确定onSaveInstanceState与onPause的关系，所以这里不列出）：
			1. A.onpause
			2. B.onCreate(如果B的选中Activity已经存在，则不会调用此方法)
			2. B.onStart
			3. B.onResume
			4. A.onStop 
	4. app在前台，然后按下电源按键（关闭屏幕显示）时，此时有可能有app监听锁屏广播而启动到前台，所以有可能被杀死，因此会调用该方法
	5. 屏幕切换方向时（如果不指定configchange属性，那么activity就会销毁再创建），此时由于activity销毁再创建，那么该方法必然要执行
2. 如果用户主动关闭当前activity，那么该方法不会调用，因为不是**意外**杀死，非意外杀死，意味着不需要恢复这个activity。所以保存数据没有意义
3. 调用时机：	如果被调用，会在onStop方法之前调用，可能在onPause之前，也可能在onPause之后。也就是说，和onPause的时间关系不确定，和onStop是确定的。


**onRestoreInstanceState**

该方法用于恢复数据，调用前提是，该activity确实被系统销毁了。如果仅仅是home出去，没有被销毁，随后点击进入，那么该方法不会被调用。一般来说，可以直接用 onCreate(Bundle)中的数据来恢复，但是在onCreate中使用需要注意判空，因为初次初始化Acitivyt时，数据是null。在onRestoreInstanceState中不用判空，因为只有需要恢复时，该方法才会被调用。

1. 该方法的超类实现，默认实现了恢复view的信息，所以类似EditText之类的用户编辑信息，不需要我们去保存。
2. 超类实现保存的view信息，类似 EditText的编辑信息，是在onRestoreInstanceState中进行恢复的，不是在onCreate中，也就是说如果在onRestoreInstanceState中不调用超类实现，那么默认的view保存信息，将无法恢复
3. 调用时机：会在onStart之后，onPostCreate之前。这也是和在onCreate之中恢复activity的一个重要不同。


### 3 IntentFilter

intent启动activity主要分为两种：Implicit(隐式)，以及 Explicit（显式）。

intentFilter主要用于activity的隐式启动，基本形式如下：


	<activity
	    android:name="com.tencent.tauth.AuthActivity"
	    android:launchMode="singleTask"
	    android:noHistory="true"
	    android:taskAffinity="com.coohuaclient">
	    <intent-filter>
	        <action android:name="android.intent.action.VIEW"/>
	
	        <category android:name="android.intent.category.DEFAULT"/>
	        <category android:name="android.intent.category.BROWSABLE"/>
	
	        <data android:scheme="tencent1101354300"/>
	    </intent-filter>
	</activity>

可以看到其中有action，category，data三要素。

1. action

	action 在隐式启动中是必须的，要想隐式启动成功，则在清单文件的intentFilter中，必须配置>=1以上的action，而隐式的intent中，只能指定一个action，且要匹配上。

	一个intent，如果只有action，没有category和data，那么只要action符合匹配条件，即可启动对应activity。
	相反，如果没有action，那么无论如何也不能启动成功。所以action是必须的。

2. category

	隐式启动中，category并不是必须的。没有手动指定category，只要action是匹配的，依然可以启动成功。

	在intent中，**category不可以脱离action单独存在**，如果一个intent没有action，只有category，即使category符合匹配规则，也无法启动activity。

	在隐式启动的intent中，系统都会默认加上Android.intent.category.DEFAULT这种category，因此，**在xml中也必须指定这种值**,这是成功调用的前提条件。

	intent中无论是否手动添加，都会有Android.intent.category.DEFAULT这个category，它和其他手动添加的category一起组合成匹配组，只要该匹配组是某个activity的xml中配置的匹配组的子集，即可匹配成功。

	
3. data

	data在隐式启动中并不是必须的，即使是xml配置了data，但是在intent中没有data，只要符合前面所说的条件，依然可以启动成功，但是如果在intent中设置了data，那么就必须匹配上，才能启动

	完整形式如下：

		<data
	      android:mimeType="image/*" 
	      android:scheme="string"
	      android:host="string"
	      android:port="string"
	      android:path="string"
	      android:pathPattern="string"
	      android:pathPrefix="string"
	      />

	可以看到，data由两部分组成，

	1. 一部分是mimeType代表了数据/资源的媒体类型，定义文件的类型，比如 image/jpeg，也可以使用 image/* 来表示所有图片格式的文件。也可以使用 / 来表示所有文件
	2. 其他的是URI，指定了资源的位置。
	
	看看URI的结构：

		<scheme>://<host>:<port>/[<path>|<pathPrefix>|<pathPattern>]

	例如： 

		content://com.baidu.haha:8080/dir/src/text.txt 
		http://www.baidu.com:80/dir/src/text.txt

	上面的两种例子说明了URI的格式。
	
	这里值得说明的是path，pathPrefix以及pathPattern的区别，path指的是完整的路径，例如上诉例子中path代表“/dir/src/text.txt”，而pathPrefix代表了完整路径的起始部分，可以是/dir也可以是/dir/src，pathPattern则是符合正则表达式的路径，可以用正则表达式来表示路径，从而实现匹配。值得注意的是，pathPattern使用中，如果要表达例如*这类在正则表达式中有作用的字符，那么需要进行转义，但是写成“\*”是不行的，因为清单文件是一个xml文件，他在被系统读取到内存的时候，会被转义一次，当pathPattern被当作正则表达式的读取的时候又会转义一次，所以这里有两次转义。所以，应该写成“`\\*`”,而“`\`”则要写成"`\\\\`"

	

	**data匹配比较严格，如果在intent不指定data，则按照action和category的规则进行匹配。如果intent要指定data，且在xml中指定了type和uri，那么inten中也必须都指定type和uri，如果在xml只指定了其中一项，那么在intent中也只能指定对应的一项。**

	值得注意的是，在intent中的setData和setType方法，可以分别指定uri和type，但是，这两个方法会分别将另一个数据置空，所以如果需要uri和type都指定，需要使用 setDataAndType() 方法。


----------

总结成功隐式启动的条件如下：(必须遵守如下条列，才能隐式启动成功)

1. intent中，只有action是必须的，category和data可以不要。但在xml中，action和category(Android.intent.category.DEFAULT)都是必须的,因为intent会被系统默认添加category(Android.intent.category.DEFAULT)。
2. action在intent中**有且只有**一个，在xml可以有多个，并且intent中必须是xml中的子集。
3. 在intent中，category必须是xml中的子集，且xml中必须含有Android.intent.category.DEFAULT
4. data匹配严格，在intent中要么别指定，要么就必须和xml中完全匹配，不能多，不能少

----------


### 4 Activity间通信

Activity之间通过intent启动并传递数据这里不做赘述，以后如果有值得注意的点再做补充。

这里说一下startActivityForResult与setResult。我们都知道这里两个方法的用途，一个启动activity B，一个在B中设置结果，返回给前一个Activity。这里的setResult有个值得注意的点：

如果setResult在onBackPressed中调用，那么setResult必须在超类的实现的之前调用，否则设置的结果无法传递到前一个activity中。调用方法类似下方：

    @Override
    public void onBackPressed() {
        Intent resultInt = new Intent();
        resultInt.putExtra("Result", "Done");
        setResult(Activity.RESULT_OK, resultInt);
        super.onBackPressed();
    }

只是因为在超类的实现中，会走到activity的finish方法，而finish代码如下：

    private void finish(int finishTask) {
        if (mParent == null) {
            int resultCode;
            Intent resultData;
            synchronized (this) {
                resultCode = mResultCode;
                resultData = mResultData;
            }
            if (false) Log.v(TAG, "Finishing self: token=" + mToken);
            try {
                if (resultData != null) {
                    resultData.prepareToLeaveProcess(this);
                }
                if (ActivityManagerNative.getDefault()
                        .finishActivity(mToken, resultCode, resultData, finishTask)) {
                    mFinished = true;
                }
            } catch (RemoteException e) {
                // Empty
            }
        } else {
            mParent.finishFromChild(this);
        }
    }

这里可以明确看到，resultCode和resultData的值被设定了，随后ActivityManagerNative使用了该值，也就是说，如果在finish后调用setResult，那么设置进去的值将无法被ActivityManagerNative使用，也就无法传递到上一个Activity中。

因此。总结来说，setResult方法，不能在finish之后调用，由于finish在onBackPressed被调用了，所以也不能在onBackPressed之后调用。

### 5 Activity启动模式

Activity有四种启动模式:







