## 用户指南
[operator-sdk user-guide](https://github.com/operator-framework/operator-sdk/blob/master/doc/user-guide.md)
```bash
本指南介绍了使用operator-sdk CLI工具和controller-runtime库API构建简单memcached-operator的示例.
```
### 要求
```bash
dep version v0.5.0+.
git
go version v1.10+.
docker version 17.03+.
kubectl version v1.10.0+. (直接拷贝一个openshift环境的oc二进制文件然后创建一个kubectl软连接,注意要unset http_proxy)
Access to a kubernetes v.1.10.0+ cluster.(直接复制一份openshift下$HOME/.kube/config文件到本机即可,注意要unset http_proxy)
```
### 快速开始
#### 检查和安装operator-sdk CLI
```bash
Operator SDK具有CLI工具，可帮助开发人员创建，构建和部署新的Operator项目
$ mkdir -p $GOPATH/src/github.com/operator-framework
$ cd $GOPATH/src/github.com/operator-framework
$ git clone https://github.com/operator-framework/operator-sdk
$ cd operator-sdk
$ git checkout master
# git checkout -b v0.0.7

$ make dep
$ make install
```
#### 用operator-sdk CLI创建和部署一个operator-sdk
```bash
# Create an app-operator project that defines the App CR.
$ mkdir -p $GOPATH/src/github.com/example-inc/
# Create a new app-operator project
$ cd $GOPATH/src/github.com/example-inc/
$ operator-sdk new app-operator
$ cd app-operator

# Add a new API for the custom resource AppService
$ operator-sdk add api --api-version=app.example.com/v1alpha1 --kind=AppService

# Add a new controller that watches for AppService
$ operator-sdk add controller --api-version=app.example.com/v1alpha1 --kind=AppService

# Build and push the app-operator image to a public registry such as quay.io
$ operator-sdk build quay.io/fenggolang/app-operator
$ docker login -u 2432018053@qq.com --password my_password quay.io

$ docker push quay.io/fenggolang/app-operator

# Update the operator manifest to use the built image name
$ sed -i 's|REPLACE_IMAGE|quay.io/fenggolang/app-operator|g' deploy/operator.yaml

# Setup Service Account
$ kubectl create -f deploy/service_account.yaml
# Setup RBAC
$ kubectl create -f deploy/role.yaml
$ kubectl create -f deploy/role_binding.yaml
# Setup the CRD
$ kubectl create -f deploy/crds/app_v1alpha1_appservice_crd.yaml
# Deploy the app-operator
$ kubectl create -f deploy/operator.yaml

# Create an AppService CR
# The default controller will watch for AppService objects and create a pod for each CR
$ kubectl create -f deploy/crds/app_v1alpha1_appservice_cr.yaml

# Verify that a pod is created
$ kubectl get pod -l app=example-appservice
NAME                     READY     STATUS    RESTARTS   AGE
example-appservice-pod   1/1       Running   0          1m

# Cleanup
$ kubectl delete -f deploy/app_v1alpha1_appservice_cr.yaml
$ kubectl delete -f deploy/operator.yaml
$ kubectl delete -f deploy/role.yaml
$ kubectl delete -f deploy/role_binding.yaml
$ kubectl delete -f deploy/service_account.yaml
$ kubectl delete -f deploy/crds/app_v1alpha1_appservice_crd.yaml
```