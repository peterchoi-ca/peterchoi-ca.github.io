---
title: "Install Openshift Local"
date: 2025-04-15T00:18:20-04:00
---

## Overview
I have been wanting to learn OpenShift as a stretch goal for some time now, and decided to take the plunge today.

RedHat courses and labs are very expensive, unless you are lucky to find work sanctioned training or vouchers.

To minimize costs, I obtained the following materials:

- purchased a self-paced online course by Sander Van Vugt

- opted for `OpenShift Local` on my PC; it's a single node cluster intended for sandbox purposes

- borrowed the Manning book `"OpenShift in Action" by Jamie Duncan and John Osborne`

Fun fact: OpenShift Local was previously called CRC or CodeReady Containers.

## Hardware Pre-requisite
Make sure your machine has at least 32GB of RAM, the more the better.  

## Install Steps

Here are the steps I took to scaffold my personal environment:

1. Enable virtualization at the BIOS level.

2. Install options are MacOS, Windows, or Linux. I opted for a Linux Mint VM on VMWare Workstation Pro (free). A heads up if you choose Windows, you will need Professional edition as Home edition won't suffice.

3. Make sure that you give your VM at least 16GB memory and enable VT-x.

4. Sign up for a free RedHat account at https://console.redhat.com

5. Search for OpenShift in the UI and you will see options for `Cloud`, `Datacenter`, and `Local`.  Select `Local`.

![Image description](/images/posts/install-openshift-local/hybrid-cloud-console-openshift.png)


6. From the UI, download the binary appropriate for your OS. For me, it was `crc-linux-amd64.tar.xz`. Extract the tar file and place the binary in your $PATH.

7. From the UI, download the pull secret and save it to your preferred location in the filesystem.

8. On the shell, start the install by running `crc setup`. Output snip below.

```
pete@linuxvm:~$ crc setup
INFO Using bundle path /home/pete/.crc/cache/crc_libvirt_4.17.7_amd64.crcbundle 
INFO Checking if running as non-root              
INFO Checking if running inside WSL2              
INFO Checking if crc-admin-helper executable is cached 
INFO Checking if running on a supported CPU architecture 
INFO Checking if crc executable symlink exists    
INFO Checking minimum RAM requirements            
INFO Check if Podman binary exists in: /home/pete/.crc/bin/oc 
INFO Checking if Virtualization is enabled        
INFO Checking if KVM is enabled                   
INFO Checking if libvirt is installed             
INFO Checking if user is part of libvirt group    
INFO Checking if active user/process is currently part of the libvirt group 
INFO Checking if libvirt daemon is running        
INFO Checking if a supported libvirt version is installed 
INFO Checking if crc-driver-libvirt is installed  
INFO Installing crc-driver-libvirt                
INFO Checking crc daemon systemd service          
INFO Setting up crc daemon systemd service        
INFO Checking crc daemon systemd socket units     
INFO Setting up crc daemon systemd socket units   
INFO Checking if AppArmor is configured           
INFO Updating AppArmor configuration              
INFO Using root access: Updating AppArmor configuration 
[sudo] password for pete:         
INFO Using root access: Changing permissions for /etc/apparmor.d/libvirt/TEMPLATE.qemu to 644  
INFO Checking if systemd-networkd is running      
INFO Checking if NetworkManager is installed      
INFO Checking if NetworkManager service is running 
INFO Checking if dnsmasq configurations file exist for NetworkManager 
INFO Checking if the systemd-resolved service is running 
INFO Checking if /etc/NetworkManager/dispatcher.d/99-crc.sh exists 
INFO Writing NetworkManager dispatcher file for crc 
INFO Using root access: Writing NetworkManager configuration to /etc/NetworkManager/dispatcher.d/99-crc.sh 
INFO Using root access: Changing permissions for /etc/NetworkManager/dispatcher.d/99-crc.sh to 755  
INFO Using root access: Executing systemctl daemon-reload command 
INFO Using root access: Executing systemctl reload NetworkManager 
INFO Checking if libvirt 'crc' network is available 
INFO Setting up libvirt 'crc' network             
INFO Checking if libvirt 'crc' network is active  
INFO Starting libvirt 'crc' network               
INFO Checking if CRC bundle is extracted in '$HOME/.crc' 
INFO Checking if /home/pete/.crc/cache/crc_libvirt_4.17.7_amd64.crcbundle exists 
INFO Getting bundle for the CRC executable        
INFO Downloading bundle: /home/pete/.crc/cache/crc_libvirt_4.17.7_amd64.crcbundle... 
5.08 GiB / 5.08 GiB [---------------------------------------------------------------------------------------------------------------------------------------------------------------------------] 100.00% 10.89 MiB/s
INFO Uncompressing /home/pete/.crc/cache/crc_libvirt_4.17.7_amd64.crcbundle 
crc.qcow2:  18.66 GiB / 18.66 GiB [-------------------------------------------------------------------------------------------------------------------------------------------------------------------------] 100.00%
oc:  158.81 MiB / 158.81 MiB [------------------------------------------------------------------------------------------------------------------------------------------------------------------------------] 100.00%
Your system is correctly setup for using CRC. Use 'crc start' to start the instance
```

9. I chose to disable telemetry. You can leave it on if you want.
```
pete@linuxvm:~/Downloads$ crc config set consent-telemetry no
Successfully configured consent-telemetry to no
```

10. Next, start the instance by running `crc start -p ~/Downloads/pull-secret`. Note that the path to your pull secret is the location where you saved it from step #7, so replace it accordingly to fit your environment. Everything went mostly well; however it errored out due to missing packages as you can see from the output below.

