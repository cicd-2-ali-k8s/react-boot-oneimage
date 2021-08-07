# sb-docker-helm-k8s-demo

Dockerizing the spring boot app, upload it to the docker image, helm chart invoke the image, then running it in k8s.

## 初步测试

### 安装nginx

目标：

- 在Jenkins机器上安装nginx，提供简单首页服务

```sh
# 通过openresty方式安装nginx


# http://openresty.org/cn/linux-packages.html

# 更新apt-get
apt-get update

# 安装导入 GPG 公钥时所需的几个依赖包（整个安装过程完成后可以随时删除它们）：
sudo apt-get -y install --no-install-recommends wget gnupg ca-certificates

# 导入GPG 密钥：
wget -O - https://openresty.org/package/pubkey.gpg | sudo apt-key add -

# 查看自己是amd64还是ard64
arch

# xm6_64:
echo "deb http://openresty.org/package/ubuntu $(lsb_release -sc) main" \
    | sudo tee /etc/apt/sources.list.d/openresty.list

# apt-get update
apt-get update

# 安装openresty软件包
apt-get -y install openresty

```

访问：http://121.41.128.117:80/ 可达openresty主页
