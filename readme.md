# 🧱 Firewall Enterprise Lab — Corporate Network Security Simulation

## 📌 Technologies Used
- pfSense
- Windows Defender Firewall
- Wireshark
- VMware Workstation Pro
- Windows Server 2022 (optional for services simulation)
- Windows 11 Clients
- NAT / Internal Network / Host-Only Networking
- VLAN Concepts (logical segmentation)
- Firewall Rules (Stateful Inspection)
- NAT (Network Address Translation)
- Logging & Monitoring
- Basic IDS/IPS (optional with Suricata)

---
## 🧾 1. Project Overview
---
This project simulates a corporate firewall infrastructure using a virtualized environment.

The goal is to design, configure and secure a network perimeter using firewall policies, segmentation rules, NAT, and traffic inspection techniques.

It replicates a real enterprise scenario where IT administrators must control:
- Incoming and outgoing traffic
- Internal segmentation between departments
- Internet access policies
- Security logging and monitoring

### 🎯 Objectives:
- Deploy a firewall appliance (pfSense)
- Segment network using LAN interfaces
- Control traffic using firewall rules
- Configure NAT for internet access
- Block unauthorized traffic between internal networks
- Enable logging and monitoring
- Simulate real enterprise security policies
- Validate rules using testing tools (ping, browser, Wireshark)


---
## 🏗️ 2. Network Architecture
---
```
                 🌐 INTERNET (NAT)
                        │
                ┌───────────────┐
                │  pfSense FW   │
                │ (Firewall VM) │
                └───────┬───────┘
                        │ LAN
        ┌───────────────┼────────────────┐
        │               │                │
   VLAN / LAN1     VLAN / LAN2     VLAN / LAN3
   (IT Net)        (Sales Net)      (Guest Net)
        │               │                │
   Windows 11     Windows 11       Windows 11
   Admin PC       Sales PC         Guest PC
```

---
## 🖥️ 3. Virtual Machine Setup
---
Objectives:
- [ ] Create VM1: Windows Server
- [ ] Create VM2, VM3, VM4: Windows 11 (clients)
- [ ] Allocate Resources (CPU, RAM, disk)
- [ ] Configure networking adapters (NAT / Host-Only)

---
VMs Required:
- VM1: Firewall → pfSense
- VM2: Windows 11 Client IT 
- VM3: Windows 11 Client SALES 
- VM4: Windows 11 Client GUEST

