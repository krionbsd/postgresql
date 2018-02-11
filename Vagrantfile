# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure('2') do |config|

  # Ensure we use our vagrant private key

    #machine.vm.box = "bento/ubuntu-16.04"
    # machine.vm.box = "ubuntu/trusty64"
    #machine.vm.box = "ubuntu/precise64"
    #machine.vm.box = "debian/jessie64"
    #machine.vm.box = "debian/wheezy64"
    #config.vm.box = "centos/7"
    config.vm.box = "generic/oracle7"
    #config.vm.box = "centos/6"

  # Fail if vagrant-vbguest if not installed.
  # vagrant plugin install vagrant-vbguest
  unless Vagrant.has_plugin?('vagrant-vbguest')
    raise 'vagrant-vbguest not installed!'
  end

  # Customize the hostsmanager ip_resolver
  # to retrive the proper ip address from the
  # guest.
  if Vagrant.has_plugin?("vagrant-hostmanager")
    config.hostmanager.ip_resolver = proc do |machine|
      result = ""
      machine.communicate.execute("ip addr | grep 'dynamic' | tail -1") do |type, data|
        result << data if type == :stdout
      end
      (ip = /inet (\d+\.\d+\.\d+\.\d+)/.match(result)) && ip[1]
    end
  end
  config.vbguest.auto_update = false
  config.vm.box_check_update = false

  config.vm.network "private_network", type: "dhcp"
  config.vm.synced_folder ".", "/vagrant", disabled: true
  # Virtualbox specific settings.
  config.vm.provider "virtualbox" do |vb|
    # Customize the amount of memory on the VM
    vb.memory = "1024"
  end
  # if vagrant-hostsupdater is installed,
  # automatically update the /etc/hosts file
  # vagrant plugin install vagrant-hostmanager
  if Vagrant.has_plugin?("vagrant-hostmanager")
    config.hostmanager.enabled = true
    config.hostmanager.manage_host = true
    config.hostmanager.manage_guest = true
  end

  VMS = [ "postgresql" ]

  groups = {
    "vagrant" => [ "postgresql" ]
  }

  VMS.each do |vm|
    config.vm.define "%s" % vm do |postgresql|

  postgresql.vm.hostname = vm

      if Vagrant.has_plugin?("vagrant-hostmanager")
        postgresql.hostmanager.aliases = [ "%s.dev" % vm.delete("postgresql") ]
      end

      postgresql.vm.provision :ansible do |ansible|
      ansible.playbook = 'playbook.yml'
      ansible.groups = groups
      ansible.limit = "all"
      ansible.raw_arguments = ['-v', '--diff']
      ansible.raw_ssh_args = ['-o ControlMaster=no']
         end
       end

     end
   end

