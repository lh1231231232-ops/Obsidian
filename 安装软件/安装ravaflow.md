

1.添加GIS源 sudo add-apt-repository ppa:ubuntugis/ubuntugis-unstable 
2.更新软件包索引 sudo apt update 
3.安装 GRASS GIS 和 GIS 开发环境 sudo apt install grass grass-core grass-dev grass-doc gdal-bin libgdal-dev build-essential
4.安装R sudo apt install r-base r-base-dev
5.启动R sudo R
6.安装R包 install.packages(c("stats", "codetools", "Rcpp", "terra", "lattice", "sp", "raster", "ROCR"))
7.退出R q()      询问是否保存回答n
8.安装python sudo apt install python3 python3-pip python3-pil

# Ubuntu 20.04 国内环境安装 r.avaflow 4.0G 完整流程

## 1. 已验证成功的环境

本次实际安装成功的环境如下：

|组件|版本/状态|
|---|---|
|Ubuntu|20.04.6 LTS|
|架构|x86_64|
|GCC|9.4.0|
|GDAL|3.0.4|
|PROJ|6.3.1|
|GEOS|3.8.0|
|GRASS GIS|7.8.2|
|Python|3.8.10|
|Pillow|7.0.0|
|R|4.5.2|
|terra|1.8-5|
|r.avaflow|4.0G|
|APT 镜像|中科大 USTC|
|CRAN 镜像|清华 TUNA|

整体结构：

```text
Ubuntu 20.04.6
│
├── GCC / G++ / Make
├── GDAL / PROJ / GEOS
├── Python 3 + Pillow
├── GRASS GIS 7.8.2
│   ├── grass-dev
│   └── grass-doc
├── R 4.5.2
│   ├── Rcpp
│   ├── terra 1.8-5
│   ├── raster
│   ├── sp
│   ├── lattice
│   ├── codetools
│   └── ROCR
└── r.avaflow 4.0G
```

---

# 2. 确认 Ubuntu 环境

```bash
lsb_release -a
uname -m
```

本次环境：

```text
Ubuntu 20.04.6 LTS
Codename: focal
x86_64
```

---

# 3. 配置国内 Ubuntu 软件源

先备份：

```bash
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
```

使用中科大 USTC：

```bash
sudo tee /etc/apt/sources.list > /dev/null <<'EOF'
deb https://mirrors.ustc.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ focal-security main restricted universe multiverse
EOF
```

更新系统：

```bash
sudo apt update
sudo apt upgrade -y
sudo apt autoremove -y
sudo apt clean
```

---

# 4. 安装基础编译工具

```bash
sudo apt install -y \
build-essential \
gcc \
g++ \
make \
cmake \
git \
wget \
curl \
unzip \
zip \
nano \
vim \
tree \
htop \
software-properties-common \
ca-certificates
```

检查：

```bash
gcc --version
g++ --version
make --version
```

本次：

```text
GCC 9.4.0
```

---

# 5. 安装 GIS 底层库

```bash
sudo apt install -y \
gdal-bin \
libgdal-dev \
libgeos-dev \
libproj-dev \
proj-bin \
libsqlite3-dev \
libxml2-dev \
libcurl4-openssl-dev \
libssl-dev \
libtbb-dev
```

检查：

```bash
gdalinfo --version
gdal-config --version
geos-config --version
```

本次：

```text
GDAL 3.0.4
PROJ 6.3.1
GEOS 3.8.0
```

---

# 6. 安装 Python 和 Pillow

```bash
sudo apt install -y \
python3 \
python3-pip \
python3-dev \
python3-pil
```

检查：

```bash
python3 --version
```

```bash
python3 -c "from PIL import Image; print('Pillow:', Image.__version__)"
```

本次：

```text
Python 3.8.10
Pillow 7.0.0
```

---

# 7. 安装 GRASS GIS

```bash
sudo apt install -y \
grass \
grass-dev \
grass-doc
```

检查：

```bash
grass78 --version
```

本次：

```text
GRASS GIS 7.8.2
```

确认开发组件：

```bash
dpkg -l | grep grass
```

应至少存在：

```text
grass
grass-core
grass-dev
grass-doc
grass-gui
```

---

# 8. 测试 GRASS

建立测试 Location：

```bash
mkdir -p ~/grassdata
grass78 -c EPSG:4326 ~/grassdata/avaflow_test --exec g.region -p
```

如果能正常输出：

```text
projection
datum
north
south
east
west
```

说明 GRASS 可以正常运行。

