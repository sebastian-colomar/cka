# CKA

## 2025/03/10

- https://training.linuxfoundation.org/certification/certified-kubernetes-administrator-cka/
- https://kubernetes.io/docs/home/
- https://learn.kodekloud.com/user/courses/cka-certification-course-certified-kubernetes-administrator
- https://labs.play-with-docker.com/
- https://labs.play-with-k8s.com/
- https://shell.cloud.google.com/

---

- https://kubernetes.io/docs/concepts/overview/
- https://en.wikipedia.org/wiki/Linux_namespaces
- https://en.wikipedia.org/wiki/Cgroups
- https://en.wikipedia.org/wiki/LXC

---
- https://kubernetes.io/docs/concepts/overview/components/

---
## 2025/03/11

- https://kubernetes.io/docs/tutorials/hello-minikube/
- https://kubernetes.io/docs/reference/kubectl/
```
kubectl
```
```
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
  labels:
    app: multi-container
spec:
  containers:
    - name: nginx
      image: nginx:latest
      ports:
        - containerPort: 80
      volumeMounts:
        - name: shared-data
          mountPath: /usr/share/nginx/html
          
    - name: busybox
      image: busybox
      command: ["/bin/sh", "-c", "echo 'Hola desde BusyBox' > /data/index.html && sleep 3600"]
      volumeMounts:
        - name: shared-data
          mountPath: /data

  volumes:
    - name: shared-data
      emptyDir: {}
```
```
kubectl create -f po.yaml
kubectl get po
kubectl get -oyaml po multi-container-pod
kubectl get po -owide
kubectl exec multi-container-pod -- ping -c1 10.244.0.5
```
```
kubectl delete po multi-container-pod
```
```
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-rc
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    app: nginx
  template:
    metadata:
      labels:
        app: nginx
        author: Sebas
        fecha: 2025
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
```
```
kubectl create -f rc.yaml

Error from server (BadRequest): error when creating "rc.yaml": ReplicationController in version "v1" cannot be handled as a ReplicationController: json: cannot unmarshal number into Go struct field ObjectMeta.spec.template.metadata.labels of type string
```
```
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-rc
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    app: nginx
  template:
    metadata:
      labels:
        app: nginx
        author: Sebas
        fecha: '2025'
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
```
```
kubectl create -f rc.yaml
```
```
kubectl get po

NAME                  READY   STATUS    RESTARTS   AGE
nginx-rc-4ldg6        1/1     Running   0          6s
nginx-rc-pgphp        1/1     Running   0          6s
nginx-rc-wvllh        1/1     Running   0          6s
```
```
kubectl get rc

NAME       DESIRED   CURRENT   READY   AGE
nginx-rc   3         3         3       58s
```
```
kubectl get po -owide

NAME                  READY   STATUS    RESTARTS      AGE   IP           NODE       NOMINATED NODE   READINESS GATES
nginx-rc-4ldg6        1/1     Running   0             17m   10.244.0.7   minikube   <none>           <none>
nginx-rc-pgphp        1/1     Running   0             17m   10.244.0.6   minikube   <none>           <none>
nginx-rc-wvllh        1/1     Running   0             17m   10.244.0.8   minikube   <none>           <none>
```
```
kubectl expose rc nginx-rc

service/nginx-rc exposed
```
```
kubectl get svc nginx-rc

NAME       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
nginx-rc   ClusterIP   10.102.123.110   <none>        80/TCP    47s
```
```
minikube ssh -- sudo iptables -S -t nat|grep SVC.*default/nginx-rc

-A KUBE-SVC-Z3R7LL5CXYDH3WP6 ! -s 10.244.0.0/16 -d 10.102.123.110/32 -p tcp -m comment --comment "default/nginx-rc cluster IP" -m tcp --dport 80 -j KUBE-MARK-MASQ
-A KUBE-SVC-Z3R7LL5CXYDH3WP6 -m comment --comment "default/nginx-rc -> 10.244.0.6:80" -m statistic --mode random --probability 0.33333333349 -j KUBE-SEP-OYEEXILGJE3EWWH6
-A KUBE-SVC-Z3R7LL5CXYDH3WP6 -m comment --comment "default/nginx-rc -> 10.244.0.7:80" -m statistic --mode random --probability 0.50000000000 -j KUBE-SEP-SHCX5DR6NLHG3ECF
-A KUBE-SVC-Z3R7LL5CXYDH3WP6 -m comment --comment "default/nginx-rc -> 10.244.0.8:80" -j KUBE-SEP-ZVXZ4JKXFV5INHUS
```
```
kubectl get po -owide

NAME                  READY   STATUS    RESTARTS       AGE   IP           NODE       NOMINATED NODE   READINESS GATES
nginx-rc-4ldg6        1/1     Running   0              28m   10.244.0.7   minikube   <none>           <none>
nginx-rc-pgphp        1/1     Running   0              28m   10.244.0.6   minikube   <none>           <none>
nginx-rc-wvllh        1/1     Running   0              28m   10.244.0.8   minikube   <none>           <none>
```
```
kubectl delete po nginx-rc-4ldg6

pod "nginx-rc-4ldg6" deleted
```
```
kubectl get po -owide

NAME                  READY   STATUS    RESTARTS       AGE   IP           NODE       NOMINATED NODE   READINESS GATES
nginx-rc-fcvfd        1/1     Running   0              23s   10.244.0.9   minikube   <none>           <none>
nginx-rc-pgphp        1/1     Running   0              29m   10.244.0.6   minikube   <none>           <none>
nginx-rc-wvllh        1/1     Running   0              29m   10.244.0.8   minikube   <none>           <none>
```
```
minikube ssh
```
```
sudo iptables -S -t nat|grep SVC.*default/nginx-rc

-A KUBE-SVC-Z3R7LL5CXYDH3WP6 ! -s 10.244.0.0/16 -d 10.102.123.110/32 -p tcp -m comment --comment "default/nginx-rc cluster IP" -m tcp --dport 80 -j KUBE-MARK-MASQ
-A KUBE-SVC-Z3R7LL5CXYDH3WP6 -m comment --comment "default/nginx-rc -> 10.244.0.6:80" -m statistic --mode random --probability 0.33333333349 -j KUBE-SEP-OYEEXILGJE3EWWH6
-A KUBE-SVC-Z3R7LL5CXYDH3WP6 -m comment --comment "default/nginx-rc -> 10.244.0.8:80" -m statistic --mode random --probability 0.50000000000 -j KUBE-SEP-ZVXZ4JKXFV5INHUS
-A KUBE-SVC-Z3R7LL5CXYDH3WP6 -m comment --comment "default/nginx-rc -> 10.244.0.9:80" -j KUBE-SEP-BR7KISXRDRYWTHB6
```
```
kubectl get ep nginx-rc

NAME       ENDPOINTS                                   AGE
nginx-rc   10.244.0.6:80,10.244.0.8:80,10.244.0.9:80   12m
```
```
kubectl exec -c busybox multi-container-pod -- nslookup nginx-rc.default.svc.cluster.local | grep Name -A1

Name:   nginx-rc.default.svc.cluster.local
Address: 10.102.123.110
```
```
kubectl get svc nginx-rc

NAME       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
nginx-rc   ClusterIP   10.102.123.110   <none>        80/TCP    104m
```
```
kubectl explain svc.spec.type
```
```
~/CKA$ kubectl get rc nginx-rc
NAME       DESIRED   CURRENT   READY   AGE
nginx-rc   3         3         3       133m

~/CKA$ kubectl get svc nginx-rc
NAME       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
nginx-rc   ClusterIP   10.102.123.110   <none>        80/TCP    114m

~/CKA$ kubectl get ep nginx-rc
NAME       ENDPOINTS                                   AGE
nginx-rc   10.244.0.6:80,10.244.0.8:80,10.244.0.9:80   114m
```
```
kubectl expose rc nginx-rc --type NodePort

kubectl exec nginx-rc-fcvfd -- curl -sI localhost

minikube ssh -- curl -sI localhost:31444
```
- https://www.diegomacedo.com.br/wp-content/uploads/2012/02/proxy.png
- https://www.imperva.com/learn/wp-content/uploads/sites/13/2019/01/reverse-proxy-02-1.jpg

---
- https://kubernetes.io/docs/reference/access-authn-authz/authentication/
- https://kubernetes.io/docs/reference/access-authn-authz/rbac/
```
kubectl config use-context cluster1

Switched to context "cluster1".
```
Archivos relevantes:
- /etc/kubernetes/admin.conf → Credenciales de administrador del clúster.
- /etc/kubernetes/kubelet.conf → Credenciales del Kubelet.
- /etc/kubernetes/controller-manager.conf → Credenciales del Controller Manager.
- /etc/kubernetes/scheduler.conf → Credenciales del Scheduler.
```
kubectl -n kube-system get sa metrics-server -oyaml

kubectl -n kube-system get rolebinding metrics-server-auth-reader -oyaml

kubectl -n kube-system get role extension-apiserver-authentication-reader -oyaml

kubectl -n kube-system get cm extension-apiserver-authentication -oyaml
```
- https://kubernetes.io/docs/concepts/security/service-accounts/
- https://kubernetes.io/docs/reference/access-authn-authz/rbac/
- https://kubernetes.io/docs/concepts/configuration/configmap/
- https://kubernetes.io/docs/concepts/configuration/secret/
