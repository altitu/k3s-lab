Vagrant.configure("2") do |config|
  # Define the master node
  config.vm.define "master" do |master|
    master.vm.box = "rockylinux/9"
    master.vm.hostname = "k3s-master"
    master.vm.network "private_network", type: "dhcp"
    master.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
      vb.cpus = 2
    end
  end

  # Define worker nodes
  (1..2).each do |i|
    config.vm.define "worker#{i}" do |worker|
      worker.vm.box = "rockylinux/9"
      worker.vm.hostname = "k3s-worker#{i}"
      worker.vm.network "private_network", type: "dhcp"
      worker.vm.provider "virtualbox" do |vb|
        vb.memory = "1024"
        vb.cpus = 2
      end
    end
  end
end
