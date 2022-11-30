# Steps to create your always free Virtual Machine in Oracle Cloud

Create an account in https://cloud.oracle.com/.

Go to https://cloud.oracle.com/compute/instances/create and create a VM with shape Ampere. You can set up to 4 OCPUs and 24 GBs of ram in their always free plan.

> **Warning**
> Don't forget to save the SSH private key to have SSH access later on.

---

Use k3sup to spin up k3s cluster in Oracle.

https://github.com/alexellis/k3sup

run the install script:

```bash

k3sup install \
  --ip=<vm-public-ip> \
  --user ubuntu \
  --sudo \
  --cluster \
  --k3s-channel=stable \
  --ssh-key ~/keys/oracle.key \
  --k3s-extra-args "--write-kubeconfig-mode=644 --kube-proxy-arg proxy-mode=ipvs --flannel-backend=none --disable-network-policy"

```

curl vm-public-ip:6443 to check if k3s is up and accessible.

(Run this if 6443 is not accessible, you will need to access the VM's shell).

```bash
sudo iptables -I INPUT -p tcp -m tcp --dport 6443 -j ACCEPT
sudo iptables -I INPUT -p tcp -m tcp --dport 443 -j ACCEPT
sudo iptables -I INPUT -p tcp -m tcp --dport 80 -j ACCEPT
sudo iptables -P INPUT ACCEPT
sudo iptables -P OUTPUT ACCEPT
sudo iptables -P FORWARD ACCEPT
sudo iptables -F
sudo iptables-save > iptables-backup
```

add calico
We disabled flannel networking while setting up cluster because we will use Calico.

```bash
kubectl apply -f https://projectcalico.docs.tigera.io/manifests/calico.yaml
```

---

To make valid SSL certificates:

https://medium.com/@mengkiatlim/how-to-add-ssl-to-your-services-k8s-704a6d2d5fd8

Private registry:

Helm definition

https://github.com/twuni/docker-registry.helm

Setup k3s.io private registry keys

https://docs.k3s.io/installation/private-registry
