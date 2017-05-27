### 概述
Handler、Looper、Message组成的系统是一个异步消息的处理系统。主要是处理不同线程之间的通信问题。

Looper会创建一个MessageQueue，用来存放Message；并不断循环的读取MessageQueue中的消息，如果有消息，则把他给对应的Handler去处理该消息，完后后继续循环；没有消息，线程会阻塞等待。而handler不仅是处理消息的，同时消息也是它产生的，它可以相对应的MessageQueue中发送消息。

### Looper
Looper最主要的就是prepare()和loop()两个方法。
prepare方法

    public static final void prepare() {  
        if (sThreadLocal.get() != null) {  
            throw new RuntimeException("Only one Looper may be created per thread");  
        }  
        sThreadLocal.set(new Looper(true));  
	}  


prepare方法非常简单，只是往ThreadLocal中存放了一个Looper实例。ThreadLocal是线程局部变量，也就是说这里的变量是只能在线程中使用的，因此每个线程都是各自独有的looper。并且从上面代码中可以看出，looper只能有唯一的一个，也就是一个线程对应一个唯一的looper。
上面用到了Looper的构造函数：

    private Looper(boolean quitAllowed) {  
        mQueue = new MessageQueue(quitAllowed);  
        mRun = true;  
        mThread = Thread.currentThread();  
	}  

在构造函数中Looper 创建了MessageQueue。因此消息队列和Looper也是一一对应的关系。

下面在来看看loop方法

    public static void loop() {  
        final Looper me = myLooper();  
        if (me == null) {  
            throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread.");  
        }  
        final MessageQueue queue = me.mQueue;  
  
        // Make sure the identity of this thread is that of the local process,  
        // and keep track of what that identity token actually is.  
        Binder.clearCallingIdentity();  
        final long ident = Binder.clearCallingIdentity();  
  
        for (;;) {  
            Message msg = queue.next(); // might block  
            if (msg == null) {  
                // No message indicates that the message queue is quitting.  
                return;  
            }  
  
            // This must be in a local variable, in case a UI event sets the logger  
            Printer logging = me.mLogging;  
            if (logging != null) {  
                logging.println(">>>>> Dispatching to " + msg.target + " " +  
                        msg.callback + ": " + msg.what);  
            }  
  
            msg.target.dispatchMessage(msg);  
  
            if (logging != null) {  
                logging.println("<<<<< Finished to " + msg.target + " " + msg.callback);  
            }  
  
            // Make sure that during the course of dispatching the  
            // identity of the thread wasn't corrupted.  
            final long newIdent = Binder.clearCallingIdentity();  
            if (ident != newIdent) {  
                Log.wtf(TAG, "Thread identity changed from 0x"  
                        + Long.toHexString(ident) + " to 0x"  
                        + Long.toHexString(newIdent) + " while dispatching to "  
                        + msg.target.getClass().getName() + " "  
                        + msg.callback + " what=" + msg.what);  
            }  
  
            msg.recycle();  
        }  
	}  

函数第一行myLooper():

    public static @Nullable Looper myLooper() {
        return sThreadLocal.get();
    }
myLooper()函数就是获得sThreadLocal.get(),也就是获得本线程的looper。

loop函数前5行就是获得looper，并且通过looper获得MessageQueue。从中可以看出，loop函数必须要在prepare函数之后执行的。并在接下来函数进入一个死循环，因此loop函数会阻塞线程。只用调用Looper.quit()或者looper.quitSafe()才能停止。

在循环中，looper不断读取MessageQueue中的message，当有消息时会把消息传给msg.target.dispatchMessage去处理。msg.target其实就是handler。

由上面可以看出来Looper主要有两个作用：

1. 和thread和messagequeue联系起来，保证，一个线程中只用一个looper和MessageQueue。
2. 不断去读取MessageQueue中的消息，并把message交给handler去处理

### Handler
Handler就是用来发送消息和处理消息的对象。具体是怎么把消息发送到MessageQueue，并在looper读取消息后返回给handler处理的。首先我们要知道handler和looper以及MessageQueue是怎么关联起来的。而上面我们已经知道MessageQueue是在looper的构造函数中创建的，是一一对应的，因此只要知道handler和looper是怎么关联就可以了。

