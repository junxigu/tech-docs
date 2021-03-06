** Java平台的不同版本体现其 平台无关性 和 伸缩性 **

不同版本间支持的API集合不同

* J2EE：适合作为高端性能的服务器上的Java平台
* J2SE：适合作为浏览器上或桌面应用上的Java平台
* J2ME：适合作为资源高度受限的嵌入式设备上的Java平台

** 影响Java程序的平台无关性的一些因素 **

* 所针对的硬件平台是否已经存在相应的Java平台实现
* Java程序使用的API集合在网路中的不同节点的Java平台是否存在
* Java平台的版本改变后对原有程序使用的API的影响
* Java程序是否使用了本地方法
* Java程序是否使用了某些硬件平台厂商提供的特定扩展库
* Java程序是否依赖特定Java平台的实现(及时终结，线程的优先级)
* 对用户界面的依赖
* Java平台中的bug
* 不同Java平台不同实现导致的功能，性能不一致

** 开发平台无关的Java程序的步骤 **

* 选定要运行的主机和设备集合
* 选定一个足够好的Java平台
* 对每个目标主机和设备选定一个Java平台实现
* 编写程序时只使用标准的API
* 编写程序时不要依赖及时终结和线程优先级
* 努力设计一个能在每个平台正常运行的界面
* 在所有目标主机和设备上测试

是否选择平台无关需要考虑性能，功能等各种因素



