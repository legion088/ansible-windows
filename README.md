### Example of the work Ansible. Ansible-Windows. Working with inventory and playbook.  

1. Install and configure Ansible on Oracle Linux 9 VM
2. Configure windows hosts
3. Finally, create 1 playbook for windows configuration:

* Allow Firewall to accept connections on port 22
* Create 1 text file with any content by path - C:\ansible\some.txt
* The ansible inventory and playbook should be stored at Centos7 VM in home directory of root user.

### Install configure Ansible on Oracle Linux 9
1. Installing packages:
* dnf update
* dnf install epel-release
* dnf install ansible
* dnf install python3-winrm.noarch
2. Checking ansible:
* ansible --version
3. Can create an inventory and playbooks

### Configure Windows
1. Checking if WinRM is running:
>powershell command: `winrm enumerate winrm/config/listener` or check status of the service `Windows Remote Management (WS-Management)`

>to enable WinRM run service `Windows Remote Management (WS-Management)` or powershell command: `winrm quickconfig`
2. Add Firewall rules (it is usually enabled by default):
>Firewall - Inbounds - Create a Rule - Predefined - Windows Remote Management - Windows Remote Management (HTTP - ...) set enable
3. Create a certificate, be sure to specify the local DNS (ipconfig /all), and copy the Thumbprint:
>`New-SelfSignedCertificate -DnsName "192.168.1.1" -CertStoreLocation Cert:\LocalMachine\My`
  
4. Enabling HTTPS Listener. Linking the DNS address and Thumbprint earlier:
>`winrm create winrm/config/Listener?Address=*+Transport=HTTPS '@{Hostname="192.168.1.1"; CertificateThumbprint="04E5SAFDJ23283238SADNA"}'`

> looking at the Thumbprint binding for https: `winrm e winrm/config/Listener`
5. Adding a rule to FW for HTTPS-IN:
>`netsh advfirewall firewall add rule name="Windows Remote Management (HTTPS-In)" dir=in action=allow protocol=TCP localport=5986`
6. We look at the general configuration `winrm get winrm/config` and if Basic Auth is in the false state, we convert it to true:
>`Set-Item -Force WSMan:\localhost\Service\auth\Basic $true`
7. Checking the general configuration:
> `winrm e winrm/config/Listener`

it should look something like this:
 > Listner<br>
	Address = *<br>
	Transport = HTTP<br>
	Port = 5985<br>
	Enabled = true<br>
	...
<br><br>
Listner<br>
	Address = *<br>
	Transport = HTTPS<br>
	Port = 5986<br>
	Hostname = this is the above-specified DNS<br>
	Enabled = true<br>
	CertificateThumbprint = this is the above-specified Thumbprint<br><br>
 8. Creating a local ansible user and adding it to the Admin group
### Inventory and Playbooks
Inventory and play books are located in linux windows folders

> ansible windows -m win_ping -i windows.ini<br>
ansible-playbook -i inventory.ini windows_playbook.yml

>ansible centos -m ping -i linux.ini<br>
ansible-playbook- -i linux.ini linux_playbook.yml


