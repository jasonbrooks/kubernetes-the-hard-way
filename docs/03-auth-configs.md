# Setting up Authentication

In this lab you will setup the necessary authentication configs to enable Kubernetes clients to bootstrap and authenticate using RBAC (Role-Based Access Control).

## Authentication

The following components will leverage Kubernetes RBAC:

* kubelet (client)
* kube-proxy (client)
* kubectl (client)

The other components, mainly the `scheduler` and `controller manager`, access the Kubernetes API server locally over the insecure API port which does not require authentication. The insecure port is only enabled for local access.

### Create the TLS Bootstrap Token

Run these steps from your `controller0` VM.

This section will walk you through the creation of a TLS bootstrap token that will be used to [bootstrap TLS client certificates for kubelets](https://kubernetes.io/docs/admin/kubelet-tls-bootstrapping/).

Generate a token:

```
BOOTSTRAP_TOKEN=$(head -c 16 /dev/urandom | od -An -t x | tr -d ' ')
```

Generate a token file:

```
cat > token.csv <<EOF
${BOOTSTRAP_TOKEN},kubelet-bootstrap,10001,"system:kubelet-bootstrap"
EOF
```

## Client Authentication Configs

This section will walk you through creating kubeconfig files that will be used to bootstrap kubelets, which will then generate their own kubeconfigs based on dynamically generated certificates, and a kubeconfig for authenticating kube-proxy clients.

Each kubeconfig requires a Kubernetes master to connect to. To support H/A the IP address assigned to the load balancer sitting in front of the Kubernetes API servers will be used.

### Set the Kubernetes Public Address

```
KUBERNETES_PUBLIC_ADDRESS=YOUR-CONTROLLER0-IP
```

## Create client kubeconfig files

### Create the bootstrap kubeconfig file

```
kubectl config set-cluster kubernetes-the-hard-way \
  --certificate-authority=ca.pem \
  --embed-certs=true \
  --server=https://${KUBERNETES_PUBLIC_ADDRESS}:6443 \
  --kubeconfig=bootstrap.kubeconfig
```

```
kubectl config set-credentials kubelet-bootstrap \
  --token=${BOOTSTRAP_TOKEN} \
  --kubeconfig=bootstrap.kubeconfig
```

```
kubectl config set-context default \
  --cluster=kubernetes-the-hard-way \
  --user=kubelet-bootstrap \
  --kubeconfig=bootstrap.kubeconfig
```

```
kubectl config use-context default --kubeconfig=bootstrap.kubeconfig
```

### Create the kube-proxy kubeconfig


```
kubectl config set-cluster kubernetes-the-hard-way \
  --certificate-authority=ca.pem \
  --embed-certs=true \
  --server=https://${KUBERNETES_PUBLIC_ADDRESS}:6443 \
  --kubeconfig=kube-proxy.kubeconfig
```

```
kubectl config set-credentials kube-proxy \
  --client-certificate=kube-proxy.pem \
  --client-key=kube-proxy-key.pem \
  --embed-certs=true \
  --kubeconfig=kube-proxy.kubeconfig
```

```
kubectl config set-context default \
  --cluster=kubernetes-the-hard-way \
  --user=kube-proxy \
  --kubeconfig=kube-proxy.kubeconfig
```

```
kubectl config use-context default --kubeconfig=kube-proxy.kubeconfig
```

## Distribute the client kubeconfig files

```
worker0=YOUR-NODE-1-IP

worker1=YOUR-NODE-2-IP

for host in $worker0 $worker1; do
  scp bootstrap.kubeconfig kube-proxy.kubeconfig ${host}:~/
done
```