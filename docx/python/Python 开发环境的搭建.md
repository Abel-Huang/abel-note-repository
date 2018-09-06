# Python 开发环境的搭建

Python 开发环境配置可以有以下的步骤：
* Python 安装
* Python 开发工具
* Python 虚拟环境
* Python 镜像源
* 具体的项目开发环境

##  Python 安装和配置
搭建 Python 开发环境第一步是需要安装Python，主要包括 Python 运行时环境，Python 基础库和 Python 解释器。在 MAC、Linux 和 Windows 环境下有所不同.下面就主要以 Mac下的安装为例进行说明。
Mac OS 系统目前内置了 Python2.7，如果需要 Python3 以前的版本需要手动安装，推荐使用 Mac下的包管理工具 brew 进行安装：  
`brew install python3`，  
brew 安装默认会安装最新版本的 Python3，也可以使用 brew 指定具体的版本，使用 brew 安装会自动添加环境变量，安装完毕可以使用 `python3 `进行 Python shell 交互式命令行，检查是否安装成功，查看具体的 Python 版本。
如果需要更好的进行 Python 版本的管理可以使用 pyenv 进行管理，类似于 Node.j s的 nvm，可以方便的在各个 Python 版本之间进行切换。  

## Python 开发工具选择
Python 开发本身是可以选择任何编辑器都可以进行，但是为了提升开发效率和质量，最好是使用 IDE 如 Pycharm 或者使用编辑器安装 Python 开发的相关插件如最近很热门的 vscode。  
个人更喜欢使用 Jetbrain 系列的 Python IDE ---  Pycharm，Jetbrain 提供了企业版和社区版，其中企业版提供了框架的支持以其他开发的高级功能，但是需要付费；社区版保留 了基本功能，是可以免费使用，并且可以通过插件进行功能扩展，对于一般的开发已经是足够了。
可以从 Jetbrain 的官网直接下载 Pycharm，根据安装向导直接安装即可，安装完后，配置一下解释器就可以进行开发了。  

## Python 的虚拟环境配置
Python 如此受欢迎其中一点就在于其丰富的第三方库，不过这也带来了一个版本依赖管理的问题：对于不同项目而言可能需要使用和依赖的第三方库版本并不相同，如果都使用同一个主环境会造成环境的污染，但是可以使用 Python 的虚拟环境解决这个问题，使用命令 virtualenv 就可以创建：
```
virtualenv  ${virtualenv_name}
```
其中 virtualenv_name  是虚拟环境名。
创建完之后可以通过 虚拟环境安装目录下的  activate (一般位于安装目录的 bin 文件夹下)  进入之前创建好的虚拟环境，


## 设置国内 pip 镜像源
pip (对于 Python3 而言应该使用  pip3) 是  Python 的包管理工具，可以非常方便的进行各种第三方包的管理 。由于默认的 pip pip 镜像源在国外，所以在没有科学上网的情况下经常会因为网络问题下载慢，导致安装第三方包失败的情况。可以通过使用国内代理进行加速，如豆瓣镜像地址：  
豆瓣镜像地址：https://pypi.douban.com/simple/  
使用命令为 ：  
`pip install -i https://pypi.douban.com/simple/   <python-package>`