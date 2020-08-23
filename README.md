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





















_Italic_
**text**
_underline **bold here**_

# header

## sub header

## Sub Sub Header

[Visit My Site for more info](http://hack3dlazy.com)

[visit **Hack3dlazy**](http://hack3dlazy.com)

![Tigers rule!](<URLfor pic>)
---![Tigers rule!](https://upload.wikimedia.org/wikipedia/commons/5/56/Tiger.50.jpg)![Tigers rule!](https://upload.wikimedia.org/wikipedia/commons/5/56/Tiger.50.jpg)
##![Tigers rule!](https://upload.wikimedia.org/wikipedia/commons/5/56/Tiger.50.jpg)![Tigers rule!](https://upload.wikimedia.org/wikipedia/commons/5/56/Tiger.50.jpg)


'''
public static void main(){
}
...

> Four score and highlighted text for the Hack3dlazy!!!

- Item 
  -sub Item
- item 2

1. num _italic_ 1
2. num 2 **Bolded** 2

All this is in one paragraph

Except this will be in another


[visit **Hack3dlazy**](http://hack3dlazy.com)


[visit **Hack3dlazy**](http://hack3dlazy.com)
