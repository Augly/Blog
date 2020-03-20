### 安装jenkins
系统centos7
```
wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
```
然后执行下面的命令
```
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
```
然后安装jenkins
```
yum install -y jenkins
```

### 安装Java sdk1.7
检查java版本
```
java -version
```
如果是1.7以下，删除自带版本

```
rpm -qa | grep java

```
然后删除那些名字中开头是java的那些
```
rpm -e --nodeps java-1.7.0-openjdk-headless-1.7.0.75-2.5.4.2.el7_0.x86_64
rpm -e --nodeps java-1.7.0-openjdk-1.7.0.75-2.5.4.2.el7_0.x86_64
```
安装java jdk1.8
```
yum list java-1.8*

```
选择一个安装
```
yum install java-1.8.0-openjdk* -y

```

### 启动jenkins服务
```
service jenkins start
```
然后输入域名8080端口就能进入Jenkins服务了

### 配置Jenkins服务

第一次进入的时候需要配置jenkins密码
```
在服务器找到密钥，输入进去，即可进入Jenkins
/var/log/jenkins/jenkins.log
```
### 配置Jenkins
第一次进入以后jenkins，选择新手推荐配置

首先，我们进入github -> setting -> developers -> Personal Access Token页面，生成一个有读写权限的用户。

![image](https://user-gold-cdn.xitu.io/2019/6/2/16b18bf344aeb4f4?imageslim)

选择这两个配置项后生成token，需要将该token记下来，因为后面是查看不到该token的，并且后续我们要使用到它。

然后在github上创建我们所需要部署的项目，进入settings页面设置webhooks。

![image](https://user-gold-cdn.xitu.io/2019/6/2/16b18c2589d6307d?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### 设置jenkins的github配置
> jenkins 安装Github Plugin插件,因为我们在配置jenkins的时候已经安装了所以这里可跳过， 如果没有 key 在jenkins 系统管理-->插件管理-->可选插件中安装。

> jenkins 新建项目，选择构建自由风格项目

![image](https://user-gold-cdn.xitu.io/2018/12/19/167c6a41270e2c12?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

![image](https://user-gold-cdn.xitu.io/2018/12/19/167c6a41270e2c12?imageslim)

> 完成之后的页面如下，后面我们做关于项目的配置设置都是基于这样页面👇👇👇

![image](https://user-gold-cdn.xitu.io/2018/12/19/167c6a639c845608?imageslim)

> jenkins 系统管理 --> 系统设置 --> GitHub --> Add GitHub Sever,设置一下jenkins关联 GitHub

![image](https://user-gold-cdn.xitu.io/2018/12/19/167c6aada71ad40f?imageslim)

![image](https://user-gold-cdn.xitu.io/2018/12/19/167c6ae0c090534d?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

> 按照如下图选择添加配置，这个时候就需要用到了我们第一步生成的 GitHub Personal Access Token，填写完毕确认添加

![image](https://user-gold-cdn.xitu.io/2018/12/19/167c6b1656858d21?imageslim)

> 选择生成的凭证，测试jenkins连接github服务器,如下图说明一切都配置 ok，最后记得在页面底部保存配置

![image](https://user-gold-cdn.xitu.io/2018/12/19/167c6bbb4b38f27c?imageslim)

#### 设置jenkins的项目 configure 配置
> General 配置
![image](https://user-gold-cdn.xitu.io/2018/12/19/167c6c20eb703fc6?imageslim)

> Build Triggers && Build Environment && Bindings 选择配置
![image](https://user-gold-cdn.xitu.io/2018/12/19/167c6e346fdb94f5?imageslim)

> Build 项目打包发布配置，添加执行 Shell，由于不同的前端项目技术架构和打包方式不一样，这里需要你根据的你项目，了解一下一些常用shell命令，这里的命令操作可以理解为在你的服务器环境中。这里我以打包 vue 项目为例，构建打包后代码文件解压到nginx的指定目录。

![image](https://user-gold-cdn.xitu.io/2018/12/19/167c6fa45311fc1e?imageslim)

```
echo $PATH
node -v
npm -v #检查编译环境
npm install chromedriver --chromedriver_cdnurl=http://cdn.npm.taobao.org/dist/chromedriver
npm install 
npm run build #编译项目
cd dist
tar -zcvf dist.tar.gz * #所有文件压缩
tar -zxvf /root/.jenkins/workspace/vue-online-admin/dist/dist.tar.gz -C /usr/share/nginx/html #压缩文件解压到nginx映射目录
cd /root/.jenkins/workspace/vue-online-admin 
rm -R dist #删除项目打包后的残留


```
![image](https://user-gold-cdn.xitu.io/2018/12/19/167c6fb240a22941?imageslim)


初次使用，文章略有不足，请多指导