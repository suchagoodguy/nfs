# -*- mode: ruby -*-
# vim: set ft=ruby :

Vagrant.configure("2") do |config|

    config.vm.define "nfsserver" do |node|
      node.vm.box = "centos/7"
      node.vm.hostname = "nfsserver"
      node.vm.network "private_network", ip: "192.168.10.10"
      node.vm.provider :virtualbox do |vb|
        vb.customize ["modifyvm", :id, "--memory", "256"]
        vb.customize ["modifyvm", :id, "--cpus", "1"]
      end
      node.vm.provision "shell", inline: <<-SHELL
        mkdir -p ~root/.ssh; cp ~vagrant/.ssh/auth* ~root/.ssh
        yum install -y epel-release
        yum install -y mc ntp nfs-utils
        systemctl enable --now nfs-server
        mkdir -p /srv/nfs/upload
        chmod 777 /srv/nfs/upload
        echo "/srv/nfs	192.168.10.20(rw,sync,no_root_squash,no_all_squash)" >> /etc/exports
        exportfs -rav
        systemctl enable --now firewalld
        firewall-cmd --add-service=nfs3 --permanent
        firewall-cmd --add-service=rpc-bind --permanent
        firewall-cmd --add-service=mountd --permanent
        firewall-cmd --reload
      SHELL
    end

    config.vm.define "nfsclient" do |node|
      node.vm.box = "centos/7"
      node.vm.hostname = "nfsclient"
      node.vm.network "private_network", ip: "192.168.10.20"
      node.vm.provider :virtualbox do |vb|
        vb.customize ["modifyvm", :id, "--memory", "256"]
        vb.customize ["modifyvm", :id, "--cpus", "1"]
      end
      node.vm.provision "shell", inline: <<-SHELL
        mkdir -p ~root/.ssh; cp ~vagrant/.ssh/auth* ~root/.ssh
        yum install -y epel-release
        yum install -y mc ntp nfs-utils
        mkdir -p /mnt/nfs
        echo "192.168.10.10:/srv/nfs	/mnt/nfs	nfs	defaults,nfsvers=3,udp,x-systemd.automount	0	0" >> /etc/fstab
        mount -a
      SHELL
    end

end
