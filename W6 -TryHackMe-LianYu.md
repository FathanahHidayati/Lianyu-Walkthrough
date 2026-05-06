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

<img width="940" height="34" alt="image" src="https://github.com/user-attachments/assets/6980a781-2133-4462-a29b-cada050e38ec" />

 

I run gobuster dir -u "http://10.49.166.253" -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

<img width="940" height="347" alt="image" src="https://github.com/user-attachments/assets/8893a169-3b78-4868-adce-c5873a4fca36" />
Result: http://10.48.130.175/island/

I search http://10.48.130.175/island/ in the search bar and I get the picture below as a result.

<img width="940" height="221" alt="image" src="https://github.com/user-attachments/assets/6e6f818d-9233-4d71-b37d-80032901a225" />

While on the /island/ page, press Ctrl + U
<img width="940" height="370" alt="image" src="https://github.com/user-attachments/assets/05dfd0a7-3d48-4814-a516-7f9ed6a2525d" />
Vigilante is the hiding words.

I run gobuster dir -u http://10.48.130.175/island/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 

<img width="939" height="341" alt="image" src="https://github.com/user-attachments/assets/c83d05d3-bd36-47dd-acf4-c06d7dbfe2d8" />
Result: http://10.48.130.175/island/2100/      

Enter the result in the search bar and I get the image below:
<img width="940" height="76" alt="image" src="https://github.com/user-attachments/assets/f9f67c33-840d-4988-b3c6-a99bb55e0cb0" />

Click Ctrl + U and I found the words .ticket
<img width="940" height="286" alt="image" src="https://github.com/user-attachments/assets/df2e2ee6-e873-47b1-a04e-f7a62e51e551" />

After I got .ticket, I run gobuster dir -u http://10.48.130.175/island/2100/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .ticket

<img width="939" height="335" alt="image" src="https://github.com/user-attachments/assets/2bd0a4a3-cd9e-43bc-84df-b0b1fde30ff0" />
Result: green_arrow.ticket

Then, I search http://10.48.130.175/island/2100/green_arrow.ticket and the result is:
<img width="940" height="128" alt="image" src="https://github.com/user-attachments/assets/45e92ccf-6153-4b1c-8c30-11feaf45743f" />

Opening green_arrow.ticket provided a Base58 encoded string: RTy8yhBQdscX.

•	Decoding: Using CyberChef, decoding from Base58 resulted in: !#th3h00d.

After obtain username and password, I log in to FTP and connect to the target IP using the credentials:

The usename and password is:
•	Username: vigilante 
•	Password: !#th3h00d
<img width="940" height="190" alt="image" src="https://github.com/user-attachments/assets/078768e3-358c-4755-975c-b079a60b38fc" />

List all files using ls -al. Download the interesting files found in the directory: .bash_history, .other_user, Leave_me_alone.png, Queen's_Gambit.png, and aa.jpg
<img width="939" height="683" alt="image" src="https://github.com/user-attachments/assets/22f187ea-57cf-40a8-93cf-3c393ac65961" />
<img width="940" height="153" alt="image" src="https://github.com/user-attachments/assets/26ecc66f-f8f3-49e3-817b-7ecb5f6a980a" />

cd .. to confirm slade file
<img width="940" height="228" alt="image" src="https://github.com/user-attachments/assets/25e275bd-d522-43fd-9a53-ba082ebc4cc9" />
 
cat command use to view the contents of the files
<img width="940" height="357" alt="image" src="https://github.com/user-attachments/assets/fd223d21-9a45-400c-97fc-f744133f0290" />

Attempting to open Leave_me_alone.png initially fails because it is not a valid PNG file.
<img width="940" height="342" alt="image" src="https://github.com/user-attachments/assets/39d51c1a-1712-494a-a016-0479a51204c9" />

I use the command hexedit Leave_me_alone.png and find the first 6 bytes are wrong.
<img width="940" height="90" alt="image" src="https://github.com/user-attachments/assets/45d58570-1f07-4733-8599-13479551b7cf" />

Hex editor analysis showed an incorrect PNG header. After correcting the magic bytes to 89 50 4E 47, the image revealed the text: password. 
<img width="940" height="371" alt="image" src="https://github.com/user-attachments/assets/2f700ede-9605-4f89-9a9d-7d28a1a6c406" />


The file aa.jpg contained an embedded zip file.
•	Extraction: steghide extract -sf aa.jpg

•	Passphrase: password (found from the fixed PNG).
•	Extracted Files: passwd.txt and shado.

•	SSH Password: Reading the shado file provided the password: M3tahuman.

 <img width="940" height="258" alt="image" src="https://github.com/user-attachments/assets/b8fcb238-09a2-436b-b5d7-087e09bf7966" />
<img width="939" height="299" alt="image" src="https://github.com/user-attachments/assets/66f47450-e79d-4324-814d-324af0be7937" />

I used the credentials for the user slade to log in via SSH:
•	Command: ssh slade@10.49.166.253

•	Password: M3tahuman

•	User Flag: Found in user.txt.
o	THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}
<img width="939" height="438" alt="image" src="https://github.com/user-attachments/assets/46157783-cc6e-4894-8a3d-ad75f1d17213" />
<img width="940" height="314" alt="image" src="https://github.com/user-attachments/assets/fcb86d00-a20a-4890-98b8-f991883a61ab" />

 Checking sudo permissions with sudo -l showed that slade can run pkexec as root without a password:
 <img width="939" height="565" alt="image" src="https://github.com/user-attachments/assets/84e59663-9952-4956-ac97-16d711730de5" />
 
By executing /usr/bin/pkexec /bin/bash, I gained a root shell.

•	Root Flag: Located in /root/root.txt.

o	THM{MY_W0RD_I5_MY_B0ND_IF_I_ACC3PT_YOUR_CONTRACT_THEN_IT_WILL_BE_COMPL3TED_OR_I'LL_BE_D34D}

 <img width="939" height="691" alt="image" src="https://github.com/user-attachments/assets/a257628c-37cd-432b-8a45-da8f94fad597" />


