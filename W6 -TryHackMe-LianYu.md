1. Reconnaissance & Enumeration
Start by scanning the target machine's IP address to identify open ports and services:
Run nmap -sS -sV -A  10.49.166.253 to discover active services. 
Ports open: 
•	21 (FTP) 
•	22 (SSH) 
•	80 (HTTP)

Search http://10.49.166.253
 <img width="952" height="324" alt="image" src="https://github.com/user-attachments/assets/b77aa552-1e89-4dae-b978-70fb66403b2d" />

Running ffuf -u http://10.48.137.141/FUZZ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt  and I find a word island.

 

I run gobuster dir -u "http://10.49.166.253" -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
 
Result: http://10.48.130.175/island/

I search http://10.48.130.175/island/ in the search bar and I get the picture below as a result.
 



While on the /island/ page, press Ctrl + U
 

I run gobuster dir -u http://10.48.130.175/island/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
 
Result: http://10.48.130.175/island/2100/      

After I run the command, the result of http://10.48.130.175/island/2100/ appeared and I enter the result in the search bar and get the image below:
 


Click Ctrl + U and I found the words .ticket
 

After I got .ticket, I run gobuster dir -u http://10.48.130.175/island/2100/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .ticket
 
Result: green_arrow.ticket

Then, I search http://10.48.130.175/island/2100/green_arrow.ticket and the result is:
 
Opening green_arrow.ticket provided a Base58 encoded string: RTy8yhBQdscX.
•	Decoding: Using CyberChef, decoding from Base58 resulted in: !#th3h00d.

After obtain username and password, I log in to FTP and connect to the target IP using the credentials:
The usename and password is:
•	Username: vigilante 
•	Password: !#th3h00d
 

List all files using ls -al. Download the interesting files found in the directory: .bash_history, .other_user, Leave_me_alone.png, Queen's_Gambit.png, and aa.jpg
 
 

cd .. to confirm slade file
 

cat command use to view the contents of the files
 

Attempting to open Leave_me_alone.png initially fails because it is not a valid PNG file.
 

I use the command hexedit Leave_me_alone.png
 

Hex editor analysis showed an incorrect PNG header. After correcting the magic bytes to 89 50 4E 47, the image revealed the text: password. 

The file aa.jpg contained an embedded zip file.
•	Extraction: steghide extract -sf aa.jpg
•	Passphrase: password (found from the fixed PNG).
•	Extracted Files: passwd.txt and shado.
•	SSH Password: Reading the shado file provided the password: M3tahuman.
 
 


I used the credentials for the user slade to log in via SSH:
•	Command: ssh slade@10.49.166.253
•	Password: M3tahuman
•	User Flag: Found in user.txt.
o	THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}
 
 








Checking sudo permissions with sudo -l showed that slade can run pkexec as root without a password:
 
By executing /usr/bin/pkexec /bin/bash, I gained a root shell.
•	Root Flag: Located in /root/root.txt.
o	THM{MY_W0RD_I5_MY_B0ND_IF_I_ACC3PT_YOUR_CONTRACT_THEN_IT_WILL_BE_COMPL3TED_OR_I'LL_BE_D34D}
 

