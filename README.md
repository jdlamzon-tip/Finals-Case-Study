# Finals Case Study (Network Automation)
Designed by: <i>John David Lamzon</i>
#### Required Resources
- 1 PC with operating system of your choice
- Virtual Box
-	DEVASC Virtual Machine
-	GNS3
-	Internet connection <br>
## IP Address Table
| Device     | Interface | IP Address     |
| :---        | :---   | :--- |
| R1      | Fa0/0      | 192.168.1.1   |
|           | S0/0     | 10.0.0.1     |
| DEVASC-LABVM | Ethernet 0     | 192.168.1.2   |
|   PC2 (VPCS)        | Ethernet 0     | 192.168.1.3    |
| R2    | Fa0/0      | 192.168.2.1   |
|           | S0/0     | 10.0.0.2     |
| Ubuntu | Ethernet 0     | 192.168.2.3   |
|   PC1 (VPCS)        | Ethernet 0     | 192.168.2.2    | <br>
## Network Topology
[![image-2022-01-14-222556.png](https://i.postimg.cc/MZmZQC9N/image-2022-01-14-222556.png)](https://postimg.cc/fkkh4rnf)
## Devices
- 2 Cisco 3745
- 2 Ethernet Switch
- 2 VPCS
- DEVASC-LABVM
- Ubuntu
## Configuring the SSH
#### DEVASC-LABVM Virtual Machine
```bash
Host CSR*
  Port 22
  User cisco
  StrictHostKeyChecking=no
  UserKnownHostsFile=/dev/null
  KexAlgorithms +diffie-hellman-group-exchange-sha1

Host *
    Port 22
    User cisco
    StrictHostKeyChecking=no
    UserKnownHostsFile=/dev/null
    KexAlgorithms +diffie-hellman-group1-sha1
    Ciphers +3des-cbc

    SendEnv LANG LC_*
    Ciphers +aes256-cbc
```

#### Ubuntu Virtual Machine
```bash
Host *
    Port 22
    User cisco
    StrictHostKeyChecking=no
    UserKnownHostsFile=/dev/null
    KexAlgorithms +diffie-hellman-group1-sha1
    Ciphers +3des-cbc

    SendEnv LANG LC_*
    Ciphers +aes256-cbc
```

## Configuring the NETPLAN
#### DEVASC-LABVM Virtual Machine
```bash
network:
  version: 2
  renderer: networkd
  ethernets:
    eth:
      match:
        name: en*
      dhcp4: yes
    enp0s3:
      dhcp4: no
      addresses:
        - 192.168.1.2/24
      gateway4: 192.168.1.1

  bridges:
    dummy0:
      dhcp4: no
      dhcp6: no
      accept-ra: no
      interfaces: [ ]
      addresses:
        - 192.0.2.1/32 # library.demo.local
        - 192.0.2.2/32 # pt-controller.demo.local
        - 192.0.2.3/32 # TBD
        - 192.0.2.4/32 # TBD
192.0.2.5/32 # TBD
```
#### Ubuntu Virtual Machine
```bash
network:
  version: 2
  renderer: networkd
  ethernets:
    eth:
      match:
        name: en*
      dhcp4: yes
    enp0s3:
      dhcp4: no
      addresses:
        - 192.168.2.3/24
      gateway4: 192.168.2.1
```
#### Apply the netplan configuration
```bash 
$sudo netplan apply
```
## Show running-config of Routers
#### Router 1 (R1)
```bash
Current configuration : 1830 bytes
!
version 12.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R1
!
boot-start-marker
boot-end-marker
!
enable secret 5 $1$dod/$WXuWbs5zIdCZ5MzXp4XnQ.
!
no aaa new-model
memory-size iomem 5
no ip icmp rate-limit unreachable
ip cef
!
!
!
!
no ip domain lookup
ip domain name www.abc.com
!
multilink bundle-name authenticated
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
username cisco password 0 cisco123
archive
 log config
  hidekeys
!
!
!
!
ip tcp synwait-time 5
ip ssh version 2
!
!
!
!
interface FastEthernet0/0
 ip address 192.168.1.1 255.255.255.252
 ip access-group 110 in
 duplex auto
 speed auto
!
interface Serial0/0
 ip address 10.0.0.1 255.255.255.252
 clock rate 2000000
!
interface FastEthernet0/1
 no ip address
 shutdown
 duplex auto
 speed auto
!
interface Serial0/1
 no ip address
 shutdown
 clock rate 2000000
!
interface Serial0/2
 no ip address
 shutdown
 clock rate 2000000
!
interface FastEthernet1/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
interface Serial2/0
 no ip address
 shutdown
 serial restart-delay 0
!
interface Serial2/1
 no ip address
 shutdown
 serial restart-delay 0
!
interface Serial2/2
 no ip address
 shutdown
 serial restart-delay 0
!
interface Serial2/3
 no ip address
 shutdown
 serial restart-delay 0
!
router ospf 1
 log-adjacency-changes
 network 0.0.0.0 255.255.255.255 area 0
!
ip forward-protocol nd
ip route 0.0.0.0 0.0.0.0 10.0.0.2
!
!
no ip http server

no ip http secure-server
!
no cdp log mismatch duplex
!
!
!
!
!
!
control-plane
!
!
!
!
!
!
!
!
!
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login local
 transport input ssh
line vty 5 15
 login local
 transport input ssh
!
!
End
```

#### Router 2 (R2)
```bash
Current configuration : 1828 bytes
!
version 12.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R2
!
boot-start-marker
boot-end-marker
!
enable secret 5 $1$EKLe$XeSxBV4laWRqm9vgsj9I7/
!
no aaa new-model
memory-size iomem 5
no ip icmp rate-limit unreachable
ip cef
!
!
!
!
no ip domain lookup
ip domain name www.abc.com
!
multilink bundle-name authenticated
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
username cisco password 0 cisco123
archive
 log config
  hidekeys
!
!
!
!
ip tcp synwait-time 5
ip ssh version 2
!
!
!
!
interface FastEthernet0/0
 ip address 192.168.2.1 255.255.255.0
 ip access-group 110 in
 duplex auto
 speed auto
!
interface Serial0/0
 ip address 10.0.0.2 255.255.255.252
 clock rate 2000000
!
interface FastEthernet0/1
 no ip address
 shutdown
 duplex auto
 speed auto
!
interface Serial0/1
 no ip address
 shutdown
 clock rate 2000000
!
interface Serial0/2
 no ip address
 shutdown
 clock rate 2000000
!
interface FastEthernet1/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
interface Serial2/0
 no ip address
 shutdown
 serial restart-delay 0
!
interface Serial2/1
 no ip address
 shutdown
 serial restart-delay 0
!
interface Serial2/2
 no ip address
 shutdown
 serial restart-delay 0
!
interface Serial2/3
 no ip address
 shutdown
 serial restart-delay 0
!
router ospf 1
 log-adjacency-changes
 network 0.0.0.0 255.255.255.255 area 0
!
ip forward-protocol nd
ip route 0.0.0.0 0.0.0.0 10.0.0.1
!
!
no ip http server
no ip http secure-server
!
no cdp log mismatch duplex
!
!
!
!
!
!
control-plane
!
!
!
!
!
!
!
!
!
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login local
 transport input ssh
line vty 5 15
 login local
 transport input ssh
!
!
End
```
## Ansible Configuration
#### hosts
```bash
R1 ansible_host=10.0.0.1
R2 ansible_host=10.0.0.2

[routers:vars]
ansible_user=cisco
ansible_password=cisco123
ansible_connection=network_cli
ansible_network_os=ios
ansible_port=22
ansible_become=yes
ansible_become_method=enable
ansible_become_pass=cisco123

[ubuntu]
ubuntu_server ansible_host=192.168.2.3 

[ubuntu:vars]
ansible_ssh_pass=cisco123 
ansible_ssh_user=andylamzoned 
ansible_password=cisco123 
ansible_port=22 
ansible_become=yes
```
#### ansible.cfg
```bash
[defaults]
inventory = ./hosts
host_key_checking = false
timeout = 15 
deprecation_warnings=False
remote_user = andylamzoned
```
#### Ansible Ping
```bash
devasc@labvm:~/Desktop/network_automation$ ansible all -m ping
R2 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
R1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
ubuntu_server | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```
## Ansible Playbook
Playbooks are one of the core features of Ansible and tell Ansible what to execute. They are like a to-do list for Ansible that contains a list of tasks. Playbooks contain the steps which the user wants to execute on a particular machine. Playbooks are the files where Ansible code is written. Ansible is a configuration management tool that automates the configuration on bunch of servers by the use of playbooks. Ansible plays are written in YAML.
#### List of Playbook in this Case Study
```
· ospf.yaml
· acl.yaml
· webserver.yaml
```
## Python Environment and Download pyATS
```bash
devasc@labvm:~/Desktop/network_automation/pyats$ python3 -m venv pyats
devasc@labvm:~/Desktop/network_automation/pyats$ source bin/activate
(pyats) devasc@labvm:~/Desktop/network_automation/pyats$
```
Install pyATS using pip3. This will take a few minutes. During installation you may see some errors. These can usually be ignored as long as pyATS can be verified as shown in the next step.
```bash
(pyats) devasc@labvm:~/Desktop/network_automation/pyats$ pip3 install pyats[full]
Collecting pyats[full]
  Downloading pyats-20.4-cp38-cp38-manylinux1_x86_64.whl (2.0 MB)

<output omitted>
```
## Use pyATS to test the network
pyATS is an Automation framework developed by cisco which has a module named genie parser contains most of the output parsing and processing.
#### List of pyATS YAML in this Case Study
```
· pyats/testbed.yaml
· pyats/ubuntu_testbed.yaml
```
#### List of pyATS output in this Case Study
```
· pyats/routers_acl
· pyats/rouuter_ospf
· pyats/show_version
· pyats/running_config
· pyats/ubuntu_ifconfig
```

