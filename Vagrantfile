Vagrant.configure("2") do |config|
  # Use Rocky Linux as the base image
  config.vm.box = "rockylinux/9"
  config.vm.box_check_update = true  # Check for updates each time

  # Define the master node
  config.vm.define "master" do |master|
    master.vm.hostname = "k3s-master"
    master.vm.network "private_network", ip: "192.168.56.10"
    master.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
      vb.cpus = 2
    end

    # Provisioning with Ansible
    master.vm.provision "ansible" do |ansible|
      ansible.playbook = "ansible/playbooks/master.yml"
      ansible.inventory_path = "ansible/inventory/hosts.ini"
      ansible.limit = "master"
      # ansible.extra_vars = { "node_role": "master" }
    end
  end

  N = 2
  # Define the worker nodes
  (1..N).each do |i|
    config.vm.define "worker#{i}" do |worker|
      worker.vm.hostname = "k3s-worker#{i}"
      worker.vm.network "private_network", ip: "192.168.56.#{10 + i}"
      worker.vm.provider "virtualbox" do |vb|
        vb.memory = "1024"
        vb.cpus = 2
      end

      # Provisioning with Ansible: Only execute once the Ansible
      # provisioner, when all the workers are up and ready.
      if i == N
        worker.vm.provision "ansible" do |ansible|
          ansible.playbook = "ansible/playbooks/workers.yml"
          ansible.inventory_path = "ansible/inventory/hosts.ini"
          ansible.limit = "all"
          # ansible.extra_vars = { "node_role": "worker" }
        end
      end
    end
  end
end
