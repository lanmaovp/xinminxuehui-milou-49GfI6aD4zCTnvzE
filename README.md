
# PostGIS安装


PostGIS 是一个开源数据库拓展，它为 PostgreSQL 数据库增加了对地理空间数据的支持。PostGIS 使得空间数据的存储、查询和分析变得简单高效。


PostGIS 是 Postgresql 的一个插件，本文将介绍 Unbuntu 平台下如何安装 PostGIS 拓展的一些步骤和注意的问题。



> 在安装 PostGIS 前应该先安装好 Postgresql。


版本说明：
Unbuntu Version：22\.04\.4
Postgresql Version：14


## 安装前准备


安装 PostGIS 前读者可以先确认下正在使用的 Postgresql 版本，根据 Postgresql 的版本来确定你要安装哪个版本的 PostGIS。



```
# 查看 postgresql 版本
# 切换到 postgres 用户
su postgres
# 进入 postgresql 数据库
psql
# 查看 postgresql 数据库版本
SELECT version();

```

PostgreSQL 与 PostGIS 对应支持关系




| PostgreSQL Version | PostGIS Version |
| --- | --- |
| 11(EOL) | 2\.5(EOL)、3\.0、3\.1、3\.2、3\.3 |
| 12 | 2\.5(EOL)、3\.0、3\.1、3\.2、3\.3、3\.4、3\.5 |
| 13 | 3\.0、3\.1、3\.2、3\.3、3\.4、3\.5 |
| 14 | 3\.1、3\.2、3\.3、3\.4、3\.5 |
| 15 | 3\.2、3\.3、3\.4、3\.5 |
| 16 | 3\.3、3\.4、3\.5 |
| 17 | 3\.3、3\.4、3\.5 |



> 统计时间为：2024\-09\-04 ，其中标注为（EOL）的是官方不再支持更新的。所以最好选择较新版本的。这个列表官方可能会有变动，最新信息见参考目录的“PostGIS 版本对应关系”。


我在安装的时候，本地的 postgresql 的版本是 14 ，所以我这里选择安装 PostGIS 3\.3。


PostGIS 还需要依赖一些库，比如：***GEOS***、***Proj***、***protobuf\-c
***、***GDAL***等，他们之间也有一些版本的依赖和匹配关系，具体关系见参考目录的“PostGIS 版本对应关系”。



> 除了上面三个特意标出来的依赖库，还需要依赖一些如xml2、libpq等库，但是暂时先不用管，到安装的时候会提示你缺少哪些东西，到时候再安装也不晚。


如果读者使用源码编译安装，那么 gcc、g\+\+、make、cmake 等这些工具都需要提前装好，或者等遇到报错后再取百度就行了，这里就不浪费篇幅了。


### 安装 Geos 库