注意：

**这个 EPSG:4326 Location 仅用于测试，不用于正式运行 r.avaflow。**

正式 r.avaflow 使用 Generic XY。

---

# 9. 安装新版 R

Ubuntu 20.04 默认 R 较旧，因此配置 CRAN 软件源。

安装工具：

```bash
sudo apt install -y gnupg dirmngr wget ca-certificates
```

添加 CRAN 密钥：

```bash
wget -qO- https://mirrors.tuna.tsinghua.edu.cn/CRAN/bin/linux/ubuntu/marutter_pubkey.asc \
| gpg --dearmor \
| sudo tee /etc/apt/trusted.gpg.d/cran_ubuntu_key.gpg > /dev/null
```

添加清华 CRAN：

```bash
echo "deb https://mirrors.tuna.tsinghua.edu.cn/CRAN/bin/linux/ubuntu focal-cran40/" \
| sudo tee /etc/apt/sources.list.d/cran-r.list
```

更新：

```bash
sudo apt update
```

安装：

```bash
sudo apt install -y r-base r-base-dev
```

检查：

```bash
R --version
```

本次：

```text
R 4.5.2
```

---

# 10. 设置 R 国内镜像和用户库

创建用户 R 库：

```bash
mkdir -p ~/R/library
```

写入：

```bash
cat > ~/.Rprofile <<'EOF'
options(repos = c(CRAN="https://mirrors.tuna.tsinghua.edu.cn/CRAN/"))
.libPaths(c("~/R/library", .libPaths()))
EOF
```

检查：

```bash
R -q -e '.libPaths()'
```

第一项应类似：

```text
/home/user/R/library
```

---

# 11. 安装 r.avaflow 所需 R 包

首先安装：

```bash
R -q -e 'install.packages("Rcpp")'
```

然后：

```bash
R -q -e 'install.packages(c("codetools","lattice","sp","ROCR"))'
```

---

# 12. terra 是本次安装最重要的坑

直接执行：

```bash
R -q -e 'install.packages("terra")'
```

会安装最新版 terra。

本次最新版：

```text
terra 1.9-46
```

在 Ubuntu 20.04 的：

```text
GDAL 3.0.4
```

环境中编译失败，典型错误：

```text
gdal_multidimensional.cpp
error: ‘GDALDimension’ was not declared in this scope
```

因此不要为了 terra 强行升级整个 GDAL/GRASS GIS 环境。

采用兼容版本：

```text
terra 1.8-5
```

先清理：

```bash
rm -rf ~/R/library/00LOCK-terra
rm -rf ~/R/library/terra
```

然后安装：

```bash
R -q -e 'install.packages("https://mirrors.aliyun.com/CRAN/src/contrib/Archive/terra/terra_1.8-5.tar.gz", repos=NULL, type="source")'
```

检查：

```bash
R -q -e 'library(terra); packageVersion("terra")'
```

本次结果：

```text
terra 1.8.5
```

检查 GDAL：

```bash
R -q -e 'library(terra); terra::gdal()'
```

本次：

```text
3.0.4
```

---

# 13. 安装 raster

terra 成功以后：

```bash
R -q -e 'install.packages("raster")'
```

---

# 14. 检查所有 r.avaflow R 依赖

执行：

```bash
R -q -e '
pkgs <- c(
  "stats",
  "codetools",
  "Rcpp",
  "terra",
  "lattice",
  "sp",
  "raster",
  "ROCR"
)
print(sapply(pkgs, requireNamespace, quietly=TRUE))
'
```

本次全部成功：

```text
    stats codetools      Rcpp     terra   lattice        sp    raster      ROCR
     TRUE      TRUE      TRUE      TRUE      TRUE      TRUE      TRUE      TRUE
```

至此 R 环境完成。

---

# 15. 下载 r.avaflow 4.0G

创建目录：

```bash
mkdir -p ~/software
```

从 r.avaflow 官方网站下载：

```text
r.avaflow.40G.zip
```

放到：

```text
~/Downloads/
```

解压：

```bash
cd ~/software
unzip ~/Downloads/r.avaflow.40G.zip
```

最终应得到：

```text
/home/user/software/r.avaflow.40G/
```

---

# 16. 创建 r.avaflow 正式 GRASS Location

正式运行 r.avaflow 使用：

```text
Generic XY
```

创建：

```bash
mkdir -p ~/grassdata
grass78 -c XY ~/grassdata/ravaflow --exec g.region -p
```

进入：

