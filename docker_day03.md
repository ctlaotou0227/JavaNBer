##### docker容器数据卷

#####docker源镜像：scratch类似于java里面的object类

| 是什么     | 有点类似Redis里面的rdb和aof文件                              |
| ---------- | ------------------------------------------------------------ |
| 能干嘛     | 容器的持久化，容器间继承+共享数据                            |
| 数据卷     | 容器内添加---直接命令  或者 添加+dockerFile添加  数据持久化的操作 |
| 数据卷容器 |                                                              |

命令：docker run -it -v /宿主机绝对路径目录：/容器内目录  镜像名

##### dockerFile：是用来构建docker镜像的构建文件，是由一系列命令和参数构成的脚本。

1. javaEE Hello.java-------->Hello.class
2. Docker Images----------->DockerFile    
3. 容器间传递共享（volumes-from）
4. 容器之间配置信息的传递,数据卷的生命周期一直持续到没有容器使用它为止

##### dockerFile解析

dockerFile写容器卷的主流步骤

1. 手动编写一个dockerFile文件，必须符合file的规范
2. 有这个文件之后，直接dockerbuild命令执行，获得一个自定义的镜像
3. run

##### dockerFile内容基础知识：

1. 每条**保留字指令**都必须为大写字母且后面要跟随至少一个参数
2. 指令按照从上到下，顺序执行
3. ‘#’表示注释
4. 每条指令都会创建一个新的镜像层，并对镜像进行提交

##### 从应用软件的角度老看，dockerFile，docker镜像与docker容器分别代表软件的三个不同阶段

1. dockerFile是软件的原材料
2. docker镜像是软件的交付品
3. docker容器则可以认为是软件的运行态

##### docker面向开发，docker镜像成为交付标准，docker容器则设计部署与运维，三者缺一不可，合力充当docker体系的基石。

#####保留字指令 				

| ONBUILD    | 当构建一个被继承的dockerfile时运行命令，父镜像在被子继承后父镜像的onbuild被触发 |
| ---------- | ------------------------------------------------------------ |
| FROM       | 基础镜像，当前新镜像是基于哪个镜像的                         |
| MAINTAINER | 镜像维护者的姓名和邮箱地址                                   |
| RUN        | 镜像构建时需要运行的命令                                     |
| EXPOSE     | 当前容器对外暴露出的端口                                     |
| WORKDIR    | 指定在创建容器后，终端默认登录的进来工作目录，一个落脚点     |
| ENV        | 用来在构建镜像过程中设置环境变量                             |
| ADD        | 将宿主机目录下的文件拷贝进镜像且add命令会自动处理url和解压tar压缩包 |
| COPY       | 类似add，拷贝文件和目录到镜像中。                            |
| VOLUME     | 容器数据卷，用于数据保存和持久化工作                         |
| CMD        | 指定一个容器启动时要运行的命令---可以有多个但是只有最后一个生效，cmd会背docker run之后的参数替换 |
| ENTRYPOINT | 指定一个容器启动时要运行的命令                               |

##### 自定义镜像

1. 编写
2. 构建
   1. docker build -t 新镜像名字：tag
   2. docker run -it 新镜像名字：tag
3. 列出镜像的变更历史：docker history 镜像名/镜像id