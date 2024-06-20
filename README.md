![image](https://github.com/cnduffield/OCPVirtNotes/assets/16171877/17c1537c-9403-4828-b7da-fd1a257b10c3)

# Virtual Machine Management
The least used Node is chosen by default

# Migrating Existing Virtual Machines
This takes time. If it does not complete in time we have vmimports project/namespace

# Bare Metal Infrastructure Management notes

  Make sure before you are in the openshift-machine-api project/namespace
  
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

Make sure namespace vmexamples


App is line 131
Domain is line 162

In clone if you don't have the names correct the backup restore will not work

# Backup and Recovery for Virtual Machines notes
Select  All projects to see the operators
Before restoring make sure you set your project/namespace to vmexamples
You will have to flip between projects/namespace
Note there is a UI but partially complete


# Network Management
Helpful blog
https://www.redhat.com/en/blog/connecting-to-vms-in-red-hat-openshift-virtualization

Your SSH credentials are located in the introduction chapter

Make sure project/namespace is set to vmexamples

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
Dont forget to clean up your lab by running:
```oc delete -f allow-host.yaml -f deny-all.yaml -n vmexamples```

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

If it did not import during lab2 then use namespace vmimported.