[Geos下载地址](https://github.com)


这里我选择的版本是 Geos3\.12\.2，下载的文件为 `geos-3.12.2.tar.bz2`。


源码安装：



```
# 解压
sudo tar jxf geos-3.12.2.tar.bz2
# 进入解压出来的目录的src目录
cd geos-3.12.2/src
# 构建
sudo cmake ../
# 编译
sudo make
# 安装
sudo make install 

```


> sudo 命令大家应该都知道，这里就不说了，如果用的是虚拟机的 root 用户安装的可以不加 sudo。


也可以直接apt安装：`sudo apt-get install libgeos-dev`


### 安装 Proj 库


[Proj 9 下载地址](https://github.com)


这里我选择的是 Proj 9\.4\.1，下载的文件为 `proj-9.4.1.tar.gz`。


源码安装：



```
# proj 9 需要依赖sqlite3和TIEF和CURL的一些库，如果之前没安装可以执行下面命令安装
sudo apt-get install libsqlite3-dev
sudo apt-get install libtiff-dev
sudo apt-get install libcurl4-openssl-dev

# 将下载的proj包解压
sudo tar zxf proj-9.4.1.tar.gz
# 进入目录
cd proj-9.4.1/
# 建个build目录
sudo mkdir build
# 进入build目录
cd build
# 构建编译和安装
sudo cmake ..
sudo cmake --build .
sudo cmake --build . --target install

```

也可以直接apt安装：`sudo apt-get install libproj-dev`


### 安装 Protobuf\-c 库


[protobuf\-c下载地址](https://github.com)
[protobuf\-c源码地址](https://github.com)


这里我选择的是 1\.5\.0，下载的文件为 `protobuf-c-1.5.0.tar.gz`。


源码安装：



```
# 解压
sudo tar zxf protobuf-c-1.5.0.tar.gz
# 配置
sudo ./configure
# 编译 
sudo make
# 安装 
sudo make install

```


> 安装 protobuf\-c 的时候需要依赖一些其他的库，比如：protobuf,pkg\-config等，也要先安装。具体可以看 protobuf\-c 的 github仓库，这里就不多说了。


也可以直接apt安装：`sudo apt-get install libprotobuf-c-dev`


### 安装 Gdal 库


[Gdal 官网地址](https://github.com)
[Gdal 仓库地址](https://github.com)


这里我选择的是 `gdal-3.5.3.tar.gz`。


源码安装：



```
# 解压
sudo tar xf gdal-3.5.3.tar.gz
# 进入解压出的目录
cd gdal-3.5.3/
# 配置
./configure --prefix=/usr/local/gdal
# 编译 
sudo make
# 安装 
sudo make install

```

也可以直接apt安装：`sudo apt-get install libgdal-dev`


## 安装 Postgis


上面的准备工作做好了，我们就可以安装 Postgis 了。当然你要是不确定哪些库你已经安装了，也可以先直接安装 Postgis 。在执行 `./configure` 的时候，如果缺少某些依赖库，它会提醒你，然后再去安装就可以了。


[PostGIS 官网](https://github.com):[veee加速器官网](https://youhaochi.com)


我这里使用的是 `postgis-3.3.6.tar.gz` 版本。


源码安装：



```
# 解压
sudo tar zxf
# 进入目录

# 配置
# --with-pgsql 要改成读者自己的 postgresql 目录下的 pg_config 文件路径。
# --with-gdalconfig 就是上面刚安装的 gdal 路径下的 gdal-config 文件路径。
sudo ./configure --with-pgsql=/usr/lib/postgresql/14/bin/pg_config --with-gdalconfig=/usr/local/gdal/bin/gdal-config
# 编译 
sudo make
# 安装 
sudo make install

```


> 如果在 执行 make 的时候报 `fatal error: postgres.h No such file or directory` 的错误，可以安装一下 sudo apt install postgresql\-server\-dev\-XX （xx换成你的postgresql版本）。


也可以直接apt安装：`sudo apt-get install postgis`


安装成功后可以进入 postgresql 数据库，在数据库中创建 postgis 拓展插件。



```
# 切换到 postgres 用户
su postgres

# 链接进入数据库
postgres@virtual-machine:/$ psql
psql (14.13 (Ubuntu 14.13-0ubuntu0.22.04.1))
Type "help" for help.
postgres=#

#创建 postgis 拓展
postgres=# create extension postgis;
CREATE EXTENSION

# 查看一下是否创建成功
postgres=# select * from pg_available_extensions where name like 'postgis%';
          name          | default_version | installed_version |                          comment
------------------------+-----------------+-------------------+------------------------------------------------------------
 postgis_raster         | 3.3.6           |                   | PostGIS raster types and functions
 postgis_tiger_geocoder | 3.3.6           |                   | PostGIS tiger geocoder and reverse geocoder
 postgis_topology       | 3.3.6           |                   | PostGIS topology spatial types and functions
 postgis                | 3.3.6           | 3.3.6             | PostGIS geometry and geography spatial types and functions
(4 rows)


```

## 使用 Postgis



```
# 创建插件拓展
create extension postgis;

# 创建表格
CREATE TABLE t_point(
  "id" int4 NOT NULL,
  "point" geometry(point, 4326),
  PRIMARY KEY ("id")
);

# 插入数据
INSERT INTO t_point
  (id,point)
VALUES
  (1,ST_GeomFromText('Point(31.2323 120.2323)',4326))

# 查询数据
SELECT id,ST_ASTEXT(point) FROM t_point WHERE id = 1

```

`ST_ASTEXT、ST_GeomFromText`是内置方法，其他一些常用方法可以参考：[官方文档](https://github.com)


完...


## 参考


* [PostGIS 版本对应关系，by osgeo.org](https://github.com)
* [PostGIS 官网，by postgis.net](https://github.com)
* [PostGIS 方法文档](https://github.com)
* [离线安装postgresql12\.5和postgis3\.0，by 晚风](https://github.com)


