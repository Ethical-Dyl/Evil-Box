# Evil-Box
This is a writeup of the CTF box named Evil-Box. 


# Initial Enumeration # 
Starting with a nmap scan I found the following services being run on the machine depicted in the image below. Nmap syntax: "sudo nmap -T5 -sC -sV -Pn -vv -A $IP"

<img width="817" alt="image" src="https://github.com/Ethical-Dyl/Evil-Box/assets/66540055/241a29bc-564a-4979-9b0c-4fb9b5c19374">

# Web Enumeration # 

Begining the enumeration by adding the IP to my host file, and navigating to the webpage.

<img width="734" alt="image" src="https://github.com/Ethical-Dyl/Evil-Box/assets/66540055/cac1f40b-bcfa-42c7-95ed-a8ad7fd210f3">


I will begin running a gobuster scan in the background while parsing through the landing page to ensure there is no unturned stone.

Upon the scan completing I see that there are two promising hits, robots.txt and secret.

<img width="914" alt="image" src="https://github.com/Ethical-Dyl/Evil-Box/assets/66540055/85a816f2-b2c6-41c1-ae73-357373937f29">

Nothing interesting on the robots.txt except a taunting text.

<img width="960" alt="image" src="https://github.com/Ethical-Dyl/Evil-Box/assets/66540055/a6635b7c-6270-4337-a688-8093b6846448">

The secret page showed a blank page, I will run a gobuster scan against this page.

<img width="956" alt="image" src="https://github.com/Ethical-Dyl/Evil-Box/assets/66540055/6402cea5-eb53-4638-8a46-ec6b7a2546d6">

The gobuster scan shows that there is a page named "evil.php"

<img width="371" alt="image" src="https://github.com/Ethical-Dyl/Evil-Box/assets/66540055/2b8c836b-f068-4994-98f2-86f908184a6e">

Navigating to the evil.php page, we get a similar blank page, let's see if I can parse any data through this page:

<img width="956" alt="image" src="https://github.com/Ethical-Dyl/Evil-Box/assets/66540055/2739f438-6a58-4035-8f29-ee563d581304">

<img width="960" alt="image" src="https://github.com/Ethical-Dyl/Evil-Box/assets/66540055/00383e18-6a35-40fd-9927-e7952690020c">


File returned a blank page as well, this is worth fuzzing to see if there are any words that would have the /etc/passwd file appear, for this I will be using ffuf:
We have a hit!

<img width="820" alt="image" src="https://github.com/Ethical-Dyl/Evil-Box/assets/66540055/e6cc319f-481b-4d16-bd73-da573772c76d">

<img width="959" alt="image" src="https://github.com/Ethical-Dyl/Evil-Box/assets/66540055/ccb03e1c-5535-4669-9cdf-e811abd15e9d">


Now I see in the /etc/passwd file there is a user "mowree". I will add this user to a user.txt file for remembrance. I also see their home directory being "/home/mowree", I will attempt to see if I can find anything interesting in the directory/subdirectories.

Upon parsing I found that I could pull their private key, using Curl I downloaded it to my local machine for cracking.

<img width="543" alt="image" src="https://github.com/Ethical-Dyl/Evil-Box/assets/66540055/36cd0ae1-df61-4519-9f3a-6a9a8037d307">

# SSH Cracking and Connection. #

Utiliizing ssh2john we can get the hash for john to crack

<img width="246" alt="image" src="https://github.com/Ethical-Dyl/Evil-Box/assets/66540055/7110c37b-71a4-423b-a8c3-09ab90f423ab">

<img width="244" alt="image" src="https://github.com/Ethical-Dyl/Evil-Box/assets/66540055/47715eca-0d4d-4a6b-8c6e-1930df160f0e">


<img width="366" alt="image" src="https://github.com/Ethical-Dyl/Evil-Box/assets/66540055/e350cc04-b8d5-4993-bbeb-db5d77ea80ce">
 
Now that we have the passphrase, the id_rsa file will need to have the permissions changed before ssh connection can sucessfully complete. Once completed I am now inside the machine.

<img width="411" alt="image" src="https://github.com/Ethical-Dyl/Evil-Box/assets/66540055/769184cf-6743-4fc0-b011-4bd636f066f2">


Upon logging in I get the first flag, there is no point in checking the history as the history goes stright into oblivion.

<img width="348" alt="image" src="https://github.com/Ethical-Dyl/Evil-Box/assets/66540055/267bee27-6a35-4bbd-832b-45b667953d16">

Running a privledge escalation check I see that we can write to the /etc/passwd file. 

<img width="738" alt="image" src="https://github.com/Ethical-Dyl/Evil-Box/assets/66540055/1b9ac10e-2767-468f-9454-1825c9e9d220">

<img width="633" alt="image" src="https://github.com/Ethical-Dyl/Evil-Box/assets/66540055/14f6479b-a989-4a0d-a37d-f196ce720c05">

Thanks to the team at Infinite Logins I was able to exploit this and get root by following the methods listed on their [site](https://infinitelogins.com/2021/02/24/linux-privilege-escalation-weak-file-permissions-writable-etc-passwd/) 

<img width="380" alt="image" src="https://github.com/Ethical-Dyl/Evil-Box/assets/66540055/20aa6c73-8e27-4bc2-ac9b-0a1703b65076">

<img width="470" alt="image" src="https://github.com/Ethical-Dyl/Evil-Box/assets/66540055/1508993e-1f48-4324-814e-510e06104149">

# Final Thoughts # 

All in all this was a simple box to get into however it highlights how weak passphrases and weak file permissions give hackers the footholds needed to get into the system/network but also vulnerabilities that help elevate their privileges.
