---
title: Home Lab Hacking
date: 2022-08-11
time: 12:47
---
# Home Lap Hacking
- Now that we have the environment set up we can start hacking it
	- One of the most important and basic tools for a hacker is that of ==nmap==
	- Ones you have both of the machines running, you can run this nmap command `nmap -sT 10.38.100-110`. The IP should be the one that you put when you set up the internal network. The `-sT` command is used to scan TCP connect port using a 3 way handshake. 
	- If you want to be a little more sneaky since using a 3 way handshake establishes a connection you can use `-sS` instead which will not establish a connection and just peace out once it receives a SNY ACK which is a response from the server.
	- Once you scan it you should be able to see this
	![[Nmap-Scan.png]]
	- As you can see these are the ports that available
		- 22/tcp which is ssh (Secure Shell) which is a way to remotely connect to this server. As you can see its closed so we are unable to connect to in remotely
		- 80/tcp which is http (Hypertext Transfer Protocol) which is the port used to send and receive web applications. And its open
		- 443/tcp which is https (Hypertext Transfer Protocol Secure) which is the port used to send and receive web application. And its open
			- The difference between http and https is that the in:
				- http - the things that are send from it are unencrypted and have been fazed away ever since https
				- https -  is the same as http but its more secure. Every traffic that's send through it is encrypted making the port that we use today for web pages.
		- Since port 80 and 443 are open we should be able to connect to it trough a web page.
		- When you type in the IP address of the vuln machine in the web browser on the main machine you will get this.
		 ![[Web-application.png]]
		- ## Attempt 1: Success
			- The first thing I did was try running all the commands that it tell me to run
			- Its should be obvious that the last command `join` will be the next step. When you type in the command `join` it asks for an email. The first thing I checked was if it has a way to check the format what the of the email that is being input, and there is. If you input anything things that isn't in the format of an email address it says invalid input.
		- ## Attempt 2: Success and Failure but we take these
			- So after trying the commands and nothing happened I thought it might have to get some other information using SQLi but I really don't like doing SQLi. So I tried something else first.
				- ### Attempt 1:
					- Recently I had learned about a program called gobuster and thought it might be a good idea to use it to figure out is there is any dir or files that are hidden but realized that gobuster isn't in the default version of kali and since I was in the internal network I couldn't connect to the internet to download it.
					- I then looked at a different type of program that does the same thing as gobuster and there was dirbuster which was installed as default in kali.
					- I then learned that dirbuster was the original of gobuster. But I find gobuster much easier to use and its much more simpler. 
					- At first I used dirbuster with a [common.txt](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/common.txt) which is a wordlist from SecList which has a bunch of wordlists for many different applications
					- But I didn't really know how dirbuster worked and I just left the options as default so the results of running dirbuster gave me a bunch of different dir and files that I didn't know how to look through. After this I thought there were no hidden dir or files and moved on to options.
					- After that failure I thought about doing SQLi but what ever I tried it didn't seam to work
					- So after being stuck for some time I thought about looking at some hints that were online and the first thing I saw was this
					 ![[dirbuster-fail.png]]
					- After seeing this I just stopped and stared at it wishing I had just downloaded gobuster.
					- After this fail I went back to dirbuster and tried again changing the options as well.
					- This was the settings that put on dirbuster
					![[dirbuster.png]]
					- And lo and behold `robots.txt` is right there and an extra file names `license.txt`
					 ![[dirbuster-result.png]]
					- And by opening `robots.txt` it gives you two files `fsocity.dic` and `key-1-of-3.txt`. If I had a more extensive wordlist I might have been able to go to `key-1-of-3.txt` with out the need for `robots.txt`. Opening `key-1-of-3.txt` gives me the first key. Great!
		- ## Attempt 3: 
			- We have the `license.txt` file but I feel like we will need to come back to this later.
			- So we will look in to `fsocity.dic` file instead.
				- .dic file is that of: I am not to sure my self. There isn't really anything that is explaining it in a simple term so I will just open it and see what it is. The way fileinfo.com explained it "Dictionary of words that can be referenced by word processors and other software programs; often used for spell-checking documents and providing correct spelling alternatives for misspelled words."
				- OK um, opening `fsocity.dic` gives me a bunch of words its over 850,000 lines long, and the first thing that came to my mind is that its a wordlist. It might be used for finding the password to log in to the vuln machine because when you open `robots.txt` it also shows `User-agent: *` maybe that the user name of the vuln machine.