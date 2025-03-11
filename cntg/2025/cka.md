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
