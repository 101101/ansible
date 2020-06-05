# basic setup and config  
https://docs.ansible.com/ansible/latest/user_guide/windows_setup.html  

## Windows  
  * check $PSVersionTable.PSVersion  >= 3.0  
  * install WinRM  
    * install the hotfix  
    * setup WinRM  
  * setup connection - ssh  
  * install -  
    * ansible  
    * chocolatey  
    * win_rm  


### WinRM Memory hotfix  

```ps1
$url = "https://raw.githubusercontent.com/jborean93/ansible-windows/master/scripts/Install-WMF3Hotfix.ps1"
$file = "$env:temp\Install-WMF3Hotfix.ps1"

(New-Object -TypeName System.Net.WebClient).DownloadFile($url, $file)
powershell.exe -ExecutionPolicy ByPass -File $file -Verbose
```

### WinRM setup  
> NOTE: The ConfigureRemotingForAnsible.ps1 script is intended for training and development purposes only and should not be used in a production environment, since it enables settings (like Basic authentication) that can be inherently insecure.  

```ps1
$url = "https://raw.githubusercontent.com/ansible/ansible/devel/examples/scripts/ConfigureRemotingForAnsible.ps1"
$file = "$env:temp\ConfigureRemotingForAnsible.ps1"

(New-Object -TypeName System.Net.WebClient).DownloadFile($url, $file)

powershell.exe -ExecutionPolicy ByPass -File $file
```

This sets up the WinRM Service on port `5986` for HTTPS  

### WinRM Listener setup  

```ps1
$selector_set = @{
    Address = "*"
    Transport = "HTTPS"
}
$value_set = @{
    CertificateThumbprint = "E6CDAA82EEAF2ECE8546E05DB7F3E01AA47D76CE"
}

New-WSManInstance -ResourceURI "winrm/config/Listener" -SelectorSet $selector_set -ValueSet $value_set
```

### validate settings
`winrm get winrm/config/Service`  
`winrm get winrm/config/Winrs`  

Should look similar to this - 
```sh
Service
    RootSDDL = O:NSG:BAD:P(A;;GA;;;BA)(A;;GR;;;IU)S:P(AU;FA;GA;;;WD)(AU;SA;GXGW;;;WD)
    MaxConcurrentOperations = 4294967295
    MaxConcurrentOperationsPerUser = 1500
    EnumerationTimeoutms = 240000
    MaxConnections = 300
    MaxPacketRetrievalTimeSeconds = 120
    AllowUnencrypted = false
    Auth
        Basic = true
        Kerberos = true
        Negotiate = true
        Certificate = true
        CredSSP = true
        CbtHardeningLevel = Relaxed
    DefaultPorts
        HTTP = 5985
        HTTPS = 5986
    IPv4Filter = *
    IPv6Filter = *
    EnableCompatibilityHttpListener = false
    EnableCompatibilityHttpsListener = false
    CertificateThumbprint
    AllowRemoteAccess = true

Winrs
    AllowRemoteShellAccess = true
    IdleTimeout = 7200000
    MaxConcurrentUsers = 2147483647
    MaxShellRunTime = 2147483647
    MaxProcessesPerShell = 2147483647
    MaxMemoryPerShellMB = 2147483647
    MaxShellsPerUser = 2147483647
```

## install chocolatey  

## install openssh  
`choco install openssh`  

IF `ssh-agent` isn't starting, you can validate startup in powershell  
by typing in `Get-Service ssh-agent | Select StartType`  
and you can modify it by typing `Get-Service -Name ssh-agent | Set-Service -StartupType Manual`  

Now, add your ssh key -  
`ssh-add ~\.ssh\<key file name>`  

```ps1
# Make sure that the .ssh directory exists in your server's home folder
ssh user1@domain1@contoso.com mkdir C:\users\user1\.ssh\

# Use scp to copy the public key file generated previously to authorized_keys on your server
scp C:\Users\user1\.ssh\id_ed25519.pub user1@domain1@contoso.com:C:\Users\user1\.ssh\authorized_keys

# Appropriately ACL the authorized_keys file on your server  
ssh --% user1@domain1@contoso.com powershell -c $ConfirmPreference = 'None'; Repair-AuthorizedKeyPermission C:\Users\user1\.ssh\authorized_keys
```

# install pywinrm on the ansible controller  

```sh
pip install pywinrm
```

setup your ansible host file
```sh
[win]
xxx.xxx.xxx.xxx

[win:vars]
ansible_user=username
ansible_password=password
ansible_connection=winrm
ansible_winrm_server_cert_validation=ignore

```




---

# validation  

You can verify the connection with this:
```sh
ansible windows -i hosts -m win_say -a "msg='Hi! This is a demo' start_sound_path='C:\\windows\\media\\ding.wav' speech_speed=2"
```

### Test with win_chocolatey  

```yml
---
- hosts: win
  gather_facts: no
  tasks:
  - win_chocolatey:
      name: procexp
      state: present

```
