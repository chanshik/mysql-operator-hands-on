# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.box_check_update = false
  node_subnet = "10.254.1"

  (1..3).each do |i|
    config.vm.define "k8s-mysql-#{i}" do |node|
      node.vm.hostname = "k8s-mysql-#{i}"
      node.vm.network "private_network", ip: "#{node_subnet}.#{i + 1}"

      node.vm.provider "virtualbox" do |vb|
        vb.name = "k8s-mysql-#{i}"
        vb.gui = false

        vb.cpus = 2
        vb.memory = "2048"
      end

      node.vm.provision "bootstrap", type: "shell", inline: <<-SHELL
        sudo curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
        sudo bash -c 'cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF'
        sudo apt update
        sudo apt install -y docker.io kubelet kubeadm kubectl
        sudo usermod -aG docker vagrant
        sudo systemctl enable docker.service

        sudo sed -i '/k8s/d' /etc/hosts
        sudo echo "#{node_subnet}.#{i + 1} k8s-mysql-#{i}" | sudo tee -a /etc/hosts
      SHELL
    end
  end
end
