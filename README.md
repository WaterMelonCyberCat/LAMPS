# **LAMPS Creation**

## Summary

### **Before everything**
- **SSH & IPFIXE**
- **Installing basic tools**
- **Securing SSH**
  
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

### **SSH & IPFIXE**

Before everything let's fix the IP address of the machine :

```where do I find the network file
nano /etc/network/interfaces
```
Then when I am in this file I put those parameters in it : 

(*Make sure to adapt the parameters for the situation*)

```Parameters for a fix IP
iface ens192 inet static
address 192.168.10.100
gateway 192.168.10.254
```
When it's done restart the networking :

```
systemctl restart networking
```

