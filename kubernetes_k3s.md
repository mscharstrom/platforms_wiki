# Installing k3s and configuring kubectl

k3s offers a simple way of installing its cluster with kubectl and other handy tools.
Please read their own material [here](https://docs.k3s.io/quick-start).

By using k3s provided install script we also get kubectl installed (along with crictl, ctr, k3s-killall.sh, and k3s-uninstall.sh).

`$ curl -sfL https://get.k3s.io | sh -`

If we try and run `$ kubectl get ns` or something equivalent you'll notice that we can't access any cluster.

```
WARN[0000] Unable to read /etc/rancher/k3s/k3s.yaml, please start server with --write-kubeconfig-mode to modify kube config permissions
error: unknown command "client" for "kubectl"
```

If we run it with sudo `$ sudo kubectl get ns` we should be able to see our cluster:
```
NAME              STATUS   AGE
default           Active   24s
kube-system       Active   24s
kube-public       Active   24s
kube-node-lease   Active   24s
```
