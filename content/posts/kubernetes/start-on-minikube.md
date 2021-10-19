---
title: "Start on Minikube"
date: 2021-10-12T22:16:51+08:00
draft: false
tags: ["Kubernates"]
---

# Minikube

> reference: https://minikube.sigs.k8s.io/docs/start/

## 安装和启动

```shell
# 安装
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
sudo dpkg -i minikube_latest_amd64.deb

# 启动
minikube start
```
在第一次启动`minikube start`过程中不小心打断了，再重试怎么都下载不了镜像，似乎是进入了某种异常状态，通过删除用户home目录下的.minikube文件件重试可以重置。

## 验证
我的minikube安装在一台闲置的linux笔记本上， 需要通过笔记本的ip访问，kubectl proxy默认监听在127.0.0.1回环地址上，所以需要启动一个非回环口上的代理。

``` shell
kubectl get po -A

# 安装dashboard组件
minikube dashboard 

# 启动访问代理
kubectl proxy --address='<ip>' --accept-hosts='^.*'
```
访问[http://k56cb.waibozie.me:8001/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/]


## 个人理解
（1）minikube采用了docker in docker的方式部署本地版本的K8s， 运行起来后只能在宿主机上看到一个kicbase容器
```
waibozie@k56cb:~$ docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED      STATUS      PORTS                                                                                                                                  NAMES
9a9c7f55d673   kicbase/stable:v0.0.27   "/usr/local/bin/entr…"   2 days ago   Up 2 days   127.0.0.1:49157->22/tcp, 127.0.0.1:49156->2376/tcp, 127.0.0.1:49155->5000/tcp, 127.0.0.1:49154->8443/tcp, 127.0.0.1:49153->32443/tcp   minikube
```

在kicbase容器里能看到运行的kubelet进程
```
root        2284       1  8 Oct17 ?        04:49:00 /var/lib/minikube/binaries/v1.22.2/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --config=/var/lib/kubelet/config.yaml --container-runtime=docker --hostname-override=minikube --kubeconfig=/etc/kubernetes/kubelet.conf --node-ip=192.168.49.2
```

以及运行在kicbase容器中的K8s关键容器
```shell
# docker ps
CONTAINER ID   IMAGE                  COMMAND                  CREATED      STATUS      PORTS     NAMES
0de208db8592   e1482a24335a           "/dashboard --insecu…"   2 days ago   Up 2 days             k8s_kubernetes-dashboard_kubernetes-dashboard-654cf69797-2c82f_kubernetes-dashboard_37761d54-143e-4201-a864-e655f484afe5_1
477bc8a2b31c   6e38f40d628d           "/storage-provisioner"   2 days ago   Up 2 days             k8s_storage-provisioner_storage-provisioner_kube-system_d5bdd9e3-fab8-4c9c-8440-44ba7f4da221_1
97f3a391f286   7801cfc6d5c0           "/metrics-sidecar"       2 days ago   Up 2 days             k8s_dashboard-metrics-scraper_dashboard-metrics-scraper-5594458c94-52xpc_kubernetes-dashboard_be895e5c-62b2-409b-bb5b-a10a6f5b3593_0
b0caec2f5566   873127efbc8a           "/usr/local/bin/kube…"   2 days ago   Up 2 days             k8s_kube-proxy_kube-proxy-2svfj_kube-system_3dfdfc23-efb5-48c3-a69c-9e4630812a5a_0
498c6eb0870c   k8s.gcr.io/pause:3.5   "/pause"                 2 days ago   Up 2 days             k8s_POD_dashboard-metrics-scraper-5594458c94-52xpc_kubernetes-dashboard_be895e5c-62b2-409b-bb5b-a10a6f5b3593_0
cd28788b0151   8d147537fb7d           "/coredns -conf /etc…"   2 days ago   Up 2 days             k8s_coredns_coredns-78fcd69978-8qj6k_kube-system_5b31f117-2003-4b90-9c22-3ae167c1851c_0
72a526ba15f0   k8s.gcr.io/pause:3.5   "/pause"                 2 days ago   Up 2 days             k8s_POD_kube-proxy-2svfj_kube-system_3dfdfc23-efb5-48c3-a69c-9e4630812a5a_0
f6030e0d5185   k8s.gcr.io/pause:3.5   "/pause"                 2 days ago   Up 2 days             k8s_POD_kubernetes-dashboard-654cf69797-2c82f_kubernetes-dashboard_37761d54-143e-4201-a864-e655f484afe5_0
da0f0a09c5ca   k8s.gcr.io/pause:3.5   "/pause"                 2 days ago   Up 2 days             k8s_POD_coredns-78fcd69978-8qj6k_kube-system_5b31f117-2003-4b90-9c22-3ae167c1851c_0
c2ac7077f685   k8s.gcr.io/pause:3.5   "/pause"                 2 days ago   Up 2 days             k8s_POD_storage-provisioner_kube-system_d5bdd9e3-fab8-4c9c-8440-44ba7f4da221_0
85c7877bc7a5   b51ddc1014b0           "kube-scheduler --au…"   2 days ago   Up 2 days             k8s_kube-scheduler_kube-scheduler-minikube_kube-system_97bca4cd66281ad2157a6a68956c4fa5_0
bd4122c06d97   004811815584           "etcd --advertise-cl…"   2 days ago   Up 2 days             k8s_etcd_etcd-minikube_kube-system_08a3871e1baa241b73e5af01a6d01393_0
8d96391f6e62   e64579b7d886           "kube-apiserver --ad…"   2 days ago   Up 2 days             k8s_kube-apiserver_kube-apiserver-minikube_kube-system_fe49842e48a1ca6bac988e8ba88ecc53_0
4be91dba8d82   5425bcbd23c5           "kube-controller-man…"   2 days ago   Up 2 days             k8s_kube-controller-manager_kube-controller-manager-minikube_kube-system_4787f37e041fd890d5499a8863561e5f_0
2774b157ce1e   k8s.gcr.io/pause:3.5   "/pause"                 2 days ago   Up 2 days             k8s_POD_kube-scheduler-minikube_kube-system_97bca4cd66281ad2157a6a68956c4fa5_0
821051fa5e1a   k8s.gcr.io/pause:3.5   "/pause"                 2 days ago   Up 2 days             k8s_POD_kube-apiserver-minikube_kube-system_fe49842e48a1ca6bac988e8ba88ecc53_0
a8be72f31e75   k8s.gcr.io/pause:3.5   "/pause"                 2 days ago   Up 2 days             k8s_POD_etcd-minikube_kube-system_08a3871e1baa241b73e5af01a6d01393_0
9141727314d5   k8s.gcr.io/pause:3.5   "/pause"                 2 days ago   Up 2 days             k8s_POD_kube-controller-manager-minikube_kube-system_4787f37e041fd890d5499a8863561e5f_0
```

（2）minikube通过给宿主机的kubectl配置访问kicbase的8443端口来访问apiserver，达成宿主机kubectl命令能正常工作的目的， 配置文件在~/.kube/config
```shell
waibozie@k56cb:~/.kube$ cat config
apiVersion: v1
clusters:
- cluster:
    certificate-authority: /home/waibozie/.minikube/ca.crt
    extensions:
    - extension:
        last-update: Sun, 17 Oct 2021 03:54:19 UTC
        provider: minikube.sigs.k8s.io
        version: v1.23.2
      name: cluster_info
    server: https://192.168.49.2:8443
  name: minikube
contexts:
- context:
    cluster: minikube
    extensions:
    - extension:
        last-update: Sun, 17 Oct 2021 03:54:19 UTC
        provider: minikube.sigs.k8s.io
        version: v1.23.2
      name: context_info
    namespace: default
    user: minikube
  name: minikube
current-context: minikube
kind: Config
preferences: {}
users:
- name: minikube
  user:
    client-certificate: /home/waibozie/.minikube/profiles/minikube/client.crt
    client-key: /home/waibozie/.minikube/profiles/minikube/client.key
```

```
waibozie@k56cb:~$ kubectl get pod -A
NAMESPACE              NAME                                         READY   STATUS    RESTARTS        AGE
kube-system            coredns-78fcd69978-8qj6k                     1/1     Running   0               2d10h
kube-system            etcd-minikube                                1/1     Running   0               2d10h
kube-system            kube-apiserver-minikube                      1/1     Running   0               2d10h
kube-system            kube-controller-manager-minikube             1/1     Running   0               2d10h
kube-system            kube-proxy-2svfj                             1/1     Running   0               2d10h
kube-system            kube-scheduler-minikube                      1/1     Running   0               2d10h
kube-system            storage-provisioner                          1/1     Running   1 (2d10h ago)   2d10h
```