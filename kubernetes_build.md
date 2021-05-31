useful link:

- [Creating a Kubernetes Cluster in Azure using Kubeadm | by Shekhar Patnaik | Medium](https://medium.com/@patnaikshekhar/creating-a-kubernetes-cluster-in-azure-using-kubeadm-96e7c1ede4a)
- [Setting up a single master Kubernetes cluster on Azure using kubeadm - DEV Community](https://dev.to/adudko/setting-up-a-single-master-kubernetes-cluster-on-azure-using-kubeadm-1cjn)
- [igbedo/kubernetes-lab-tutorial: Kubernetes Lab Tutorial (github.com)](https://github.com/igbedo/kubernetes-lab-tutorial)
- [Kubernetes系列教程(一)---通过Kubeadm部署K8s集群 | 指月小筑 (lixueduan.com)](https://lixueduan.com/post/kubernetes/01-install/)
- [Kubeadm | Kubernetes](https://kubernetes.io/zh/docs/reference/setup-tools/kubeadm/)
- [study/Kubernetes学习笔记.md at master · wyyl1/study (github.com)](https://github.com/wyyl1/study/blob/master/Kubernetes/Kubernetes学习笔记.md)





```bash
az vm create -n kube-master -g CustomKubeCluster --image ubuntults \
  --size Standard_D2_v4 \
  --data-disk-sizes-gb 30 \
  --public-ip-address-dns-name ns-kubeadm-master \
  --admin-username azuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
  
  
  
az vm create -n kube-worker-1 -g CustomKubeCluster --image ubuntults \
  --size Standard_D2_v4 \
  --data-disk-sizes-gb 30 \
  --public-ip-address-dns-name ns-kubeadm-worker1 \
  --admin-username azuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

run as root

```bash
sudo su -

# These steps need to be performed on all nodes
sudo apt update
# Install Docker
sudo apt install docker.io -y
sudo systemctl enable docker
# Get the gpg keys for Kubeadm
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add
sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
# Install Kubeadm
sudo apt update
sudo apt install kubeadm -y
```



create kubeadm.yaml

```yaml
apiVersion: kubeadm.k8s.io/v1beta2
kind: ClusterConfiguration
controllerManager:
    extraArgs:
        horizontal-pod-autoscaler-use-rest-clients: "true"
        horizontal-pod-autoscaler-sync-period: "10s"
        node-monitor-grace-period: "10s"
apiServer:
    extraArgs:
        runtime-config: "api/all=true"
kubernetesVersion: "stable-1.21"
```



disable swap

```bash
sudo swapoff -a
```



run init on ***master***

```bash
kubeadm init --config kubeadm.yaml
```

remember

```bash
kubeadm join 172.0.0.4:6443 --token k3l04j.hqomm39r7peu9isb \
        --discovery-token-ca-cert-hash sha256:fbc68d9f12faa65777c801c62ae6094c242c125294f867a7edb4a0994736e916
```

run

```
kubectl describe node kube-master

Name:               kube-master
Roles:              control-plane,master
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=kube-master
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/control-plane=
                    node-role.kubernetes.io/master=
                    node.kubernetes.io/exclude-from-external-load-balancers=
Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Sun, 30 May 2021 17:34:05 +0000
Taints:             node-role.kubernetes.io/master:NoSchedule
                    node.kubernetes.io/not-ready:NoSchedule
Unschedulable:      false
Lease:
  HolderIdentity:  kube-master
  AcquireTime:     <unset>
  RenewTime:       Sun, 30 May 2021 17:37:29 +0000
Conditions:
  Type             Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----             ------  -----------------                 ------------------                ------                       -------
  MemoryPressure   False   Sun, 30 May 2021 17:34:20 +0000   Sun, 30 May 2021 17:34:03 +0000   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure     False   Sun, 30 May 2021 17:34:20 +0000   Sun, 30 May 2021 17:34:03 +0000   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure      False   Sun, 30 May 2021 17:34:20 +0000   Sun, 30 May 2021 17:34:03 +0000   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready            False   Sun, 30 May 2021 17:34:20 +0000   Sun, 30 May 2021 17:34:03 +0000   KubeletNotReady              container runtime network not ready: NetworkReady=false reason:NetworkPluginNotReady message:docker: network plugin is not ready: cni config uninitialized
Addresses:
  InternalIP:  172.0.0.4
  Hostname:    kube-master
Capacity:
  cpu:                2
  ephemeral-storage:  30309264Ki
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             8153436Ki
  pods:               110
Allocatable:
  cpu:                2
  ephemeral-storage:  27933017657
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             8051036Ki
  pods:               110
System Info:
  Machine ID:                 a48287e4a0414a608db2d8f15562fa05
  System UUID:                3b6faf2b-0646-2945-821d-583775634fba
  Boot ID:                    4fef25f5-a871-4157-8b57-3d2b0c38ed93
  Kernel Version:             5.4.0-1047-azure
  OS Image:                   Ubuntu 18.04.5 LTS
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  docker://20.10.2
  Kubelet Version:            v1.21.1
  Kube-Proxy Version:         v1.21.1
Non-terminated Pods:          (5 in total)
  Namespace                   Name                                   CPU Requests  CPU Limits  Memory Requests  Memory Limits  Age
  ---------                   ----                                   ------------  ----------  ---------------  -------------  ---
  kube-system                 etcd-kube-master                       100m (5%)     0 (0%)      100Mi (1%)       0 (0%)         3m24s
  kube-system                 kube-apiserver-kube-master             250m (12%)    0 (0%)      0 (0%)           0 (0%)         3m22s
  kube-system                 kube-controller-manager-kube-master    200m (10%)    0 (0%)      0 (0%)           0 (0%)         3m22s
  kube-system                 kube-proxy-nc8mc                       0 (0%)        0 (0%)      0 (0%)           0 (0%)         3m18s
  kube-system                 kube-scheduler-kube-master             100m (5%)     0 (0%)      0 (0%)           0 (0%)         3m23s
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests    Limits
  --------           --------    ------
  cpu                650m (32%)  0 (0%)
  memory             100Mi (1%)  0 (0%)
  ephemeral-storage  100Mi (0%)  0 (0%)
  hugepages-1Gi      0 (0%)      0 (0%)
  hugepages-2Mi      0 (0%)      0 (0%)
Events:
  Type    Reason                   Age                    From        Message
  ----    ------                   ----                   ----        -------
  Normal  NodeHasSufficientMemory  3m35s (x5 over 3m36s)  kubelet     Node kube-master status is now: NodeHasSufficientMemory
  Normal  NodeHasNoDiskPressure    3m35s (x5 over 3m36s)  kubelet     Node kube-master status is now: NodeHasNoDiskPressure
  Normal  NodeHasSufficientPID     3m35s (x5 over 3m36s)  kubelet     Node kube-master status is now: NodeHasSufficientPID
  Normal  Starting                 3m23s                  kubelet     Starting kubelet.
  Normal  NodeHasSufficientMemory  3m23s                  kubelet     Node kube-master status is now: NodeHasSufficientMemory
  Normal  NodeHasNoDiskPressure    3m23s                  kubelet     Node kube-master status is now: NodeHasNoDiskPressure
  Normal  NodeHasSufficientPID     3m23s                  kubelet     Node kube-master status is now: NodeHasSufficientPID
  Normal  NodeAllocatableEnforced  3m22s                  kubelet     Updated Node Allocatable limit across pods
  Normal  Starting                 3m16s                  kube-proxy  Starting kube-proxy.
azuser@kube-master:~$

```



install network pod - weave

```bash
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```

remove taints

```b	

kubectl taint nodes --all node-role.kubernetes.io/master-

## double check taint is removed

azuser@kube-master:~$ kubectl describe node kube-master
Name:               kube-master
Roles:              control-plane,master
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=kube-master
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/control-plane=
                    node-role.kubernetes.io/master=
                    node.kubernetes.io/exclude-from-external-load-balancers=
Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Sun, 30 May 2021 17:34:05 +0000
Taints:             <none>
Unschedulable:      false
Lease:
```

deploy ***Dashboard*** plugin

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.2.0/aio/deploy/recommended.yaml


azuser@kube-master:~$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.2.0/aio/deploy/recommended.yaml
namespace/kubernetes-dashboard created
serviceaccount/kubernetes-dashboard created
service/kubernetes-dashboard created
secret/kubernetes-dashboard-certs created
secret/kubernetes-dashboard-csrf created
secret/kubernetes-dashboard-key-holder created
configmap/kubernetes-dashboard-settings created
role.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard created
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
deployment.apps/kubernetes-dashboard created
service/dashboard-metrics-scraper created
deployment.apps/dashboard-metrics-scraper created
azuser@kube-master:~$


kubectl proxy
#Starting to serve on 127.0.0.1:8001


#check using curl to prove access is ok
curl -Is http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/ | head -n 1
HTTP/1.1 200 OK
azuser@kube-master:~$
```



persistence

```bash

$ kubectl apply -f https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/ceph/common.yaml
$ kubectl apply -f https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/ceph/operator.yaml
$ kubectl apply -f https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/ceph/crds.yaml
$ kubectl apply -f https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/ceph/cluster.yaml
```

checkpods

```bash
azuser@kube-master:~$ kubectl get pods --all-namespaces -o wide
NAMESPACE              NAME                                         READY   STATUS            RESTARTS   AGE     IP          NODE            NOMINATED NODE   READINESS GATES
kube-system            coredns-558bd4d5db-bh57x                     1/1     Running           0          3h4m    10.32.0.3   kube-master     <none>           <none>
kube-system            coredns-558bd4d5db-ttqkn                     1/1     Running           0          3h4m    10.32.0.2   kube-master     <none>           <none>
kube-system            etcd-kube-master                             1/1     Running           0          3h4m    172.0.0.4   kube-master     <none>           <none>
kube-system            kube-apiserver-kube-master                   1/1     Running           0          3h4m    172.0.0.4   kube-master     <none>           <none>
kube-system            kube-controller-manager-kube-master          1/1     Running           0          3h4m    172.0.0.4   kube-master     <none>           <none>
kube-system            kube-proxy-4242p                             1/1     Running           0          75m     172.0.0.5   kube-worker-1   <none>           <none>
kube-system            kube-proxy-nc8mc                             1/1     Running           0          3h4m    172.0.0.4   kube-master     <none>           <none>
kube-system            kube-scheduler-kube-master                   1/1     Running           0          3h4m    172.0.0.4   kube-master     <none>           <none>
kube-system            weave-net-l4vj6                              2/2     Running           0          75m     172.0.0.5   kube-worker-1   <none>           <none>
kube-system            weave-net-x2l57                              2/2     Running           1          148m    172.0.0.4   kube-master     <none>           <none>
kubernetes-dashboard   dashboard-metrics-scraper-856586f554-8x9k2   1/1     Running           0          60m     10.44.0.1   kube-worker-1   <none>           <none>
kubernetes-dashboard   kubernetes-dashboard-78c79f97b4-wb584        1/1     Running           0          60m     10.44.0.2   kube-worker-1   <none>           <none>
rook-ceph              rook-ceph-csi-detect-version-vdh8s           0/1     PodInitializing   0          25s     10.44.0.4   kube-worker-1   <none>           <none>
rook-ceph              rook-ceph-operator-75c6d6bbfc-pqrkt          1/1     Running           4          4m48s   10.44.0.3   kube-worker-1   <none>           <none>
azuser@kube-master:~$
```

```bash
#重置
$ kubeadm reset
# 将$HOME/.kube文件移除
$ rm -rf $HOME/.kube
#然后重新执行初始化（记得先把 kubeadm-init.log 文件删掉）
$ rm -rf kubeadm-init.log
$ kubeadm init --config=kubeadm.yml --upload-certs | tee kubeadm-init.log
```

