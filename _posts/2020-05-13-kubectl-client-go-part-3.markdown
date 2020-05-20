---
layout: post
title:  "Kubectl and client-go Part 3"
categories: "kubernetes kubectl client-go"
---

- [Kubectl and client-go Part 1 Clientset and Nodes](http://yuezhizizhang.github.io/kubernetes/kubectl/client-go/2020/05/13/kubectl-client-go-part-1.html){:target="_blank"}

- [Kubectl and client-go Part 2 Pods](http://yuezhizizhang.github.io/kubernetes/kubectl/client-go/2020/05/13/kubectl-client-go-part-2.html){:target="_blank"}

- [Kubectl and client-go Part 3 Secrets and Apply File](http://yuezhizizhang.github.io/kubernetes/kubectl/client-go/2020/05/13/kubectl-client-go-part-3.html){:target="_blank"}

### Secrets

Read more on [Kubernets Secrets](https://kubernetes.io/docs/concepts/configuration/secret/){:target="_blank"}. In **client-go**, [SecretInterface](https://github.com/kubernetes/client-go/blob/master/kubernetes/typed/core/v1/secret.go){:target="_blank"} includes all the APIs to deal with Secrets.

I will demonstrate how to create a Secret from **admin.conf** file. As we know, **admin.conf** file contains the credentials of the Kubernetes cluster admin.

> Create Secret from admin.conf

```golang
func CreateSecret(clientset kubernetes.Interface, kubeconfigPath, namespace, name string) (*v1.Secret, error) {
    conf, err := ioutil.ReadFile(kubeconfigPath)
    if err != nil {
        return nil, err
    }

    data := make(map[string][]byte)
    data["admin.conf"] = conf

    return clientset.CoreV1().Secrets(namespace).Create(&v1.Secret{
        ObjectMeta: metav1.ObjectMeta{
            Name: name,
        },
        Data: data,
    })
}
```

### Deploy from a yaml/json file

> kubectl

```bash
kubectl apply -f <yaml_or_json_file>
```

> client-go

What's crucial here is how to decode the yaml/json file into corresponding Object. I will demonstrate how to do it in client-go.

```golang
import(
    "bytes"
    "log"

    v1 "k8s.io/api/core/v1"
    appsV1 "k8s.io/api/apps/v1"
    "k8s.io/client-go/kubernetes/scheme"
    "k8s.io/apimachinery/pkg/runtime"
)

func Decode(data []byte) (runtime.Object, error) {
    decode := scheme.Codecs.UniversalDeserializer().Decode
    obj, _, err := decode(data, nil, nil)
    return obj, err
}

func Apply(yaml []byte) {
    yamlFiles := bytes.Split(yaml, []byte("---"))
    for _, f := range yamlFiles {
        if len(f) == 0 || string(f) == "\n" {
            // ignore empty cases
            continue
        }

        obj, err := Decode(f)
        if err != nil {
            log.Print(err)
            continue
        }
    
        switch o := obj.(type) {
        case *v1.Service:
            // CreateService
        case *appsV1.Deployment:
            // CreateDeployment
        case *v1.Namespace:
            // CreateSecret
        // ....
        default:
    }
}
```