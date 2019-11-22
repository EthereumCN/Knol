# 快速上手

## 如何安装

本指南主要内容为如何将Py-EVM作为库使用。

### Ubuntu系统

Py-EVM需要Python 3.6以及一些工具来编译其依赖项。

在Ubuntu上，`python3.6-dev`软件包包含全部所需。运行以下命令进行安装：

```text
apt-get install python3.6-dev
```

Py-EVM是通过pip软件包管理器安装的，如果pip在系统上尚不可用，我们需要通过以下命令安装`python3-pip`软件包：

```text
apt-get install python3-pip
```

{% tabs %}
{% tab title="注意" %}
**可选步骤：**通常，维持干净Python 3环境的最佳方式是[virtualenv](https://virtualenv.pypa.io/en/stable/)。如果尚未安装

`virtualenv`，则首先需要通过pip安装。

```text
pip install virtualenv
```

之后，我们就可以初始化一个全新的虚拟环境`venv`，如下：

```text
virtualenv -p python3 venv
```

随后将创建一个新的目录`venv`，以便软件包在其中进行安装，并与全局安装的Python隔离开。

要激活虚拟目录，我们必须对其进行_source_。

```text
. venv/bin/activate
```
{% endtab %}
{% endtabs %}

最后，我们就可以通过pip安装`py-evm`包了：

```text
pip3 install -U py-evm
```

### 

### macOS系统

首先，通过brew安装Python 3：

```text
brew install python3
```

{% tabs %}
{% tab title="注意" %}
**可选步骤：**通常，维持干净Python 3环境的最佳方式是[virtualenv](https://virtualenv.pypa.io/en/stable/)。如果尚未安装

`virtualenv`，则首先需要通过pip安装。

```text
pip install virtualenv
```

之后，我们就可以初始化一个全新的虚拟环境`venv`，如下

```text
virtualenv -p python3 venv
```

随后将创建一个新的目录`venv`，以便软件包在其中进行安装，并与全局安装的Python隔离开。

要激活虚拟目录，我们必须对其进行_source_。

```text
. venv/bin/activate
```
{% endtab %}
{% endtabs %}

最后，我们就可以通过pip安装`py-evm`包了：

```text
pip3 install -U py-evm
```



| ✅ **提示** |
| :--- |
| 五分钟之内就可以在Py-Evm上[构建你的第一个应用程序](https://py-evm.readthedocs.io/en/latest/guides/building_an_app_that_uses_pyevm.html)！ |