```
pete@linuxvm:~$ crc start -p ~/Downloads/pull-secret
INFO Using bundle path /home/pete/.crc/cache/crc_libvirt_4.17.7_amd64.crcbundle 
INFO Checking if running as non-root              
INFO Checking if running inside WSL2              
INFO Checking if crc-admin-helper executable is cached 
INFO Checking if running on a supported CPU architecture 
INFO Checking if crc executable symlink exists    
INFO Checking minimum RAM requirements            
INFO Check if Podman binary exists in: /home/pete/.crc/bin/oc 
INFO Checking if Virtualization is enabled        
INFO Checking if KVM is enabled                   
INFO Checking if libvirt is installed             
INFO Checking if user is part of libvirt group    
INFO Checking if active user/process is currently part of the libvirt group 
INFO Checking if libvirt daemon is running        
INFO Checking if a supported libvirt version is installed 
INFO Checking if crc-driver-libvirt is installed  
INFO Checking crc daemon systemd socket units     
INFO Checking if AppArmor is configured           
INFO Checking if systemd-networkd is running      
INFO Checking if NetworkManager is installed      
INFO Checking if NetworkManager service is running 
INFO Checking if dnsmasq configurations file exist for NetworkManager 
INFO Checking if the systemd-resolved service is running 
INFO Checking if /etc/NetworkManager/dispatcher.d/99-crc.sh exists 
INFO Checking if libvirt 'crc' network is available 
INFO Checking if libvirt 'crc' network is active  
INFO Loading bundle: crc_libvirt_4.17.7_amd64...  
INFO Creating CRC VM for OpenShift 4.17.7...      
INFO Generating new SSH key pair...               
INFO Generating new password for the kubeadmin user 
INFO Starting CRC VM for openshift 4.17.7...      
Error starting machine: Error in driver during machine start: virError(Code=9, Domain=10, Message='operation failed: Unable to find a satisfying virtiofsd')
```

11. Next, I installed the missing packages by running `sudo apt install virtiofsd virt-manager` and tried again:

```
pete@linuxvm:~$ crc start -p ~/Downloads/pull-secret
INFO Using bundle path /home/pete/.crc/cache/crc_libvirt_4.17.7_amd64.crcbundle 
INFO Checking if running as non-root              
INFO Checking if running inside WSL2              
INFO Checking if crc-admin-helper executable is cached 
INFO Checking if running on a supported CPU architecture 
INFO Checking if crc executable symlink exists    
INFO Checking minimum RAM requirements            
INFO Check if Podman binary exists in: /home/pete/.crc/bin/oc 
INFO Checking if Virtualization is enabled        
INFO Checking if KVM is enabled                   
INFO Checking if libvirt is installed             
INFO Checking if user is part of libvirt group    
INFO Checking if active user/process is currently part of the libvirt group 
INFO Checking if libvirt daemon is running        
INFO Checking if a supported libvirt version is installed 
INFO Checking if crc-driver-libvirt is installed  
INFO Checking crc daemon systemd socket units     
INFO Checking if AppArmor is configured           
INFO Checking if systemd-networkd is running      
INFO Checking if NetworkManager is installed      
INFO Checking if NetworkManager service is running 
INFO Checking if dnsmasq configurations file exist for NetworkManager 
INFO Checking if the systemd-resolved service is running 
INFO Checking if /etc/NetworkManager/dispatcher.d/99-crc.sh exists 
INFO Checking if libvirt 'crc' network is available 
INFO Checking if libvirt 'crc' network is active  
INFO Loading bundle: crc_libvirt_4.17.7_amd64...  
INFO Starting CRC VM for openshift 4.17.7...      
INFO CRC instance is running with IP 192.168.130.11 
INFO CRC VM is running                            
INFO Updating authorized keys...                  
INFO Configuring shared directories               
INFO Check internal and public DNS query...       
INFO Check DNS query from host...                 
WARN Wildcard DNS resolution for apps-crc.testing does not appear to be working 
INFO Verifying validity of the kubelet certificates... 
INFO Starting kubelet service                     
INFO Waiting for kube-apiserver availability... [takes around 2min] 
INFO Adding user's pull secret to the cluster...  
INFO Updating SSH key to machine config resource... 
INFO Waiting until the user's pull secret is written to the instance disk... 
INFO Changing the password for the kubeadmin user 
INFO Updating cluster ID...                       
INFO Updating root CA cert to admin-kubeconfig-client-ca configmap... 
INFO Starting openshift instance... [waiting for the cluster to stabilize] 
INFO 2 operators are progressing: authentication, image-registry 
INFO Operator console is progressing              
INFO All operators are available. Ensuring stability... 
INFO Operators are stable (2/3)...                
INFO Operators are stable (3/3)...                
INFO Adding crc-admin and crc-developer contexts to kubeconfig... 
Started the OpenShift cluster.

The server is accessible via web console at:
  https://console-openshift-console.apps-crc.testing

Log in as administrator:
  Username: kubeadmin
  Password: (redacted)

Log in as user:
  Username: developer
  Password: developer

Use the 'oc' command line interface:
  $ eval $(crc oc-env)
  $ oc login -u developer https://api.crc.testing:6443
```

12. If everything went well, you will be provided with login instructions to start creating new OpenShift projects.

![Image description](/images/posts/install-openshift-local/openshift-local-ui.png)

## Install Issues
For me, the cluster wouldn't boot unless I temporarily disabled WSL and memory integrity. It may or may not be relevant for you.

## Next Steps
I plan on first learning the fundamentals of Openshift, deploying some apps, and exploring integrations with HashiCorp Vault since dynamic secrets management is something I've been focusing on extensively for customers recently. Thanks for reading!