# vim: set ft=ruby ts=2 sw=2 et:
# -*- mode: ruby -*-


VAGRANT_API_VERSION = '2'
Vagrant.configure(VAGRANT_API_VERSION) do |config|

  config.vm.box = 'centos/7'
  config.vm.box_check_update = false

  config.vm.define :kubernetes do |d|

    d.vm.hostname = 'kubernetes'
    d.vm.synced_folder '.', '/vagrant', id: 'vagrant-root', disabled: true

    (8080..8089).each do |port|
      d.vm.network 'forwarded_port', host: port, guest: port
    end
    d.vm.network 'forwarded_port', host: 9090, guest: 9090
    d.vm.network 'forwarded_port', host: 8079, guest: 443
    d.vm.network 'forwarded_port', host: 3000, guest: 3000
    d.vm.network 'forwarded_port', host: 3001, guest: 3001
    d.vm.network 'forwarded_port', host: 3002, guest: 3002
    d.vm.network 'forwarded_port', host: 3003, guest: 3003


    d.vm.provision :ansible do |ansible|
      ansible.playbook = 'ansible/playbook.yml'
      ansible.tags = ENV['ANSIBLE_TAGS']
      ansible.groups = {
        'vagrant' => ['kubernetes']
      }
      ansible.limit = 'vagrant'

      ::File.directory?('.vagrant/provisioners/ansible/inventory/') do
        ansible.inventory_path = '.vagrant/provisioners/ansible/inventory/'
      end

    end

    d.vm.provider :virtualbox do |v|
      v.customize 'pre-boot', ['modifyvm', :id, '--nictype1', 'virtio']
      v.customize [ 'modifyvm', :id, '--name', 'kubernetes', '--memory', '6096', '--cpus', '1' ]
    end

  end
end
