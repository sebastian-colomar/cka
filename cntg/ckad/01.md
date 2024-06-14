```
kubectl run tres-containers-pod --image=busybox:1.28 --env="ORDER=FIRST" --port=8080 --dry-run=client -oyaml|tee 01.yaml
```
