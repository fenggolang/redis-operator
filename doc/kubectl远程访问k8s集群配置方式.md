#### 下载kubectl二进制文件
```bash
# 把公司环境的k8s/openshift集群中安装好的kubectl/oc二进制文件拷贝到本机/usr/bin目录即可
sudo scp root@172.17.12.79:/usr/bin/oc /usr/bin/
cd /usr/bin/ && ln -s oc kubectl
```

#### 配置$HOME/.kube/config
```bash
# 把公司环境的k8s/openshift集群中生成好的$HOME/.kube/config文件拷贝到本机$HOME/.kube目录即可
scp m8@172.17.12.79:/home/m8/.kube/config ./
```

#### 测试
```bash
wangpengcheng@wangpengcheng-PC:~$ kubectl version
Client Version: version.Info{Major:"1", Minor:"11+", GitVersion:"v1.11.0+d4cacc0", GitCommit:"d4cacc0", GitTreeState:"clean", BuildDate:"2018-10-16T11:18:47Z", GoVersion:"go1.10.2", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"11+", GitVersion:"v1.11.0+d4cacc0", GitCommit:"d4cacc0", GitTreeState:"clean", BuildDate:"2018-10-17T03:34:44Z", GoVersion:"go1.10.3", Compiler:"gc", Platform:"linux/amd64"}
wangpengcheng@wangpengcheng-PC:~$ oc version
oc v3.11.0+fc76ca3-7
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://m8-master-12-79:443
openshift v3.11.0+84fe0ad-23
kubernetes v1.11.0+d4cacc0
wangpengcheng@wangpengcheng-PC:~$ 

```