## 环境

开发环境：尽量减少使用(机器，网络都不太好)。开发阶段在开发人员本地电脑测试自身服务功能没问题即可。如果别人必须依赖你的服务，在开发环境部署 master。
项目环境： 联调联测, 是开发和测试使用的主要测试环境。由多个开发和测试共同协作完成。所有新的功能性测试尽量迁移到该环境测试，避免预发环境分支堆积。
日常环境：主干环境，只部署 master 代码，避免影响别人测试。
预发环境：上线前的回归测试，尽量减少测试时间，避免堆积。所有新的功能性测试要迁移项目环境提前测试。
