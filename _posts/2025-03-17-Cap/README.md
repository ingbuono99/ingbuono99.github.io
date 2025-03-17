## Cap  
![Alt text](/assets/2025-03-17-Cap/image-16.png)
# Walkthrough
While the nmap was scanning, i reached that IP via broswer. There was an active service on http:  
![Alt text](/assets/2025-03-17-Cap/image.png)

Then the nmap showed its results:  
![Alt text](/assets/2025-03-17-Cap/image-1.png) 
![Alt text](/assets/2025-03-17-Cap/image-2.png) 
![Alt text](/assets/2025-03-17-Cap/image-3.png) 
Three ports open: 21 (FTP), 22 (SSH), 80 (HTTP).  
Let's also fuzz for unknown directories:  
![Alt text](/assets/2025-03-17-Cap/image-4.png)   
  
![Alt text](/assets/2025-03-17-Cap/image-5.png)   
Unfortunately no Anonymous login on FTP  
  
From the browser there was the possibility to download previously done scans by other user by changing the ID in the url:  
![Alt text](/assets/2025-03-17-Cap/image-7.png)   
Downloading the number 0 scan, there were a FTP packet exchange were the FTP credentials were in cleartext:  
![Alt text](/assets/2025-03-17-Cap/image-6.png)   
  
By logging via FTP, i found the user flag:  
![Alt text](/assets/2025-03-17-Cap/image-8.png)   
  

## Privilege escalation
The same credentials also worked for ssh, I used them to open a shell and execute linpeas.  
![Alt text](/assets/2025-03-17-Cap/image-11.png)   
![Alt text](/assets/2025-03-17-Cap/image-9.png)   
![Alt text](/assets/2025-03-17-Cap/image-10.png)   
![Alt text](/assets/2025-03-17-Cap/image-12.png)   
  
The last screenshot is an interesting evidence.  
According to https://www.elastic.co/guide/en/security/8.17/potential-privilege-escalation-via-python-cap-setuid.html, it seems that there is the possibility to elevate privileges by leveraging the cap_setuid.  
I found the python commands online:  
![Alt text](/assets/2025-03-17-Cap/image-13.png)   
So i opened a pyhton shell and executed these commands:  
``` import os; os.setuid(0); os.execl(“/bin/sh”, “sh”, “-c”, “reset; exec sh”) ```  
I got a root shell!  
![Alt text](/assets/2025-03-17-Cap/image-14.png)   
Browsing through the root directory, i finally got the root flag:  
![Alt text](/assets/2025-03-17-Cap/image-15.png)  
