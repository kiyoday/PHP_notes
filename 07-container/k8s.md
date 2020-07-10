## minikube快速搭建k8s单节点

![image-20200710124756020](C:\Users\12605\Desktop\PHP_notes\.img\image-20200710124756020.png)



```shell
$ cd labs/pod-basic
$ pcat pod_nginx.yml
```

![image-20200710125114544](C:\Users\12605\Desktop\PHP_notes\.img\image-20200710125114544.png)

```shell
kubectl create -f 
kubectl version


kubectl create -f pod_nginx.yml
kubectl delete -f pod_nginx.yml #删除

kubectl get pods
kubectl get pods -o wide #详细信息

```

![image-20200710125418501](C:\Users\12605\Desktop\PHP_notes\.img\image-20200710125418501.png)