---
layout: page
title: SDK's Notes
menubar: cheatsheets_menu
hide_footer: true
show_sidebar: false
toc: true
---

## Introduction

These notes have been created as a reminder of the use of the different SDK's 
used in the project. Unfortunately each of the technologies has a different SDK 
manager which makes it complicated to configure the different programming 
frameworks for different environments.

### Java and Maven

For installing Java/Maven we use the The Software Development Kit Manager:
[SDKMAN](https://sdkman.io)

```
curl -s "https://get.sdkman.io" | bash
```

```
sdk list java
sdk install java 8.0.292.hs-adpt
sdk current java
sdk list java | grep -E "installed|local"
sdk list maven | grep \*

sdk current java
sdk use java java-11-fedora
sdk env init

sdk selfupdate
```

### NodeJS and NPM

For installing NodeJS (and NPM) we use the Node Version Manager:
[NVM](https://github.com/nvm-sh/nvm#install--update-script)

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.2/install.sh | bash
```
```
nvm ls
nvm ls-remote
nvm install 16.2.0
nvm use node 14.15.5

node -v
v14.15.5

npm -v
6.14.11
```

As we can see the node installation manager itself installs a version of NPM, 
if we want to upgrade to the latest version we will use this command:

```
nvm install-latest-npm

npm -v
7.14.0

nvm install 18.12.1
Downloading and installing node v18.12.1...
Downloading https://nodejs.org/dist/v18.12.1/node-v18.12.1-linux-x64.tar.xz...
#################################################
100.0%
Computing checksum with sha256sum
Checksums matched!
Now using node v18.12.1 (npm v8.19.2)
```
Creating aliases:

```
nvm alias tamer v18.12.1
tamer -> v18.12.1

nvm alias otherproject v14.16.0
otherproject -> v14.16.0

nvm use tamer
Now using node v18.12.1 (npm v8.19.2)
```
### Python 

We always install Python environments with Miniconda:
[Miniconda](https://docs.conda.io/en/latest/miniconda.html)

```
curl -LO https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
sh Miniconda3-latest-Linux-x86_64.sh
conda create -n superset python=3.7.9
conda activate superset

(superset) pip install apache-superset
```

### Go

#### Vanilla Go without package manager

[Go Downloads](https://go.dev/dl/)
```
curl -LO https://go.dev/dl/go1.18.6.linux-amd64.tar.gz
tar xvzf go1.18.6.linux-amd64.tar.gz -C $HOME/.local/share
mkdir -p $HOME/go/{bin,pkg,src}
export PATH=$PATH:$HOME/.local/share/go/bin
export GOPATH=$HOME/go
export PATH=$PATH:$GOPATH/bin
```


#### Go with a package manager

We can install Go versions with Go Version Manager:
[GVM](https://github.com/moovweb/gvm)

```
bash < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)
source ~/.gvm/scripts/gvm
gvm version
Go Version Manager v1.0.22 installed at ...

$ gvm listall
$ gvm install go1.7.3 -B
Installing go1.7.3 from binary source

$ gvm list
gvm gos (installed)
   go1.7.3
   system

$ gvm use go1.7.3
Now using version go1.7.3

$ which go
~/.gvm/gos/go1.7.3/bin/go

$ gvm use system
Now using version system

$ which go
/usr/lib/golang/bin/go
$ go env
...
GOPATH="${HOME}/.gvm/pkgsets/go1.7.3/global"
...

$ gvm use go1.7.3 --default
Now using version go1.7.3

$ gvm list
gvm gos (installed)
=> go1.7.3
   system
```

### Operator SDK

Helper script for Operator SDK installation:

```
# Custom settings
export BINPATH="$HOME/.local/bin"
export SDKVERSION=v1.24.0
# Change above custom settings

echo "==> 01 - Getting architecture/os target platform"

export ARCH=$(case $(uname -m) in x86_64) echo -n amd64 ;; aarch64) echo -n arm64 ;; *) echo -n $(uname -m) ;; esac)
export OS=$(uname | awk '{print tolower($0)}')
echo -ne "\tArchitecture: $ARCH\n"
echo -ne "\tOS: $OS\n"

echo "==> 02 - Download the binary for your platform"

export OPERATOR_SDK_DL_URL=https://github.com/operator-framework/operator-sdk/releases/download/$SDKVERSION
[ ! -f operator-sdk_${OS}_${ARCH} ] && curl -LO ${OPERATOR_SDK_DL_URL}/operator-sdk_${OS}_${ARCH}

echo "==> 03 - Verify the downloaded binary"

# Import the operator-sdk release GPG key from keyserver.ubuntu.com
gpg --keyserver keyserver.ubuntu.com --recv-keys 052996E2A20B5C7E
# Download the checksums file and its signature, then verify the signature
curl -LO ${OPERATOR_SDK_DL_URL}/checksums.txt
curl -LO ${OPERATOR_SDK_DL_URL}/checksums.txt.asc
gpg -u "Operator SDK (release) <cncf-operator-sdk@cncf.io>" --verify checksums.txt.asc
grep operator-sdk_${OS}_${ARCH} checksums.txt | sha256sum -c -
rm checksums.txt checksums.txt.asc

echo "==> 04 - Install the release binary in your PATH"

chmod +x operator-sdk_${OS}_${ARCH} && mv operator-sdk_${OS}_${ARCH} $BINPATH/operator-sdk
echo
echo -ne "\toperator-sdk cli installed at $BINPATH\n"
```

