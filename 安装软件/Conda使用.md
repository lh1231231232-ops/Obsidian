---
date: 2025-11-20
tags:
  - conda
---

### 一、创建环境

1. 查看当前环境列表：
```
conda env list
```
2. 在项目中创建虚拟环境（标准 Python 方式）：
```
python -m venv .venv
```
3. 创建指定 Python 版本的 Conda 新环境：
```
conda create --name <环境名称> python=<Python版本>
```
4. 激活环境：

```
conda activate <环境名称>
```
5. 验证环境信息（查看版本与已安装包）：
```
python --version
conda list
```

### 二、配置镜像源（清华源）

1. 生成配置文件并设置显示通道地址：
```
conda config --set show_channel_urls yes
```
2. 添加清华镜像通道（建议按顺序执行）：
```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/pro
```

3. 检查配置是否成功：
```
conda config --show channels
```

---

### 三、安装运行库

1. 切换到目标环境：
```
conda activate <环境名称>
```
2. 一站式安装常用数据科学包（推荐）：
```
conda install numpy pandas matplotlib seaborn plotly jupyterlab scikit-learn
```

---

### 四、Jupyter Lab 的环境配置

1. 打开 Jupyter Lab：
```
python -m jupyter lab
```

2. 安装 ipykernel（允许 Jupyter 识别环境）：

```
pip install ipykernel
```

3. 将当前环境注册为 Jupyter 内核：

```
python -m ipykernel install --user --name <环境名称> --display-name "你想设置的名字"
```
4. **注意**：执行完上述步骤后，需要重启 Jupyter Lab。 
5. 查看所有已注册的内核：
 
```
jupyter kernelspec list
```

---

### 五、环境管理

1. 查看所有环境：

```
conda env list
```

2. 删除指定环境：

```
conda env remove --name myenv
```
3. 退出当前环境：
```
conda deactivate
```
