# rk3288-
rk3288 Android source 4.4.2  bottom driver system

----------------------------------
## RK3288 android4.4.2系统源码
- abi
- art
- bionic
- bootable
- bulid
- cts
- dalvik
- developers
- development
- device
- docs
- external
- frameworks
- hardware    |  串口
- kernel 内核             | 开机logo
- libcore
- libnativehelper
- ndk     |  开发套件
- out
- packages          |  apps系统应用 （Launcher）
`图库、APK 安装、谷歌市场、浏览器、计算器、日历、摄像、闹钟、下
载、电子邮件、资源管理器、Gmail、谷歌地图、音乐、录音、设置、视
频播放器、GTalk、MovieStudio、CTS`

- pdk     |  开发套件
- prebuilts ...misc
- rkst
- RKDocs
- RKTools RK工具
- rockdev           |    固件
- sdk               | 开发套件
- system
- tools
- u-boot   |   超强大、支持多系统、多功能全开放源码项目--系统引导
- verdor     |   供应商

---------------------------------------------------------
**关于写hal的一些流程**

1. 创建专用驱动
需要能report 需要能接收tty3和tty4信息

2. 创建对应hal层序 --- 结束后会自动放在/system/lib/hw下， 如果没有需要自行防止
打开需要控制的文件比如report驱动 xfm驱动
并不绝对要绑定驱动，我们可以只使用read与write进行操作。
绑定的是hal与service和aidl , kernel 与上层有绝对的分离 

3. 创建aidl接口 ----- 具有跨进程访问能力的描述性语言`（android interface definition language,aidl）` --- aidl会自动生成一个stub,我们需要在service中继承该stub，并且在service中使用该接口

 - 硬件访问服务接口一般在在`/framework/base/core/java/android/os/`该目录下
    `IFregService.aidl`
      例子： 
`{
  package android.os;
  interface IFregServer{ ---- 以下两个接口是给予service 所用的
  void setVal(int val);
  int getVal();
  };
 }`

 - 加入到`/framework/base`中的`LOCAL_SRC_FILE`中并且进行编译
      需要在`/framework/base`中进行编译，编译过后的`framework.jar` 包含了`IFregServer`接口，它继承了`android.os.interface.`

4. 创建jni接口 ---- `/framework/base/service/jni`
我们把该方法`/framework/base/services/jni/com_android_service_FregService.cpp` 放在该目录中`#include <hardware/freg.h> `
该头文件无法找到
加入`jni_onload.cpp`中

5. 创建server程序 ---- `/framework/base/core/java/android/service/FregService.java`
--- 在该server内部定义了一个Binder本地对象Stub类，实现了`IFregService`的接口，并继承binder类。
server程序必须把aidl中所有的接口都实现，否则编译器报错

6. 启动硬件访问服务
`/framwork/base/services/java/com/android/server/SystemService.java`

  其中`addService("reeman",new ReemanService()); `--- 该接口决定了上次`getservice`的时候需要传递的参数

7. 在app层中加入反射，并且加入aidl编一下

        `try{
          Object object = new Object();
          Method getService = Class.forName("android.os.ServiceManager").getMethod("getService", String.class);
          Object obj = getService.invoke(object, new Object[]{new String("reeman")});
          //System.out.println(obj.toString());
          reemanService = IReemanService.Stub.asInterface((IBinder)obj);
          }catch(ClassNotFoundException ex){
          //ignored
          }catch(NoSuchMethodException ex){
          //ignored
          }catch(IllegalAccessException ex){
          //ignored
          }catch(InvocationTargetException ex){
          //ignored
          }`
