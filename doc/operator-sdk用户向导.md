## 用户指南
```markdown
本指南介绍了使用operator-sdk CLI工具和controller-runtime库API构建简单memcached-operator的示例.
```
### 要求
```markdown
dep version v0.5.0+.
git
go version v1.10+.
docker version 17.03+.
kubectl version v1.10.0+.
Access to a kubernetes v.1.10.0+ cluster.
```
### 安装operator sdk cli
```markdown
Operator SDK具有CLI工具，可帮助开发人员创建，构建和部署新的Operator项目
$ mkdir -p $GOPATH/src/github.com/operator-framework
$ cd $GOPATH/src/github.com/operator-framework
$ git clone https://github.com/operator-framework/operator-sdk
$ cd operator-sdk
$ git checkout master
$ make dep
$ make install
```