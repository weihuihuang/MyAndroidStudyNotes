# Study Of Service

## Service的定义

官方链接：https://developer.android.com/guide/components/services.html  
Service 是一个可以在后台执行长时间运行操作而不提供用户界面的应用组件。服务可由其他应用组件启动，而且即使用户切换到其他应用，服务仍将在后台继续运行。 
此外，组件可以绑定到服务，以与之进行交互，甚至是执行进程间通信 (IPC)。常见的有音乐播放器的实现。    

### 服务的启动
当应用组件（如 Activity）通过调用 startService() 启动服务时，服务即处于“启动”状态。一旦启动，服务即可在后台无限期运行，即使启动服务的组件已被销毁也不受影响。

### 服务的绑定
当应用组件通过调用 bindService() 绑定到服务时，服务即处于“绑定”状态。绑定服务提供了一个客户端-服务器接口，允许组件与服务进行交互、发送请求、获取结果，甚至是利用进程间通信 (IPC) 跨进程执行这些操作。 仅当与另一个应用组件绑定时，绑定服务才会运行。 多个组件可以同时绑定到该服务，但全部取消绑定后，该服务即会被销毁。  

### 启动和绑定的最大区别
startService开启服务以后，与组件没有关联，独立运行，组件被销毁依然存在。
bindService开启服务以后，与组件存在关联，不再使用时时必须调用unbindService方法，否则会报ServiceConnection泄漏的错误。
通过adb方法查看当前app存在的Service: adb shell dumpsys activity services + 要查询的app的applicationId
