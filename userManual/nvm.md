# nvm
## 介绍  
它是管理node版本的工具，一个电脑中可以安装多个node版本，当我们想使用哪个版本就切换成哪个版本，而nvm则是提供切换node版本的工具。
## 下载  
[NVM](https://github.com/coreybutler/nvm-windows/releases)  
## 使用命令
1. nvm list [available]  查看本地安装的node所有版本；可选参数available，显示所有可下载的版本
2. nvm install 12.3.0  安装的，指令中的版本号是可选参数，版本号可根据(1)查询出来
3. nvm use + 版本号  使用指定版本node  
4. nvm uninstall + 版本号 卸载指定版本node

## 注意事项  

1.设置全局包的位置  
   npm config set prefix D:\software\nvm\v10.23.1\node_global  
   npm config set cache D:\software\nvm\v10.23.1\node_cache  
2. Npm包全局命令失效：  
  设置环境变量path，加D:/software/nvm/v16.14.0/node_global

