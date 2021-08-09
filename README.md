# 流水线样例

阿里云上配置cicd流水线，把一个React + Springboot的前后端分离服务部署到阿里ACK环境。

## 待完善项目
[ ] 加速Maven build速度。配置镜像
[ ] 分离build jar包和docker image步骤
[ ] 前端独立部署
[ ] 增加对数据库的使用
[ ] Jenkins改用工作节点build
[ ] 增加前后端单元测试样例
[ ] 使用Helm参数化k8s部署
[ ] 改用ACK专用版（？）

## 配置Jenkins ECS
### 申请ECS实例
可以使用现有实例，如果没有可以申请一台。
例子中使用的操作系统是 Alibaba Cloud Linux  3.2104 64位。如果使用的是其它操作系统，操作可能略有区别。

### 安装Jenkins
[官方参考文档](https://www.jenkins.io/doc/book/installing/)
Jenkins有多种安装方式，本次样例采用在宿主机Linux环境直接安装。

``` bash
# 配置yum源
sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo yum upgrade
sudo systemctl daemon-reload
# 安装
sudo yum install jenkins java-11-openjdk-devel
```

### 安装Git Docker
``` bash
sudo yum install git
```
这里有两个选择，使用阿里云dnf版或社区版。本样例使用样例版。
参考： https://help.aliyun.com/document_detail/264695.html
#### 安装dnf默认版。
``` bash
dnf -y install docker
```
注意如果安装dnf版，没有守护进程。不需要systemctl 启动或者停止docker。

#### 安装社区版
``` bash
# Ali linux3 需要用这个改写地址。 参考 https://help.aliyun.com/knowledge_detail/257767.html
dnf install dnf-plugin-releasever-adapter --repo alinux3-plus

sudo yum install -y \
    yum-utils \
    device-mapper-persistent-data \
    lvm2

sudo yum-config-manager \
    --add-repo \
    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

Def install docker-ce
```

配置阿里云Docker镜像。镜像地址可以在阿里云账号的镜像服务中查到。使用内网地址避免产生外网流量。
创建 `/etc/docker/daemon.json`
``` json
{
  "registry-mirrors": ["https://registry-vpc.cn-shanghai.aliyuncs.com"]
}
```

确保Jenkins用户可以使用docker
``` bash
sudo usermod -a -G docker jenkins
```

启动Docker
``` bash
sudo systemctl start docker
```

### 启动Jenkins
``` bash
sudo systemctl start jenkins
```

### 配置Jenkins
#### 在ECS安全规则中添加规则，开启8080端口

#### 本地浏览器打开Jenkins 页面 http://<ECS_public_ip>:8080

#### 解锁Jenkins，按照页面提示，把初始密码填入页面
![unlock jenkins](doc/pic/unlock.png)

#### 创建用户名密码

#### 安装建议的Plugin

#### 添加新pipeline

![new pipeline](doc/pic/new-task.png)

点击New Item >> Multibranch Pipeline

![new pipeline](doc/pic/new-pipeline-2.png)

#### 设置git hub credentials

![github credentials](doc/pic/github-credentials.png)

如图，本例使用的是Github：

- 设置仓库的git地址
- 点击`添加`，选择credentials所影响的层级，根级别即整个jenkins级别，亦可选择本project级别，点击之后，将进入凭据设置页面
- 进入凭据设置页面之后，如图所示，凭据设置为：用户名(Github登录用户名)+token，其中[token创建](https://github.com/settings/tokens)，token的权限只要在`repo`即可。注意：token不能包含空格，拷贝的时候注意。
- 输入用户名+token之后，jenkins会自动验证

#### 设置jenkins-pr

![github pr](doc/pic/git-pr.png)

这里选择`仅仅具有拉取请求的分支`

#### Jenkinsfile

![jenkins file](doc/pic/Jenkinsfile.png)

如图，build configuration，是指使用哪个脚本文件来指示jenkins进行流水线build作业。默认是扫描branch分支下的`Jenkinsfile`文件。所以仓库中的Jenkinsfile必须要存在

上面设置完成之后，点击`保存`保存配置。这里暂时禁用pipeline，等等k8s配置完成后再执行。

#### 设置Git hook自动触发流水线
待更新……

## 配置ACK集群

### 开通ACK集群
按默认选项创建，可以在创建集群时选择工作节点的ECS规格生成。也可以预先创建好ECS加入集群。
本样例中选择的k8s版本为1.18.8
注意：加入集群会清空ECS硬盘。
等待集群创建成功。
错误排查：出现 CSI provider启动不成功的情况时，把pod数设置为1

### 在Jenkins服务器上安装kubectl
``` bash
curl -LO "https://dl.k8s.io/release/v1.18.8/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```
更新jenkens 用户的 ~/.kube/config 文件。把集群信息->连接信息中的内容粘贴进去。

### 为Jenkins准备docker 仓库密码

### 为ACK集群生成docker 仓库密码
``` base
kubectl -n=demo create secret docker-registry ali-registry --docker-server=registry-vpc.cn-shanghai.aliyuncs.com --docker-username=vicwu_sh --docker-password=<密码>
```

