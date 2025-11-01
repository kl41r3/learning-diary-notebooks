# Virtual Environment Control
**Major difference**: 生成`.venv`虚拟环境就放在根目录下面，每个项目自己携带自己的环境，不统一查看和管理。而在conda中是独立的，可以通过conda env list 查看。所以conda的环境名字都要不一样,`.venv`是生是死，都叫`.venv`。。

## `.venv`使用手册
|Command |Function ||
|:------|:------|------|  
|`uv init`| 直接生成(.gitignore, python-version, README.md, pyproject.toml)||
|`uv venv` | 创建虚拟环境 ||
|`source .venv/bin/activate` |激活 ||
|`uv sync`|synchronize, 同步下拉代码中的包||
|`uv pip install <packages>`| 装包||
|`deactivate`| 解除||
|``|||

遇到pull code，直接`uv sync`, `source .venv/bin/activate`。


## Conda 使用手册

### 基础思路
```bash
conda create -n myenv   #创造
conda activate myenv    #激活
pip install ...         #装包
conda deactivate        #解除
```


|Command |Function ||
|:------|:------|------|   
|`conda env list` | 列出conda中所有环境 ||
|`conda list` | 列出环境中的包 ||
|`conda create -n/--name myenv python=3.11`| 创造新环境||
|`conda activate myenv`| 激活环境||
|`conda deactivate`| 解除环境||
|`conda create -f environment.yml`| -f: file. Create env from `yml` file||
|``|||


### conda 和 pip 的关系

要确保pip在当前conda环境中工作。使用`which pip`检查。  
同理，使用`which python`检查python路径是否正确。  

```bash 
/opt/anaconda3/bin/python
#path of base environment

/opt/anaconda3/envs/myproject/bin/python
#path of the project environment
```

尽量使用`pip install <package_name>`从PyPL (Python Package Index)安装包。

```bash
pip install -r requirements.txt
```

## Poetry

创建项目：poetry new project_name
安装依赖：poetry add package_name
运行项目：poetry run python script.py
激活虚拟环境：poetry shell
