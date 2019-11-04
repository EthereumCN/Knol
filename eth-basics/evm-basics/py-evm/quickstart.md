# 快速上手

## Quickstart

### Installation

This guide teaches how to use Py-EVM as a library. For contributors, please check out the [Contributing Guide](https://py-evm.readthedocs.io/en/latest/contributing.html) which explains how to set everything up for development.

#### Installing on Ubuntu

Py-EVM requires Python 3.6 as well as some tools to compile its dependencies. On Ubuntu, the `python3.6-dev` package contains everything we need. Run the following command to install it.

```text
apt-get install python3.6-dev
```

Py-EVM is installed through the pip package manager, if pip isn’t available on the system already, we need to install the `python3-pip` package through the following command.

```text
apt-get install python3-pip
```

Note

**Optional:** Often, the best way to guarantee a clean Python 3 environment is with [virtualenv](https://virtualenv.pypa.io/en/stable/). If we don’t have `virtualenv` installed already, we first need to install it via pip.

```text
pip install virtualenv
```

Then, we can initialize a new virtual environment `venv`, like:

```text
virtualenv -p python3 venv
```

This creates a new directory `venv` where packages are installed isolated from any other global packages.

To activate the virtual directory we have to _source_ it

```text
. venv/bin/activate
```

Finally, we can install the `py-evm` package via pip.

```text
pip3 install -U py-evm
```

#### Installing on macOS

First, install Python 3 with brew:

```text
brew install python3
```

Note

**Optional:** Often, the best way to guarantee a clean Python 3 environment is with [virtualenv](https://virtualenv.pypa.io/en/stable/). If we don’t have `virtualenv` installed already, we first need to install it via pip.

```text
pip install virtualenv
```

Then, we can initialize a new virtual environment `venv`, like:

```text
virtualenv -p python3 venv
```

This creates a new directory `venv` where packages are installed isolated from any other global packages.

To activate the virtual directory we have to _source_ it

```text
. venv/bin/activate
```

Then, install the `py-evm` package via pip:

```text
pip3 install -U py-evm
```

Hint

[Build a first app](https://py-evm.readthedocs.io/en/latest/guides/building_an_app_that_uses_pyevm.html) on top of Py-EVM in under 5 minutes

