kubernetes 沙箱

一、启动虚拟机
安装必要的插件:
vagrant plugin install vagrant-vbguest
运行启动命令:
$ vagrant up

查看全局状态,验证虚拟机运行情况:
$ vagrant global-status
id       name            provider   state   directory
--------------------------------------------------------------------------------
d3e76fd  k8s-master01    virtualbox running /home/hu/Vagrant/sandbox
1ec10ed  k8s-master02    virtualbox running /home/hu/Vagrant/sandbox
6de52c7  k8s-master03    virtualbox running /home/hu/Vagrant/sandbox
3ab9552  worker01        virtualbox running /home/hu/Vagrant/sandbox
ffdd57c  worker02        virtualbox running /home/hu/Vagrant/sandbox
192602b  harbor          virtualbox running /home/hu/Vagrant/sandbox
c2c1e3a  remotecontrol01 virtualbox running /home/hu/Vagrant/sandbox 

进入 remotecontrol01 安装必要组件 git,python,python-pip
sudo yum install python-ipaddr
pip install netaddr
某些OS需要手动开启允许root远程登录 
$ sudo vi /etc/ssh/sshd_config
PermitRootLogin yes （默认为#PermitRootLogin prohibit-password）

二、初始化所有虚拟机
1、进入ansible 控制机
$ vagrant ssh remotecontrol01

2、初始化集群
运行:
vagrant@remotecontrol01:~$ sudo ansible-playbook -i /vagrant/kube-cluster/hosts  /vagrant/kube-cluster/initial.yml
( 
  通常root用户执行以下命令
  # 更安全 Ed25519 算法
    ssh-keygen -t ed25519 -N '' -f ~/.ssh/id_ed25519
 ，然后copy 到各台虚拟机 
    ssh-copy-id -f -i ~/.ssh/id_ed25519.pub 192.168.33.10
  通过 ssh 连一次各节点机
)

三、安装及配置
1、安装依赖
因为墙的缘故，我们需要手动将需要的images现行安装到各节点机上
以下是以1.15.3 为例,需要安装的images:
k8s.gcr.io/kube-apiserver:v1.15.3
k8s.gcr.io/kube-controller-manager:v1.15.3
k8s.gcr.io/kube-proxy:v1.15.3
k8s.gcr.io/etcd:3.3.10
k8s.gcr.io/coredns:1.3.1
k8s-gcr-io/pause:3.1

2、配置
配置master
vagrant@remotecontrol01:~$ sudo ansible-playbook -i /vagrant/kube-cluster/hosts /vagrant/kube-cluster/masters.yml
配置worker
vagrant@remotecontrol01:~$ sudo ansible-playbook -i /vagrant/kube-cluster/hosts /vagrant/kube-cluster/workers.yml

四、验证安装
hu@AI-wow:~/Vagrant/AI21ST/demo$ kubectl get pods --all-namespaces
NAMESPACE              NAME                                        READY   STATUS    RESTARTS   AGE
ingress-controller     nginx-ingress-controller-78b8b9944b-55wf9   1/1     Running   0          4h25m
ingress-controller     nginx-ingress-controller-78b8b9944b-tc2wd   1/1     Running   0          4h25m
kube-system            calico-kube-controllers-7d6f95cb5f-jr6hs    1/1     Running   0          4h26m
kube-system            calico-node-45psv                           1/1     Running   0          4h26m
kube-system            calico-node-d7hzp                           1/1     Running   0          3h59m
kube-system            calico-node-dbb4s                           1/1     Running   0          4h26m
kube-system            calico-node-djnlw                           1/1     Running   0          4h26m
kube-system            calico-node-rp6k5                           1/1     Running   0          4h26m
kube-system            calico-typha-9d55dcf7b-7sfdb                1/1     Running   0          4h26m
kube-system            calico-typha-9d55dcf7b-c4l8b                1/1     Running   0          4h26m
kube-system            calicoctl-fqb62                             1/1     Running   0          4h26m
kube-system            calicoctl-jbffz                             1/1     Running   0          4h26m
kube-system            calicoctl-kxqzb                             1/1     Running   0          3h59m
kube-system            calicoctl-q2np2                             1/1     Running   0          4h26m
kube-system            calicoctl-szpkx                             1/1     Running   0          4h26m
kube-system            coredns-cf8fb6d7f-m27n2                     1/1     Running   0          4h28m
kube-system            coredns-cf8fb6d7f-pz6gr                     1/1     Running   0          4h28m
kube-system            etcd-node1                                  1/1     Running   0          4h28m
kube-system            etcd-node2                                  1/1     Running   0          4h26m
kube-system            etcd-node3                                  1/1     Running   0          4h26m
kube-system            kube-apiserver-node1                        1/1     Running   0          4h28m
kube-system            kube-apiserver-node2                        1/1     Running   0          4h26m
kube-system            kube-apiserver-node3                        1/1     Running   0          4h26m
kube-system            kube-controller-manager-node1               1/1     Running   0          4h28m
kube-system            kube-controller-manager-node2               1/1     Running   0          4h26m
kube-system            kube-controller-manager-node3               1/1     Running   0          4h26m
kube-system            kube-proxy-dkzzd                            1/1     Running   0          4h28m
kube-system            kube-proxy-ggzkg                            1/1     Running   0          4h26m
kube-system            kube-proxy-npgp6                            1/1     Running   0          4h26m
kube-system            kube-proxy-qt2gx                            1/1     Running   0          4h26m
kube-system            kube-proxy-rr2zj                            1/1     Running   0          3h59m
kube-system            kube-scheduler-node1                        1/1     Running   0          4h28m
kube-system            kube-scheduler-node2                        1/1     Running   0          4h26m
kube-system            kube-scheduler-node3                        1/1     Running   0          4h26m
kube-system            lb-nginx-node1                              1/1     Running   0          4h28m
kube-system            lb-nginx-node2                              1/1     Running   0          4h26m
kube-system            lb-nginx-node3                              1/1     Running   0          4h26m
kube-system            lb-nginx-node4                              1/1     Running   0          3h59m
kube-system            lb-nginx-node5                              1/1     Running   0          4h26m
kube-system            metrics-server-56f6f9cfd4-wqhww             1/1     Running   0          4h24m
kubernetes-dashboard   dashboard-metrics-scraper-7c5f777f6-czm6r   1/1     Running   0          3h35m
kubernetes-dashboard   kubernetes-dashboard-797849fcd6-mgjgb       1/1     Running   0          3h35m

(如果有问题， 运行 kubectl describe pods $POD_NAME )

vagrant@k8s-master01:~$ kubectl get nodes
NAME           STATUS   ROLES    AGE     VERSION
k8s-master01   Ready    master   7h52m   v1.15.3
worker01       Ready    <none>   7h45m   v1.15.3
worker02       Ready    <none>   7h45m   v1.15.3



五、其他
our Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

