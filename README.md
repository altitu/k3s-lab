# K3s Test Environment with Vagrant and Ansible

## Prerequisite

- A Vagrant provider, such as Virtualbox.
- Vagrant
- Run `ansible-galaxy collection install -r ansible/requirements.yml` as it will allow us to utilize `helm` with Ansible

## Project Structure

- `Vagrantfile`: Defines the VM environment with master and worker nodes.
- this folder: Contains Ansible inventory, playbooks, and roles.
- `inventory/hosts.ini`: Inventory file defining master and worker IPs.
- `roles/`: Organized by tasks: `common`, `k3s-master`, `k3s-worker` and others.

## Deployment Steps

1. Run `ansible-playbook ansible/playbook.yml --skip-tags destroy` to create the VMs and run the deployment.
2. The Vagrantfile is configured provisione a defined number of VM.
(The VMs groups can be found in the Ansible inventory).

## Debugging

- If you would like to execute a part of the deployment, or simply to test your changes, you can, simply run
`ansible-playbook ansible/playbooks/master.yml`.
You will be prompted for a password, by default Vagrant uses `vagrant` for its user `vagrant`.
- To know the state of you VMs, you can run `vagrant status`
- To test the grafana installation, you can `vagrant ssh master` then `kubectl get pods -A | grep grafana`
- To assess the presence of grafana

## Exploring the VMs

1. Connect to a node (let's say, 'master') and with `vagrant ssh master`
2. Run `k3s kubectl get node` on a node to list nodes.

## Stoping the VMs and reseting the VM

- To stop the VMs, run `vagrant halt`.
- To delete all data on the VMs, run `vagrant destroy -f`

The result will be a Kubernetes environment with K3s installed on the master and worker nodes.

# Tips
## Adding Grafana
- `kubectl port-forward svc/grafana 8080:80 --address 127.0.0.1 -n monitoring`
to get grafana credentials:
- `kubectl get secrets -n monitoring grafana --template='{{ index .data "admin-user" | base64decode }}'`.
- `kubectl get secrets -n monitoring grafana --template='{{ index .data "admin-password" | base64decode }}'`.

Vagrant need eth0 for its Nat (when we do `vagrant ssh master`) so flannel is configured to use eth1.

- the ansible.cfg file automatically specify `-u vagrant -i ansible/inventory/hosts.ini` and there is no need for `-k` because of the vagrant ssh private_key defined in the inventory.
- To automaticaly add the structure of the Grafana role, we placed ourselves in `ansible/roles` and Ran `ansible-galaxy init grafana`.
- If you wanted to install another collection to your playbook, you could do so by running `ansible-galaxy collection install kubernetes.core` for example. To delete it, run 2 commandes: `rm -rf ~/.ansible/collections/ansible_collections/kubernetes.core-*.info` and `rm -rf ~/.ansible/collections/ansible_collections/kubernetes/core`.