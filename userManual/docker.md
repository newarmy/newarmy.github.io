# docker
## linux 命令
1. lsb_release -a，查看发行版本信息，并且方法可以适用于所有的Linux发行版本。  
## ubuntu安装docker[查看](https://docs.docker.com/engine/install/ubuntu/)  
### 设置存储库  
1. 更新apt软件包索引并安装软件包，以允许apt通过HTTPS使用存储库
``` bash 
   # 更新apt软件包索引
  sudo apt-get update
   # 允许apt通过HTTPS使用存储库
  sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```  
2. 添加Docker的官方GPG密钥  
``` bash 
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```  
3. 使用以下命令设置稳定的存储库。要添加 nightly 或 test 存储库，请在下面的命令中的单词stable之后添加单词nightly或test（或两者）。了解nightly频道和test频道
``` bash  
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```  
### 安装Docker引擎  
1. 更新apt软件包索引，并安装最新版本的Docker Engine、containerd和Docker Compose，或转至下一步安装特定版本  
``` bash 
 sudo apt-get update
 sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```  
2. 要安装特定版本的Docker Engine，请在repo中列出可用版本，然后选择并安装  
a. 列出回购协议中可用的版本：  
    apt-cache madison docker-ce  
b. 使用第二列中的版本字符串安装特定版本，例如5:20.10.15~3-0~ubuntu-focal  
``` bash 
sudo apt-get install docker-ce=5:20.10.15~3-0~ubuntu-focal docker-ce-cli=5:20.10.15~3-0~ubuntu-focal
 containerd.io docker-compose-plugin
```  

3. 通过运行hello world映像，验证Docker引擎是否已正确安装  
``` bash  
 # 此命令下载测试映像并在容器中运行。当容器运行时，它打印一条消息并退出。
 sudo docker run hello-world
```  

### 卸载 docker
1. 删除安装包：  
sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-compose-plugin
2. 删除镜像、容器、配置文件等内容：  
 sudo rm -rf /var/lib/docker  
 sudo rm -rf /var/lib/containerd
### [docker命令](https://www.runoob.com/docker/docker-command-manual.html)   

1. 启动docker服务
sudo service docker start  
2. 查看ubuntu版本  
https://hub.docker.com/_/ubuntu?tab=tags&page=1  
3. 拉取最新版的 Ubuntu 镜像  
 docker pull ubuntu:latest  
4. 查看本地镜像  
 docker images  
5. 运行容器，并且可以通过 exec 命令进入 ubuntu 容器  
``` bash  
# -i: 以交互模式运行容器，通常与 -t 同时使用；
# -t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用
# -p: 指定端口映射，格式为：主机(宿主)端口:容器端口
# --name="nginx-lb": 为容器指定一个名称；
# -d:  后台运行容器，并返回容器ID； 
docker run -itd --name ubuntu-test ubuntu  
# 在运行的容器中执行命令
# -d :分离模式: 在后台运行
#  -i :即使没有附加也保持STDIN 打开
# -t :分配一个伪终端
docker exec -it ubuntu-test /bin/bash  
# 退出 
exit 
# 杀掉一个运行中的容器
docker kill [OPTIONS] container [container...]
```  
6. 通过 docker ps 命令查看容器的运行信息   

7. 查看docker当前镜像路径：docker info
``` bash
 Docker Root Dir: /var/lib/docker
``` 
### Dockerfile实践
Dockerfile 是由一系列的参数、命令构成的可执行脚本，用来构建、定制 Docker 镜像。  