我们在handler的构造函数中有所发现：

    public Handler() {  
        this(null, false);  
	}  
	public Handler(Callback callback, boolean async) {  
        if (FIND_POTENTIAL_LEAKS) {  
            final Class<? extends Handler> klass = getClass();  
            if ((klass.isAnonymousClass() || klass.isMemberClass() || klass.isLocalClass()) &&  
                    (klass.getModifiers() & Modifier.STATIC) == 0) {  
                Log.w(TAG, "The following Handler class should be static or leaks might occur: " +  
                    klass.getCanonicalName());  
            }  
        }  
  
        mLooper = Looper.myLooper();  
        if (mLooper == null) {  
            throw new RuntimeException(  
                "Can't create handler inside thread that has not called Looper.prepare()");  
        }  
        mQueue = mLooper.mQueue;  
        mCallback = callback;  
        mAsynchronous = async;  
    } 


	public Handler(Looper looper) {
        this(looper, null, false);
    }
	public Handler(Looper looper, Callback callback, boolean async) {
        mLooper = looper;
        mQueue = looper.mQueue;
        mCallback = callback;
        mAsynchronous = async;
    } 

由上面四个构造函数我们发现，在构造函数中，handler和looper进行了关联，并把looper中的MessageQueue与handler进行了关联。

前两个构造函数说明，在没有传入指定looper的情况下，handler默认关联本线程的looper。并且其中可以看出handler必须是在Looper.prepaer()调用后才能有looper，并创建handler，但是我们平常在新建handler好像并没有要我们调用该函数。这是因为主线程在activity的启动代码中已经调用了Looper.prepare()函数。所有主线程中我们不需要，但是其他线程中必须调用Looper.prepare()。

后面两个构造函数，我们可以看出来，handler除了关联本线程的Looper，我们还可以自己指定looper关联，因此可以通过该方法在其他线程中给ui线程发消息。

上面只是说明了handler和MessageQueue的关联，那具体是怎么发消息的呢？
我们来看sendMessage方法：

    public final boolean sendMessage(Message msg) {  
     	return sendMessageDelayed(msg, 0);  
	}  

	public final boolean sendEmptyMessageDelayed(int what, long delayMillis) {  
     Message msg = Message.obtain();  
     msg.what = what;  
     return sendMessageDelayed(msg, delayMillis);
	}  

	public final boolean sendMessageDelayed(Message msg, long delayMillis)  
    {  
       if (delayMillis < 0) {  
           delayMillis = 0;  
       }  
       return sendMessageAtTime(msg, SystemClock.uptimeMillis() + delayMillis);  
    }  

	public boolean sendMessageAtTime(Message msg, long uptimeMillis) {  
       MessageQueue queue = mQueue;  
       if (queue == null) {  
           RuntimeException e = new RuntimeException(  
                   this + " sendMessageAtTime() called with no mQueue");  
           Log.w("Looper", e.getMessage(), e);  
           return false;  
       }  
       return enqueueMessage(queue, msg, uptimeMillis);  
	}  

辗转反则最后调用了sendMessageAtTime，在此方法内部有直接获取MessageQueue然后调用了enqueueMessage方法，我们再来看看此方法：

	private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {  
       msg.target = this;  
       if (mAsynchronous) {  
           msg.setAsynchronous(true);  
       }  
       return queue.enqueueMessage(msg, uptimeMillis);  
	}  

enqueueMessage中首先为meg.target赋值为this，【如果大家还记得Looper的loop方法会取出每个msg然后交给msg,target.dispatchMessage(msg)去处理消息】，也就是把当前的handler作为msg的target属性。最终会调用queue的enqueueMessage的方法，也就是说handler发出的消息，最终会保存到消息队列中去。

现在已经很清楚了Looper会调用prepare()和loop()方法，在当前执行的线程中保存一个Looper实例，这个实例会保存一个MessageQueue对象，然后当前线程进入一个无限循环中去，不断从MessageQueue中读取Handler发来的消息。然后再回调创建这个消息的handler中的dispathMessage方法，下面我们赶快去看一看这个方法：

    public void dispatchMessage(Message msg) {  
        if (msg.callback != null) {  
            handleCallback(msg);  
        } else {  
            if (mCallback != null) {  
                if (mCallback.handleMessage(msg)) {  
                    return;  
                }  
            }  
            handleMessage(msg);  
        }  
    }  

