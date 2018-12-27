

Ubuntu安装最新版nodejs
```bash
sudo apt update -y
sudo apt install -y nodejs nodejs-legacy npm
sudo npm config set registry https://registry.npm.taobao.org
sudo npm install n -g
sudo n stable
```
 n是一个Node工具包，它提供了几个升级命令参数：
```bash
n                              显示已安装的Node版本
n latest                       安装最新版本Node
n stable                       安装最新稳定版Node
n lts                          安装最新长期维护版(lts)Node
n <version>                    根据提供的版本号安装Node
```