# -*- mode: ruby -*-
# vi: set ft=ruby :

# From https://superuser.com/a/992220
module LocalCommand
    class Config < Vagrant.plugin("2", :config)
        attr_accessor :command
    end

    class Plugin < Vagrant.plugin("2")
        name "local_shell"

        config(:local_shell, :provisioner) do
            Config
        end

        provisioner(:local_shell) do
            Provisioner
        end
    end

    class Provisioner < Vagrant.plugin("2", :provisioner)
        def provision
            result = system "#{config.command}"
        end
    end
end

Vagrant.configure("2") do |config|

  config.vm.define "master" do |master|
    master.vm.box = "centos/7"
    master.vm.provider "hyperv" do |hv|
      hv.vmname = "K8s-master"
      hv.memory = 2048
      hv.maxmemory = 4096
      hv.cpus = 2
      hv.linked_clone = true
    end
    master.vm.network "public_network", bridge: "Default Switch"
    master.vm.synced_folder ".", "/vagrant", type: "rsync",
      rsync__exclude: ".git/"

    master.vm.hostname = "master.localdomain"
    master.vm.provision "shell", path: "disable-swap.sh"
    master.vm.provision "shell", path: "install-docker.sh"
    master.vm.provision "shell", path: "install-k8s.sh"
    master.vm.provision "shell", path: "setup-master.sh"
    # master.vm.provision "local_shell", command: "vagrant ssh  -c 'cat ~/.kube/config' master | out-file ~/.kube/config -encoding ascii"
  end


  config.vm.define "nodea" do |nodea|
    nodea.vm.box = "centos/7"
    nodea.vm.provider "hyperv" do |hv|
      hv.vmname = "K8s-nodea"
      hv.memory = 1024
      hv.maxmemory = 2048
      hv.cpus = 2
      hv.linked_clone = true
    end
    nodea.vm.synced_folder ".", "/vagrant", type: "rsync",
      rsync__exclude: ".git/"

    nodea.vm.hostname = "nodea.localdomain"
    nodea.vm.network "public_network", bridge: "Default Switch"

    nodea.vm.provision "shell", path: "disable-swap.sh"
    nodea.vm.provision "shell", path: "install-docker.sh"
    nodea.vm.provision "shell", path: "install-k8s.sh"
    nodea.vm.provision "shell", inline: "sh /vagrant/tmp/join.sh"
  end


  config.vm.define "nodeb" do |nodeb|
    nodeb.vm.box = "centos/7"
    nodeb.vm.provider "hyperv" do |hv|
      hv.vmname = "K8s-nodeb"
      hv.memory = 1024
      hv.maxmemory = 2048
      hv.cpus = 2
      hv.linked_clone = true
    end
    nodeb.vm.synced_folder ".", "/vagrant", type: "rsync",
      rsync__exclude: ".git/"

    nodeb.vm.hostname = "nodeb.localdomain"
    nodeb.vm.network "public_network", bridge: "Default Switch"

    nodeb.vm.provision "shell", path: "disable-swap.sh"
    nodeb.vm.provision "shell", path: "install-docker.sh"
    nodeb.vm.provision "shell", path: "install-k8s.sh"
    nodeb.vm.provision "shell", inline: "sh /vagrant/tmp/join.sh"
  end

end