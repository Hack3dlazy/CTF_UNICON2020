# CTF_UNICON2020
UNICON 2020 CTF write-up hosted on the netwars  platform




# UNICON >>> KnowYourPayloads.counterhack.com CTF 
We've detected malicious activity on an endpoint after a recruiter downloaded a 
resume.doc which executed some sort of malware. We were able to take that 
endpoint offline before it could do any major damage (we think), but we'd like you 
to investigate what exactly the executable does. This CT F will be 3 levels and 
require you to run 3 different pieces of synthetic malware and analyze what it 
does. Level 1 and 2 are question/answer format while level 3 asks you to go way 
deeper. 

## THANKS AGIAN SCYTHE-SANS 
## CTF PREGAME 

• Start - PowerShell - right click- Run as Administrator
>Set-MpPreference -DisableRealtimeMonitoring $true

After downloading the Level 1 payload and unzipping it we can alt+space using powertoys and search cmd you can run as root but you dont have to for this one. Navigate to the path/to/file and execute certutil commands to check hashes. There are other ways to get hashes as well using powershell and cmd like Get-FileHash. Also you can use File Checksum Integrity Util it's a freebie and is pretty easy to install and use. 

Install File Checksum integrity Verifier utility:
[**Integrity Verifier Utility**](https://www.microsoft.com/en-us/download/details.aspx?id=11533)

More info on how to grab hash files here:
[**Compute Hash on MS**](https://support.microsoft.com/en-gb/help/889768/how-to-compute-the-md5-or-sha-1-cryptographic-hash-values-for-a-file)

The command I used to grab the command was 
>certutil -hashfile level1.exe sha1



## Now execute the payload. We need the IP the malware is calling back to?

You need to download Wireshark for this one.

[**WireShark**](https://www.wireshark.org/)

Choose your adapter get ready to attack er...

Run the capture for a bit, it will help down the line when we need to get a good fill of how the malware functions.

Also check this site out for filters to help with your pcap analysis. This site will let you know a great filter fo different scenarios one being filters for finding RAT exe or S2 buckets
[**Filter Expressions**](https://unit42.paloaltonetworks.com/using-wireshark-display-filter-expressions/)

use this command in your filters bar:
>(http.request or ssl.handshake.type == 1 or tcp.flags eq Ox0002 or dns) and !(udp.port eq 1900)

![image](https://user-images.githubusercontent.com/61480759/90970361-9a822000-e4c9-11ea-8fe8-1051df19931c.png)

We can see the port thats being used to communicate with the C2 server as TCP port 443 = https

BTW nice DJ SKILLS Keith Meyr's I bounced to that music lunch breakout. Shout out to Thai message.

This next part of the CTF challenge was rough not knowing how to read the timings for how often the malware called home? After searching a bunch of rabbit holes finding all kinds of info on RDP streams and trying this and that getting no data output. I eventually asked for rederiction. So dmayar on discord thanks agian for being CTF Support. I found that I wasnt looking in the right place. I actually looked at the statistics tab and selected tcp stream timings. I dont think it was reading exact bc I was informed later that starting the payload 3 times offset my timings but I rounded up based on that on got super lucky. The best way to do this i found was to look at the timings of the malware manualy and calculate it yourself.

![image](https://user-images.githubusercontent.com/61480759/90970768-7d038500-e4ce-11ea-9224-9b6f890b7ffc.png)

Lasty to end off level 1 we have to dig into the level1.exe properties and check the details to see that the original file name was dropper_cs.exe handy info for digging into CVE data that may be related to this type of malware. 

![image](https://user-images.githubusercontent.com/61480759/90970842-5bef6400-e4cf-11ea-9330-bf6c46e351e6.png)

# LEVEL 2- GET READY FOR SOME SYSMON

[**C2 Matrix Lab**](https://howto.thec2matrix.com/lab-infrastructure/c2-matrix-eval-lab)

To start level 2 labs we can go visit the above link to C2 Matrix eval lab. For this level we need download sysinternals or as this lab suggests just the single tool from the sysinternals toolkit sysmon. 

Download the sysmon tools from MS then install them using the command line. First go to the derictory you saved the folder and cd into the folder Sysmon. Run the below command to get the tool started and running. 
>Sysmon64.exe -i -h md5 -l -n

Here is some documentation to help you get started installing and figuring out the sysmon tool a bit before you get to threat hunting. 

[**QCURE SYSMON**](https://cqureacademy.com/blog/server-monitoring/sysmon)

For my notes here on this write-up im adding in this step before level 3 where it comes into play. We will need to enable DNS logging to the sysmon tool by adding a script into the sysmon folder and running another set of commands to start the DNS logging in the sysmon tool.

![image](https://user-images.githubusercontent.com/61480759/90971061-9e19a500-e4d1-11ea-9523-ab5315e307a4.png)

First Flag of level 2 is to stop the malware level1.exe process using the task manager or process monitor.

![image](https://user-images.githubusercontent.com/61480759/90970974-ec7a7400-e4d0-11ea-90d6-180cd0b9d061.png)

Grab the MD5 hash of level2 payload this time using:

>certutil -hashfile filename md5
  
![image](https://user-images.githubusercontent.com/61480759/91073396-9c9aca80-e600-11ea-8b6a-597cd6dd1bac.png)

Open Wireshark now to the same eth port you were listing on before. We need to grab the IP that level2.bat connects to. I used sysmon to get this information first but you could have grabbed it just as easy with wireshark.

![image](https://user-images.githubusercontent.com/61480759/91073698-0ca95080-e601-11ea-9503-6e54c1b632d0.png)

Sysmon logs the port the .bat file used as well.

![image](https://user-images.githubusercontent.com/61480759/91073841-48441a80-e601-11ea-8ee5-dca977ef8a85.png)

The command to filter the the IP using wireshark would be:

>ip.addr = ip of attack machine
  
We can see how often the connection on average calls home using sysmon as well.

![image](https://user-images.githubusercontent.com/61480759/91074233-db7d5000-e601-11ea-822b-6a709ce048b3.png)

We can locate the jitter manually by viewing it using wireshark and adding doing the math or you can also get a rough estimate using statistics analysis > sequence numbers. My jitter is above the actual variance level becuase I ran the shell 3 times and didnt close the first two process before running this capture.  

![image](https://user-images.githubusercontent.com/61480759/91084060-ff945d80-e610-11ea-8531-a82ae409bab5.png)

The more accurate way of doing this is manually with WireShark.

![image](https://user-images.githubusercontent.com/61480759/91084112-15a21e00-e611-11ea-9469-7af7ae56a69b.png)

Somewhere in the questions we are asked what url the payload calls to in order to offload the exploit to the vulnerable host. I love to use tshark for this stuff you can also use **Packet Total**. Here is a command I run after saving the pcap file to the host device. 
> tshark -Y http.request.uri -r file.pcap

![image](https://user-images.githubusercontent.com/61480759/91085070-8269e800-e612-11ea-86b8-2d8fd361a754.png)

we can try to analyse wireshark further filtering by IP and then drilling down the picture I have was not the right IP but is the same concept to find the correct URL link.

> http://159.89.227.95/news.php

![image](https://user-images.githubusercontent.com/61480759/91085297-db398080-e612-11ea-8216-afda23c8cecf.png)

**This says its admin/get.php but its not it but similar findings are made for the correct URL.**

To find the processes created by level2.bat grab sysmon, BTW im starting to love this tool for system monitoring ;]

![image](https://user-images.githubusercontent.com/61480759/91085567-43886200-e613-11ea-8376-aaf2dc56e104.png)

First process is **cmd.exe**.
Then second process it **powershell.exe**.

![image](https://user-images.githubusercontent.com/61480759/91085809-98c47380-e613-11ea-9e0f-d44ae0ed3deb.png)

You can see that the first trigger for Sysmon to pop is Event ID 1.

![image](https://user-images.githubusercontent.com/61480759/91085869-b0036100-e613-11ea-925c-45653fd7fbd4.png)

Network connection ID = 3 

![image](https://user-images.githubusercontent.com/61480759/91086151-1d16f680-e614-11ea-8ec5-cc3b8aedf297.png)

To end level 2 we are asked what C2 Framework was used? I racked my brain checking out the C2 lab link and after reading more about C2 framework I started down the black hole. I dicided to snag a hint it went something like **Its one of the most popular frameworks...dummy**. I always over complicate the EZPZ stuff. I searched that information and put two and two together. 

![image](https://user-images.githubusercontent.com/61480759/91086563-b3e3b300-e614-11ea-825f-ba2a179ba3b4.png)

# LEVEL 3 - THE GATE TO THE SWAG!

![image](https://user-images.githubusercontent.com/61480759/91086597-c100a200-e614-11ea-9790-b00c5822408b.png)

After a quick sign-up from the swag bag form I hopped back over to level3.

Once agian stop the old payload from running and download the new payload and grab the sha256 hash of the unzipped payload.

> certutil -hashfile filename sha256

**Here is the point were we need to enable DNS logging with sysmon.**

So create the file:
> config-dnsquery.xml
and place it into the sysmon folder were you orginally installed it and write this script info into the file. Then Save. 

![image](https://user-images.githubusercontent.com/61480759/91087580-446ec300-e616-11ea-9ae5-577cf76a62ff.png)

Then run the command:
>Sysmon.exe -c config-dnsquery.xml

![image](https://user-images.githubusercontent.com/61480759/91087647-69633600-e616-11ea-9d78-d498697ca5e7.png)

 Me at this point, **"At this point enabling sysmon to accept dns traffic was critcal I only had like 47 min left and I thought to myself how hard is this going to be."**
 
Next what is the domain level3.exe calls out to? Just use wireshark and analyse the DNS filter. You find that when you fire up the payload shell it calls to madrid.scythedemo.com you can use sysmon or wireshark. 

You can then use NSLookup to see the IP associated with or madrid.scythedemo.com. **I forget to do this step and did it 10 seconds after time ran out.** I was pushing that last 45 min agianst the clock and dont have pictures of the sysmon or wireshark outputs. Will be adding those after tuesdays walkthrough along with other usefull information.

![image](https://user-images.githubusercontent.com/61480759/91088494-bb588b80-e617-11ea-94cf-2e23f0a331cd.png)

Our last few questions are about average of time the malware takes to heartbeat home? and jitter intervals. Agian we can analyse this data manualy by viewing it in wireshark or finding the event ID's in sysmon. 

PS. im still analyzing the payload on level3 will have write finished after submission time. 

I loved the rundown on malware analysis that this CTF showed us. Never have I done a CTF and learned so much in just a few hours that I could take to the real world as practical skills. Later the next day I used this technique to dig through a system in our sandbox environment at my workplace. Showed some poeple what was up, would love to see CTF likes this pop up agian. Also check out other content by unicon and scythe. Can't wait to do it agian next year. See you tuesday for an run-down **jorge@scythe.io**

[visit **Hack3dlazy**](http://hack3dlazy.com)
