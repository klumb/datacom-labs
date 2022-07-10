
Subnetting
==========================

Like with cars, too much traffic causes congestion. Networks can be segmented to separate traffic. The segments are called subnetworks, or subnets. The subnet is defined by a subnet mask. 

Learning Objectives
--------------------------
By the end of this lesson, you will be able to:

1. Determine the subnet mask.
2. Change the subnet for a deployed instance
3. Explain how subnetting effects the ability of computers on a network to communicate.

It is really, really important that you watch the videos this week to see the visuals of what is going on inside of AWS. I am not providing pictures here as I really need you to watch the videos to get oriented with the lab. 

Steps
--------------------------

### Step 1: Either restart or launch the instances that you used last week. 

In this part, you will need to either use the instances from last week or, if you terminated them, simply built a new instance just like you did last week. 


### Step 2: Build a new instance for this lab but on a new subnet

First, what are subnets? 

Subnet masks define the IP addresses for a subnetwork. Remember that IPv4 addresses are numbers ranging from 0 to 255. Subnet masks are more limited. It is easiest to see how subnet masks are defined in binary. The subnet mask 255.255.255.0 is very common, and is represented in binary as 11111111.11111111.11111111.00000000. [CIDR notation](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#CIDR_notation) can represent this subnet simply as "/24." Looking at the binary notation, from left to right there are 24 1's. Valid subnets always fill up the 1's on the left and zeros on the right; they are never mixed together.

|Netmask notation | Binary Representation               | [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#CIDR_notation) | Hosts |
|-----------------|-------------------------------------|:----:|------:|
|255.255.0.0      | 11111111.11111111.00000000.00000000 | /16  | 65,533|
|255.255.255.0    | 11111111.11111111.11111111.00000000 | /24  | 254   |
|255.255.255.128  | 11111111.11111111.11111111.10000000 | /25  | 126   |
|255.255.255.240  | 11111111.11111111.11111111.11110000 | /28  | 14    |

You can calculate the number of hosts with a simple formula: (2^host bits)-2. So for the /28 subnet, there are 4 bits (i.e. 4 0's) for the host addresses. So we calculate 2^4-2=14. You can connect 14 computers on the /28 subnet. Each subnet has two reserved addresses--the [network address](https://en.wikipedia.org/wiki/Subnetwork#subnet_zero) and the [broadcast address](https://en.wikipedia.org/wiki/Broadcast_address).

Consider two computers in an organization that are both configured with a /28 subnet mask. Computer A has an IP address of 192.168.16.36. Computer B has an IP address of 192.168.16.42. Are these computers in the same subnetwork? We start by determining the number of IP addresses in each network. In the /28 network, 4 bits are reserved for hosts, the broadcast, and network address. So, 2^4 gives us 16 IP addresses reserved for each network. Next, we start with the base network.

The first 2 octets of the /28 subnet mask are all 255, so we know that all network addresses will begin with the first 2 octets of our IP addresses: 192.168. We know that the first network will end with a 0, which gives us a starting network address of 192.168.X.0. For X, we simply substitute the number of IP addresses per subnet--16. So, our first network address will be 192.168.16.0. The next IP address will be the first valid host. The broadcast address will be the 16th IP address in the next work address. The last valid host will be the IP address immediately preceding the broadcast address.

Network Address | 1st Valid Host | Last Valid Host | Broadcast Address |
----------------|----------------|-----------------|-------------------|
192.168.16.0    | 192.168.16.1   | 192.168.16.14   | 192.168.16.15     |
192.168.16.16   | 192.168.16.17  | 192.168.16.30   | 192.168.16.31     |
192.168.16.32   | 192.168.16.33  | 192.168.16.46   | 192.168.16.47     |
192.168.16.48   | 192.168.16.49  | 192.168.16.62   | 192.168.16.63     |
192.168.16.64   | 192.168.16.65  | 192.168.16.78   | 192.168.16.79     |
192.168.16.80   | 192.168.16.81  | 192.168.16.94   | 192.168.16.95     |
192.168.16.96   | 192.168.16.97  | 192.168.16.110  | 192.168.16.111    |
192.168.16.112  | 192.168.16.113 | 192.168.16.126  | 192.168.16.127    |
192.168.16.128  | 192.168.16.129 | 192.168.16.142  | 192.168.16.143    |
192.168.16.144  | 192.168.16.145 | 192.168.16.158  | 192.168.16.159    |
192.168.16.160  | 192.168.16.161 | 192.168.16.174  | 192.168.16.175    |
192.168.16.176  | 192.168.16.177 | 192.168.16.190  | 192.168.16.191    |
192.168.16.192  | 192.168.16.193 | 192.168.16.206  | 192.168.16.207    |
192.168.16.208  | 192.168.16.209 | 192.168.16.222  | 192.168.16.223    |
192.168.16.224  | 192.168.16.225 | 192.168.16.238  | 192.168.16.239    |
192.168.16.240  | 192.168.16.241 | 192.168.16.254  | 192.168.16.255    |

There are only 16 subnets available on the /28 subnet. Looking at the preceding table of subnetworks, a clear pattern emerges. The first line takes some thought, but after that, you just add 16 to get the next network. Returning to the question asked previously, are computers A and B with IP addresses 192.168.16.36 and 192.168.16.42 on the same subnet? The answer is yes. Looking at the table above, the third network has IP addresses that range from 192.168.16.33 through 192.168.16.46. Computers A and B are both in that range, so they can communicate without having to route between networks.

It should be noted that machines on different networks can be configured with the same subnetwork settings but be unable to communicate. The 192.168 address space is reserved for local networks. A public library in Seattle might give and IP address to a computer of 192.168.1.1/24 to a user, while a business in Houston might assign the IP address 192.168.1.2/24 to another user. If those two addresses were on the same network, they could communicate. However, they are on separate networks. They are physically and logically disconnected. Traffic from the Seattle library network would have to be routed to the Houston business network for those machines to communicate. On a single network, IP addresses must be unique.

Now for finishing this step.... launch a third machine but this time, when you are in Step 3: Configure Instance Details, choose a different subnet than what your other two instances are on. Additionally, when you are in the security part of things, include both SSH and ICMP as security rules for the new instance. Once you've made this selection, you can follow the remaining steps as you have done in the past and simply launch the instance -- noting that the primary difference is that it is on a different subnet. 

### Step 3: Test Connections Between Machines on Different Subnets with Private IPs

First, "a public IP address identifies you to the wider internet so that all the information you're searching for can find you. A private IP address is used within a private network to connect securely to other devices within that same network. Each device within the same network has a unique private IP address". (from https://www.avast.com/c-ip-address-public-vs-private). In this next step, trying pinging private IP addresses between the machines (one on one subnet and the other on the other subnet). If your security profiles are set up as totally open for ICMP, you should be able to ping the private IP addresses between all machines (because they are on the same virtual private cloud), no matter what subnet they are on. 

### Step 4: Modify your Security Rules to Isolate Subnets

Like last week's lab, we are going to go back into the security profiles and modify our ICMP rules to isolate our subnets. In short, you can simply modify one of the instances from last week from 0.0.0.0 to 172.31.44.16/20 (or whatever your IPv4 CIDR is for your two machines from last week). In doing this you are limiting the different instances to only be able to communicate within their own subnet. You find your specific IPv4 CIDR by clicking on the details of one of your instances from last week, scroll down under the instance details to the Subnet ID and click that. You open a new window and you can see the IPv4 CIDR in the fifth column. Copy that and it's this number that you use to replace the 0.0.0.0 mentioned above in the security profile. 

Once you've isolated all three of your instances, try pinging between the two that are on the same subnet (two instances from last week) and between subnets (last week's instances with this week's new instance). The former should work, the latter should not work. 

### Step 5: Cleanup
You can stop all of your instances. This will help in not spending all of your AWS credits. 

Great job! You are done with this lab (8/10). Remember to take screenshots and provide narrative for each. This is really important for your submissions. A few notes: 

- Remember to add the additional component for another point (9/10)
- Remember to add  your experiences and difficulties for another point (10/10)
- Make sure that your screenshots are readable. Sometimes people provide really low quality images or the images simply have too much information, making it hard to see what is going on. 
- Make sure to submit your assignment as a SINGLE PDF. This is really important and I won't grade your assignment if you submit it in any other way. Again, SINGLE PDF. 
- Include your name in your PDF. Sometimes I download the entire class set of submissions and work off of my local machine. If you don't include your name, it's pretty hard for me to figure out who completed the submission 

If you have any questions, please don't hesitate to reach out to me. I'm happy to help. 

