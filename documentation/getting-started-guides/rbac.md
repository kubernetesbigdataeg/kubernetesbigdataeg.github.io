---
layout: page
title: Kubernetes RBAC
menubar: cheatsheets_menu
hide_footer: true
show_sidebar: false
toc: true
---

## Who, What, Where

**Kubernetes Role-Based Access Control (RBAC)** is a form of identity and access 
management (IAM) that involves a set of permissions or template that determines 
who (subjects) can execute what (verbs), where (namespaces). RBAC is an evolution 
from the traditional attribute-based access control (ABAC)—which grants access 
based on user name rather than user responsibilities:

* **Who**: Users, User Groups, Pods.
* **What**: Verbs, almost all object resource types support the standard HTTP verbs 
  GET, POST, PUT, PATCH, and DELETE. Kubernetes also uses its own verbs, 
  which are often written lowercase to distinguish them from HTTP verbs. Kubernetes 
  uses the term **list** to describe returning a collection of resources to distinguish 
  from retrieving a single resource which is usually called a **get**. If you sent an 
  HTTP GET request with the **?watch** query parameter, Kubernetes calls this a **watch** 
  and not a **get** (see Efficient detection of changes for more details). For PUT 
  requests, Kubernetes internally classifies these as either **create** or **update** 
  based on the state of the existing object. An **update** is different from a **patch**; 
  the HTTP verb for a **patch** is PATCH.
* **Where**: Namespace resources, non-namespaced resources.

<img align="center" width="90%" src="../../../images/relations-RBAC.png">

## Building blocks: Roles and bindings

Role-based access control (RBAC) is a method of regulating access to computer 
or network resources based on the roles of individual users within your organization.

RBAC authorization uses the ```rbac.authorization.k8s.io``` API group to drive authorization 
decisions, allowing you to dynamically configure policies through the Kubernetes API.

The RBAC API declares four kinds of Kubernetes object: Role, ClusterRole, RoleBinding 
and ClusterRoleBinding.


<img align="center" width="100%" src="../../../images/kubernetes-RBAC.png">

## Examples

## Key points

| Concept  | Description |
| ------------- | ------------- |
| White listing security model | An RBAC Role or ClusterRole contains rules that represent a set of permissions. Permissions are purely additive (there are no "deny" rules) |
| Role -> Namespace  | A Role always sets permissions within a particular namespace; when you create a Role, you have to specify the namespace it belongs in.  |
| ClusterRole -> Non Namespace| ClusterRole, by contrast, is a non-namespaced resource. The resources have different names (Role and ClusterRole) because a Kubernetes object always has to be either namespaced or not namespaced; it can't be both. |
|RoleBinding | A role binding grants the permissions defined in a role to a user or set of users. It holds a list of subjects (users, groups, or service accounts), and a reference to the role being granted. A RoleBinding grants permissions within a specific namespace |
|ClusterRoleBinding | The same than RoleBinding but a ClusterRoleBinding grants that access cluster-wide |

