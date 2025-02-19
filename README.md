# Gaming PC

Ansible roles and scripts for setting up a Windows PC for gaming

## Pre-requisites

### Chocolatey

Ansible relies on chocolatey for installing packages.
Whilst it will automatically install chocolatey if running locally, ideally any
pre-rquisites we need will also need to be installed via chocolatey.

For example to install ansible itself if we run on localhost of the system

For the latest instructions, refer to the chocolatey website

[https://chocolatey.org/install](https://chocolatey.org/install)

but for quick execution, the current method is the following

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force;
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

### Ansible (for local only running)

To install ansible directly, we must install python first

```powershell
choco install python3
```

For the latest For the latest instructions, refer to the ansible install
[documentation](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-and-upgrading-ansible-with-pip)

Then we have install ansible via pip

```powershell
    # If available but sometimes windows store will hijack it
    python3 -m pip install ansible

    # If so, find the exact version installed by choco
    python3.13 -m pip install ansible
```

### SSH (if not using localhost or winrm)

This can be installed directly through powershell.

For the For the latest instructions, refer to the microsoft support page

[Enable SSH](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse?tabs=powershell&pivots=windows-server-2025#enable-openssh-for-windows-server-2025)

but for quick execution, the current method is the following

```powershell
    # Check if openssh server is installed
    Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'

    # If it's not present, install it
    Add-WindowsCapability -Online -Name OpenSSH.Server;

     # Now start it
    Start-Service sshd;

    # To automatically run at startup
    Set-Service -Name sshd -StartupType 'Automatic';

    # Confirm the Firewall rule is configured. It should be created automatically by setup. Run the following to verify
    if (!(Get-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -ErrorAction SilentlyContinue | Select-Object Name, Enabled)) {
        Write-Output "Firewall Rule 'OpenSSH-Server-In-TCP' does not exist, creating it..."
        New-NetFirewallRule -Name 'OpenSSH-Server-In-TCP' -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
    } else {
        Write-Output "Firewall rule 'OpenSSH-Server-In-TCP' has been created and exists."
    }
```
