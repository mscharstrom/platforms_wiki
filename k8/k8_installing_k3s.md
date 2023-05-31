# Installing k3s and configuring kubectl
*The main purpose of this documentation is for my own learning.*

k3s offers a simple way of installing its cluster with kubectl and other handy tools. Please read their own material [here](https://docs.k3s.io/quick-start). By using k3s provided install script we also get kubectl installed (along with crictl, ctr, k3s-killall.sh, and k3s-uninstall.sh). Most of the information written here can be found on this [source](https://www.baeldung.com/ops/k3s-getting-started). 

<br>

Start by running k3s install script.

`$ curl -sfL https://get.k3s.io | sh -`

<br>

If we try and run `$ kubectl get ns` or something equivalent you'll notice that we can't access any cluster.

```
WARN[0000] Unable to read /etc/rancher/k3s/k3s.yaml, please start server with --write-kubeconfig-mode to modify kube config permissions
error: unknown command "client" for "kubectl"
```

<br>

But, if we run it with sudo `$ sudo kubectl get ns` we should be able to see our cluster:
```
$ sudo kubectl get ns
NAME              STATUS   AGE
default           Active   24s
kube-system       Active   24s
kube-public       Active   24s
kube-node-lease   Active   24s
```

<br>

### :warning: Warning
> If you do not have the ~/.kube folder available you can proceed with the following steps, if you already have a ~/.kube/config where you connect to an already available cluster you might want to setup aliases in your ~/.bash_profile file to switch between different clusters. Please read further down for these instructions.
###

<br>

With the default installation, k3s will place its config file for the cluster on this location '/etc/rancher/k3s/k3s.yaml', lets create the config file where k8 is looking per default instead.

```
$ mkdir -p ~/.kube
$ sudo k3s kubectl config view --raw | tee ~/.kube/config
$ chmod 600 ~/.kube/config
```

<br>

Now, lets point KUBECONFIG to the new config file:

`$ export KUBECONFIG=~/.kube/config`

When running `kubectl get ns` you should now see this:

```
$ kubectl get ns
NAME              STATUS   AGE
default           Active   13m
kube-system       Active   13m
kube-public       Active   13m
kube-node-lease   Active   13m
```

Since I don't want to write `$ export KUBECONFIG=~/.kube/config` each time I log in on my server, I will add an alias in my ~/.bash_aliases file.

`alias k3cluster="export KUBECONFIG=~/.kube/config"`
`$ source ~/.bash_aliases`

All I have to do to connect to the k3s cluster is now to write k3cluster in my terminal.

<br>

## :information_source: Aliases for different configs

If you have different ~/.kube/configs or are in need of it, you can put your different config files under the ~/.kube/<folder>/ and create aliases in your ~/.bash_aliases (or equivalent) to point KUBECONFIG to these files.

<br>

```
alias local-test-conf='export KUBECONFIG=$HOME/.kube/local/local-test-config`
alias local-prod-conf='export KUBECONFIG=$HOME/.kube/local/local-prod-config`
```

## Uninstalling
If you wish to uninstall k3s with its other tools installed by the first script, simply run:

`$ k3s-uninstall.sh`

<br>
  
Also, if you created the ~/.kube config you migt want to remove that folder as well. But be careful so that you don't delete any config files that you might need.

`rm -rf ~/.kube`

<br>

EOF
