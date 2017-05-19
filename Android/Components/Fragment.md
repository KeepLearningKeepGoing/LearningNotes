##基础知识

###一生命周期

Fragment的生命周期是依赖于activity的，如下图可见，fragment的生命周期和activity有对应关系。

![activity_lifecycle](Images/Fragment/fragment_activity_lifecycle.png)

一般来说，我们继承activity，随后我们重写activity的生命周期方法，并在方法内调用基类的生命周期方法，那么这个Activity的生命周期和其中引用的Fragment的生命周期是什么关系呢。这里有一点值得注意的事情：

虽然说Fragment的生命周期和activity有对应关系，但是这种关系并非是绝对的。这里，分为两种方式讨论，也就是Fragment的两种加载方式，静态和动态

####1静态加载
  静态加载就是在xml中，直接使用fragment标签，在name中指定全类名，以此来加载fragment。这种形式下，fragment的初始化是setContentView触发的，一般我们会在oncreate方法的基类调用后面紧接着调用setContentView。这种情况下，Fragment的生命周期如下：

	I/MainActivity: onCreate begin
	I/MainActivity: onCreate end
	I/FragmentTest: 0 onAttach
	I/FragmentTest: 0 onCreate
	I/FragmentTest: 0 onCreateView
	I/MainActivity: onStart begin
	I/FragmentTest: 0 onActivityCreated
	I/FragmentTest: 0 onStart
	I/MainActivity: onStart end
	I/MainActivity: onResume begin
	I/MainActivity: onResume end
	I/FragmentTest: 0 onResume

上图中，activity的生命周期有个begin和end两个日志，这里日志的添加方式如下：

	@Override
    protected void onStart() {
        Log.i(TAG, "onStart begin");
        super.onStart();
        Log.i(TAG, "onStart end");
    }

也就是说，我在基类方法的前后都写了日志，从上面的fragment和activity的生命周期中可以看出，fragment的有的生命周期，实在基类方法中调用的，实际上从源码中也能看到。有的如onresume实际上是在基类方法之后才调用的。实际上是在onResumeFragments方法中调用的。oncreate也是一样，实际上是在基类的setcontentview方法中调用的。

**注意**
静态加载的fragment，不能使用事务来做replace，remove等操作，即使remove了，视图依然会存在，会引发一些不可预料的问题。


####2动态加载

在Activity的oncreate后紧接着add一个fragment，生命周期如下：

	I/MainActivity: onCreate begin
	I/MainActivity: onCreate end
	I/MainActivity: onStart begin
	I/FragmentTest: 0 onAttach
	I/FragmentTest: 0 onCreate
	I/FragmentTest: 0 onCreateView
	I/FragmentTest: 0 onActivityCreated
	I/FragmentTest: 0 onStart
	I/MainActivity: onStart end
	I/MainActivity: onResume begin
	I/MainActivity: onResume end
	I/FragmentTest: 0 onResume

可以看到，和上面的静态加载还是有不同的，上面的静态加载，fragment的onAttach，onCreate，onCreateView都是在setcontentview方法中完成的，而在动态加载中，这里确实在onStart的基类方法中完成的。


**结论**
  也就是说，android会尽量保证生命周期的对应性，但是，fragment的生命周期是在基类的生命周期方法中执行，还是在之后执行，有时候有不确定性，写代码的时候，尽量不要去依赖activity和fragment生命周期的执行顺序，例如在Activity中初始化mName变量：

    @Override
    protected void onStart() {
        Log.i(TAG, "onStart begin");
        super.onStart();
        mName = "name";
        Log.i(TAG, "onStart end");
    }

随后在Fragment的onStart中使用，这里是会出问题的，因为其实Fragment的onStart方法是在Activity的super.onStart中调用的，也就是说，在mName初始化的前一行中被调用，这样一来，在使用的时候，mName还没有来得及初始化。


###二FragmentTransaction

动态加载的fragment往往需要做出各种操作，这里就需要使用FragmentTransaction。

操作有如下：add，remove，replace，hide，show，attach，detach。

####1.add

add就是添加一个fragment到布局容器中。生命周期在上面的动态加载中已经有了。这里不再赘述。

####2.remove

