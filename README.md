# Terraform and Ansible Deployments

This project was inspired by [45Drives](https://www.youtube.com/watch?v=OkJAPc9Xo5Q) [Github](https://github.com/45Drives/terraform-ansible-demo). I've updated to work in my own lab, and hopefully will be useful for others that will do the same thing. 

I will update this description more after testing. 

Please feel free to drop into my discord if you have questions. [Invite Link](https://discord.gg/Q7p4BmzynM)


## How to use

We are using a combination of Terraform and Ansible. The Ansible file generates the TF configs and applies them using a list of VARs in the all.yml file. 


1. First configure the all.yml file. I do recommend creating an ansible vault for the pve_credentials.
```
vm_config:
  memory: 2048
  cores: 2
  disk_size: "32G"
  disk_storage: "vm-images"
  username: "ubuntu"
  password: ""
  hagroup: ""
  ...
pve_credentials:
  api_user: "root@pve"
  api_endpoint: "https://pve.home.lab:8006/api2/json"
  api_token_id: "root@pve!token_api_thingy"
  api_secret: "SECRETGOESHERE"
  api_password: "API PASSWORD GOES HERE"
```
 

2. You will need to set up a new VM that will be the ansible controller (sysmgr). Simply update the sysmgr with the correct ip address or hostname. And run the init-sysmgr.yml file. This will set up the system with all prereq software and directories required for the rest of the package to work. 
```
ansible-playbook -i hosts init-sysmgr.yml --key-file ~/.ssh/id_rsa
```
3. Once the VM is set up we can move on to adding our hosts to the all.yml file. Currently the variable is gateway but that will be changed. 
- UUID: is the unique identifier for the group of VMs you wish to create
- COMMENTS: They are comments added to the description of the VM.
- NODES: This is where you specify the name of the VM that will be prefixed with DEN and ending with the index number + 1. 
```
gateways:
  - uuid: development
    comments: "Testing Servers"
    nodes:
      - name: 'dev-michael'
```  

4. Now you can run the deploy-vm.yml playbook. 


To remove a group of VMs simply change the state to absent and run the remove-vm.yml