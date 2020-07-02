# opengauss-docker
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# 快速参考
- **维护者**：
[blueapple OpenSource Team](https://github.com/blueapple168)
- **哪里可以获取帮助**：
[墨天轮-openGauss](https://www.modb.pro/openGauss)

# 支持的tags和 `Dockerfile`链接
-	[`1.0.0`, `latest`](https://github.com/blueapple168/opengauss-docker/blob/master/1.0.0/dockerfile)


# 关于openGauss
openGauss是一款开源关系型数据库管理系统，采用木兰宽松许可证v2发行。openGauss内核早期源自PostgreSQL，深度融合华为在数据库领域多年的经验，结合企业级场景需求，持续构建竞争力特性。同时openGauss也是一个开源、免费的数据库平台，鼓励社区贡献、合作。

openGauss社区官方网站：[https://opengauss.org/](https://opengauss.org/)

![logo](https://github.com/blueapple168/opengauss-docker/blob/master/opengauss-text.png)

# blueapple openGuass镜像的特点
* 紧跟openGauss的源码变化，第一时间发布镜像的新版本。
* 云端数据库，虚拟机数据库以及容器版本数据库均会使用同样的初始化最佳实践配置，这样当您在应对各种不同需求时会有几乎相同的体验。
* 会持续发布不同CPU架构（x86或者ARM）之上，不同操作系统的各种镜像

**目前已经支持x86-64架构。**
- x86-64架构的openGuass运行在[CentOS7.6操作系统](https://www.centos.org/)中

# 如何使用本镜像

## 启动openGuass实例

```console
$ docker run --name opengauss --privileged=true -d -e GS_PASSWORD=secretpassword@123 blueapple/opengauss:latest
```

## 环境变量
为了更灵活的使用openGuass镜像，可以设置额外的参数。未来我们会扩充更多的可控制参数，当前版本支持以下变量的设定。

### `GS_PASSWORD`
在你使用openGauss镜像的时候，必须设置该参数。该参数值不能为空或者不定义。该参数设置了openGauss数据库的超级用户omm以及测试用户gaussdb的密码。openGauss安装时默认会创建omm超级用户，该用户名暂时无法修改。测试用户gaussdb是在[docker-entrypoint.sh](https://github.com/blueapple168/opengauss-docker/blob/master/1.0.0/docker-entrypoint.sh)中自定义创建的用户。

openGauss镜像配置了本地信任机制，因此在容器内连接数据库无需密码，但是如果要从容器外部（其它主机或者其它容器）连接则必须要输入密码。

**openGauss的密码有复杂度要求，需要：密码长度8个字符以上，必须同时包含英文字母，数字，以及特殊符号**


## 从容器外部连接容器数据库
openGauss的默认监听启动在容器内的5432端口上，如果想要从容器外部访问数据库，则需要在`docker run`的时候指定`-p`参数。比如以下命令将允许使用54321端口访问容器数据库。
```console
$ docker run --name opengauss --privileged=true -d -e GS_PASSWORD=secretpassword@123 -p 54321:5432 blueapple/opengauss:latest
```
在上述命令正常启动容器数据库之后，可以通过外部的gsql进行数据库访问。
```console
$ gsql -d postgres -U gaussdb -W'secretpassword@123' -h your-host-ip -p8888
```


## 持久化存储数据
容器一旦被删除，容器内的所有数据和配置也均会丢失，而从镜像重新运行一个容器的话，则所有数据又都是呈现在初始化状态，因此对于数据库容器来说，为了防止因为容器的消亡或者损坏导致的数据丢失，需要进行持久化存储数据的操作。通过在`docker run`的时候指定`-v`参数来实现。比如以下命令将会指定将openGauss的所有数据文件存储在宿主机的/blueapple/opengauss下。

```console
$  docker run --name opengauss --privileged=true -d -e GS_PASSWORD=secretpassword@123 \
    -v /blueapple/opengauss:/var/lib/opengauss \
    blueapple/opengauss:latest
```
