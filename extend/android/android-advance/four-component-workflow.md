# 1、四大组件运行状态

Android的四大组件中除了BroadcastReceiver以外，其他三种组件都必须在Android-Manifest中注册，对于BroadcastReceiver来说，它既可以在AndroidManifest中注册也可以通过代码来注册。在调用方式 上，Activity、Service和BroadcastReceiver需要借助Intent，而ContentProvider则无须借助Intent。

Activity是一种展示型组件，用于向用户直接地展示一个界面，并且可以接收用户的输入信息从而进行交互。Activity是最重要的一种组件，对用户来说，Activity就是一个Android应用的全部，这是因为 其他三大组件对用户来说都是不可感知的。Activity的启动由Intent触发，其中Intent可以分为显式Intent和隐式Intent，显式Intent可以明确地指向一个Activity组件，隐式Intent则指向一个或多个目标Activity组件， 当然也可能没有任何一个Activity组件可以处理这个隐式Intent。同一个Activity组件在不同的启动模式下会有不同 的效果。Activity组件是可以停止的，在实际开发中可以通过Activity的finish方法来结束一个Activity组件的运行。

Service是一种计算型组件，用于在后台执行一系列计算任务。由于Service组件工作在后台，因此用户无法直接感知到它的存在。Service组件和Activity组件略有不同，Activity组件只有一种运行模式，即 Activity处于启动状态，但是Service组件却有两种状态：启动状态和绑定状态。当Service组件处于启动状态时，这个时候Service内部可以做一些后台计算，并且不需要和外界有直接的交互。尽管Service组件 是用于执行后台计算的，但是它本身是运行在主线程中的，因此耗时的后台计算仍然需要在单独的线程中去完成。当Service组件处于绑定状态时，这个时候Service内部同样可以进行后台计算，但是处于这 种状态时外界可以很方便地和Service组件进行通信。Service组件也是可以停止的，停止一个Service组件稍显复杂，需要灵活采用stopService和unBindService这两个方法才能完全停止一个Service组件。

BroadcastReceiver是一种消息型组件，用于在不同的组件乃至不同的应用之间传递消息。BroadcastReceiver同样无法被用户直接感知，因为它工作在系统内部。BroadcastReceiver也叫广播，广播的注册有 两种方式：静态注册和动态注册。由于BroadcastReceiver的特性，它不适合用来执行耗时操作。BroadcastReceiver组件一般来说 不需要停止，它也没有停止的概念。

ContentProvider是一种数据共享型组件，用于向其他组件乃至其他应用共享数据。对于一个ContentProvider组件来说，它的内部需要 实现增删改查这四种操作，在它的内部维持着一份数据集合，这个数据集合既可以通过数据库来实现，也可以采用其他任何类型来实现，比如List和Map，ContentProvider对数据集合的具体实现并没有任何 要求。ContentProvider内部的insert、delete、update和query方法需要处理好线程同步，因为这几个方法是在Binder线程池中被调用的，另外ContentProvider组件也不需要手动停止。

# 2、Activity 的工作过程

[Android 11源码分析： Activity的启动流程 - 掘金 (juejin.cn)](https://juejin.cn/post/6994823348190445604#heading-6)

由于Android的内部实现多数都比较复杂，在 研究内部实现上应该更加侧重于对整体流程的把握，而不能深入代码细节不能自拔，太深入代码细节往往会导致“只见树木不见森林”的状态。

要分析Activity的启动过程，至于启动模式以及任务栈等概念本节中并未涉及。

从Activity的startActivity方法开始分析，startActivity方法有好几种重载方式，但它们最终都会调用startActivityForResult方法。

![startActivity.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/159736e4542c491fb3e94d34a8b33f57~tplv-k3u1fbpfcp-zoom-in-crop-mark:3780:0:0:0.awebp)

1.**Activity**的**startActivity**最终会调用**startActivityForResult**方法。再由**Instrumentation**内部执行**execStartActivity**方法开始旅程

2.任务经过一系列的类处理ActivityTaskManagerService->ActivityStarter->RootWindowContainer->ActivityStack->ClientLifecycleManager->ClientTransaction->ApplicationThread最终在**ApplicationThread**里触发ActivityThread父类的scheduleTransaction方法，他内部发送了一个消息。

3.ActivityThread内部类**H**收到消息后，执行scheduleTransaction，又经过一系列调用后，最后由**Instrumentation**完成**Activity的创建**和调用**Activity的onCreate**方法

# 3、Service 的工作过程

# 4、BroadCastReceiver 的工作过程

# 5、ContentProvider 的工作过程