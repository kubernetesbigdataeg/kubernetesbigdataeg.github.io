---
layout: page
title: Operators Development
hero_height: is-medium
hide_footer: true
---

The Big Data software stack we use in this project is built using the 
Kubernetes [operator pattern](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/)

Operators are a design pattern made public in a 2016 by CoreOS. The goal 
of an Operator is to put operational knowledge into software. Previously this 
knowledge only resided in the minds of administrators, various combinations 
of shell scripts or automation software.

Operators implement and automate common Day-1 (installation, configuration, etc) 
and Day-2 (re-configuration, update, backup, failover, restore, etc.) activities 
in a piece of software running inside your Kubernetes cluster, by integrating 
natively with Kubernetes concepts and APIs. 

There are different frameworks for the creation of operators, in this project we 
have decided to use [The Operator Framework](https://operatorframework.io/). 


<img align="center" width="20%" src="../images/operator-framework.png">

The **Operator Framework** includes:

* **Operator SDK**: Enables developers to build operators based on their expertise 
  without requiring knowledge of Kubernetes API complexities.
* **Operator Lifecycle Management**: Oversees installation, updates, and management 
  of the lifecycle of all of the operators running across a Kubernetes cluster.
* **Operator Metering**: Enables usage reporting for operators that provide 
  specialized services.

# Setup your operator development environment

## Operator SDK Installation

### Prerequisites:
* git
* go version 1.18

### Vanilla Go compiler installation example

```
curl -LO https://go.dev/dl/go1.18.6.linux-amd64.tar.gz
tar xvzf go1.18.6.linux-amd64.tar.gz -C $HOME/.local/share
mkdir -p $HOME/go/{bin,pkg,src}
export PATH=$PATH:$HOME/.local/share/go/bin
export GOPATH=$HOME/go
export PATH=$PATH:$GOPATH/bin
```

### Helper script for Operator SDK installation

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

### Version checking

| Command  | Output  |
|----------|---------|
| go version  | go version go1.18.6 linux/amd64   |
| operator-sdk version  | operator-sdk version: "v1.24.0", commit: "de6a14d03de3c36dcc9de3891af788b49d15f0f3", kubernetes version: "1.24.2", go version: "go1.18.6", GOOS: "linux", GOARCH: "amd64"  |

### Operator Maturity Model: Capability Levels

The Operator Framework defines a [Capability Model](https://
operatorframework.io/operator-capabilities/) that categorizes Operators
based on their functionality and design. This model helps to break down Operators based
on their maturity, and also describes the extent of an Operator's interoperability with
OLM and the capabilities users can expect when using the Operator.

The Capability Model is divided into five hierarchical levels. Operators can be published
at any one of these levels and, as they grow, may evolve and graduate from one level to the
next as features and functionality are added.

| Level | Funcionalitiy |
|----------|---------|
| Level 1 | [Basic Install](https://sdk.operatorframework.io/docs/overview/operator-capabilities/#level-1---basic-install) |
| Level 2 | [Seamless Upgrades](https://sdk.operatorframework.io/docs/overview/operator-capabilities/#level-2---seamless-upgrades)   |
| Level 3 | [Full Lifecycle](https://sdk.operatorframework.io/docs/overview/operator-capabilities/#level-3---full-lifecycle)   |
| Level 4 | [Deep Insights](https://sdk.operatorframework.io/docs/overview/operator-capabilities/#level-4---deep-insights)   |
| Level 5 | [Auto Pilot](https://sdk.operatorframework.io/docs/overview/operator-capabilities/#level-5---auto-pilot)   |

In this project we will use the **Level 0** capability to indicate the operator is
in the **Planning Stage**, the bootstrapping of the operator, and early development
stages.

**Happy hacking!**

