---
layout: page
title: About
subtitle: Kubernetes Big Data Engineering Group
hero_height: is-medium
hide_footer: true
---

<img align="left" width="20%" src="../images/kubernetes-big-data-eg.png">

# Big Data on Kubernetes

<br>

This is a community resource for big data and data science related software projects on 
techniques and best practices for integrating with Kubernetes.

This group is different from Kubernetes Big Data User Group because, unlike 
Kubernetes User Groups where though their central goal is not a deliverable 
piece of work, one of the objectives of this project is to create user-ready 
deliverables and software tools.

<br>
<br>

# Challenges

<p>
The deployment of Big Data technologies in environments such as Kubernetes is a
challenge that is not well documented today, in a centralized way. There are 
many challenges to be solved due to the idiosyncrasies of Big Data tools and 
the particularities of Kubernetes, a system with a priori design strategies 
that are radically different from the needs of Big Data technologies.
</p>

Without being exhaustive, some of the challenges we are going to study in this 
project are listed below:

* **High availability and resilency**: Many of the Big Data technologies have 
  their own high availability and resiliency mechanisms, which can conflict 
  with the native Kubernetes mechanisms. How we adapt the technologies so 
  as not to incur in anti-patterns in Kubernetes is a question we must answer.

* **Networking**: Some of the technologies have features specially designed to avoid the 
  networking stack (UNIX domain socket short-circuit local reads) for 
  performance reasons. However, Kubernetes is a technology strongly coupled to 
  networking layers. How can we deal with these features?

* **Security**: How do we design a security strategy that centralizes the creation of 
  certificates, kerberos and fine-grained authorization, the usual mechanisms 
  of big data technologies (integration of Apache Ranger, for example)?

* **Configuration**: How do we design a unified configuration strategy, which allows to centralize 
  the huge configuration options of the usually complex Big Data technologies, 
  what Kubernetes configuration pattern do we use for this task.