可以看到，其中最后调用了handleMessage方法，而我们知道，handleMessage是一个空方法，是需要我们去实现的，也就是我们自己最后去处理这些消息。

到此，这个流程已经解释完毕，让我们首先总结一下
1. 首先Looper.prepare()在本线程中保存一个Looper实例，然后该实例中保存一个MessageQueue对象；因为Looper.prepare()在一个线程中只能调用一次，所以MessageQueue在一个线程中只会存在一个。
2. Looper.loop()会让当前线程进入一个无限循环，不端从MessageQueue的实例中读取消息，然后回调msg.target.dispatchMessage(msg)方法。
3. Handler的构造方法，会首先得到当前线程中保存的Looper实例，进而与Looper实例中的MessageQueue想关联。
4. Handler的sendMessage方法，会给msg的target赋值为handler自身，然后加入MessageQueue中。
5. 在构造Handler实例时，我们会重写handleMessage方法，也就是msg.target.dispatchMessage(msg)最终调用的方法。

### Handler post
handler除了上面说到的sendMessage一系列的方法，还有一个系列的方法比较重要，就是post，就是发送一个Runnable。

下面我们看下post过程相关函数：

    public final boolean post(Runnable r)
    {
       return  sendMessageDelayed(getPostMessage(r), 0);
    }

	private static Message getPostMessage(Runnable r) {  
      Message m = Message.obtain();  
      m.callback = r;  
      return m;  
	}  

可以看到，在getPostMessage中，得到了一个Message对象，然后将我们创建的Runable对象作为callback属性，赋值给了此message.
注：产生一个Message对象，可以new  ，也可以使用Message.obtain()方法；两者都可以，但是更建议使用obtain方法，因为Message内部维护了一个Message池用于Message的复用，避免使用new 重新分配内存。

最终和handler.sendMessage一样，调用了sendMessageAtTime，然后调用了enqueueMessage方法，给msg.target赋值为handler，最终加入MessagQueue.
可以看到，这里msg的callback和target都有值，那么会执行哪个呢？
其实上面已经贴过代码，就是dispatchMessage方法：

	public void dispatchMessage(Message msg) {  
       if (msg.callback != null) {  
           handleCallback(msg);  
       } else {  
           if (mCallback != null) {  
               if (mCallback.handleMessage(msg)) {  
                   return;  
               }  
           }  
           handleMessage(msg);  
       }  
	}  


if (msg.callback != null)则执行callback回调，也就是我们的Runnable对象。也就是说post是把一个runnable发送到Looper相关联的线程执行。

### 内存泄漏
Handler使用不当可能引起内存泄漏

原因：handler是作为非静态内部类或者匿名内部类时，可能引起内存泄漏。非静态内部类和匿名内部类会保存外部内的引用，在这就是activity，而前面提到，message会保存handler应用（target）。而如果在activity结束时，还有没有被处理的message，这些message会保存在MessageQueue中，并不会马上消失，而是要等到该message被处理。这就导致了handler以及handler引用的外部类也就是activity都无法被及时回收，从而导致内存泄漏。

解决方法：

首先，上面已经明确了内存泄漏来源：

1. 只要有未处理的消息，那么消息会引用handler，非静态的handler又会引用外部类，即Activity，导致Activity无法被回收，造成泄漏；

2. Runnable类属于非静态匿名类，同样会引用外部类。为了解决遇到的问题，我们要明确一点：静态内部类不会持有对外部类的引用。所以，我们可以把handler类放在单独的类文件中，或者使用静态内部类便可以避免泄漏。另外，如果想要在handler内部去调用所在的外部类Activity，那么可以在handler内部使用弱引用的方式指向所在Activity，这样统一不会导致内存泄漏。对于匿名类Runnable，同样可以将其设置为静态类。因为静态的匿名类不会持有对外部类的引用。