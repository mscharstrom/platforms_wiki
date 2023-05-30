# Installing k3s and configuring kubectl from scratch

k3s offers a simple way of installing its cluster with kubectl and other handy tools. Please read their own material [here](https://docs.k3s.io/quick-start).


By using k3s provided install script we also get kubectl installed (along with crictl, ctr, k3s-killall.sh, and k3s-uninstall.sh).


`$ curl -sfL https://get.k3s.io | sh -`


If we try and run `$ kubectl get ns` or something equivalent you'll notice that we can't access any cluster.


```
WARN[0000] Unable to read /etc/rancher/k3s/k3s.yaml, please start server with --write-kubeconfig-mode to modify kube config permissions
error: unknown command "client" for "kubectl"
```


But, if we run it with sudo `$ sudo kubectl get ns` we should be able to see our cluster:
```
NAME              STATUS   AGE
default           Active   24s
kube-system       Active   24s
kube-public       Active   24s
kube-node-lease   Active   24s
```


### Important - aliases for different configs
*If you do not have the ~/.kube folder available you can proceed with the following steps, if you already have a ~/.kube/config where you connect to an already available cluster you might want to setup aliases in your ~/.bash_profile file to switch between different clusters. Please read further down for these instructions.*
###


With the default installation, k3s will place its config file for the cluster on this location '/etc/rancher/k3s/k3s.yaml', lets create the config file where k8 is looking per default instead.


```
$ mkdir -p ~/.kube
$ sudo k3s kubectl config view --raw | tee ~/.kube/config
$ chmod 600 ~/.kube/config
```

Now, lets point KUBECONFIG to the new config file `$ export KUBECONFIG=~/.kube/config`.
When running `kubectl get ns` you should now see this:

```
NAME              STATUS   AGE
default           Active   13m
kube-system       Active   13m
kube-public       Active   13m
kube-node-lease   Active   13m
```

### Aliases for different configs

If you have different ~/.kube/configs or are in need of it, you can put your different config files under the ~/.kube/<folder>/config and point create aliases in your ~/.bash_aliases (or equivalent) to point KUBECONFIG to these different files.

`alias local-test='export KUBECONFIG=$HOME/.kube/local/local-test-config`
  
 
