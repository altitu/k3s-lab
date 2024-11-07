# K3s Test Environment with Vagrant and Ansible

## Prerequisite

- A Vagrant provider, such as Virtualbox.
- Vagrant

## Project Structure

- `Vagrantfile`: Defines the VM environment with master and worker nodes.
- `ansible/`: Contains Ansible inventory, playbooks, and roles.
- `inventory/hosts.ini`: Inventory file defining master and worker IPs.
- `roles/`: Organized by tasks: `common`, `k3s-master`, and `k3s-worker`.

## Deployment Steps

1. Run `vagrant up` to create the VMs.
2. The Vagrantfile is configured to execute the playbooks according to the VMs groups.
(The VMs groups can be found in the Ansible inventory).

## Debugging

- If you would like to execute the Ansible playbooks to debug it (let's say, 'master.yml'), simply run
`ansible-playbook ansible/playbooks/master.yml -u vagrant -i ansible/inventory/hosts.ini -k`.
You will be prompted for a password, by default Vagrant uses `vagrant` for its user `vagrant`.
- To know the state of you VMs, you can run `vagrant status`

## Exploring the VMs

1. Connect to a node (let's say, 'master') and with `vagrant ssh master`
2. Run `k3s kubectl get node` on a node to list nodes.

## Stoping the VMs and reseting the VM

- To stop the VMs, run `vagrant halt`.
- To delete all data on the VMs, run `vagrant destroy -f`

The result will be a Kubernetes environment with K3s installed on the master and worker nodes.

# Tips
## Adding Grafana

- To automaticaly add the structure of the Grafana role, we placed ourselves in `ansible/roles` and Ran `ansible-galaxy init grafana`
