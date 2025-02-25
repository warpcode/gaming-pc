# Gaming PC

Ansible roles and scripts for setting up a Windows PC for gaming

## Pre-requisites

### Installing Windows 11

Pretty basic but if you're using older hardware, there are some tricks and hacks
to ensure you are able to install Windows 11.

#### Ignore Windows 11 requirements on install

If your hardware is old enough, you won't meet the minimum requirements
to install Windows.
However, this can be bypassed.

If you get the screen warning you about the minimum requirements,
you can run the code from

https://github.com/warpcode/gaming-pc/blob/136309a14f3293f02a2469227d0c4a8603a3a551/scripts/BypassWindows11Requirements.bat#L1-L6

#### What is this random install helper watermark remover??

[Magic????](https://github.com/massgravel/Microsoft-Activation-Scripts)

### Execution policy

Many scripts require an execution policy of at least RemoteSigned

First check what your policy is using

```powershell
Get-ExecutionPolicy
```

If this is restricted we can run the following to set it to RemoteSigned

```powershell
Set-ExecutionPolicy RemoteSigned -Force
```

Please note, Chocolate sets the policy to Bypass

### Chocolatey

Ansible relies on chocolatey for installing packages.
Whilst it will automatically install chocolatey if running locally, ideally any
pre-rquisites we need will also need to be installed via chocolatey.

For example to install ansible itself if we run on localhost of the system

For the latest instructions, refer to the chocolatey website

[https://chocolatey.org/install](https://chocolatey.org/install)

but for quick execution, the current method is the following

https://github.com/warpcode/gaming-pc/blob/9be8da3ea764ea8554ffdbd4863806c411089538/scripts/ProvisionWithChocolatey.ps1#L1-L2

### Local

As of writing this, ansible does not support being run directly on a Windows host.
We must use OpenSSH or WinRM

### SSH (if not using winrm)

This can be installed directly through powershell.

For the For the latest instructions, refer to the microsoft support page

[Enable SSH](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse?tabs=powershell&pivots=windows-server-2025#enable-openssh-for-windows-server-2025)

However, the following worked for me

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force;
winget install "openssh beta";
```

But trying the more official way below did not work for me.
sshd would be scheduled for removal and gone on reboot

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

## Emulation

Emulation is a tricky and complex thing to handle in ansible.
There are existing tools that make setting up emulation on Windows far easier including:

- [EmuDeck](https://www.emudeck.com/)
- [RetroBat](https://wiki.retrobat.org/)

For entire OS solutions there are projects like [Batocera](https://batocera.org)
