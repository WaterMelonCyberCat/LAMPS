# **LAMPS Creation**

## Summary

### **Before everything**
- **SSH & IPFIXE**
- **Installing basic tools**
  
### **Installation & Discorvery of Apache2**
- **Installing Apache2**
- **Creating vHost for WordPress**
- **Securing Apache2**

### **Installation & Discovery of MariaDB**
- **Installing MariaDB**
- **Securing MariaDB**
- **Creating user for WordPress**

### **Installing PHP**
- **Checking PHP version**

### **WordPress**
- **Installing WordPress**
- **Setting up WordPress**

---

## **Before everything**

### **SSH & IPFIXED**

Before everything let's fixed the IP address of the machine :

```where do I find the network file
nano /etc/network/interfaces
```
Then when I am in this file I put those parameters in it : 

(*Make sure to adapt the parameters for the situation*)

```Parameters for a fixed IP
iface ens192 inet static
address 192.168.10.100
gateway 192.168.10.254
```
When it's done restart the networking :

```
systemctl restart networking
```
Now with my IP fixed let's get to SSH :

```Installing SSH
apt install -y ssh
````

I will install SUDO for usefull purpuses later :

```Installing SUDO
apt install -y sudo
```

Now I will secure my SSH in the sshd_config file :

```Sshd_config file path
nano /etc/ssh/sshd_config
```

#### When I am in the sshd_config file I will do this list of steps :
   ! Before any modification open another ssh window before !

   (*change the (*****)with the actual file*)


  - Decomment PermitRootLogin Yes & change it to No
  - Change the LoginGraceTime 120 to 30
  - Add a MaxAuthTries set with only 3 connexions tries
  - Add a MaxSessions parameter with only 3 sessions autorized
  - Add the AlllowUsers with all the users you need to have
  - Declared the autorized algorithms with HostKeyAlgorithms
  - Forbid the interactive keyboard with PasswordAuthentication set on No
  - Pass the StrictModes parameter to Yes
  - Declared explicitly the emplacement of the SSH keys in AuthorizedKeyFile
  
  ```Secure SSH
  LoginGraceTime 30
  AllowUsers watermeloncybercat
  PasswordAuthentication no
  PermitRootLogin no
  StrictModes yes
  MaxAuthTries 3
  MaxSessions 3
  HostKeyAlgorithms ssh-rsa,ssh-ed25519
  PubkeyAuthentication yes
  AuthorizedKeyFile     ***************
  ```
  
