#### operator介绍
[coreos operator](https://coreos.com/operators/)  
```markdown
  Operator是一种打包，部署和管理Kubernetes应用程序的方法。Kubernetes应用程序是一个部署在Kubernetes上并使用Kubernetes API
和kubectl工具进行管理的应用程序。
  为了能够充分利用Kubernetes，你需要一组内聚的API来扩展，以便为在Kubernetes上运行的应用程序提供服务和管理。你可以将Operators
视为在Kubernetes上管理此类应用程序的运行时。
```
---

#### operator-framework介绍
[operator-framework github地址](https://github.com/operator-framework)
```markdown  
  Operator Framework是一个开源项目，提供开发人员和运行时Kubernetes工具，使你能够加速Operator的开发。Operator Framework
包括如下几个：
- operator-sdk: 用于k8s应用程序的SDK,提供高级API、有用的抽象和项目脚手架，使开发人员无需了解k8s API的复杂性;
- operator lifecycle manager: 监督在k8s集群中运行的所有operators(及其相关服务)的生命周期的安装、更新和管理;
- operator metering: operator计量，为提供专业服务的operator启用使用情况报告.
```
---

#### operator sdk
[operator 用户使用指南](https://github.com/operator-framework/operator-sdk/blob/master/doc/user-guide.md)
```markdown
  sdk提供了构建、测试和打包operator的工具。最初，sdk有助于将应用程序的业务逻辑(例如,如何扩展、升级和备份)与k8s api结合起来执行这些操作.
随着时间的推移，sdk可以让工程师更智能地使应用程序具有云服务的用户体验。sdk中包含了跨operator共享的主要实践和代码模式，以帮助防止重新发明轮子.
```  
---

#### operator lifecycle manager
[在k8s集群上安装operator lifecycle manager](https://github.com/operator-framework/operator-lifecycle-manager/blob/master/Documentation/install/install.md)
```markdown
  构建后，需要在k8s集群上部署operator. Operator Lifecycle Manager是便于管理k8s集群上Operator的基架。有了它，管理员
可以控制Operator在哪些namespace中可用，以及谁可以与正在运行的Operator交互。他们还可以管理Operator及资源的整个生命周期
例如触发对运营商及其资源的更新.  
```
---

#### operator metering
```markdown
  略
```

