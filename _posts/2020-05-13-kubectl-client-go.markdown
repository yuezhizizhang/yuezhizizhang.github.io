---
layout: post
title:  "Kubectl and client-go Part 1"
categories: "kubernetes kubectl client-go"
---

For the past two weeks, I've been learning the APIs of [client-go](https://github.com/kubernetes/client-go){:target="_blank"}. **client-go** is the official client library of Kubernetes, which is used internally by Kubernetes as well. The Kubernetes command line tool **kubectl** is built on **client-go**.

For a Kubernetes starter like me, it's not an easy job. One reason is because there are very few discussions about **client-go** online. So for any problems you met, there is no such easy way of Googling out an answer. The only way you can do is digging out an answer from the source codes by yourself. 

I'm happy with my progress so far. I'm to the point that I'm able to write any commonly used **kubectl** commands with **client-go**. 

### Resources

- [Kubernetes 1.8 API](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/){:target="_blank"}

- [Kubernetes API Conventions](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-architecture/api-conventions.md){:target="_blank"}

- [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/){:target="_blank"}

### Nodes 

Read more on [Kubernets Nodes](https://kubernetes.io/docs/concepts/architecture/nodes/){:target="_blank"}. In **client-go**, [NodeInterface](https://github.com/kubernetes/client-go/blob/master/kubernetes/typed/core/v1/node.go){:target="_blank"} includes all the APIs to deal with Nodes.

- **<span style="text-decoration: underline; color: #2f5687; font-size: 1.2rem;">List</span>**

> kubectl

```bash
kubectl get nodes
```

> client-go

```golang
import (
    "context"

    v1 "k8s.io/client-go/kubernetes/typed/core/v1"
    apiV1 "k8s.io/api/core/v1"
    metav1 "k8s.io/apimachinery/pkg/apis/meta/v1"
)

func ListNodes(n v1.NodeInterface) ([]apiV1.Node, error) {
    nodes, err := n.List(context.TODO(), metav1.ListOptions{})

    if err != nil {
        return nil, err
    }

    return nodes.Items, nil
}
```

As we know, ***kubectl get nodes*** displays 5 columns:

<pre>
| NAME               | STATUS | ROLES  | AGE | VERSION |
|--------------------|--------|--------|-----|---------|
| k8s-worker-node1   | Ready  | &lt;none&gt; | 13d | v1.18.2 |
| k8s-master-node1   | Ready  | master | 13d | v1.18.2 |
</pre>

A big effort I spent on is to figure out which column maps to which property of **Node**.

**1. Name**

Name is the [Metadata](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-architecture/api-conventions.md#metadata) of Node. 

**2. Status**

To see if a Node is Ready:

```golang
import (
    "k8s.io/api/core/v1"
)

func IsReady(node *v1.Node) bool {
    var cond v1.NodeCondition
    for _, c := range node.Status.Conditions {
        if c.Type == v1.NodeReady {
            cond = c
            break
        }
    }
	
    return cond.Status == v1.ConditionTrue
}
```

**3. Roles**

To check if a Node is a master node, search if the label **"node-role.kubernetes.io/master"** is present.

```golang
func IsMaster(node *v1.Node) bool {
    if _, ok := node.Labels["node-role.kubernetes.io/master"]; ok {
        return true
    }
	
    return false
}
```

**4. Age**

Calculate the age of a Node from the **CreationTimestamp**.

```golang
func Age(node *v1.Node) uint {
    diff := uint(time.Now().Sub(node.CreationTimestamp.Time).Hours())
    return uint(diff / 24)
}
```

**5. Version**

Get the Kubernetes version from the Node status.

```golang
func KubeletVersion(node *v1.Node) string {
    return node.Status.NodeInfo.KubeletVersion
}
```

- **<span style="text-decoration: underline; color: #2f5687; font-size: 1.2rem;">Get</span>**

**NOTE: To get the details of a Node, instead of using "kubectl describe", a better command is "kubectl get -o yaml/json". The output will be in the same structure as the client-go struct.**

> kubectl

```bash
kubectl get node <node_name> -o yaml
```

> client-go

```golang
func GetNode(n v1.NodeInterface, name string) (*v1.Node, error) {
    return n.Get(context.TODO(), name, metav1.GetOptions{})
}
```