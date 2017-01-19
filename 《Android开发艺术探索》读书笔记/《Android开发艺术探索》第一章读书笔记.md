# Activity的生命周期

##### 典型情况下的生命周期

> 生命周期（不包含onRestart): onCreate-->onStart-->onResume-->onPause-->onStop-->onDestroy
>
> 用户返回原Activtiy：onStop-->onRestart-->onStart

关于onStart、onResume、onPause、onStop对于我们的区别：
> onStart和onStop是从Activity是否可见这个角度来回调的
>
> onResume和onPause是从Activity是否处于前台这个角度来回调的

Activity启动过程简易理解：启动Activity的请求由Instrumention来处理，然后它通过Binder向ActivityManagerService（AMS）发送请求，AMS内部维护了一个ActivityStack并负责栈内的Activity的状态同步，AMS通过ActivityThread去同步Activity的状态从而完成生命周期的调用。

- 从一个Activity开启新的Activity时，会先执行原Activity的onPause，然后执行新Activity的onCreate--onStart--onResume。所以，为了让新的Activity尽快的显示出来，在onPause中不能执行重量级的操作，应当尽量在onStop中做操作，但onStop也不能做耗时的操作。

##### 异常状态下的生命周期

###### 资源相关的系统配置发生改变导致Activity被杀死并重新创建
> 如果不对Activity做特殊的处理，当系统配置发生改变时，Activity会被销毁并重新创建。
> 
> 在这种情况下，Activty是被异常终止的，会依次调用onPause，onStop，onDestroy方法，并且在onStop之前一定会调用onSaveInstanceState来保存当前的Activity的状态，并将保存的Bundle对象作为参数传递给onRestoreInstanceState和onCreate方法，所以可以通过判断Bundle对象是否为空来判断Activity是否被重建了，如果重建了可以获取保存的数据并恢复，从时序上看，onRestoreInstanceState在onStart之后被调用。
>
> 关于保存和恢复View层次结构，系统的工作流程：Activity被意外终止，Activity会调用onSaveInstanceState去保存数据，然后Activity会委托Window保存数据，Window委托在它上面的顶层容器保存数据，顶层容器再一一通知它的子元素来保存数据。这是一种典型的委托思量。数据恢复过程也类似这个过程。
> 
> 要注意的是，系统只有在Activity被异常终止时，才会调用onSaveInstanceState和onRestoreInstanceState来存储和恢复数据。

###### 资源内存不足导致优先级低的Activity被杀死
这种情况与上一种情况的数据存储和恢复过程一致。
Activity优先级由高到低：	

1. 前台Activity-----与用户正在交互。优先级最高。
2. 可见Activity-----Activity可见，但不能交互。
3. 后台Activity-----执行了onStop的Activity。优先级最低。

解决由于系统配置发生改变而导致Activity被重新创建：
> 给Activity设置： androdi:configChanges="orientation|locale|keyboardHidden|screenSize"
> 
> orientation指屏幕方向发生了改变
> 
> local指设备的本地位置发生了改变，一般指切换系统语言
> 
> keyboardHidden指键盘的可访问性发生了改变，比如用户调出了键盘
> 
> screenSize屏幕尺寸信息发生了改变，当屏幕旋转时，会导致屏幕尺寸信息发生改变。但这个选项比较特殊，它与编译选项有关，若minSdkVision和targetSdkVersion均低于13时，此选项不会导致Acitivy重启，否则会导致Activity重启。

设置configChanges后，当系统配置发生改变后，不会销毁Activity重建，也不会调用onSaveInstanceState和onRestoreInstanceState来存储和恢复数据，而是调用onConfigurationChanged方法，这个时候可以做自己特殊的处理。

# Activity的启动模式