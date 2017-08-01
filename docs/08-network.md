# Managing the Container Overlay Network

Now that each worker node is online we need to add routes to make sure that Pods running on different machines can talk to each other. In this lab we will use flannel and CNI to provision an overlay network.

Several projects provide Kubernetes pod networks using CNI, some of which also support [Network Policy](https://kubernetes.io/docs/concepts/services-networking/networkpolicies/). See the [add-ons](https://kubernetes.io/docs/concepts/cluster-administration/addons/) page for a complete list of available network add-ons.

```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel-rbac.yml

kubectl apply -f https://raw.githubusercontent.com/jasonbrooks/flannel/support-selinux-kube/Documentation/kube-flannel.yml
```