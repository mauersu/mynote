#Cheess项目容灾分析

##项目整体架构图如图所示

![skin-ease](http://mauersu.github.io/img/cheess/cheessArch.png)

##系统容灾分析（其情况分析中，如若未说明，则默认其他模块系统功能正常）：

![skin-ease](http://mauersu.github.io/img/cheess/cheessProb.png)

###1.图中标示1处为web模块
* 如若2处关联点系统（zookeeper）崩溃，对web系统无影响，web系统crud正常，cheess系统失去动态热更新配置功能
* 如若2处关联点系统（db）崩溃，web系统崩溃，cheess系统失去动态热更新配置功能
* 如若2处关联点系统（web）崩溃，cheess系统失去动态热更新配置功能

###2.假如图中标示2处为server模块
* 如若2处关联点系统（db，zookeeper）崩溃，server集群崩溃，并无法对外服务，对重新启动系统有影响，启用载入本地配置功能(4处）
* 如若2处关联点系统（db，zookeeper）崩溃，server集群崩溃，并无法对外服务，对连接此server集群的正在运行系统有影响，无法正常收到cheess系统动态更新配置消息，此server集群热更新配置功能失效
* 如若2处关联点系统（server）崩溃，对连接此server的正在运行系统无影响，client将连接其他server，有其他server继续提供服务

###3.图中标示3处通信采用接收信息确认机制
* 如若通信失败，对重新启动系统有影响，其将尝试载入本地配置(4处）
* 如若通信失败，对正在运行系统有影响，无法正常收到cheess系统动态更新配置消息，此项目热更新配置功能失效

###4.图中标示4处为本地文件系统，cheess.data文件备份最近一次获取的配置
* 如若不存在cheess.data文件，则项目启动失败



