---
layout: post
title:  "Kubectl and client-go Part 2"
categories: "kubernetes kubectl client-go"
---

- [Kubectl and client-go Part 1 Clientset and Nodes](http://yuezhizizhang.github.io/kubernetes/kubectl/client-go/2020/05/13/kubectl-client-go-part-1.html){:target="_blank"}
- [Kubectl and client-go Part 2 Pods](http://yuezhizizhang.github.io/kubernetes/kubectl/client-go/2020/05/13/kubectl-client-go-part-2.html){:target="_blank"}
- [Kubectl and client-go Part 3 Secrets and Apply File](http://yuezhizizhang.github.io/kubernetes/kubectl/client-go/2020/05/13/kubectl-client-go-part-3.html){:target="_blank"}

### Pods

Read more on [Kubernets Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/){:target="_blank"}. In **client-go**, [PodInterface](https://github.com/kubernetes/client-go/blob/master/kubernetes/typed/core/v1/pod.go){:target="_blank"} includes all the APIs to deal with Pods.

- **<span style="text-decoration: underline; color: #2f5687; font-size: 1.2rem;">List</span>**

> kubectl

```bash
kubectl get pods -n <namespace>
```

> client-go

```golang
import (
    "context"

    "k8s.io/client-go/kubernetes"
    v1 "k8s.io/api/core/v1"
    metav1 "k8s.io/apimachinery/pkg/apis/meta/v1"
)

func ListPods(clientset kubernetes.Interface, namespace string) ([]v1.Pod, error) {
    pods, err := clientset.CoreV1().Pods(namespace).List(context.TODO(), metav1.ListOptions{})

    if err != nil {
        return nil, err
    }

    return pods.Items, nil
}
```

***kubectl get pods -n &lt;namespace&gt;*** displays 5 columns:

<pre>
| NAME                          | READY | STATUS  | RESTARTS | AGE |
|-------------------------------|-------|---------|----------|-----|
| kube-flannel-ds-amd64-gdltp   | 1/1   | Running | 0        | 14d |
</pre>

**1. Name**

Name is the [Metadata](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-architecture/api-conventions.md#metadata) of Pod.

**2. Status**

To see if a Node is Pending, Running, Succeeded, Failed or Unknown:

```golang
import (
    "k8s.io/api/core/v1"
)

func Status(pod *v1.Pod) v1.PodPhase {
    return pod.Status.Phase
}
```

**3. Restarts**

Get the restart count of the Pod:

```golang
func RestartCount(pod *v1.Pod) int32 {
    if len(pod.Status.ContainerStatuses) > 0 {
        return pod.Status.ContainerStatuses[0].RestartCount
    }
	
    return 0
}
```

**4. Age**

I believe the Age of the Pod could be calculated from the **CreationTimestamp** as well. Or you could calculate it from the **StartTime** of the Pod.

```golang
func Age(pod *v1.Pod) uint {
    diff := uint(time.Now().Sub(pod.Status.StartTime.Time).Hours())
    return uint(diff / 24)
}
```

- **<span style="text-decoration: underline; color: #2f5687; font-size: 1.2rem;">Get</span>**

> kubectl

```bash
kubectl get pod <pod_name> -n <namespace> -o yaml
```

> client-go

```golang
func GetPod(clientset kubernetes.Interface, namespace, name string) (*v1.Pod, error) {
    return clientset.CoreV1().Pods(namespace).Get(context.TODO(), name, metav1.GetOptions{})
}
```