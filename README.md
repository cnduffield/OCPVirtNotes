![image](https://github.com/cnduffield/OCPVirtNotes/assets/16171877/ae39eda0-884f-446d-896e-cd6ee32c0b04)


# Virtual Machine Management
The least used Node is chosen by default

# Migrating Existing Virtual Machines
This takes time. If it does not complete in time we have vmimports namespace

# Bare Metal Infrastructure Management notes

  Make sure before you are in the openshift-machine-api project
  
  ```
  spec:
    architecture: x86_64
    automatedCleaningMode: metadata
    bmc:
      address: 'ipmi://192.168.123.1:6237'
      credentialsName: worker4-bmc-secret
    bootMACAddress: 'de:ad:be:ef:00:07'
    bootMode: UEFI
    online: true
    rootDeviceHints:
      deviceName: /dev/vda
  ```


# Storage Management notes

Make sure vmexamples

Helpful blog
https://www.redhat.com/en/blog/connecting-to-vms-in-red-hat-openshift-virtualization

App is line 131
Domain is line 162

In clone if you don't have the names correct the backup restore will not work

#Backup and Recovery for Virtual Machines notes
Select  All projects to see the operators
Before restore make sure you set your project to vmexamples
You will have to flip between projects

# Network Management
Make sure project is set to vmexamples
For the MultiNetwork Policy you will ssh into the bastion host
```
echo "apiVersion: k8s.cni.cncf.io/v1beta1
kind: MultiNetworkPolicy
metadata:
  name: deny-by-default
  annotations:
    k8s.v1.cni.cncf.io/policy-for: vmexamples/vlan0
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  ingress: []" > deny-all.yaml
```
```
echo "apiVersion: k8s.cni.cncf.io/v1beta1
kind: MultiNetworkPolicy
metadata:
  name:  ingress-ipblock
  annotations:
    k8s.v1.cni.cncf.io/policy-for: vmexamples/vlan0
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  ingress:
  - from:
    - ipBlock:
        cidr: <IP_ADDR_FROM_FEDORA02>/32" > allow-host.yaml
```
# Template and InstanceType Management
Make sure you clean up after the last lab
```
userData: |-
  #cloud-config
  user: centos
  password: ${CLOUD_USER_PASSWORD}
  chpasswd: { expire: False }
  packages:
    - mariadb-server
  runcmd:
    - systemctl enable married
    - systemctl start mariadb
```

# Working with Virtual Machines and Applications

if it did not import then use vmimported
