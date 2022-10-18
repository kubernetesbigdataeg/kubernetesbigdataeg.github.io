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

