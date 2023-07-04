# yanbingliu.github.io
这个博客主要记录我的地震学研究笔记
#GMT安装记录：
 # 安装编译所需软件包
 $ sudo apt update
 $ sudo apt install build-essential cmake libcurl4-gnutls-dev libnetcdf-dev
 # 安装可选软件包
 $ sudo apt install ghostscript gdal-bin libgdal-dev libglib2.0-dev libpcre3-dev libfftw3-dev liblapack-dev
 # 安装制作动画所需的软件包
 $ sudo apt install graphicsmagick ffmpeg
 # 下载软件包
 新建~/software/GMT-src 目录
 $ cd ~/software/GMT-src
 在http://mirrors.ustc.edu.cn/gmt上下载最新版本的gmt gsshhg dcw 拷贝至该目录
 # 解压三个压缩文件
 $ tar -xvf *
 # 将 gshhg 和 dcw 数据复制到 gmt 的 share 目录下
 $ mv * gmt-6.4.0/share/gshhg-gmt
 $ mv * gmt-6.4.0/share/dcw-gmt
 # 切换到 gmt 源码目录下
 $ cd gmt-6.4.0
 # 用文本编辑器新建并打开 CMake 用户配置文件
 $ vim cmake/ConfigUser.cmake
     set (CMAKE_INSTALL_PREFIX "/home/yanbing/opt/GMT-6.4.0")
     set (GMT_USE_THREADS TRUE)
     set (GMT_ENABLE_OPENMP TRUE)
 # 注意，此处新建的 build 文件夹位于 gmt-6.4.0目录下，不是 gmt-6.4.0/cmake 目录下
 # 编译安装，如果先安装了 anaconda，需要先注释掉环境变量
 $ mkdir build
 $ cd build/
 $ cmake ..
 $ make
 $ make install
 # 添加环境变量
 $ vim ~/.bashrc    # 取消 anaconda 的注释
     # GMT6
     export GMT6HOME=/home/yanbing/opt/GMT-6.4.0
     export PATH=${GMT6HOME}/bin:$PATH
     export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:${GMT6HOME}/lib64
     # 关闭单行命令完成自动打开图片
     export GMT_END_SHOW=off
     # 将数据、配置文件放到自定义路径
     export GMT_DATADIR=/home/zhao/data/gmtdata/
     export GMT_CACHEDIR=/home/zhao/data/gmtdata/cache
     export GMT_USERDIR=/home/zhao/data/gmtdata/
 $ source ~/.bashrc
 # GMT 添加中文字体
 $ vim $GMT_DATADIR/PSL_custom_fonts.txt
     STSong-Light--UniGB-UTF8-H  0.700    1
     STFangsong-Light--UniGB-UTF8-H  0.700    1
     STHeiti-Regular--UniGB-UTF8-H   0.700   1
     STKaiti-Regular--UniGB-UTF8-H   0.700   1
     STSong-Light--UniGB-UTF8-V  0.700    1
     STFangsong-Light--UniGB-UTF8-V  0.700    1
     STHeiti-Regular--UniGB-UTF8-V   0.700   1
     STKaiti-Regular--UniGB-UTF8-V   0.700   1
 # ghostscript 配置中文
 $ sudo apt install poppler-data
 $ sudo apt install fonts-arphic-uming fonts-arphic-ukai  # 安装 gs 默认 Linux 字体
 # 新建 winfonts 文件夹
 $ sudo mkdir /usr/share/fonts/winfonts/
 # 将 Windows 下的中文字体拷贝过来
 $ sudo cp /mnt/c/windows/fonts/{simhei.ttf,simkai.ttf,simsun.ttc,simfang.ttf} /usr/share/fonts/winfonts
Linux主要存在的问题是众多fonts文件的缺失，所以当我们编译中文pdf时，经常会出现fonts cannot found的现象。本文主要解决这个问题：
从别处拷贝需要的字体，从Windows的C:/Windows/Fonts下拷贝最常使用的几种字体：msyh.ttf（微软雅黑） simfang.ttf（仿宋） simhei.ttf（黑体） simkai.ttf（楷体） simsun.ttc（宋体）。
拷贝到Linux的/usr/share/fonts/winfonts/目录下
#chmod a+x  /usr/share/fonts/winfonts/ 
# cd /usr/share/fonts/winfonts/
# mkfontscale
# mkfontdir
# fc-cache
 # 修改 gs 中文配置文件
 $ sudo vim /etc/ghostscript/cidfmap.d/90gs-cjk-resource-gb1.conf
     % 原配置文件的内容，与 STSong-Light 等相关的四行必须删除
     /BousungEG-Light-GB <</FileType /TrueType /Path (/usr/share/fonts/truetype/arphic/uming.ttc) /SubfontId 0 /CSI [(GB1) 4] >> ;
     /GBZenKai-Medium    <</FileType /TrueType /Path (/usr/share/fonts/truetype/arphic/ukai.ttc) /SubfontId 0 /CSI [(GB1) 4] >> ;
     /Song-Medium /GBZenKai-Medium ;
     /Adobe-GB1      /BousungEG-Light-GB ;
     /Adobe-GB1-Bold /GBZenKai-Medium ;
     % 新增 Windows 字体的支持
     /STSong-Light <</FileType /TrueType /Path (/usr/share/fonts/winfonts/simsun.ttc) /SubfontId 0 /CSI [(GB1) 4] >> ;
     /STFangsong-Light <</FileType /TrueType /Path (/usr/share/fonts/winfonts/simfang.ttf) /SubfontId 0 /CSI [(GB1) 4] >> ;
     /STHeiti-Regular <</FileType /TrueType /Path (/usr/share/fonts/winfonts/simhei.ttf) /SubfontId 0 /CSI [(GB1) 4] >> ;
     /STKaiti-Regular <</FileType /TrueType /Path (/usr/share/fonts/winfonts/simkai.ttf) /SubfontId 0 /CSI [(GB1) 4] >> ;
 $ sudo update-gsfontmap
 # 中文测试
 $ vim gmt6.4.0-cn-test.sh
     #!/bin/bash
     gmt begin GMT_Chinese png
     gmt set FONT_TITLE 25p,41,black
     gmt set FONT_LABEL 15p,39,black
     gmt text -R0/8/0/4 -JX12c/4c -Bxaf+l"X轴" -Byaf+l"Y轴" -BWSen+t"中文标题" -F+f << EOF
     2 3.5 25p,39,black 中文宋体
     2 2.5 25p,40,blue  中文仿宋
     2 1.5 25p,41,red   中文黑体
     2 0.5 25p,42,green 中文楷体
     4 3.5 25p,43,black 中文宋体
     5 3.5 25p,44,blue  中文仿宋
     6 3.5 25p,45,red   中文黑体
     7 3.5 25p,46,green 中文楷体
     EOF
     gmt end
 $ bash gmt6.4.0-cn-test.sh
 # 将数据服务器更改为科大镜像
 $ gmt set GMT_DATA_SERVER http://china.generic-mapping-tools.org
 $ mv gmt.conf $GMT_DATADIR/