For the virtualization of the machines we will:
- Use VMware Workstation Pro (25H2u1)
- Download [PFSense 2.7.2 Community Edition](https://atxfiles.netgate.com/mirror/downloads/) ISO for the Firewall
- Download [Windows 11 23H2](https://www.microsoft.com/es-es/software-download/windows11arm64) ISO for the client

For the network adapters, we will use the following configuration, where the Firewall sits between both environments:
| VM               | Adapter 1           | Adapter 2 | 
| :--------------: | :-----------------: | :-------: |
|Firewall|	WAN (NAT)|   LAN (Host-Only)   |           |
|Clients        |   LAN (Host-Only)   |	❌     |

Why are we using these adapters?
1. WAN → 🛜 Internet access simulation
2. LAN → 🔒 Internal corporate network
<br><br><br>


Given the context, we will start by creating the virtual networks in VMWare:
<img src="/screenshots/1.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />   

<img src="/screenshots/2.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />   

Note that the host-only adapter has DHCP disabled, since the server will host it.

|      Network      |     Purpose     |       Range      |
| :---------------: | :-------------: | :--------------: |
|      VMnet8       |  Internet (WAN) | 192.168.248.0/24 |
| FW LAB:Host-Only	| Internal LAN	   | 192.168.10.0/24  |

We will be assigning the following static IPs for the LAN:
|        Host       |        IP        | 
| :---------------: | :--------------: |
| Hosting Local PC  | 192.168.10.1 (default)|
| Server       	| 192.168.10.2  |
| W11 CLIENT (IT)   | 192.168.10.3  |
| W11 CLIENT (SALES)| 192.168.10.4  |
| W11 CLIENT (GUEST)| 192.168.10.5  |

<br><br><br>



We will procede to create pfSense Firewall VM, with the following hardware:
<img src="/screenshots/3.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />   


<br>

Now we have all the machines set up and with connection between them, we can configure Pfsense

<br><br><br>

---
## 🌐 4. pfSense Installation & Setup
---
Steps:
Install pfSense on VM
Assign interfaces:
WAN → NAT adapter
LAN → Internal network adapter

Access Web GUI:

https://192.168.1.1

Default credentials:

admin / pfsense

---
Let's open the machine and install the OS:
<img src="/screenshots/5.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
Set up the interfaces; em0 --> WAN; em1 --> LAN:
<img src="/screenshots/6.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 

We are also creating the client machines, with the following hardware:
<img src="/screenshots/4.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 

Now we are gonna set **192.168.10.2** as the Static IP Address for the Firewall inside the LAN, we can skip the IPV6 configuration since we will not need it:
<img src="/screenshots/7.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 

Now that the networking is configured in the server, we can test connectivity with our host PC, by pinging 192.168.10.1, we can also do the same in our host PC by pinging 192.168.10.2:
<img src="/screenshots/8.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<img src="/screenshots/9.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<br><br><br>


We will procede to create the Windows clients, with the following hardware:
<img src="/screenshots/10.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />   
To make this faster, we will only show the configuration of one of the machines (IT), but the other two will follow exactly the same process.
<img src="/screenshots/11.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />  
Once installed, we will configure the static IP, also set the Server as gateway:
<img src="/screenshots/12.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />  
And we can finally test the connectivity:
<img src="/screenshots/13.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />  
<img src="/screenshots/14.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />  
<br><br><br>

Now we can access the Web GUI in our machine host:
1. Open browser: https://192.168.10.2
2. ⚠ Ignore certificate warning → Advanced → Continue
3. Login with default credentials:
     -user: admin 
     -password: pfsense

<img src="/screenshots/15.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 

We will run the PFSense initial configuration wizard:
<img src="/screenshots/16.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 

Then we will setup the following details:
<img src="/screenshots/17.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<img src="/screenshots/18.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 

Finally, on the client machines we can change the DNS server from the google one (8.8.8.8) to the Firewall (192.168.10.2):
<img src="/screenshots/19.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 

<br><br><br>

---
## 🔧 5. Interface Configuration
---
Segmentation (advanced):
LAN1: IT → 192.168.10.0/24
LAN2: Sales → 192.168.20.0/24
LAN3: Guest → 192.168.30.0/24

---
We now redesign the network into:
- IT:	192.168.10.0/24
- SALES:	192.168.20.0/24
- GUEST:	192.168.30.0/24

This is where the firewall rules become meaningful since corporate isolation exists.
Right now, we only use one adapter for all 3 clients, called:
<img src="/screenshots/20.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 


But for network segmentation, we need multiple Host-Only VMware networks, one for each machine:

|VMware Adapter     | 	Department     |
| :---------------: | :---------------: | 
| FW LAB:Host-Only	|        IT         |
| FW LAB:Host-Only2	|       SALES       |
| FW LAB:Host-Only3	|       GUEST       |

After creating the adapters, we need to switch Sales and Guest machines adapters to the new ones:
<img src="/screenshots/21.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<br>
The Firewall also has to have all 3 host-only adapters, since it will host the rules for every subnet.
<img src="/screenshots/23.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 

Now we need to set the adapters on the firewall:
<img src="/screenshots/24.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 

And also we have to set the static IP address for every adapter:
<img src="/screenshots/25.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<img src="/screenshots/26.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 

We have all the adapters set up with an IP Address:
<img src="/screenshots/27.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<br>

Now in the clients, we have to switch the IP and the gateway:
- IT:	192.168.10.10/24
<img src="/screenshots/33.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<br>
- SALES:	192.168.20.10/24
<img src="/screenshots/28.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<br>
- GUEST:	192.168.30.10/24
<img src="/screenshots/36.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<br>

<br><br><br>
Now to allow communication in the subnets, we need to copy the default rules from the LAN interface to OPT1 and OPT2 in the firewall, for that, go to **Firewall --> Rules --> LAN**:
<img src="/screenshots/22.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<img src="/screenshots/29.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<img src="/screenshots/30.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<br><br><br>

Now we can asure that we have communication in all clients:
- IT:
<img src="/screenshots/34.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<img src="/screenshots/35.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<br>
- SALES:
<img src="/screenshots/31.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<img src="/screenshots/32.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<br>
- GUEST:
<img src="/screenshots/37.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<img src="/screenshots/38.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<br><br><br>


---
## 🔐 6. Firewall Rules Configuration
---
🌍 WAN Rules:
| Action	| Protocol | Source | Destination |      Description      |
| :-----: | :------: | :----: | :---------: | :-------------------: |
|  Block  |    Any   |   Any  |   Firewall  | Block inbound traffic |



🖥️ LAN Rules:
| Action	| Protocol |  Source  | Destination |      Description      |
| :-----: | :------: | :------: | :---------: | :-------------------: |
|  Allow  |    Any   | LAN net  |     Any     | Allow internet access |

🧠 Internal segmentation rules:
1. IT Network:
- Full access to all networks

2. Sales Network:
- Block access to IT network
- Allow internet only

3. Guest Network:
- Internet only
- Block internal networks completely

| Source | Interface | Destination |  Result  |
| ------ | --------- | ----------- | -------- |
|   IT   |    LAN    | Any         | ✅ Allow | 
| SALES  |    OPT1   | IT          | ❌ Block |
| SALES  |    OPT1   | Internet    | ✅ Allow |
| GUEST  |    OPT2   | IT          | ❌ Block |
| GUEST  |    OPT2   | SALES       | ❌ Block |
| GUEST  |    OPT2   | Internet    | ✅ Allow |


---
At the moment, all networks can communicate between eachother, because we copied the default “Allow Any” rule.

But now, we will transform this into a real corporate firewall.

It is also good to mention, that in pfsense:
- The rules are proccessed in priority by **TOP → DOWN**
- Everything is **DENIED** by default unless explicitly allowed.

Let's proceed to apply the rules based on the [initial stated segmentation rules:](#-6-firewall-rules-configuration)
1. |   IT   |    LAN    | Any         | ✅ Allow |:
     This rule is already set.
<img src="/screenshots/39.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<br>

2. | SALES  |    OPT1   | IT          | ❌ Block |
<img src="/screenshots/40.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<br>

3. | SALES  |    OPT1   | Internet    | ✅ Allow |
<img src="/screenshots/41.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<br>

4. | GUEST  |    OPT2   | IT          | ❌ Block |
<img src="/screenshots/43.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<br>

5. | GUEST  |    OPT2   | SALES       | ❌ Block |
We can do this one by copying the past rule and modifying the destination from the IT network (192.168.10.0/24) to Sales Network (192.168.20.0/24), and also the description of the rule:
<img src="/screenshots/44.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<img src="/screenshots/45.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<br>

6. | GUEST  |    OPT2   | Internet    | ✅ Allow |
We can do this one by copying the 3rd rule from OPT1 and modifying the description of the rule:
<img src="/screenshots/47.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<img src="/screenshots/48.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<img src="/screenshots/49.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<img src="/screenshots/50.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<br>

The final order of the Rules is very important as mentioned before. 
Also we don't need any rule that work on IPV6 Protocol, since we are not using it, so we can disable it. 
And of course, we also need to disable any default rule that we had before which allowed us to communicate indiscriminately within network, because we would because our blocking rules would make no sense at all. 

So all the final interfaces should have the rules applied and this would be the correct order:

- LAN:
<img src="/screenshots/51.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<br>

- OPT1:
<img src="/screenshots/42.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<br>

- OPT2:
<img src="/screenshots/46.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<br><br><br>


---
## 🌍 7. NAT Configuration
---
Configure outbound NAT:
- Mode: Automatic NAT
- Allow LAN → WAN translation

This allows:
- Internal users to access internet
- Firewall to mask internal IPs

---
Without Network Address Translation (NAT) our internal private networks (192.168.x.x) cannot reach the Internet, so NAT allows us to:
- translates internal IPs into the WAN IP.
- VMware NAT network then gives Internet access.

```
192.168.20.10  →  pfSense WAN IP  → Internet
```

The good news is that pfSense usually configures this automatically, but we should verify it is enabled correctly. For that, let's head to ***Firewall → NAT → Outbound*** in the PFSense Web GUI:

1. Make sure that it is checked in **Automatic outbound NAT rule generation**
2. Verify the automatic generated rules have as source all **internal networks**.
<img src="/screenshots/52.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 

<br><br><br>


---
## 📊 8. Logging & Monitoring
---
Enable:
1. Firewall logs
2. Traffic logs
3. Blocked connection logs

Tools:
- Wireshark (packet analysis)
- pfSense live logs dashboard

---

This is where the project starts looking like a real SOC / enterprise firewall setup.

Firewall logging in pfSense is mostly already active, but we verify + improve it, for that head to ***Status → System Logs → Settings*** and apply all the logs:
<img src="/screenshots/53.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 

We're also gonna be modifying all the BLOCK rules applied before from the OPT1 & OPT2 interfaces, so we Log packets that are handled by this rule:
<img src="/screenshots/54.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<img src="/screenshots/55.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<img src="/screenshots/56.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 

To view all the logs, we can head into ***Status → System Logs → Firewall***:
<img src="/screenshots/57.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /> 
<br>

We can also apply Real-time monitoring (Dashboard), by heading into ***Status → Dashboard***
<img src="/screenshots/58.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />
For example, we have added:
- Firewall Logs:
<img src="/screenshots/59.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />
- Traffic Graphs:
<img src="/screenshots/60.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />
- Interfaces:
<img src="/screenshots/61.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />
<br>

Finally, in our host PC, we can use Wireshark for external validation, for example, we will sniff all the packages that are in the VMnet5 Interface, that corresponds to the 192.168.30.0/24 network, in which we have our Guest Client and the Firewall:
1. Sniff VMnet5 interface:
<img src="/screenshots/62.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />
2. Send a package from any machine in the network (192.168.30.0/24), for example, we can send ICMP packages by pinging google servers (8.8.8.8) from our GUEST client machine:
<img src="/screenshots/63.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" /><br><br>
3. Check Wireshark and search the ICMP packages and their responses:
<img src="/screenshots/64.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />

<br><br><br>

---
## 🧪 9. Testing Scenarios
---
Connectivity tests:
- Ping from client → 8.8.8.8 ✔️
- Access Google from clients' browser ✔️
- Access from IT → all networks ✔️
- Blocked access from Sales → IT ❌
- Blocked access from Guest → IT ❌
- Blocked access from Guest → Sales ❌
- Firewall logs show blocked attempts ✔️

---

1. Ping from client → 8.8.8.8 ✔️
- IT CLIENT:
<img src="/screenshots/72.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />
- SALES CLIENT:
<img src="/screenshots/75.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />
- GUEST CLIENT:
<img src="/screenshots/65.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />
<br>


2. Access Google from browser ✔️
- IT CLIENT:
<img src="/screenshots/73.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />
- SALES CLIENT:
<img src="/screenshots/76.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />
- GUEST CLIENT:
<img src="/screenshots/66.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />
<br>

3. Access from IT → all networks ✔️
<img src="/screenshots/74.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />
<br>

4. Blocked access from Sales → IT ❌
<img src="/screenshots/77.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />
<br>

5. Blocked access from Guest → IT ❌
<img src="/screenshots/69.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />
<br>

6. Blocked access from Guest → Sales ❌
<img src="/screenshots/68.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />
<br>


7. Firewall logs show blocked attempts ✔️
- SALES CLIENT:
<img src="/screenshots/78.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />
<img src="/screenshots/79.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />
<br>
- GUEST CLIENT:
<img src="/screenshots/71.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />
<img src="/screenshots/70.png" alt="ERROR LOADING IMAGE" style="display: block; margin-left: auto; margin-right: auto;" />
<br><br><br>


---
## 🛡️ Security Policies Implemented
---
Stateful firewall inspection
Default deny inbound traffic
Network segmentation (logical isolation)
NAT hiding internal IPs
Logging of all denied traffic
Least privilege access model


---
## 📈 Results
---
The firewall environment was successfully deployed and tested.

All objectives were achieved:

Traffic control working correctly
Network segmentation enforced
Internet access properly routed
Logging system operational


---
## ⚠️ Challenges Encountered
---

Misconfigured LAN interface blocking access to GUI
NAT not allowing internet access initially
Firewall rules order incorrectly blocking all traffic
DNS resolution failures on clients
VLAN segmentation complexity
Windows Firewall blocking outbound ICMP packages (ping) in clients for testing

---
## 🧠 Skills Demonstrated
---
Network security fundamentals
Firewall configuration (enterprise-level thinking)
Traffic filtering & rule design
NAT configuration
Network segmentation (VLAN concept)
Troubleshooting connectivity issues
Packet analysis with Wireshark
Real-world IT infrastructure simulation

---
## 📄 License
---
This project is for educational purposes.
Free to use, modify and expand.
