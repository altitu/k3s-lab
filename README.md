# K3s Test Environment with Vagrant and Ansible

## Project Structure

- `Vagrantfile`: Defines the VM environment with master and worker nodes.
- `ansible/`: Contains Ansible inventory, playbooks, and roles.
- `inventory/hosts.ini`: Inventory file defining master and worker IPs.
- `roles/`: Organized by tasks: `common`, `k3s-master`, and `k3s-worker`.

## Deployment Steps

1. Run `vagrant up` to create the VMs.
2. Execute the Ansible playbooks:
   - `ansible-playbook playbooks/master.yml`
   - `ansible-playbook playbooks/workers.yml`

The result will be a Kubernetes environment with K3s installed on the master and worker nodes.
