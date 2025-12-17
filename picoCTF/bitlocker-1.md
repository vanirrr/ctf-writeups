# picoCTF - Forensics - Medium
## Writeup:
Another forensics medium woohoo!  
The challenge description:  

![view](src/bitdesc)  

Since it says **"simple password"** it's obvious that they're implying hash cracking for the bitlocker drive.  
After downloading the drive i used bitlocker2john on it to see what hashes it contains:  

![view](src/bitlocker2john)  

I found the user password hash: ```$bitlocker$0$16$cb4809fe9628471a411f8380e0f668db$1048576$12$d04d9c58eed6da010a000000$60$68156e51e53f0a01c076a32ba2b2999afffce8530fbe5d84b4c19ac71f6c79375b87d40c2d871ed2b7b5559d71ba31b6779c6f41412fd6869442d66d```  
Afterwards i got my rockyou wordlist but only limited it to 6967 words (nice)  

```bash
head -n 6967 /usr/share/wordlists/rockyou.txt > att.txt
```
Then i ran my hashcat on it on mode 22100 for bitlocker hash. -w 3 is for more workload btw  

```bash
hashcat -m 22100 hash.txt att.txt
```
![view](src/hashcat)

Using the --show option i find that the password is **"jacqueline**  

After that, i made a directory for store the contents of the bitlocker-1.dd file, and used dislocker to unlock it with my found password  
```bash
mkdir unlocked
sudo dislocker bitlocker-1.dd -ujacqueline unlocked
sudo ls unlocked
```
Matter of fact i DID find the dislocker-file, so one more last step is mounting it. So i did just, by making a directory to mount the bitlocker file to, and mounted it to it.  
```bash
mkdir mount
sudo mount -o loop unlocked/dislocker-file mount
```

After entering the mount directory and listing it's contents we find the flag.txt file:  

![view](src/ls)

With cat we find that the flag is: ```picoCTF{us3_b3tt3r_p4ssw0rd5_pl5!_3242adb1}```
