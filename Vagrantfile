Vagrant.configure("2") do |config|
    ip_address = "192.168.56.50"
    host_port = 5859

    config.vm.box = "gusztavvargadr/windows-11"
    config.vm.hostname = "windows11"
    config.vm.network "private_network", ip: ip_address
    config.vm.boot_timeout = 600

    config.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--memory", 4096]
      vb.customize ["modifyvm", :id, "--cpus", 2]
      # vb.customize ["modifyvm", :id, "--vram", "64"]
    end

    ansible_vars = {
      ansible_host: "localhost",
      ansible_user: 'vagrant',
      ansible_password: 'vagrant',
      ansible_become_method: 'runas',
      ansible_become_user: 'vagrant',
      ansible_become_password: 'vagrant',
      ansible_host: ip_address,
      ansible_port: host_port,
      ansible_ssh_private_key_file: '.vagrant/machines/windows11/virtualbox/private_key',
    }

  # run a powershell script inline on the windows machine
  config.vm.provision "shell", path: "scripts/ProvisionWithChocolatey.ps1"

end
