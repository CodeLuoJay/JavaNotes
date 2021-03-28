# Docker+nginx部署SpringBoot+vue前后端分离项目

## 学习视频笔记记录

### 2021-03-25 window环境部署

#### 前端

1.需要安装nginx来部署，nginx安装1.8稳定版本
2.nginx需要配置nginx.conf文件，找到location的位置在下面添加一行

```bash
try_files $uri $uri/ /index.html last;
```

vue + nginx的配置， vue路由必须先加载 index.html 或者xx.js 才能识别到路由，如果不配置这个默认是发布目录下找，即把路由当做文件夹
使用try_files 进行从url尝试，如果获取不到资源，加载index.html 再利用 rewrite的 last参数，保持路由路径不变，从而实现刷新页面。
$uri 是请求文件的路径
$uri/ 是请求目录的路径

更改nginx后需要重新再启动nginx服务，然后直接输入localhost即可以访问

#### 后端

后端需要使用maven打包SpringBoot成jar包，然后以`java -jar xxx 形式启动`
maven打包名令,后面的`-Dmaven.test.skip=true`表示跳过测试文件打包，注意`=`前后不能有空格，否则会报错

```bash
mvn clean package -Dmaven.test.skip=true
```

`java -jar xxx.jar `后面还可以指定使用哪个配置文件启动，不指定的话默认使用default的配置文件启动

`java -jar xxx.jar --spring.profiles.active=dev`
`java -jar springboot jar --server.port=8181`

另外如果idea的终端命令提示mvn不是批处理命令，则需要配置maven的环境变量
具体步骤如下
1.配置MAVEN_HOME,内容为安装Maven的目录
2.在path下增加内容`%MAVEN_HOME%/bin`，让windows能全局识别mvn命令
3.需要配置idea的maven的maven home directory
4.重启idea再试一次

### 2021-03-26 docker环境部署

dockers部署思路示意图

![image-20210327224237855](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210327224237855.png)

dockers部署思路示意图说明

如同windows部署一样，前端的vueblog-vue 需要打包成dist文件夹并把所有打包出来的文件存放至nginx的html文件下配合nginx才能从webpack打包的html中找到

对应的路由，而后端vueblog需要使用maven打包成jar包并且需要依赖MySQL和Redis才能成功启动。

所以整理一下思路和步骤：

1. 前端webpack打包dist文件夹的内容存放至nginx的html文件夹
2. 编写nginx.conf文件，让html文件能顺利找到路由和渲染页面
3. 后端需要打包成jar形式以后台jar包运行，后端需要依赖mysql的数据和redis的缓存

所以软件安装的思路和步骤：

1. 安装docker
2. 安装docker compose
3. 编写docker file 来进行容器编排
4. 编写docker-compose.yaml来进行容器服务的编排
5. 容器服务编排（nginx服务、redis服务、mysql服务）相当于对应的软件安装
6. 配置容器的目录和宿主主机的目录挂载（nginx的html和nginx.conf指向Linux的中对应的存放目录）
7. 进入容器中的MySQL服务创建表和导入数据

#### Linux软件安装

##### 安装docker

```bash
yum install docker
```

##### 安装docker compose

官方文档https://docs.docker.com/compose/install/

```shell
sudo curl -L "https://github.com/docker/compose/releases/download/1.28.6/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

#### 编写容器编排文件

##### 编写docker File

```docker
# 使用java的版本
FROM java:8

# 暴露端口
EXPOSE 8080

# 添加项目并重新命名为 app.jar
ADD vueblog-0.0.1-SNAPSHOT.jar app.jar
RUN bash -c 'touch /app.jar'

# 编写启动的命令，以逗号隔开，并指定使用的配置文件
ENTRYPOINT ["java","-jar","app.jar","--spring.profiles.active=pro"]
```

##### 编写docker-compose.yaml

```yaml
#编排的版本        
version: '3'

services:
  #服务名称
  nginx:
    #镜像
    image: nginx:latest
    #暴露端口
    ports:
      - 80:80
    #指定配置文件的位置
    volumes:
      - "/root/nginx/nginx.conf: /etc/nginx/nginx.conf"
      - "/root/nginx/html: /usr/share/nginx/html"
    #授权才能调用
    privileged: true
  mysql:
    image: mysql:5.7.27
    ports:
      - 3306:3306
    #新建root必须指定用户密码，需要配置环境
    environment:
      - MYSQL_ROOT_PASSWORD=root
  redis:
    image: redis:latest
  vueblog:
    image: vueblog:latest
    #点表示在当前文件下构建 因为vueblog在官方镜像没有对应，要从本地构建 
    build: .
    ports:
      - 8081:8081
    #构建vueblog依赖的服务
    depends_on:
      - mysql
      - redis        
```

#### 创建宿主主机挂载目录

创建nginx指定的volumes对应的文件路径,在root下创建nginx文件夹再创建html文件夹

```bash
# 创建 nginx文件夹
mkdir nginx
# 移动到nginx文件夹
cd nginx/
# 创建目录
mkdir html
# 创建nginx配置文件
touch nginx.conf
```

更改vueblog-vue项目axios.js配置文件

```javascript
 // 更改成对应的服务器的地址
axios.defaults.baseURL = "http://121.37.234.95:8081"
```

重新打包

``` shell
npm run build
```

将打包好的dist文件夹以zip文件形式上传到/root/nginx/html文件夹下，并解压才能保证nginx服务能正确寻找到css、js、img等文件

```shell
# 解压
unzip dist.zip
# 删除
rm -rf dist.zip
```

配置nginx.conf文件，将windows下的文件复制拷贝一份，修改一点内容

```shell
location / {
            # 更改根目录下 映射到docker的中的html
            root   /usr/share/nginx/html;
			try_files $uri $uri/ /index.html last;
            index  index.html index.htm;
        }
```

将修改好的conf文件，复制到/root/nginx/nginx.conf

```shell
vim nginx.conf
# 复制内容保存推出
```

打包上传需要进行容器编排依赖的文件

- 打包上传vueblog的jar包，存放在根目录下
- 上传Dockerfile，存在根目录下
- 上传docker-compose.yaml

并且注意这三个文件最好在同一个包下面，因为docker编排容器会基于Dockerfile和docker-compose.yaml构建服务

### 2021-03-27 docker编排服务

```shell
docker-compose up -d
```

然后需要在docker的mysql容器中创建对应的数据库才能保证访成功
常用的docker命令，**<CONTAINER_ID>**是一个变量名，代表容器ID，实际运行命令要将它替换成具体的容器ID

```shell
# 查看docker容器进程列表 -a表示all 列出所有（停止和启动，默认只显示启动的）
docker ps -a
# 进入docker容器
docker exec -it <CONTAINER_ID> /bin/bash
# 查看运行jar包实时日志 -f 实时输出  --tail表示要实时输出多少行
docker logs -f --tail=100 <CONTAINER_ID> 
# 退出docker返回linux主机 
exit
# 停止容器
docker stop <CONTAINER_ID>
# 启动容器
docker start  <CONTAINER_ID>
```