#### 在wsl2中启动vscode
在wsl2, ubuntu中执行： code .  
[查看](https://docs.microsoft.com/zh-cn/windows/wsl/tutorials/wsl-vscode)   
#### 创建 app.js 
``` javascript  
const http = require('http');
const PORT = 30010;

const server = http.createServer((req, res) => {
    res.end('Hello Docker');
})

server.listen(PORT, () => {
    console.log('Running on http://localhost:', PORT);
});
```  
#### 创建package.json 
``` javascript  
{ 
    "name": "hello-docker", 
    "version": "1.0.0",
    "description": "", 
    "author": "May",
    "main": "app.js",   
    "scripts": { 
      "start": "node app.js"
    },
    "dependencies": { 
      
    }
  }
```
#### .dockerignore 
``` javascript  
 .git
node_modules
```
#### Dockerfile
``` bash  
FROM node:10.0 

# 在容器中创建一个目录 
RUN mkdir -p /root/nodePro/ 

# 定位到容器的工作目录 
WORKDIR /root/nodePro/

# RUN/COPY 是分层的，package.json 提前，只要没修改，就不会重新安装包
COPY package.json /root/nodePro/package.json
RUN cd /root/nodePro/
RUN npm i 

# 把当前目录下的所有文件拷贝到 Image 的 /root/nodePro/ 目录下
COPY . /root/nodePro/

EXPOSE 30010
CMD npm start
```
FROM：FROM 是构建镜像的基础源镜像，该 Image 文件继承官方的 node image  
RUN：后面跟的是在容器中执行的命令  
WORKDIR：容器的工作目录  
COPY：拷贝文件至容器的工作目录下，.dockerignore 指定的文件不会拷贝  
EXPOSE：将容器内的某个端口导出供外部访问  
CMD：Dockerfile 执行写一个 CMD 否则后面的会被覆盖，CMD 后面的命令是容器每次启动执行的命令，多个命令之间可以使用 && 链接，例如 CMD git pull && npm start    
### 构建 hello-docker 镜像文件  
``` bash 
# Dockerfile 文件创建好之后，使用 docker image build 命令创建镜像文件，-t 参数用来指定镜像的文件名称，最后一个 . 也不要省略，表示 Dockerfile 文件的所在目录
docker image build -t hello-docker .  
# docker images 查看
docker images

```  
### 运行容器  
``` bash  
# 镜像构建成功之后通过 docker container run 命令来生成一个容器，几个参数说明：
# -d：表明容器的运行模式在后台
# -p：端口映射，将本机的 5000 端口映射到容器的 30010 端口，这样在外网就可通过 30000 端口访问到我们的服务
# hello-docker：为我们的镜像名字
docker container run -d -p 5000:30010 hello-docker
```  

### 检查日志
``` bash 
# 查看运行日志，“c2891d477edf” 为容器 ID 
docker logs -f c2891d477edf
```  
### 容器进入退出
``` bash  
# 为了方便排查内部容器文件，可以通过 docker exec -it c2891d477edf /bin/sh 命令进入容器，c2891d477edf 为容器 ID
docker exec -it c2891d477edf /bin/sh
ls # 列出目录列表
```  

## Registry实践
Registry 是一个注册服务器，是一个集中存放镜像仓库的地方，这里着重介绍下 Docker Hub，它是官方维护的一个公共仓库，我们的大部分需求也都可从这里下载
###  注册 Docker 账号
在开始之前你需要先去 Docker 官网注册一个账号 https://hub.docker.com/ 后续讲解发布镜像需要用到  
### 镜像搜索  
``` bash 
# 使用 docker search 镜像名称 可以搜索你需要的镜像，搜索结果会根据 STARS 进行排序
docker search nginx
``` 
### 镜像拉取
``` bash 
# 搜索到需要的镜像后执行 docker pull 命令拉取镜像
docker pull nginx
``` 
### 发布镜像实现共享
1. 登陆 Docker，已登陆的可以忽略这一步  
``` bash 
docker login
``` 
2. 为本地镜像打标签，tag 不写默认为 latest  
``` bash 
# docker image tag [imageName] [username]/[repository]:[tag]
$ docker image tag hello-docker xjdong/hello-docker
``` 
3. 发布镜像文件  
``` bash 
# docker image push [username]/[repository]:[tag] 
$ docker image push xjdong/hello-docker
``` 
## 参考  
[docker入门](https://www.nodejs.red/#/devops/docker-base)  
[菜鸟docker](https://www.runoob.com/docker/docker-tutorial.html)