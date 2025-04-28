# DNS Troubleshooting Report

## Scenario Overview
Your internal web dashboard (hosted on internal.example.com) is suddenly unreachable from multiple systems. The service seems up, but users get "host not found" errors. You suspect a DNS or network misconfiguration. Your task is to troubleshoot, verify, and restore connectivity to the internal service.

---

### 1. *Verify DNS Resolution*
   - **Check local DNS resolution (from /etc/resolv.conf):
     check local DNS server 

     Command:
     
     cat /etc/resolv.conf
    - *if the local DNS server found then try to ping internal.example.com 
      command:
      ping internal.example.com 

    - *if it not found so add the entry then try to ping again 
      add manually in /etc/resolv.conf  
   

   - *Check DNS resolution using Google DNS (8.8.8.8)*:

     Command:
     nslookup internal.example.com 8.8.8.8
     
---

### 2. *Diagnose Service Reachability*
   - *check if the web service (HTTP/HTTPS) is running 
     systemctl status httpd
   - *if the service is not runnig 
     systemctl start httpd


   - *if the service is up and running  
   - *Check if the web service (HTTP/HTTPS) is reachable on port 80 or 443 (its default no need to write it)*:

     curl -I http://192.168.1.10
     
     
   - *Check if the HTTP service is listening on the expected port (80 or 443)*:

     ss -tuln | grep ':80'
     
---


   - *Firewall/Network Issues* 

  
     firewall-cmd --list-all
     
   - *if the firewall doesnt have the ports then add it 
     Command to open port 80 and 443 (if blocked):
     sudo firewall-cmd  --add-port=80/tcp --permanent
     sudo firewall-cmd  --add-port=443/tcp --permanent
     sudo firewall-cmd --reload
     

     

   - *SELinux*: If SELinux is blocking the service, you can check its status and temporarily disable it for testing.

     Command to check SELinux status:
     getenforce
     

     Command to disable SELinux (for testing):
     bash
     sudo setenforce 0
     
   - *if SELINUX was the issue , instead of disabling it, allow the web service to use the ports

     semanage port -a -t http_port_t -p tcp 80

   - *Routing Issues*: Check the routing table to make sure traffic can reach the destination.

     Command to check routes:
     bash
     ip route show
     

---

### 5. **Fixing and Verifying DNS with /etc/hosts (Bonus Task)**
   - To bypass DNS issues temporarily, you can add an entry in /etc/hosts to resolve the domain to the correct IP.

     Edit the file /etc/hosts:
     bash
     sudo nano /etc/hosts
     

     Add the following entry:
     
     192.168.1.10 internal.example.com
     

   - *Persist DNS Changes*: For a more permanent DNS configuration, use systemd-resolved or NetworkManager to change DNS settings.

     sudo systemctl restart systemd-resolved
     

---

