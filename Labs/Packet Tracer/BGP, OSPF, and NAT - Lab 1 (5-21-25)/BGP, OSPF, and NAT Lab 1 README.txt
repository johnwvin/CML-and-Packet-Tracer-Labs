BGP, OSPF, and NAT Lab 1:

4 BGP Areas interconnected, each consisting of: 

1x AS

- AS running OSPF internally, with 3 routers each interconnected and connecting to one adjacent AS, all advertising their public IPs by BGP protocol. I have injected all BGP routes into each AS's OSPF network, allowing for traffic to be routed between the ASes. 

- All internal AS private IPs are hidden from traceroute because of a NAT rule that translates them into public IPs at the AS's router interface linked to the customer's public IP router interface(the smart plug), exactly like the NAT rule at the customer's external public IP router interface. 

Here is an example of a host using the public IP 25.0.1.1 in BGP AS 1 tracerouting 32.0.1.1 in BGP AS 4:

`

Tracing route to 32.0.1.1 over a maximum of 30 hops: 

  1   0 ms      0 ms      0 ms      10.1.4.1
  2   0 ms      0 ms      0 ms      10.1.3.1
  3   0 ms      0 ms      0 ms      10.1.6.1
  4   0 ms      0 ms      0 ms      10.1.7.1
  5   0 ms      0 ms      0 ms      192.168.0.1
  6   0 ms      0 ms      0 ms      192.168.1.1
  7   0 ms      0 ms      2 ms      192.168.0.10
  8   0 ms      0 ms      0 ms      25.0.0.1
  9   *         *         *         Request timed out.
  10   *         *         *         Request timed out.
  11   *         *         *         Request timed out.
  12   *         *         *         Request timed out.
  13   *         *         *         Request timed out.
  14   *         *         *         Request timed out.
  15   *         *         *         Request timed out.
  16   0 ms      0 ms      0 ms      32.0.1.1

`

4x interconnected identical 3-tier LANs

- LAN pairs with identical IP address mappings, capable of communication between each other using inside and outside NAT translations. Here are the results of the `show IP NAT translations` command on each left side and right side LAN:

Left Side LAN:

`

Pro  Inside global     Inside local       Outside local      Outside global
---  172.16.1.1        10.0.0.2           ---                ---
---  172.16.1.2        10.0.4.2           ---                ---
---  172.16.1.3        10.1.0.2           ---                ---
---  172.16.1.4        10.1.4.2           ---                ---
---  ---               ---                172.16.1.1         10.0.0.2
---  ---               ---                172.16.1.2         10.0.4.2
---  ---               ---                172.16.1.3         10.1.0.2
---  ---               ---                172.16.1.4         10.1.4.2

`

Right Side LAN:

`

Pro  Inside global     Inside local       Outside local      Outside global
---  172.16.2.1        10.0.0.2           ---                ---
---  172.16.2.2        10.0.4.2           ---                ---
---  172.16.2.3        10.1.0.2           ---                ---
---  172.16.2.4        10.1.4.2           ---                ---
---  ---               ---                172.16.2.1         10.0.0.2
---  ---               ---                172.16.2.2         10.0.4.2
---  ---               ---                172.16.2.3         10.1.0.2
---  ---               ---                172.16.2.4         10.1.4.2

`

	As you can see, the shared inside private IPs get translated into a unique outside private IP that allows communication between the two identically IP-mapped networks. The source address gets translated into an assigned unique address, and the destination gets translated into the respective address in both directions, allowing for communication between them. This would be practical in a situation where you merge two LANs that each are small in size but share the same private address range. For merging two large networks, you would need a scalable solution beyond Packet Tracer's capabilities.


- Each pair is also assigned a public IP address. Per eBGP autonomous system we have two interconnected private LAN pairs sharing it internally. BGP isn't necessary for the two to communicate as OSPF and NAT handles it locally within the AS.

- All LAN pairs are capable of communicating with each other by a NAT rule that translates private IPs into a single public IP at the external router that is connected to the AS client-border router using Port Address Translation(PAT)/NAT Overload.