remove从容器中移除一个fragment。
	
	I/FragmentTest: 0 onPause
	I/FragmentTest: 0 onStop
	I/FragmentTest: 0 onDestroyView
	I/FragmentTest: 0 onDestroy
	I/FragmentTest: 0 onDetach

生命周期很简单。

####3.replace

replace是add和remove的联合使用，使用fragment1替换0，生命周期如下：

	I/FragmentTest: 1 onAttach
	I/FragmentTest: 1 onCreate
	I/FragmentTest: 0 onPause
	I/FragmentTest: 0 onStop
	I/FragmentTest: 0 onDestroyView
	I/FragmentTest: 0 onDestroy
	I/FragmentTest: 0 onDetach
	I/FragmentTest: 1 onCreateView
	I/FragmentTest: 1 onActivityCreated
	I/FragmentTest: 1 onStart
	I/FragmentTest: 1 onResume

这里的生命周期顺序不像activity有明确说明，前一个activity被后一个覆盖时，会先执行前一个的onpause，然后是后一个的onstart，onresume，这里只能看到目前的现象是这样，受限于平台和各种因素，很难说两个fragment的生命周期就是一定的，所以这里最好不要依赖于他们的顺序来处理业务。

####4.hide，show

hide，show隐藏或显示一个fragment，不会触发任何生命周期方法， onHiddenChanged(boolean hidden)，如果参数为true，代表被隐藏了。

####4.detach

将fragment的ui从activity上分离，生命周期如下：

	0 onPause
	0 onStop
	0 onDestroyView

可以看到，不会销毁这个fragment，他的状态依然由activity维护，如果此时退出app，拿吗fragment也会执行onDestroy和onDetach。


####4.attach

将已经从UI上detach的fragment重新attach到UI上，如果没有detach，那么attach是没有任何反应的。detach之后再attach，生命周期如下。

	0 onCreateView
	0 onActivityCreated
	0 onStart
	0 onResume

attach需要和detach配合使用。



###三回退栈

addToBackStack(@Nullable String name)是transaction中的将当前事务加入回退栈的方法。从这里可以很好的看出为什么需要使用transaction来执行fragment的各项操作。

fragment的操作往往不是单一的，比如先hide fragment1再show fragment2，其实这是两个动作，但是在返回的时候，希望这两个动作一起回退，那么这里就需要把这两个动作组合成一组行为，所以就需要事务这个概念，它可以保证多个行为组合回退的时候的原子性。即一起回退。

FragmentTransaction的实现类是 BackStackRecord，从名称中可以看出，实际上transaction是为回退服务的，他的实现类是一条回退记录。BackStackRecord中是如何来保存各个操作以及它们的顺序呢？实际上是使用它的静态内部类：

	static final class Op {
	        Op next;
	        Op prev;
	        int cmd;
	        Fragment fragment;
	        int enterAnim;
	        int exitAnim;
	        int popEnterAnim;
	        int popExitAnim;
	        ArrayList<Fragment> removed;
	    }

看名字就知道，这个类是专门记录操作的，并且，这个类和链表节点非常相似，可以看到，这个类可以记录下前一个动作和后一个动作。以此就记录下了一个事务中的所有操作和顺序。在回退的时候，会从记录的尾巴往头开始执行恢复操作。

我们看看下面这个图：

![activity_lifecycle](Images/Fragment/fragment_lifecycle.png)

图的右边有一个箭头，从onDestroyView到onCreateView，这里说的是，fragment从返回栈中返回到布局中。也就是说，如果fragment添加到了返回栈中，那么fragment的操作中的生命周期将会受到一定的影响。

Remove会受到影响，由于replace是remove和add和结合体，所以也会有影响，如果有这些操作并将此次事务加入了回退栈，那么被remove掉的那个fragment的生命周期将和上面的不同，而会如下：

	0 onPause
	0 onStop
	0 onDestroyView

也就是说，没有执行onDestroy，onDetach。这样，当返回的时候，会有：

	0 onCreateView
	0 onActivityCreated
	0 onStart
	0 onResume

也就是说没有执行onAttach和onCreate，和上面图示的一致，也就是说如果被加入了回退栈，有恢复的可能，那么就不会完全销毁掉fragment，而是销毁他的view。


