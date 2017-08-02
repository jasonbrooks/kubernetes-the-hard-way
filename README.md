# Kubernetes The Hard Way

This tutorial is an adaptation of Kelsey Hightower's popular howto for running Kubernetes on Google Compute Engine, modified to use with Fedora or CentOS Atomic Host.

As with Kelsey's original tutorial, this guide will walk you through setting up Kubernetes the hard way. This guide is not for people looking for a fully automated command to bring up a Kubernetes cluster. If that's you then check out [these Ansible scripts](https://github.com/kubernetes/contrib/tree/master/ansible).

This tutorial is optimized for learning, which means taking the long route to help people understand each task required to bootstrap a Kubernetes cluster.

> The results of this tutorial should not be viewed as production ready, and may receive limited support from the community, but don't let that prevent you from learning!

## Target Audience

The target audience for this tutorial is someone planning to support a production Kubernetes cluster and wants to understand how everything fits together. After completing this tutorial I encourage you to automate away the manual steps presented in this guide.

## Cluster Details

* Kubernetes 1.7.0
* Docker 1.12.6
* etcd 3.1.4
* [CNI Based Networking](https://github.com/containernetworking/cni)
* Secure communication between all components (etcd, control plane, workers)
* Default Service Account and Secrets
* [RBAC authorization enabled](https://kubernetes.io/docs/admin/authorization)
* [TLS client certificate bootstrapping for kubelets](https://kubernetes.io/docs/admin/kubelet-tls-bootstrapping)
* DNS add-on

### What's Missing

The resulting cluster will be missing the following features:

* Cloud Provider Integration
* [Logging](https://kubernetes.io/docs/concepts/cluster-administration/logging/)
* [Cluster add-ons](https://github.com/kubernetes/kubernetes/tree/master/cluster/addons)

## Labs

* [Infrastructure Provisioning](docs/01-infrastructure.md)
* [Setting up a CA and TLS Cert Generation](docs/02-certificate-authority.md)
* [Setting up TLS Client Bootstrap and RBAC Authentication](docs/03-auth-configs.md)
* [Bootstrapping an etcd cluster](docs/04-etcd.md)
* [Bootstrapping a Kubernetes Control Plane](docs/05-kubernetes-controller.md)
* [Bootstrapping Kubernetes Workers](docs/06-kubernetes-worker.md)
* [Configuring the Kubernetes Client - Remote Access](docs/07-kubectl.md)
* [Managing the Container Overlay Network](docs/08-network.md)
* [Deploying the Cluster DNS Add-on](docs/09-dns-addon.md)
* [Smoke Test](docs/10-smoke-test.md)
