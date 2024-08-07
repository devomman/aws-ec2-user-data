When to use?
 1. If firewall is blocked (ufw), ssh connection port is not reachable.
 2. If Accidentally enabled UFW on your Ubuntu instance and cant Revert.
 3. Tested: Ubuntu 18.04 Instances.
 
 **Step 1**:
  Open view/change user data in instance settings.
  
  ![GitHub Logo](/step1.png)
  
 **Step 2**:
 Add the script(mentioned below) and save it 
 ```
 #cloud-config
bootcmd:
 - cloud-init-per always fix_broken_ufw_1 sh -xc "/usr/sbin/service ufw stop >> /var/tmp/svc_$INSTANCE_ID 2>&1 || true" 
 - cloud-init-per always fix_broken_ufw_2 sh -xc "/usr/sbin/ufw disable>> /var/tmp/ufw_$INSTANCE_ID 2>&1 || true"
 ```
 
 ![GitHub Logo](/step2.png)

 
**Step 3**: 
 Restart the instance:(machine ip will be changed)
  Script will executed on boot, ufw will be disabled.


**Another Method**
- Source ex [aws](https://repost.aws/knowledge-center/ec2-linux-resolve-ssh-connection-errors)
  
```
Content-Type: multipart/mixed; boundary="//"
MIME-Version: 1.0
 
--//
Content-Type: text/cloud-config; charset="us-ascii"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
Content-Disposition: attachment; filename="cloud-config.txt"
 
#cloud-config
cloud_final_modules:
- [scripts-user, always]
 
--//
Content-Type:
    text/x-shellscript; charset="us-ascii"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
Content-Disposition: attachment; filename="userdata.txt"
 
#!/bin/bash
iptables -P INPUT ACCEPT
iptables -F
systemctl restart sshd.service || service sshd restart
if [[ $( cat /etc/hosts.[ad]* | grep -vE '^#' | awk 'NF' | wc -l) -ne 0 ]];\
then sudo sed -i '1i sshd2 sshd : ALL: allow' /etc/hosts.allow; fi
--//
```
