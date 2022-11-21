# Quick launch guide

## Run Ansible playbooks below

```shell
ansible-playbook -i ~/Kubernetes/Inventories/hosts-commnon-user.yaml ~/Kubernetes/Cluster/user.yaml
```

```shell
ansible-playbook -i ~/Kubernetes/Inventories/hosts-commnon-user.yaml ~/Kubernetes/Cluster/dependensies.yaml
```

```shell
ansible-playbook -i ~/Kubernetes/Inventories/hosts-k8s-user.yaml ~/Kubernetes/Cluster/master.yaml
```

```shell
ansible-playbook -i ~/Kubernetes/Inventories/hosts-k8s-user.yaml ~/Kubernetes/Cluster/workers.yaml
```
