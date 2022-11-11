---
layout: page
title: Kubeflow
menubar: cheatsheets_menu
hide_footer: true
show_sidebar: false
toc: true
---

<img align="center" width="70%" src="../../images/kubeflow-logo.jpg">

## Introduction

Machine learning development or model development essentially follows the path: data -> information 
-> knowledge -> insight. Model development life cycle (MDLC) is a term commonly used to describe the flow
between training and inference.

Kubeflow is a collection of cloud native tools for all of the stages of MDLC (data
exploration, feature preparation, model training/tuning, model serving, model testing, 
and model versioning). Kubeflow also has tooling that allows these traditionally
separate tools to work seamlessly together. An important part of this tooling is the
pipeline system, which allows users to build integrated end-to-end pipelines that con‐
nect all components of their MDLC.

The Kubeflow project was first announced at **KubeCon + CloudNativeCon North America 2017**
by Google engineers David Aronchick, Jeremy Lewi, and Vishnu Kannan.

Kubeflow started as an open sourcing of the way Google ran TensorFlow internally, 
based on a pipeline called **TensorFlow Extended (TFX)**. It began as just a simpler 
way to run TensorFlow jobs on Kubernetes, but has since expanded to be a multi-architecture, 
multi-cloud framework for running end-to-end machine learning workflows.