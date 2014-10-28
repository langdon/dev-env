# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure('2') do |config|
  # Example configuration of new VM..
  config.vm.define :rhel7_dev do |vagrant_host|
    # Box name
    vagrant_host.vm.box = 'rhel-7.0'
    vagrant_host.vm.hostname = "vagrant-host"

    # Domain Specific Options
    vagrant_host.vm.provider :libvirt do |domain|
      domain.memory = 2048
      domain.cpus = 1
    end
    vagrant_host.vm.network :private_network,
        :libvirt__network_name => 'either_nat'

    password = ENV["SUBSCRIPTION_MANAGER_PASSWORD"]
    
    vagrant_host.vm.provision 'shell', inline: "sudo grep devel /etc/yum.repos.d/redhat.repo > /dev/null && rm /etc/yum.repos.d/redhat.repo || :" #cleaning up in case the box has a bad repo file
    vagrant_host.vm.provision 'shell', inline: "sudo subscription-manager status | grep 'Current' || sudo subscription-manager register --username rht-devsuite --password #{password} --auto-attach"
    vagrant_host.vm.provision 'shell', inline: "sudo subscription-manager repos --enable rhel-7-server-extras-rpms"
    vagrant_host.vm.provision 'shell', inline: "sudo yum -y install bash-completion bind-utils setroubleshoot python-pip system-storage-manager"
    vagrant_host.vm.provision 'shell', inline: "sudo yum -y groupinstall 'Development Tools'"
    vagrant_host.vm.provision 'shell', inline: "sudo yum -y install docker"
    vagrant_host.vm.provision 'shell', inline: "sudo systemctl stop docker" #in case this isn't first run
    vagrant_host.vm.provision 'shell', inline: "sudo groupadd docker > /dev/null 2>&1 || : "
    vagrant_host.vm.provision 'shell', inline: "sudo usermod -a -G docker vagrant"

#[vagrant@vagrant-host eclipse-orion]$ git config --global user.email "langdon@fedoraproject.org"
#[vagrant@vagrant-host eclipse-orion]$ git config --global user.name "langdon"
#[vagrant@vagrant-host eclipse-orion]$ mv /vagrant/github-id_rsa ~/.ssh/
#[vagrant@vagrant-host eclipse-orion]$ mv /vagrant/config ~/.ssh/

#gonna worry about this monkey business later, hate that I can't share disk storage, but whatever
#nfs is not letting me control permissions enough on these files
#9p doesn't work
#    vagrant_host.vm.provision 'shell', inline: "if [[ ! -h /var/lib/docker ]]; then sudo mv /var/lib/docker /tmp/; fi"
#    vagrant_host.vm.provision 'shell', inline: "if [[ ! -h /var/lib/docker ]]; then sudo mv /var/lib/docker /mnt/host-vms/vagrant-vms-docker/`hostname`-docker; fi"
#    vagrant_host.vm.provision 'shell', inline: "sudo mkdir /mnt/host-vms/vagrant-vms-docker/`hostname`-docker && sudo ln -sf /mnt/host-vms/vagrant-vms-docker/`hostname`-docker /var/lib/docker"
    vagrant_host.vm.provision 'shell', inline: "sudo systemctl enable docker && sudo systemctl start docker"
    vagrant_host.vm.provision 'shell', inline: "sudo chown root:docker /var/run/docker.sock"
#    vagrant_host.vm.provision 'shell', inline: "git clone https://github.com/baude/docker-utils.git"
    
#

  end
  
  config.vm.synced_folder '/mnt/vms', '/mnt/host-vms', type: 'nfs'
  config.vm.synced_folder '/mnt/nbu/loc-projects', '/mnt/host-projects', type: 'nfs'


  # config.vm.provision "shell" do |s|
  #   s.inline = "sudo grep devel /etc/yum.repos.d/redhat.repo > /dev/null && rm /etc/yum.repos.d/redhat.repo || :" #cleaning up in case the box has a bad repo file
  #   s.inline = "sudo subscription-manager status | grep 'Current' || sudo subscription-manager register --username rht-devsuite --password #{password} --auto-attach"
  #   s.inline = "sudo subscription-manager repos --enable rhel-7-server-extras-rpms"
  #   s.inline = "sudo yum -y install bash-completion bind-utils setroubleshoot"
  #   s.inline = "sudo yum -y groupinstall 'Development Tools'"
  #   s.inline = "sudo yum -y install docker"
  #   s.inline = "sudo systemctl stop docker" #in case this isn't first run
  #   s.inline = "sudo groupadd docker > /dev/null 2>&1 || : "
  #   s.inline = "sudo usermod -a -G docker vagrant"
  #   s.inline = "if [[ ! -h /var/lib/docker ]]; then sudo mv /var/lib/docker /mnt/host-vms/rhel7-docker; fi"
  #   s.inline = "sudo ln -sf /mnt/host-vms/rhel7-docker /var/lib/docker"
  #   s.inline = "sudo systemctl enable docker && sudo systemctl start docker"
  #   s.inline = "sudo chown root:docker /var/run/docker.sock"
  # end
  
  # Options for libvirt vagrant provider.
  config.vm.provider :libvirt do |libvirt|
    libvirt.driver = 'kvm'
    libvirt.connect_via_ssh = false
    libvirt.username = 'root'
    libvirt.storage_pool_name = 'mnt_vms'
  end
end

