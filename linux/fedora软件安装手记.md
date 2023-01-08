[首页](http://events.jianshu.io/)[下载APP](http://events.jianshu.io/apps?utm_medium=desktop&utm_source=navbar-apps)[会员](http://events.jianshu.io/vips)[IT技术](http://events.jianshu.io/techareas)

# fedora软件安装手记

[![img](https://cdn2.jianshu.io/assets/default_avatar/2-9636b13945b9ccf345bc98d0d81074eb.jpg)布谷李](http://events.jianshu.io/u/06e4c23ca1f9)

# fedora软件安装手记

[![img](https://cdn2.jianshu.io/assets/default_avatar/2-9636b13945b9ccf345bc98d0d81074eb.jpg)](http://events.jianshu.io/u/06e4c23ca1f9)

[布谷李](http://events.jianshu.io/u/06e4c23ca1f9)

IP属地: 北京

0.5772018.07.20 16:44:15字数 3,440阅读 683

#### 1. 用户管理

1). 切换用户
 **方式一**：命令su然后输入root密码
 此种方式只是切换了root身份，但Shell环境仍是普通用户的Shell，pwd命令一下，目录仍然是普通用户的工作目录。
 **方式二**：命令：su -然后输入root密码
 此种方式，是连用户和Shell环境一起切换成root身份。 工作目录变成root的工作目录，pwd命令一下，即可看到。
 只有切换用户，才不会出现PATH环境变量错误。可以用命令 echo $PATH  看一下su、su - 后的环境变量有何不同。

2). 重置root密码操作
 通过sudo命令获取权限

```ruby
$ sudo su
输入当前用户的密码
```

通过password更改，重置root密码

```bash
# passwd root
根据提示输入新密码.
```

3). 修改普通用户用户名
 先查看该用户的id和所属组相关信息：

```objectivec
$ id [user-name]
uid=1000(brian) gid=1000(kaifa) groups=1000(kaifa),10(wheel)
```

查看当前用户所属用户组：

```ruby
$ groups
kaifa wheel
```

首先切换到root用户下，注意要使用`su -`

```ruby
$ su - 或 su - root
```

修改用户名(当前修改的普通用户不能在登录状态)：

```bash
# usermod -l [新用户名] -d /home/[新用户名] -m [老用户名] 
# usermod -l brian -d /home/brian -m kaifa
```

修改用户组名：

```ruby
# groupmod -n [新用户组名] [老用户组名]
# groupmod -n brian kaifa
$ groups
brian wheel
```

4). 启用sudo
 sudo命令可以临时提升权限，不用每次都输入sudo密码，将**[your_user_name]**加入了sudo组的方法如下：

- 安装sudo

```bash
# dnf install sudo
```

- 查看/修改sudoers内容

```bash
# cat /etc/sudoers
# vi /etc/sudoers
```

在sudoers文件中找到一行root ALL=(ALL)   ALL的下一行，按i，进入插入模式，输入
 **[your_user_name]** ALL=(ALL)   ALL
 然后按Esc，输入“：”再输入:wq，保存退出。

- 也可以在root用户下使用visudo命令

```undefined
visudo 
```

- 添加目录权限

```undefined
sudo chown -R [user_name]:[user_group_name] /work 
```

#### 2. 配置软件安装源

##### 启用fastestmirror

fastestmirror是yum的一个插件，让系统自动选择最快的镜像：

```undefined
yum install yum-fastestmirror  -y 
```

YUM插件的配置目录在：/etc/yum/pluginconf.d/
 查看fastestmirror配置：

```ruby
$ cat /etc/yum/pluginconf.d/fastestmirror.conf 
```

从fedora22开始，YUM逐步更改为DNF，而DNF已经内置了fastestmirror功能，该功能默认是关闭的。要打开该功能，需要修改`/etc/dnf/dnf.conf`配置文件，在[main]部分里面加入一行

```bash
fastestmirror=true
```

即可生效。

##### 添加国内yum源

1. 启用阿里云软件源
    在fedora22之前，一般是下载新的fedora.repo和fedora-updates.repo 到/etc/yum.repos.d/

```cpp
wget -O /etc/yum.repos.d/fedora-aliyun.repo http://mirrors.aliyun.com/repo/fedora.repo

wget -O /etc/yum.repos.d/fedora-updates-aliyun.repo http://mirrors.aliyun.com/repo/fedora-updates.repo
```

在fedora22之后，由于采用了DNF管理软件依赖，可以采用：

```csharp
sudo dnf config-manager –add-repo=http://mirrors.aliyun.com/repo/fedora.repo
```

Note: 国内其他源地址(具体选择哪个源，可以使用ping检查一下速度)。

```bash
网易：
http://mirrors.163.com/.help/fedora-163.repo
http://mirrors.163.com/.help/fedora-updates-163.repo
搜狐：
http://mirrors.sohu.com/help/fedora-sohu.repo
http://mirrors.sohu.com/help/fedora-updates-sohu.repo
```

1. 启用 FZUG 源
    FZUG 源由 [Fedora 中文社区(FZUG)](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.fdzh.org%2F)/[Fedora 中文用户组网站](https://links.jianshu.com/go?to=https%3A%2F%2Fzh.fedoracommunity.org%2F)维护，有大量面向国内用户的常用软件，如网易云音乐，有道词典，chromium 浏览器等。

```csharp
sudo dnf config-manager --add-repo=http://repo.fdzh.org/FZUG/FZUG.repo
```

[FZUG官方wiki](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FFZUG%2Frepo%2Fwiki%2F%E6%B7%BB%E5%8A%A0-FZUG-%E6%BA%90)的rpm安装方法：
 Fedora 27安装方法：

```cpp
# dnf install https://repo.fdzh.org/FZUG/free/27/x86_64/noarch/fzug-release-27-0.2.noarch.rpm
```

截至2018年7月23日，FZUG尚未支持Fedora28(可访问[fzug-repo](https://links.jianshu.com/go?to=https%3A%2F%2Frepo.fdzh.org%2FFZUG%2Ffree%2F)进行验证)。

查看FZUG软件包列表：

```cpp
$ dnf list --disablerepo=* --enablerepo=fzug-free --enablerepo=fzug-nonfree | grep -i fzug
```

3）启用RPM Fusion仓库
 RPM Fusion是一个流行的仓库，它主要是系列依赖于非自由软件包的闭源软件集合，它也包括了尚未收录进入Fedora官方仓库的软件。
 Fedora 21启用RPM Fusion仓库：

```ruby
    $ sudo yum localinstall --nogpgcheck http://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-21.noarch.rpm
    $ sudo yum localinstall --nogpgcheck http://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-21.noarch.rpm
```

Fedora 25启用RPM Fusion仓库：

```shell
# RPM Fusion Free 源
sudo dnf install http://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-25.noarch.rpm
# RPM Fusion Nonfree 源
sudo dnf install http://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-25.noarch.rpm
```

Fedora 28启用RPM Fusion仓库：

```shell
# RPM Fusion Free 源
sudo dnf install http://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-28.noarch.rpm
# RPM Fusion Nonfree 源
sudo dnf install http://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-28.noarch.rpm
```

4）生成缓存

```ruby
$ sudo dnf clean all
$ sudo dnf makecache
```

clean all命令参数是指让DNF清除全部软件源信息以及下载的软件包缓存信息.
 makecache命令参数是指让DNF重新获取并建立软件源元数据信息缓存。

1. 查看repo列表
    方法1： 查看*.repo文件

```bash
$ls -l /etc/yum.repos.d/
```

方法2： 使用dnf命令

```cpp
$ dnf repolist

仓库标识                    仓库名称                                      状态
code                        Visual Studio Code                                39
fedora                      Fedora 28 - x86_64 - **aliyun**                   57,327
*rpmfusion-free             RPM Fusion for Fedora 28 - Free                  563
*rpmfusion-free-updates     RPM Fusion for Fedora 28 - Free - Updates        237
*rpmfusion-nonfree          RPM Fusion for Fedora 28 - Nonfree               203
*rpmfusion-nonfree-updates  RPM Fusion for Fedora 28 - Nonfree - Updates      64
```

##### 安装yum优先级插件priority

Yum Priorities 插件可以用来强制保护源。它通过给各个源设定不同的优先级，使得系统管理员可以将某些源（比如 Linux  发行版的官方源）设定为最高优先级，从而保证系统的稳定性（同时也可能无法更新到其它源上提供的软件最新版本）.  一般是默认先从官方base或者镜像安装，然后从社区用户contribute的软件中安装，再从第三方软件仓库中安装。

```bash
#yum install yum-priorities 
```

用法：

- 检查配置文件
   确保配置文件中包含下面的行  enabled=1

```undefined
vi /etc/yum/pluginconf.d/priorities.conf
```

- 编辑repo文件,设置优先级
   编辑 /etc/yum.repos.d/ 目录中后缀为.repo的文件来设置软件仓库的优先级， priority=N(N是整数，范围从1-99，数字越小代表优先级越高) .
   官方推荐配置是：[base], [addons], [updates], [extras] 这几部分 priority=1 , [centosplus],[contrib] 这几部分priority=2

##### 升级系统：

```ruby
$ sudo yum update
或者
$ sudo dnf update -y
```

#### 3. 安装必备软件

#### 安装git

```ruby
#sudo dnf -y install git
$ git --version
git version 2.14.5
```

#### 安装wget

```bash
#yum install wget
```

详见[wget学习资料](https://links.jianshu.com/go?to=http%3A%2F%2Fwww.cnblogs.com%2Fpeida%2Farchive%2F2013%2F03%2F18%2F2965369.html). Fedora 28已内置wget。

#### 安装zsh

```php
$ echo $SHELL  //查看系统当前使用的shell
/bin/bash
$ cat /etc/shells  //查看系统是否安装了zsh
$ sudo dnf -y install zsh  //安装zsh
$ chsh -s /bin/zsh  //将当前用户的shell切换为zsh
重启服务器后，可使用
$ reboot //重启服务器
$ echo $SHELL  //再次查看系统当前使用的shell
```

安装oh my zsh

```cpp
//在当期目录安装oh my zsh
sudo wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
$ ls -l /home/brian/.oh-my-zsh/
```

配置oh my zsh主题
 查看[主题列表](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Frobbyrussell%2Foh-my-zsh%2Fwiki%2Fthemes)<找到自己喜欢的菜>：默认主题是robbyrussell，似乎 ys 主题最受大家欢迎

```cpp
$ vim ~/.zshrc  //修改主题
ZSH_THEME="ys"  //将ZSH_THEME改成ys
$ source ~/.zshrc  //更新配置
```

配置oh my zsh自动完成插件

```cpp
//下载[Incremental completion on zsh](http://mimosa-pudica.net/zsh-incremental.html)
到oh-my-zsh插件库指定目录
$ wget http://mimosa-pudica.net/src/incr-0.2.zsh -P ~/.oh-my-zsh/plugins/incr 
$ vim ~/.zshrc  //修改配置，在~/.zshrc文件末尾加上
source ~/.oh-my-zsh/plugins/incr/incr*.zsh
$ source ~/.zshrc   //更新配置
```

#### 安装WPS

libreoffice 基本不可用，彻底删除 libreoffice：

```csharp
$ sudo dnf remove libreoffice*
```

Fedora安装wps，一般使用如下命令.

```ruby
$ sudo dnf install wps-office 
```

提示没有找到软件，原因是FZUG中没有对应当前系统版本的源.
 从[WPS社区](https://links.jianshu.com/go?to=http%3A%2F%2Fcommunity.wps.cn%2Fdownload%2F)找到路径，使用rpm为Fedora 28安装WPS For Linux 6634（2018-06-05）：

```ruby
$ dnf install http://kdl.cc.ksosoft.com/wps-community/download/6634/wps-office-10.1.0.6634-1.x86_64.rpm
```

打开WPS，提示"系统缺失字体：symbol, windings, mt extra等",这些字体是WPS for Linux公式显示需要的Symbol字体。
 具体操作步骤如下：

1). 下载缺失的字体文件，然后复制到Linux系统中的/usr/share/fonts文件夹中。
 国外下载地址：[https://www.dropbox.com/s/lfy4hvq95ilwyw5/wps_symbol_fonts.zip](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.dropbox.com%2Fs%2Flfy4hvq95ilwyw5%2Fwps_symbol_fonts.zip)
 国内下载地址：[https://pan.baidu.com/s/1eS6xIzo](https://links.jianshu.com/go?to=https%3A%2F%2Fpan.baidu.com%2Fs%2F1eS6xIzo)
 下载完成后，解压并进入目录中，继续执行：

```jsx
sudo cp * /usr/share/fonts
```

字体文件包括： mtextra.ttf  WEBDINGS.TTF  WINGDNG2.ttf
 symbol.ttf   wingding.ttf  WINGDNG3.ttf

2). 执行以下命令,生成字体的索引信息：

```bash
cd /usr/share/fonts
sudo mkfontscale
sudo mkfontdir
```

3). 运行fc-cache命令更新字体缓存。

```bash
sudo fc-cache
```

4). 重启wps即可，字体缺失的提示不再出现。

#### 安装搜狗输入法

需要先添加Fedora 中文社区软件源 [http://repo.fdzh.org/](https://links.jianshu.com/go?to=http%3A%2F%2Frepo.fdzh.org%2F),下载地址： [http://repo.fdzh.org/FZUG/FZUG.repo](https://links.jianshu.com/go?to=http%3A%2F%2Frepo.fdzh.org%2FFZUG%2FFZUG.repo)

```bash
#mv FZUG.repo /etc/yum.repos.d 
#yum makecache 
#yum install sogoupinyin 
```

fedora 20 安装fcitx + 搜狗拼音输入法，开机出现unable to keep input method running 解决

# mate-session-properties

把里面的fcitx 和 sougou 都关掉，注销/重启
 fcitx -- Input Method Starter

#### 安装Chrome

```csharp
//Fedora 28/27
dnf install fedora-workstation-repositories
dnf config-manager --set-enabled google-chrome

//Fedora 26/25 CentOS/RHEL 7.5
cat << EOF > /etc/yum.repos.d/google-chrome.repo
[google-chrome]
name=google-chrome
baseurl=http://dl.google.com/linux/chrome/rpm/stable/x86_64
enabled=1
gpgcheck=1
gpgkey=https://dl.google.com/linux/linux_signing_key.pub
EOF
```

安装chrome

```cpp
// Fedora 28/27/26/25 
dnf install google-chrome-stable
// CentOS/RHEL 7.5 
yum install google-chrome-stable
```

#### Fedora修改默认语言

修改/etc/locale.conf文件，将LANG="zh_CN.UTF-8" 改成LANG="en_US.UTF-8"，中文改成英文
 重新加载一下 /etc/locale.conf 文件：

```ruby
$ source /etc/locale.conf
或
$ . /etc/locale.conf
```

重启

也可以直接使用命令localectl再重启：

```bash
$ sudo localectl set-locale LANG=en_US.UTF-8
$ sudo localectl set-locale LANG=zh_CN.UTF-8 
```

查看Fedora版本：

```ruby
$ cat /etc/redhat-release
$ uname -a
```

配置随处右键打开终端 - 即将右键菜单添加'Open in Terminal '

```ruby
$ sudo dnf install gnome-terminal-nautilus
$ nautilus -q
```

#### 4. 安装必备软件

#### 安装unrar解压工具

```ruby
$ sudo dnf install unrar
```

#### 安装rar压缩和解压软件

```cpp
//访问网址： https://www.rarlab.com/download.htm
# wget https://www.rarlab.com/rar/rarlinux-x64-5.7.0.tar.gz 
# tar zxvf rarlinux-x64-5.7.0.tar.gz  //解压
# cd rar
# sudo make //编译
# sudo make install  //安装
mkdir -p /usr/local/bin
mkdir -p /usr/local/lib
cp rar unrar /usr/local/bin
cp rarfiles.lst /etc
cp default.sfx /usr/local/lib
# rar //验证是否安装成功 
```

##### VLC Media Player

该播放器是在RPMFusion repository中，安装方法如下：

```cpp
$ sudo dnf install https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm  //添加RPM repo
# ls /etc/yum.repos.d/ |grep rpm //查看repo是否添加成功
rpmfusion-free.repo
rpmfusion-free-updates.repo
rpmfusion-free-updates-testing.repo
//安装VLC Media Player
$ sudo dnf install vlc
//卸载系统自带播放器
$ sudo dnf remove exaile parole 
```

##### 安装Teamviewer

访问官网[https://www.teamviewer.com/en/download/linux/](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.teamviewer.com%2Fen%2Fdownload%2Flinux%2F)，下载Red Hat, CentOS, Fedora系安装文件，注意选择32/64位不同版本：

```dart
$ wget https://download.teamviewer.com/download/linux/teamviewer.x86_64.rpm
$ sudo yum install teamviewer.x86_64.rpm //使用yum可以自动安装必要的依赖
============================================================================================
 Package                  Arch        Version                       Repository         Size
============================================================================================
Installing:
 teamviewer               x86_64      13.1.8286-0                   @commandline       12 M
Installing dependencies:
 qt5-qtlocation           x86_64      5.10.1-3.fc28                 fedora            2.7 M
 qt5-qtquickcontrols      x86_64      5.10.1-1.fc28                 fedora            1.1 M
 qt5-qtsensors            x86_64      5.10.1-1.fc28                 fedora            217 k
 qt5-qtwebchannel         x86_64      5.10.1-1.fc28                 fedora             84 k
 qt5-qtwebkit             x86_64      5.212.0-0.20.alpha2.fc28      fedora             13 M
```

安装后，打开TeamViewer，接受授权信息，刷新即可看到ID信息，也可以在命令行查看：

```ruby
$ teamviewer info
 TeamViewer                           13.1.8286  (RPM) 
 TeamViewer ID:                        1239483421 
 teamviewerd status                   ● teamviewerd.service - TeamViewer remote control daemon
   Loaded: loaded (/etc/systemd/system/teamviewerd.service; enabled; vendor preset: disabled)
   Active: active (running) since Tue 2018-07-31 15:29:42 CST; 3min 35s ago
  Process: 9466 ExecStart=/opt/teamviewer/tv_bin/teamviewerd -d (code=exited, status=0/SUCCESS)
 Main PID: 9468 (teamviewerd)
    Tasks: 53 (limit: 6143)
   Memory: 11.3M
   CGroup: /system.slice/teamviewerd.service
           └─9468 /opt/teamviewer/tv_bin/teamviewerd -d
```

##### 安装SS代理

参考[Fedora下 ShadoWsocks（VPN）配置全局可用](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.southcity-oldboy.com%2F1697.html)
 安装shadowsocks

```css
sudo dnf install shadowsocks-qt5.x86_64
```

#### 5. 安装开发工具

##### 安装typora

- 下载Typora压缩包。

地址：https://www.typora.io/#linux

- 文件解压。

```shell
tar -zxvf Typora-linux-x64.tar.gz 
```

- 将解压后的文件移动到/opt目录下。

```shell
mv ~/Downloads/bin/Typora-linux-x64 /opt
```

- 此时直接执行Typora目录下的可执行文件就可以开始使用Typora了。执行命令：

```shell
/opt/Typora-linux-64/Typora
```

完成上面的步骤后Typora就可以使用了，但是每次都要从命令行打开，比较麻烦，下面开始添加快捷方式。

添加快捷方式前，我们需要两个路径：

- 软件启动脚本路径：`/opt/Typora-linux-x64/Typora`
- 软件图标路径：`/opt/Typora-linux-x64/resources/app/asserts/icon/icon_256x256.png`

新建*.desktop文件并配置

```shell
vim /usr/share/applications/Typora.desktop
[Desktop Entry]
Encoding=UTF-8
Name=Typora
Exec=/opt/Typora-linux-x64/Typora
Terminal=false
Type=Application
Icon=/opt/Typora-linux-x64/resources/app/asserts/icon/icon_256x256.png
StartupNotify=true
Categories=Development
```

##### 安装Git

##### 安装meld

[meld](https://links.jianshu.com/go?to=http%3A%2F%2Fmeldmerge.org%2F)是一款可视化的文件及目录比较工具.

```undefined
sudo yum -y install meld
==================================================================
 Package        Arch             Version                 Repository        Size
==================================================================
Installing:
 meld           noarch           3.18.0-1.fc27           fedora           774 k
```

##### 安装VNote 笔记软件

下载软件： [https://github.com/tamlok/vnote](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Ftamlok%2Fvnote)
 Fedora发行版考虑下载[VNote-2.6-x86_64.AppImage版本](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Ftamlok%2Fvnote%2Freleases%2Fdownload%2Fv2.6%2FVNote-2.6-x86_64.AppImage)
 根据[fedora官网](https://links.jianshu.com/go?to=https%3A%2F%2Ffedoraproject.org%2Fwiki%2FAppImage)介绍，AppImage是一种跨平台打包格式，直接可以运行在linux上。

```cpp
$ cd /work/soft
$ chmod +x VNote-2.6-x86_64.AppImage  //添加可执行权限
$ ./VNote-2.6-x86_64.AppImage  //在命令行运行程序
$ wget https://raw.githubusercontent.com/tamlok/vnote/master/screenshots/vnote.png
```

添加到菜单中

```ruby
$ sudo vi /usr/share/applications/vnote.desktop
```

编辑文件：

```jsx
[Desktop Entry]
Encoding=UTF-8
Comment=A note-taking applicatio.
Comment[zh_CN]=VNOTE笔记
Name=VNote
Exec=/work/soft/VNote-2.6-x86_64.AppImage
Icon=/work/soft/vnote.png
Terminal=false
Type=Application
Categories=Development;
```

此时，在applications-> programming就可以找到VNote了。

##### 安装Docker

添加docker-repo

```ruby
$ sudo vim /etc/yum.repos.d/docker-ce.repo
```

docker-ce.repo内容如下：

```ruby
[docker-ce-stable]
name=Docker CE Stable for fedora 27
baseurl=https://download.docker.com/linux/fedora/27/x86_64/stable
enabled=1
gpgcheck=1
gpgkey=https://download.docker.com/linux/fedora/gpg
```

查看repo是否已起作用

```cpp
$ sudo dnf list docker-ce
Available Packages
docker-ce.x86_64                                           3:18.09.0-3.fc27   
```

安装docker

```ruby
$ sudo dnf -y install  docker-ce  
```

启动docker服务，请运行：

```ruby
$ sudo systemctl start docker
```

已创建docker组，但未向该组添加任何用户。将当期用户添加到该组以运行没有sudo的docker命令。

```jsx
$ sudo usermod -aG docker $(whoami)
$ su root             //切换到root用户
$ su ${USER}          //再切换到原来的应用用户以上配置才生效
$ docker ps  //验证是否生效
```

验证安装的Docker版本

```ruby
$ docker version  或者docker --version
```

安装docker-compose

```ruby
$ sudo yum -y install docker-compose  
```

验证安装的docker-compose版本

```ruby
$ docker-compose version  或者docker-compose --version
```

新版本的zsh支持docker和docker-compose的命令自动补全。

```jsx
$ ls ~/.oh-my-zsh/plugins/docker
$ ls ~/.oh-my-zsh/plugins/docker-compose/
```

##### 安装Pycharm

1. 下载安装包
    pycharm下载地址：[https://www.jetbrains.com/pycharm/download/#section=linux](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.jetbrains.com%2Fpycharm%2Fdownload%2F%23section%3Dlinux)
    JDK下载地址： [http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html](https://links.jianshu.com/go?to=http%3A%2F%2Fwww.oracle.com%2Ftechnetwork%2Fjava%2Fjavase%2Fdownloads%2Fjdk8-downloads-2133151.html)

2）安装JDK8
 Pycharm是基于Java开发的，因此需要安装JDK.
 进入JDK压缩包所在目录，将Java安装到/opt/目录

```ruby
$ sudo tar zxf ./jdk-8u181-linux-x64.tar.gz -C /opt/
```

JDK安装路径： `/opt/jdk1.8.0_181`

配置环境变量时一般可以直接修改 /etc/profile 文件，但更加灵活、更容易维护的方法是在  /etc/profile.d/ 中添加1个shell脚本文件，如jdk.sh等。删除该文件，环境变量就失效。

```bash
$cd /etc/profile.d/ 
$sudo vi jdk.sh
```

在  jdk.sh 文件内容如下：

```bash
# JDK settings
export JAVA_HOME=/opt/jdk1.8.0_181
export JDK_HOME=$JAVA_HOME
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
```

重新加载/etc/profile，启用环境变量，并测试：

```bash
$sudo source /etc/profile
$java -version //查看JAVA版本 
```

如果提示sudo: source: command not found，可以分为两步执行

```bash
$sudo -s
$source /etc/profile
```

1. 安装pycharm
    解压安装包

```ruby
$ sudo tar zxf ./pycharm-professional-2018.1.3.tar.gz -C /opt/
$ cd /opt/pycharm-2018.1.3/bin/
```

安装pycharm

```bash
$ cd /opt/pycharm-2018.1.3/bin/
# cp ./JetbrainsCrack2.7.jar /opt/pycharm-2018.1.3/bin/
# java -jar JetbrainsCrack2.7.jar //提示破解方法和激活码
```

在bin目录中查找vmoptions 文件：

```css
windows系统: 编辑pycharm64.exe.vmoptions 文件;
linux系统: 编辑pycharm64.vmoptions 文件
```

在文件末尾添加

```jsx
-javaagent:/opt/pycharm-2018.1.3/bin/JetbrainsCrack2.7.jar
```

运行*[idea|clion|...].sh.

```undefined
./pycharm.sh 
```

填写激活码，即可完成激活。
 根据指示创建启动脚本： /usr/local/bin/charm

1. 创建桌面快捷方式
    安装gnome-tweak-tool：

```css
dnf install gnome-tweak-tool
安装:
gnome-tweaks         noarch   3.28.1-1.fc28      fedora   330 k
正在安装安装依赖关系:
 gnome-shell-extension-user-theme   noarch   3.28.1-1.fc28      fedora    24 k
```

Fedora28中，运行`#gnome-tweak-tool`提示未找到命令，而使用 `$ gnome-tweaks`可以打开当前用户名对应的为'**优化(英文版是tweaks)**'的程序。 但在'优化'程序UI中，没有找到 桌面 标签栏。这是Fedora28的一个bug，尚未完成修订。

查看已安装的应用程序快捷方式：

```undefined
ls /usr/share/applications
```

添加pycharm.desktop文件

```undefined
sudo vi /usr/share/applications/pycharm.desktop
```

编辑文件：

```ruby
[Desktop Entry]
Name=Pycharm
Comment=pycharm ide
Exec=/usr/local/bin/charm
Icon=/opt/pycharm-2018.1.3/bin/pycharm.png
Terminal=false
Type=Application
Categories=Application;Development;IDE;
StartupNotify=true
```

属性解释：

```dart
    Name=应用程序名称
    Comment=鼠标经过上面时的提示名称
    Exec=启动指定应用程序所要执行的命令
    Icon=当前Desktop Entry文件在应用程序浏览器或是在文件浏览器中所显示的图标
    Terminal=是否需要在终端窗口中运行
    Type=定义了Desktop Entry文件的类型，常见的 “Type” 数值是 “Application” 和 “Link”
    Categories=菜单所属类别，可以确定该菜单的位置
```

有了pycharm.desktop文件，则可以通过[徽标按钮show applications]弹出的搜索窗口中找到pycharm，进行启动。

##### 安装[Visual Studio Code](https://links.jianshu.com/go?to=https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fsetup%2Flinux)

```swift
$ sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
$ sudo sh -c 'echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/vscode.repo'
$ dnf check-update
$ sudo dnf install code
```

安装code插件：

- markdown
  - [Markdown All in One](https://links.jianshu.com/go?to=https%3A%2F%2Fmarketplace.visualstudio.com%2Fitems%3FitemName%3Dyzhang.markdown-all-in-one)
  - [Markdown PDF](https://links.jianshu.com/go?to=https%3A%2F%2Fmarketplace.visualstudio.com%2Fitems%3FitemName%3Dyzane.markdown-pdf) - This extension convert Markdown file to pdf, html, png or jpeg file.

##### 安装c/c++开发环境(gcc/g++/clang)

```swift
// 安装
$ sudo dnf install gcc
$ sudo dnf install gcc-c++ libstdc++-devel
$ sudo dnf install clang
// 查看版本
$ gcc --version
gcc (GCC) 8.0.1 20180324 (Red Hat 8.0.1-0.20)
$ g++ --version
g++ (GCC) 8.0.1 20180324 (Red Hat 8.0.1-0.20)
$ clang --version
clang version 6.0.0 (tags/RELEASE_600/final)
Target: x86_64-unknown-linux-gnu
Thread model: posix
InstalledDir: /usr/bin
```

##### 安装PostMan API开发和自动化测试工具

PostMan官网： [https://www.getpostman.com/](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.getpostman.com%2F)

```cpp
# wget https://dl.pstmn.io/download/latest/linux64
//解压
# tar -xzf Postman-linux-x64-7.0.6.tar.gz 
//建立软链接
# sudo ln -s /work/soft/Postman/app/Postman  /usr/bin/postman
```

配置桌面图标：
 `sudo vim /usr/share/applications/postman.desktop`

```jsx
[Desktop Entry]
Encoding=UTF-8
Comment=API First Development.
Comment[zh_CN]=API开发助手
Name=Postman
Exec=/urs/bin/postman
Icon=/work/soft/Postman/app/resources/app/assets/icon.png
Terminal=false
Type=Application
Categories=Development;
```

在菜单（Mate桌面：Applications->Programming）或应用程序栏(Gnome桌面)就可以找到Postman。

##### 安装dbeaver通用数据库访问软件

下载dbeaver-ee-5.1-linux.gtk.x86_64.tar.gz
 解压(/opt/dbeaver/)

```undefined
 sudo tar -xzvf /home/brian/Downloads/dbeaver-ee-5.1.0-linux.gtk.x86_64.tar.gz -C /opt/
```

配置

```undefined
sudo cp /opt/dbeaver/dbeaver.desktop /usr/share/applications/
sudo vi /usr/share/applications/dbeaver.desktop
sudo chown -R brian:brian /opt/dbeaver/
```

运行：$ ./dbeaver 企业版ee需要license.

##### 安装Android开发环境

1. 确认是否已安装JDK

```php
java -version
echo $JAVA_HOME //查看JDK安装路径  /opt/jdk1.8.0_181
```

1. 下载Android SDK
    官方地址：[https://developer.android.google.cn/studio/releases/sdk-tools](https://links.jianshu.com/go?to=https%3A%2F%2Fdeveloper.android.google.cn%2Fstudio%2Freleases%2Fsdk-tools)
    中国社区地址：[http://tools.android-studio.org/index.php/sdk/](https://links.jianshu.com/go?to=http%3A%2F%2Ftools.android-studio.org%2Findex.php%2Fsdk%2F)
    下载 [android-sdk_r24.4.1-linux.tgz](https://links.jianshu.com/go?to=http%3A%2F%2Fdl.google.com%2Fandroid%2Fandroid-sdk_r24.4.1-linux.tgz)
2. 下载Android NDK
    官方地址：[https://developer.android.google.cn/ndk/downloads/](https://links.jianshu.com/go?to=https%3A%2F%2Fdeveloper.android.google.cn%2Fndk%2Fdownloads%2F)
    最新版本(2019-2)：[android-ndk-r19b-linux-x86_64](https://links.jianshu.com/go?to=https%3A%2F%2Fdl.google.com%2Fandroid%2Frepository%2Fandroid-ndk-r19b-linux-x86_64.zip)
    其它版本(修改r19b，如r16，可以得到r16版本的下载地址)：[android-ndk-r16-linux-x86_64](https://links.jianshu.com/go?to=https%3A%2F%2Fdl.google.com%2Fandroid%2Frepository%2Fandroid-ndk-r6-linux-x86_64.zip)
3. 下载Android Studio
    官方地址： [https://developer.android.google.cn/studio/](https://links.jianshu.com/go?to=https%3A%2F%2Fdeveloper.android.google.cn%2Fstudio%2F)
    最新版本： [android-studio3.3.1.0](https://links.jianshu.com/go?to=https%3A%2F%2Fdl.google.com%2Fdl%2Fandroid%2Fstudio%2Fide-zips%2F3.3.1.0%2Fandroid-studio-ide-182.5264788-linux.zip)
    其它版本：
4. 解压安装

```bash
$ tar zxvf android-sdk_r24.4.1-linux.tgz
$ unzip android-studio-ide-182.5264788-linux.zip 
$ unzip android-ndk-r16-linux-x86_64.zip 
 //配置环境变量
$ sudo vi /etc/profile.d/brian-config.sh 
//添加以下内容 - Android-SDK环境变量
export ANDROID_HOME=/work/soft/android-sdk-linux
export PATH=$ANDROID_HOME/tools:$PATH
export PATH=$ANDROID_HOME/platform-tools:$PATH
//Android-NDK环境变量
export NDK_HOME=/work/soft/android-ndk-r16 
export PATH=$PATH:$NDK_HOME

$ source /etc/profile //执行命令文件，使环境变量立即生效
```

更新sdk，安装所有包：

```cpp
android update sdk --no-ui //安装所有版本 
android list sdk --all //列出当前的sdk
android update sdk -u --all --filter 1,2,3,5,11,12,22,23,24,25,26,27,28,29,88,89 //根据序号进行选择性安装
```

运行Android Studio

```cpp
./android-studio/bin/studio.sh //安装向导会确认SDK和开发环境设置，可能会提示下载其它版本的API
```

打开File->Setting，找到System Setting/Android SDK，查看当前SDK安装情况。

必要时，可能需要下载gradle
 下载地址：  [https://services.gradle.org/distributions/gradle-4.4-all.zip](https://links.jianshu.com/go?to=https%3A%2F%2Fservices.gradle.org%2Fdistributions%2Fgradle-4.4-all.zip)
 或者删除 /home/brian/.gradle， 重新启动Android Studio

#### 安装Scala

```ruby
$ chmod 755 scala-2.10.5.tgz 
$sudo mkdir /opt/scala
$ sudo tar -xzvf ./scala-2.10.5.tgz -C /opt/scala/
$ sudo vi /etc/profile
```

添加以下配置

```bash
    export SCALA_HOME=/opt/scala/scala-2.10.5
    export PATH=$SCALA_HOME/bin:$PATH
```

启用配置和测试：

```ruby
$source /etc/profile
$ scala -version
```

Scala安装路径：/opt/scala/scala-2.10.5

#### 安装Spark

```ruby
$ chmod 755 spark-1.5.2-bin-hadoop2.4.tgz 
$ sudo mkdir /opt/spark
$ sudo tar -xzvf spark-1.5.2-bin-hadoop2.4.tgz -C /opt/spark/
$ sudo vi /etc/profile
```

添加以下配置

```bash
    # Spark environment config
    export SPARK_HOME=/opt/spark/spark-1.5.2-bin-hadoop2.4
    export PATH=$SPARK_HOME/bin:$PATH
```

启用配置和测试：

```ruby
$source /etc/profile
$ spark-shell
```

spark安装路径： /opt/spark/spark-1.5.2-bin-hadoop2.4

#### 安装nodejs

方法1： 编译安装
 以node-v0.10.43版本为例

```ruby
$wget https://nodejs.org/download/release/v0.10.43/node-v0.10.43.tar.gz
$cd node-v0.10.43
$./configure
$make
$sudo make install
$ node -v
v0.10.43
$ npm -v
1.4.29
```

编译安装后，

- /usr/local/bin/  -- 该目录是执行文件，包括node、npm命令
- /usr/local/lib/ -- 存放node_modules，即nodejs的各种模块，包括npm
- /usr/include -- 放置了nodejs扩展开发需要的头文件
- npm配置信息：/home/brian/.npmrc

方法2： 直接使用编译好的安装包
 以node-v8.11.4版本为例

```php
//下载安装包
$wget https://nodejs.org/dist/v8.11.4/node-v8.11.4-linux-x64.tar.xz
//解压
$ xz -d node-v8.11.4-linux-x64.tar.xz
$ tar -xf node-v8.11.4-linux-x64.tar
//移动到/opt/目录
$ sudo mv node-v8.11.4-linux-x64 /opt/
//获取安装路径
$ pwd
/opt/node-v8.11.4-linux-x64/bin
//设置软连接
ln -s /opt/node-v8.11.4-linux-x64/bin/node /usr/bin/node
ln -s /opt/node-v8.11.4-linux-x64/bin/npm /usr/bin/npm
//测试
$ node -v
v8.11.4
$ npm -v
5.6.0
//npm配置信息可以在此指定（采用ini格式）：/home/brian/.npmrc
```

#### 更新Firefox

[更新Firefox步骤参考](https://links.jianshu.com/go?to=http%3A%2F%2Fwww.centoscn.com%2Fimage-text%2Finstall%2F2014%2F1112%2F4111.html)

- 查找firefox安装路径

```jsx
$ whereis firefox
firefox: /usr/bin/firefox /usr/lib64/firefox /usr/share/man/man1/firefox.1.gz
```

如果是32位的系统,一般安装路径是/usr/lib/firefox;64位系统一般安装路径为/usr/lib64/firefox

- 删除旧的firefox目录

```bash
$sudo rm -rf /usr/lib64/firefox
```

- 将解压的新版 firefox 子目录复制到 /usr/lib64 目录下

```bash
$sudo mv firefox /usr/lib64
```

- 更新软链接
   删除/usr/bin目录下的 firefox 脚本，回到主目录或根目录，创建一个软链接，指向新的firefox启动脚本(/usr/lib64/firefox/firefox)

```ruby
$ cd /usr/bin
$ ll firefox
-rwxr-xr-x. 1 root root 6546 10月 30 2013 firefox
$sudo rm firefox 
$ sudo ln -s /usr/lib64/firefox/firefox /usr/bin/firefox 
```

此时在 shell 终端执行 firefox 命令，就能启动最新版的 firefox （启动前确认已经终止所有旧版的 firefox 进程）

```ruby
$ firefox 
```

注意，对于这一步而言，需要 firefox 占用一个 shell 虚拟终端（pts/n），该虚拟终端不能执行任何其它命令，直到关闭 firefox 进程。

- 配置桌面图标
   修改系统左上方的 firefox 浏览器图标的属性，在 “命令”字段右侧的文本框中，输入
   `/usr/lib64/firefox/firefox %u`

#### 安装openconnect插件

网络管理器--添加openconnect插件-vpn，适用于Cisco AnyConnect, Juniper，[这里是详细步骤](https://links.jianshu.com/go?to=https%3A%2F%2Fwiki.gnome.org%2FProjects%2FNetworkManager%2FVPN)

```bash
# dnf install NetworkManager-openconnect
# dnf install NetworkManager-openconnect-gnome 
```

#### 安装terminator

terminator能够基于1个窗口提供多个终端，使用格子进行排列。

```ruby
$ sudo dnf install terminator
```

#### 显卡驱动

查看显卡信息，终端输入：

```cpp
$ lspci |grep -i VGA
03:00.0 VGA compatible controller: NVIDIA Corporation GF119 [NVS 315] (rev a1) //NVIDIA NVS 315 专业显卡
```

在网站]上查找驱动程序安装脚本，进行安装：
 参考资料：
 Fedora 20安装Nvidia 显卡驱动
 [https://blog.csdn.net/icemango1209/article/details/18766353](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2Ficemango1209%2Farticle%2Fdetails%2F18766353)

```css
# dnf install kernel-devel kernel-headers dkms acpid libglvnd-glx libglvnd-opengl libglvnd-devel pkgconfig
Installed:
  acpid.x86_64 2.0.28-7.fc28                                                                
  dkms.noarch 2.5.0-3.20180306gitb1b9033.fc28                                               
  kernel-devel.x86_64 4.16.3-301.fc28                                                       
  libglvnd-devel.x86_64 1:1.0.1-0.5.20180327git5baa1e5.fc28                                 
  libglvnd-opengl.x86_64 1:1.0.1-0.5.20180327git5baa1e5.fc28                                
  elfutils-libelf-devel.x86_64 0.170-11.fc28                                                
  libglvnd-core-devel.x86_64 1:1.0.1-0.5.20180327git5baa1e5.fc28                            
  zlib-devel.x86_64 1.2.11-7.fc28  
```