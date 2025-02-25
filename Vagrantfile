Vagrant.configure("2") do |config|
  ip_address = "192.168.56.50"
  host_port = 5985

  config.vm.box = "gusztavvargadr/windows-11"
  config.vm.hostname = "windows11"
  config.vm.network "private_network", ip: ip_address
  config.vm.boot_timeout = 600

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", 3078]
    vb.customize ["modifyvm", :id, "--cpus", 2]
    # vb.customize ["modifyvm", :id, "--vram", "64"]
  end

  # winrm
  ansible_vars = {
    ansible_host: "localhost",
    ansible_user: 'vagrant',
    ansible_password: 'vagrant',
    ansible_become_method: 'runas',
    ansible_become_user: 'vagrant',
    ansible_become_password: 'vagrant',
    ansible_shell_type: 'powershell',
    ansible_host: ip_address,
    # ansible_port: host_port,
    # ansible_connection: 'winrm',
    # ansible_winrm_transport: 'basic',
    ansible_connection: 'ssh',
    ansible_port: 22,
    # ansible_ssh_private_key_file: '.vagrant/machines/windows11/virtualbox/private_key',
  }

  # run a powershell script inline on the windows machine
  # config.vm.provision "shell", path: "scripts/ProvisionWithChocolatey.ps1"
  config.vm.provision "shell", path: "scripts/ProvisionWithOpenSSH.ps1"

  config.vm.provision "setup_windows", type: "ansible" do |ansible|
    ansible.playbook = './setup-windows.yml'
    ansible.limit    = 'vagrant'
    ansible.inventory_path = 'inventory.example'
    # ansible.verbose = "vvv"
    ansible.extra_vars = ansible_vars
  end

  config.vm.provision "setup_gaming", type: "ansible" do |ansible|
    ansible.playbook = './setup-gaming.yml'
    ansible.limit    = 'vagrant'
    ansible.inventory_path = 'inventory.example'
    # ansible.verbose = "vvv"
    ansible.extra_vars = ansible_vars
  end

  config.vm.provision "reboot", type: "ansible", run: "never" do |ansible|
    ansible.playbook = './reboot.yml'
    ansible.limit    = 'vagrant'
    ansible.inventory_path = 'inventory.example'
    # ansible.verbose = "vvv"
    ansible.extra_vars = ansible_vars
  end

  config.vm.provision "update", type: "ansible", run: "never" do |ansible|
    ansible.playbook = './install-updates.yml'
    ansible.limit    = 'vagrant'
    ansible.inventory_path = 'inventory.example'
    # ansible.verbose = "vvv"
    ansible.extra_vars = ansible_vars
  end
end
