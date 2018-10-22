### 安装
```markdown
1. 下载
https://github.com/golang/dep/releases/download/v0.5.0/dep-windows-amd64.exe

2. 将dep-windows-amd64.exe放入GOPATH/bin下，修改名称为dep
```
---
### 使用
#### dep 帮助文档
| 命令                                  | 说明                                     |
| ------------------------------------- | ---------------------------------------- |
| dep init                              | 初始化新项目                             |
| dep ensure                            | 确保安装项目的依赖                       |
| dep ensure -update                    | 更新所有依赖项的锁定版本                 |
| dep ensure -add github.com/pkg/errors | 为项目添加依赖项                         |
| dep status                            | 报告项目依赖项的状态                     |
| dep version                           | 显示dep版本                              |
| dep check                             | 检查导入，Gopkg.toml和Gopkg.lock是否同步 |



#### dep 使用示例
```markdown
1. 切换到你需要开发的项目下
cd %GOPATH%/src/github.com/xxx/dep-demo

2. 初始化dep init

参考：https://www.jianshu.com/p/e3c9f9039542
```