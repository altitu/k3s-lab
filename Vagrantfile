Vagrant.configure("2") do |config|
  # Use Rocky Linux as the base image
  config.vm.box = "rockylinux/9"
  config.vm.box_url = "https://dl.rockylinux.org/vault/rocky/9.4/images/x86_64/Rocky-9-Vagrant-Vbox-9.4-20240509.0.x86_64.box"
  config.vm.box_check_update = true  # Check for updates each time

  # Define the master node
  config.vm.define "master" do |master|
    master.vm.hostname = "k3s-master"
    master.vm.network "private_network", ip: "192.168.56.10"
    master.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
      vb.cpus = 2
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
    end
  end
end
