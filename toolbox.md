#### toolbox运行cwm
##### windows server 2016 说明
docker官方有为windows server 2016提供docker企业版。 但是需要付费。 所以只能使用toolbox
1. 安装toolbox, https://get.daocloud.io/toolbox/ 里下载最新的toolbox
2. 安装完成后，点击 Docker Quickstart Terminal, 直到正常出现docker的鲸鱼图案
3. 打开 Oracle VM VirtuaBox ,此时可以看到运行中的default，进入设置选项，选择网络， 将网卡1的连接方式设置成桥接， 点击ok, 重启default
4. 待default重新运行成功， 右键点击显示，可看到一个linux系统的窗口。在里面输入 ``` ip addr show ``` ,记录下与宿主机相同的ip段
5. 由于虚拟机里操作十分艰难，我们选择用ssh连接到这个linux虚拟机。 选择合适的ssh工具， 执行 ssh docker@<上一步记录的ip>， 密码为 tcuser
6. 连接成功后，发现docker命令是可以正常执行的。 但是docker-compose却无法执行。
7. 执行如下命令安装docker-compose
```
curl -L https://github.com/docker/compose/releases/download/1.11.2/docker-compose-`uname -s`-`uname -m` > ~/docker-compose
chmod +x ~/docker-compose
sudo mv ~/docker-compose /usr/local/bin/docker-compose
```
8. 将cwm解压到宿主机的 C:\User目录下。 此目录是默认共享到虚拟机到目录， 你也可以在Vbox中修改共享目录
9. 在ssh工具里， 在根目录下就能看到有c的目录，进入目录。找到cwm所在位置。 执行 sh init.sh
10. 在浏览器 https://<ip>:30001就能访问了