```bash
grass78 --text ~/grassdata/ravaflow/PERMANENT
```

成功以后终端变成：

```text
GRASS 7.8.2 (ravaflow):~ >
```

检查：

```bash
g.proj -p
```

正确结果：

```text
XY location (unprojected)
```

---

# 17. 安装 r.avaflow 4.0G

在 GRASS 环境内：

```bash
g.extension extension=r.avaflow.40G url=/home/user/software/r.avaflow.40G/
```

本次成功输出：

```text
Fetching <r.avaflow.40G> ...
Compiling...
Installing...
Updating private addons metadata file...
Installation of <r.avaflow.40G> successfully finished
```

---

# 18. 验证 r.avaflow

运行：

```bash
r.avaflow.40G --help
```

成功显示：

```text
The mass flow simulation tool

Usage:
r.avaflow.40G ...
```

检查路径：

```bash
which r.avaflow.40G
```

本次：

```text
/home/user/.grass7/addons/scripts/r.avaflow.40G
```

这说明：

**r.avaflow 4.0G 本体安装完成。**

---

# 19. 下载 Cascadia 官方测试案例

创建：

```bash
cd ~/software
mkdir -p cascadia
cd cascadia
```

下载并解压 Cascadia 4.0G 数据。

最终目录：

```text
ca.avaflow.start.sh

DATA/
├── ca_avalanche.tif
├── ca_debrisflow.tif
├── ca_elev.tif
├── ca_frictiograph.txt
├── ca_hydrograph3.txt
├── ca_hydrograph5.txt
├── ca_impactarea.tif
├── ca_landslide.tif
├── ca_moraine.tif
├── ca_ortho.tif
├── ca_progressiveflow.tif
├── ca_rockglacier.tif
└── ca_viscousflow.tif
```

进入 Generic XY GRASS Location 后：

```bash
cd ~/software/cascadia
```

执行：

```bash
sh ca.avaflow.start.sh
```

即可运行官方 Cascadia 测试案例。

---

# 20. 以后启动 r.avaflow 的方法

Ubuntu 重启以后，不需要重新安装任何东西。

只需要：

```bash
grass78 --text ~/grassdata/ravaflow/PERMANENT
```

进入：

```text
GRASS 7.8.2 (ravaflow):~ >
```

然后：

```bash
r.avaflow.40G --help
```

即可使用。

如果要跑 Cascadia：

```bash
cd ~/software/cascadia
sh ca.avaflow.start.sh
```

---

# 21. 本次最关键的经验

### ① Ubuntu 20.04 是一个比较稳的 r.avaflow 环境

本次：

```text
Ubuntu 20.04
GRASS 7.8.2
GDAL 3.0.4
GCC 9.4
```

组合工作正常。

### ② 国内电脑建议先换源

Ubuntu：

```text
USTC
```

R：

```text
清华 TUNA
```

否则安装 R 包和系统依赖会比较慢。

### ③ 不建议随意升级系统 GIS 栈

不要因为某个 R 包版本过新就轻易升级：

```text
GDAL
PROJ
GEOS
GRASS
```

GIS 软件之间依赖关系比较紧密。

### ④ terra 需要特别注意版本

本次验证：

```text
terra 1.9-46 + GDAL 3.0.4
        ❌ 编译失败

terra 1.8-5 + GDAL 3.0.4
        ✅ 正常工作
```

所以这套 Ubuntu 20.04 环境建议暂时保留：

```text
terra 1.8-5
```

### ⑤ 正式 r.avaflow 使用 Generic XY

不要直接在：

```text
EPSG:4326
经纬度
单位：度
```

Location 中运行。

本次使用：

```text
XY location (unprojected)
```

r.avaflow 中的水平尺度按米处理。

---

# 22. 最终状态

```text
Ubuntu 20.04.6                  ✅
GCC 9.4                         ✅
GDAL 3.0.4                      ✅
PROJ 6.3.1                      ✅
GEOS 3.8.0                      ✅
Python 3.8.10                   ✅
Pillow 7.0.0                    ✅
GRASS GIS 7.8.2                 ✅
R 4.5.2                         ✅
Rcpp                            ✅
terra 1.8-5                     ✅
raster                          ✅
sp                              ✅
ROCR                            ✅
codetools                       ✅
lattice                         ✅
r.avaflow 4.0G                  ✅
Generic XY Location             ✅
Cascadia 官方训练数据            ✅
```

**至此，r.avaflow 4.0G 的 Ubuntu 20.04 国内环境安装流程完成。**


