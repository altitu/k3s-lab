# K3s Test Environment with Vagrant and Ansible

This project aim at perfectioning oneself with Ansible and/or a Kubernetes environment.

It showcases a k3s test environment locally (1 master node and 2 worker nodes), deployed via Ansible in three Vagrant managed VM.

## Prerequisite

- Ansible
- A Vagrant provider, such as Virtualbox.
- Vagrant

Run `ansible-galaxy collection install -r ansible/requirements.yml` as it will allow us to utilize `helm` with Ansible.

## Deployment Steps

1. Run `ansible-playbook playbook.yml --skip-tags destroy` to create the VMs and run the deployment.
Please note that your local kubeconfig will be modified to include information about our newly deployed virtual cluster.
2. The Vagrantfile is configured to provision a defined number of VM: you can modify it if needed.

As Is, the project would use around 3.5Gio of ram and 6 vcpu.
(The VMs groups can be found in the Ansible inventory).

## Project Structure

- `Vagrantfile`: Defines the VM environment with master and worker nodes.
- this folder: Contains Ansible inventory, playbooks, and roles.
- `inventory/hosts.ini`: Inventory file defining master and worker IPs.
- `roles/`: Organized by tasks: `common`, `k3s-master`, `k3s-worker` and others.

## Debugging

- If you would like to execute a part of the deployment, or simply to test your changes, I would advise you to use Ansible tags inside playbook.yml
such as `ansible-playbook playbook.yml --tags start,k3s-config,argocd-config`.
You can also skip a tag on which you are working on with `--skip-tags` instead.
- To know the state of you VMs, you can run `vagrant status`
- To test the grafana installation, you can `vagrant ssh master` then `kubectl get pods -A | grep grafana`
- To assess the presence of grafana

Known issue (maybe fixed ?): If you are frequently destroying and rebuilding the VMs with Virtualbox as a Vagrant backend, you may face an error stating that your ram is too low.
You should in this case either restart (impractical) or drop the kernel cache `echo 3 | sudo tee /proc/sys/vm/drop_caches` (less impractical).

## Exploring the VMs

1. Connect to a node (let's say, 'master') and with `vagrant ssh master`
2. Run `k3s kubectl get node` on a node to list nodes.

Or, alternatively, you can use kubectl on your localhost.

## Stoping the VMs and reseting the VM

- Run `ansible-playbook playbook.yml --tags destroy` to halt and destroy the VMs.

or, alternatively

- To stop the VMs, run `vagrant halt`.
- To delete all data on the VMs, run `vagrant destroy -f`

The result will be a Kubernetes environment with K3s installed on the master and worker nodes.

# Tips

To forward a service to an address, you can run (for instance, with Grafana):
- `kubectl port-forward svc/grafana 8080:80 --address 127.0.0.1 -n monitoring`

To forward it to the master node address accessible through our network via the Virtualbox bridge:
- `kubectl port-forward svc/grafana 8080:80 --address 192.168.56.10 -n monitoring`
You can now navigate to http://192.168.51.10:8080/ from your host machine.

To get grafana credentials from its secret:
- `kubectl get secrets -n monitoring grafana --template='{{ index .data "admin-user" | base64decode }}'`.
- `kubectl get secrets -n monitoring grafana --template='{{ index .data "admin-password" | base64decode }}'`.
Default credentials for Grafana are currently set to admin:password.

# Architectural choices:

Because the goal of this project is to learn and be as straightforward as possible, tls has not been configured, nor have been domains.
Instead, everything is served through ingress / ingressroute on the ip master node (192.168.56.10).

Vagrant need eth0 for its Nat (when we do `vagrant ssh master`) so flannel is configured to use eth1.

- the ansible.cfg file automatically specify `-u vagrant -i inventory/hosts.ini` and there is no need for `-k` because of the vagrant ssh private_key defined in the inventory.
- To automaticaly add the structure of the Grafana role, we placed ourselves in `ansible/roles` and Ran `ansible-galaxy init grafana`.
- If you wanted to install another collection to your playbook, you could do so by running `ansible-galaxy collection install kubernetes.core` for example. To delete it, run 2 commandes: `rm -rf ~/.ansible/collections/ansible_collections/kubernetes.core-*.info` and `rm -rf ~/.ansible/collections/ansible_collections/kubernetes/core`.

# Todo:
- Deploy everything through Argo CD
- Deploy Argo CD with an Helm chart instead (it will be easier than what it is currently on the argocd branch)
