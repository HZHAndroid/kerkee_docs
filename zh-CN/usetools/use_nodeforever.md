---
name: NodeJS工程化部署方案
---

# 背景概述
在前端技术快速发展的今天，前端项目越来越多基于NodeJS，如NodeOS、Ghost blog等。单项目的部署也比较简单，直接 `node xx.js`命令即可搞定，如果要让Node项目在后台运行，可以使用forever、nohup等工具实现。但又面临了几个问题，如何让node项目开机自启动；部署多个node项目时，如何工程化一键守护；如何让新增项目扩展灵活。带着这两个问题，今天介绍下如何实现NodeJS工程化部署方案。

# forever的安装
记得加-g，forever要求安装到全局环境下

```
sudo npm install forever -g
```
#forever的使用
网络上有很多使用教程，我这里收集并罗列常用的命令

```
// 简单的启动
forever start app.js

// 指定forever信息输出文件，当然，默认它会放到~/.forever/forever.log
forever start -l forever.log app.js

// 指定app.js中的日志信息和错误日志输出文件
//  -o 就是console.log输出的信息，-e 就是console.error输出的信息
forever start -o out.log -e err.log app.js

// 追加日志，forever默认是不能覆盖上次的启动日志，
//  所以如果第二次启动不加-a，则会不让运行
forever start -l forever.log -a app.js

// 监听当前文件夹下的所有文件改动
forever start -w app.js

// 显示所有运行的服务
forever list

// 停止所有运行的node App
forever stopall

// 停止其中一个node App
forever stop app.js

// 当然还可以这样
// forever list 找到对应的id，然后：
forever stop [id]

// 重启所有
forever restartall

// 开发和线上建议配置
// 开发环境下
NODE_ENV=development forever start -l forever.log -e err.log -a app.js
// 线上环境下
NODE_ENV=production forever start -l ~/.forever/forever.log -e ~/.forever/err.log -w -a app.js
```

# 工程化方案

## 创建守护脚本
创建sh目录,用来专门存放脚本的目录，当然，你也可以放到其他目录

```
mkdir ~/sh
```
编写一个启动脚本nodeforever.sh，放到 `~/sh` 目录下

```
cd ~/sh
vim nodeforever.sh
```
内容如下：

```
#!/bin/bash  
### BEGIN INIT INFO  
# Provides:          zihong  
# Required-Start:    $all  
# Required-Stop:     $all  
# Default-Start:     2 3 4 5  
# Default-Stop:      0 1 6  
# Short-Description: Start daemon at boot time  
# Description:       Enable service provided by daemon.  
### END INIT INFO  
# chkconfig: 345 88 08  
# description: Forever for Node.js  

export PATH=$PATH:/usr/local/bin  #在这里指定一下Node的可执行程序安装目录，我的是/usr/local/bin
export NODE_PATH=$NODE_PATH:/usr/local/lib/node_modules  #这里是Node类库的路径

#LOG=/data/log/nodeforever/node.log  #可选，日志文件目录
DIRPATH=/data/nodeforever //项目的启动文件都放在这个目录
cd $DIRPATH

#a=`find . -type l |xargs ls -l|awk -F '->' '{print $2}'`
#echo $a

node=node
forever=forever

filelist=`ls $DIRPATH`
for file in $filelist
do

DEAMON=$file   #这里需要填写你自己的Node项目的启动脚本文件
PID=$DIRPATH/.pid/$DEAMON.pid  #必填内容，用于记录forever的进程号  

echo -e "\nbegin >>>>>>>>> $DEAMON"
echo "node path:$NODE_PATH"
  
#node=node  
#forever=forever  
LOG=/data/log/nodeforever/$DEAMON.log  #可选，日志文件目录
  
case "$1" in  
    start)  
        NODE_ENV=production $forever start -l $LOG --pidFile $PID -a $DEAMON  
        ;;  
    stop)  
        $forever stop --pidFile $PID $DEAMON  
        ;;  
    stopall)  
        $forever stopall --pidFile $PID  
        ;;  
    restartall)  
        $forever restartall --pidFile $PID  
        ;;  
    reload|restart)  
        $forever restart -l $LOG --pidFile $PID -a $DEAMON  
        ;;  
    list)  
        $forever list
        exit 1
        ;;  
    *)  
        echo -e "\033[31m Usage: /etc.init.d/node {start|stop|restart|reload|stopall|restartall|list} \033[0m"
        exit 1  
        ;;  
esac

done
exit 0

```

保存，并给nodeforever.sh设置权限

```
chmod 755 nodeforever.sh
```

创建软链接到init.d目录

```
ln -s ~/sh/nodeforever.sh /etc/rc.d/init.d/nodeforever
```
添加脚本

```
chkconfig --add nodeforever
```

系统启动时自启动

```
chkconfig --level 345 nodeforever on
```
编辑rc.local文件

```
vim /etc/rc.d/rc.local
```
在rc.local中也添加个启动命令

```
service nodeforever start
```
至此，守护脚本已创建完毕

## 项目部署
项目部署比较简单，所有Node项目的xx.js文件都软链接到nodeforever.sh中指定的`/data/nodeforever/`目录下集中管理即可。完成后，若要立即生效，可以重启nodeforever服务，或者重启服务器

```
service nodeforever restart
```

当有新增Node项目时，只要把项目的xx.js软链接到`/data/nodeforever/`下即可，至于软链接过来的文件名称随便起，只要在该目录下不重名即可



