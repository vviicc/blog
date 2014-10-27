Handoff 是iOS8 和 OSX Yostmite的一个新特征，Handoff可以让你从一个设备转移到另一个设备而不发生打断。  
本教程包含Handoff的基本知识以及在基于非文档app中运用。 

###Handoff概述
*  开始入门：了解你的设备是否兼容handoff，运行基本测试使得能正常工作
*  用户活动：用户活动是handoff的核心工作单元
*  活动类型：知道如何设置活动类型以及活动类型的意义

###开始入门  
handoff不仅仅是在iOS设备与osx设备之间转移，还可以在iOS设备之间转移，目前handoff还不支持在iOS模拟器上运行，
因此，本教程需要您有两个兼容handoff的iOS设备。

###设备兼容性：iOS  
检查iOS设备是否兼容handoff，到`Setting`并选择`General`，如果你能看到`Handoff & Suggested Apps`，
说明你的设备兼容handofff。  
Handoff主要取决于以下东西：  
1.  一个iclound账户，如果你想使用handoff你必须在每个设备都使用同一个iclound账户；
2.  蓝牙 LE 4.0，handoff通过蓝牙LE信号广播活动，因此发送者和接收者都必须这次蓝牙LE 4.0
3.  iclound匹配，设备都应该已经通过iclound匹配，当你在兼容handoff设备上登录iclound帐号时，每个设备都和其他兼容handoff设备匹配。

此时，确保你有两个兼容handoff的设备运行在iOS8或以上使用同一个iclound账户。

###用户活动（User Activities）  
Handoff是基于用户活动的概念，用户活动是独立的集合单元。  
`NSUserActivity`类表示一个用户活动的实例，它在某种程度上概括了可以和其他设备相关的程序的状态。  
有三种方法可以和`NSUserActivity`对象交互：  
*  创建user activity,源app创建一个`NSUserActivity`并调用`becomeCurrent()`方法开始广播进程，比如：

    let activity = NSUserActivity(activityType: "com.razeware.shopsnap.view")  
    activiey.title = "Viewing"  
    activity.userInfo = ["shopsnap.item.key":["Apple","Orange","Banana"]]  
    self.userActivity = activity  
    self.userActivity?.becomeCurrent()  

你可以使用`NSUserActivity`的`userInfo`来传递自然数据类型给接收设备。自然数据类型包括`NSArray,NSData,NSDate,NSDcitionary,NSNull,NSNumber,NSSet,NSString,NSUUID,NSURL` 

*  更新user activity,一旦`NSUserActivity`实例成为当前时，操作系统会周期性地在最前面的view controller 调用`updateUserActivityState(activity:)`方法给你一个机会更新user activity,比如：

    override func updateUserActivityState(activity:NSUserActivity){
    let activityListItem = // ... get updated list of items
    activity.addUserInfoEntriesFromDictionary(["shopsnap.item.key":activityListItems])
    super.updateUserActivityState(activity)
    }
    
你不用设置`userInfo`到一个新的字典或者直接更新它，而是直接调用`addUserInfoEntriesFromDictionary`方法  

*  接收user activity,当你接收的app伴随user activity的handoff启动后，app 代理调用`application(:willContinueUserActivityWithType)`方法，注意这个方法并不传输`NSUserActivity`实例，因为它需要一段时间直到handoff下载并传递`NSUserActivity`数据到你的app。接下来，一旦user activity下载完之后下面的代理回调会调用  

        func application(application:UIApplication!,continueUserActivity userActivity:NSUserActivity!,restorationHandler:(([AnyObject]! -> Void)!) -> Bool){  
          // Do some checks to make sure you can proceed  
          if let window = self.window{  
            window.rootViewController?.restoreUserActivityState(userActivity)  
          }  
          return true  
        }
