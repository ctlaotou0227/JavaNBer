#####Docker 是一个c/s结构，docker守护进程运行在主机上，然后通过socket连接从客户端访问，守护进程从客户端接受命令。

|            | docker             | 虚拟机                  |
| ---------- | ------------------ | ----------------------- |
| 操作系统   | 与宿主共享os       | 宿主os运行虚拟机os      |
| 存储大小   | 镜像小，便于存储   | 镜像庞大（vmdk，vdi等） |
| 运行性能   | 几乎无额外性能损失 | os额外的cpu，内存消耗   |
| 移植性     | 轻便灵活           | 笨重                    |
| 硬件亲和性 | 面相软件开发者     | 面向硬件开发者          |
| 部署速度   | 秒级               | 分钟级                  |

  ##### docker命令

1. docker version：查看版本
2. docker info：信息
3. docker --help：类似于Linux的man，帮助命令

##### 镜像命令

1. docker images：列出本地主机上的镜像 
   1. -a：列出本地所有的镜像
   2. -q：只显示镜像id
   3. --digests：显示镜像的摘要信息
   4. --no-trunc：显示完整的镜像信息
   5. tag：镜像的标签
   6. image id：镜像id
   7. 同一仓库源可以有多个tag
2. docker search 某个xxx镜像名字：去docker上面找，从阿里云下载
   1. docker search -s 数字（点赞数）镜像名字：docker search -s  30 tomcat就是找到tomcat镜像文件点赞数超过30的
3. docker pull 某个镜像的名字：下载镜像
   1. dockerpull tomcat 等价于 docker tomcat：latest
4. docker rmi 某个镜像名字id ：删除某个镜像
   1. 删除单个：docker rmi -f 镜像id
   2. 删除多个：docker rmi -f镜像名字1 镜像名字2
   3. 删除全部：docker rmi -f $（docker images -qa）

**容器命令**

1. 有镜像才可以创建容器
   1. -i：以交互模式运行容器，通常与-t同时使用；
   2. -t：为容器重新分配一个伪输入终端，通常与-i同时使用；
   3. -l：显示最近创建的容器
   4. -q：静默模式，只显示容器编号
   5. docker run image：新建并启动容器
   6. docker ps：列出当前正在运行的容器
   7. exit：容器停止退出
   8. crlt+p+q容器不停止退出
   9. docker start 容器id或者容器名：启动容器
   10. docker stop容器id或者容器名：停止容器
   11. 强制停止容器：docker kill 容器id或者容器名
   12. -p：主机端口：docker容器端口 例子：docker run -it -p 8888：8080 tomcat
   13. -P：随机分配端口

##### 重要的容器命令

1. 启动守护式容器：docker run -d 容器名
2. 查看容器日志：docker logs -f -t --tail 容器id
   1. -t是加入时间戳
   2. -f跟随最新的日志打印
   3. --tail数字显示最后多少条
3. 查看容器内部的细节：docker inspect 容器id
4. 从宿主机进入容器：docker  attach 容器id
   1. attach：直接进入容器启动命令的终端，不会启动新的进程
   2. exec：是在容器中打开新的终端，并且可以启动新的进程

##### docker的logo：鲸鱼背上有集装箱

| 蓝色的大海里面 | 宿主机系统                     |
| -------------- | ------------------------------ |
| **鲸鱼**       | **docker**                     |
| **集装箱**     | **容器实例from我们的镜像模板** |

##### 镜像是层层叠加的

##### docker commit 提交容器副本使之成为一个新的镜像

##### docker commit -m “提交的秒数信息” -a=“作者” ：容器id要创建的目标镜像名：【标签名】



