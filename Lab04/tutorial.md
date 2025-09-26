# Lab04: RDK-X5 Network Fundamentals and Configuration Practice

**Learning Objectives:**
*   Master basic network environment configuration and diagnosis for RDK-X5.
*   Understand network interface configuration methods in Linux systems.
*   Learn to deploy simple network services (e.g., DHCP server, Python network applications).
*   Become familiar with common network diagnostic tools.

---

## Chapter 1: RDK-X5 Environment Setup and Network Exploration

This chapter will guide you through the initial connection to RDK-X5 and introduce how to use basic Linux commands to view and test network configurations, helping you establish a preliminary understanding of the RDK-X5 network environment.

### 1.1 RDK-X5 Initial Connection and SSH Access

Connect RDK-X5 directly to your laptop with an Ethernet cable (LAN cable) and perform the first SSH login. In this direct connection scenario, both RDK-X5 and your laptop need to be configured with static IP addresses so they can communicate within the same private network.

1.  **Physical Connection:** Use an Ethernet cable (LAN cable) to directly connect your RDK-X5 device to your laptop's Ethernet port.
2.  **RDK-X5 Startup:** Ensure RDK-X5 has started correctly.
3.  **Configure RDK-X5's Static IP (Assumed):**
    *   **Important Note:** For the first connection, RDK-X5 usually comes with a preset static IP address, such as `192.168.127.10`. [This IP address is preset by the RDK-X5 manufacturer at the factory](https://d-robotics.github.io/rdk_doc/en/Quick_start/hardware_introduction/rdk_x5/).
4.  **Configure Laptop's Static IP:**
    *   On your laptop, find the Ethernet adapter connected to RDK-X5 (usually "Local Area Connection" or "Ethernet").
    *   Go to its network settings, change the IPv4 configuration method from "Obtain an IP address automatically (DHCP)" to "**Manually**" or "**Use the following IP address**".
    *   **IP Address:** Set an IP address in the same subnet as RDK-X5 but different, for example, `192.168.127.12`.
    *   **Subnet Mask:** Set to `255.255.255.0`.
    *   **Gateway and DNS:** In this direct private network connection, a gateway and DNS are usually not required, or they can be left blank.
    *   Save and apply these settings.
5.  **Verify Connectivity (Optional but Recommended):**
    *   Open a terminal or command prompt on your laptop and try to `ping` the IP addresses of RDK-X5 and the Laptop:
        ```bash
        ping 192.168.127.10
        ping 192.168.127.12
        ```
    *   If you receive replies, the connection is successful. If `ping` fails, carefully check if RDK-X5 is powered on, if the network cable is securely connected, and if your and RDK-X5's static IP configurations are in the same subnet and do not conflict.
6.  **SSH Login:** Open a terminal (Linux/macOS) on your laptop or use an SSH client (Windows, such as PuTTY or WSL), and log in to RDK-X5 using the following command:
    ```bash
    ssh sunrise@192.168.127.10
    ```
7.  **Connect to the Internet via VNC:** After logging into RDK via SSH, enter the following command in the RDK terminal:
    *   **Note:** If the `x11vnc` command does not exist, you might need to install it first: `sudo apt install x11vnc`.
    ```bash
    x11vnc -forever -usepw
    ```
    *   Then use VNC Viewer to open RDK's GUI interface and connect to a WiFi network (e.g., ISDN300C).
    *   Close VNC Viewer to free up RDK system resources occupied by the GUI. However, you need to keep the `x11vnc -forever -usepw` command line running.
    *   Reopen a terminal on your Laptop and re-establish an SSH connection. At this point, RDK can maintain a private wired network connection with the Laptop and also connect to the Internet via WiFi.

### 1.2 Understanding RDK-X5 Network Interfaces

RDK-X5 typically includes an Ethernet interface (`eth0`) and a wireless interface (`wlan0`). Understanding their current status is the first step in network configuration.

**Task 1.2.1: View Network Interface Information**
1.  After logging into RDK-X5, use the following command to view detailed information for all network interfaces:
    ```bash
    ip addr show
    # Or use the older command
    ifconfig
    ```
2.  Identify `eth0` (Ethernet interface) and `wlan0` (wireless interface) in the output.
3.  Record their IP addresses, MAC addresses, and current status (UP/DOWN).

#### **Question:** What is the IPv4 address and MAC address of your ethernet interface? How about your wireless interface?

### 1.3 Basic Network Connectivity Test

Install dns library:

```bash
sudo apt install dnsutils
```

`ping` and `dig` are the most commonly used network diagnostic tools for testing host connectivity and DNS resolution.

**Task 1.3.1: Test Network Connectivity (ping)**
1.  **Find your local network's gateway (router):** This is the first stop for traffic leaving your RDK-X5.
    ```bash
    ip route | grep default
    ```
    The output will look something like `default via 192.168.1.1 dev wlan0`. The IP address shown (`192.168.1.1` in this case) is your router.

2.  **Test connectivity to your local router:** Use the IP you found in the previous step. This checks if your local network connection is working. Press `Ctrl+C` to stop.
    ```bash
    ping <Your_Router_IP_Address>
    ```

3.  **Test connectivity to the external network:**
    ```bash
    ping google.com
    ```
    Observe if you receive replies, which indicates that RDK-X5 can access the Internet.

4.  **Test connectivity to your laptop:**
    ```bash
    ping <Your_Laptop_IP_Address>
    ```
    Verify direct connectivity between RDK-X5 and your laptop.

**Task 1.3.2: DNS Resolution Test (dig)**
1.  Use the `dig` command to query the IP address of a domain name to test if DNS resolution is working correctly:
    ```bash
    dig google.com
    ```
    Observe the `ANSWER SECTION` in the output to confirm if the correct IP address is resolved. This helps understand the role of DNS (Domain Name System) in the network.

#### **Question:** What is the average RTT when you ping google.com vs. when you ping your local router? Why are they so different?

#### **Question:** Use dig to find the IP address for github.com. What is it?

---

## Chapter 2: RDK-X5 Network Interface Configuration and Security Practices

This chapter will delve into how to modify RDK-X5's network interface configuration. Since these operations may cause you to lose access to the device, this chapter specifically emphasizes the importance of a "secure channel" and guides you on how to use RDK-X5's wireless capabilities to ensure operational safety.

### 2.1 Understanding Network Configuration Risks and the "Secure Channel"

When modifying the Ethernet interface (`eth0`) configuration of RDK-X5, if done incorrectly, your current SSH connection established via Ethernet may be immediately interrupted, preventing you from continuing operations. To avoid this, we need to establish an independent, stable "secure channel."

**Core Concepts:**
*   **Configuration Risk:** When you SSH into `eth0` and modify `eth0`'s IP address the current SSH session will be terminated. Or if you modify `eth0`'s DHCP settings, if the configuration is incorrect, current SSH session will also be terminated.
*   **Secure Channel:** Refers to an independent connection channel that is unaffected by current configuration changes, allowing you to manage the device and troubleshoot issues even if the primary connection is lost.
*   **Advantages of Wi-Fi as a "Secure Channel":** RDK-X5 typically comes with a wireless interface (`wlan0`). If `wlan0` can establish a network connection independently of `eth0` (e.g., connecting to an external Wi-Fi router, or acting as a Wi-Fi access point itself), it can provide a very convenient "secure channel."

**Task 2.1.1: Evaluate and Establish a Secure Channel (Preparation)**

Before making any Ethernet interface configuration changes, it is crucial to establish a reliable secure channel. Here are common scenarios and recommendations:

**Prerequisite: Both RDK-X5 and your laptop are connected to the same external Wi-Fi network.**
This is the ideal situation.
1.  **Confirm RDK-X5's `wlan0` is connected:** In the current SSH session on RDK-X5, use the `ip addr show wlan0` command to check if `wlan0` has obtained an IP address.
2.  **Confirm laptop is connected to the same Wi-Fi:** Ensure your laptop is also connected to the same Wi-Fi network.
3.  **Get `wlan0` IP address:** Record RDK-X5's `wlan0` IP address.
4.  **Exit the current Ethernet SSH session:** Type `exit`.
5.  **Connect to RDK-X5 via Wi-Fi SSH:**
    ```bash
    ssh sunrise@<RDK-X5_WLAN_IP_Address>
    ```
    You have now established a secure management connection via Wi-Fi.

**Important Note:** **Before proceeding with any configuration changes in this chapter, be sure to select and establish a reliable secure channel based on your actual situation. If a secure channel cannot be established, you must understand and accept the risk of potentially losing SSH connection.**

### 2.2 Change RDK-X5's Ethernet IP Address

This section will guide you on how to modify the IP address of RDK-X5's Ethernet interface.

**Warning:** **Be sure to perform this operation ONLY AFTER connecting to RDK-X5 via Wi-Fi SSH as instructed in Task 2.1.1!**

**Task 2.2.1: Change Ethernet Interface (eth0) IP Address to a Temporary Address**
1.  Use the following command to change `eth0`'s IP address to, for instance, `192.168.127.199` with a subnet mask of `/24`:
    ```bash
    sudo ip addr change 192.168.127.199/24 dev eth0
    ```
2.  **Verify IP Address:** Use the `ip addr show eth0` command again to confirm that the IP address has been successfully changed.
3.  **Test Connectivity:** Try to `ping` the new `eth0` IP address from your laptop, or `ping` your laptop from RDK-X5, to verify connectivity.


**Task 2.2.2: Restore Ethernet Interface (eth0) IP Address**
1.  Use the following command to change `eth0`'s IP address back to `192.168.127.10` with a subnet mask of `/24`:
    ```bash
    sudo ip addr change 192.168.127.10/24 dev eth0
    ```
2.  **Verify IP Address:** Use the `ip addr show eth0` command again to confirm that the IP address has been successfully changed back to the original IP address.
3.  **Test Connectivity:** Try to `ping` the original `eth0` IP address from your laptop, or `ping` your laptop from RDK-X5, to verify connectivity.

---

## Chapter 3: RDK-X5 Network Services and Application Practice

This chapter will guide you through deploying simple network services on RDK-X5, including custom Python network applications and a standard DHCP server.

### 3.1 Run Simple Python Network Server and Client

Python's `socket` module provides a simple way to create network applications.

**Task 3.1.1: Write and Run Python Server**
1.  **Create Server File (`server.py`):**
    ```python
    # server.py
    import socket

    HOST = '0.0.0.0'  # Listen on all available interfaces
    PORT = 65432      # Listening port

    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.bind((HOST, PORT))
        s.listen()
        conn, addr = s.accept()
        with conn:
            print(f"Connected by {addr}")
            while True:
                data = conn.recv(1024)
                if not data:
                    break
                print(f"Received: {data.decode()}")
                conn.sendall(b"Hello from RDK-X5 server!")
    ```
2.  **Run Server on RDK-X5:**
    ```bash
    python3 server.py
    ```

**Task 3.1.2: Write and Run Python Client**
1.  **Create Client File (`client.py`):** You can run this client on your laptop.
    ```python
    # client.py
    import socket

    HOST = '<RDK-X5_IP_Address>'  # RDK-X5's IP address (eth0 or wlan0)
    PORT = 65432              # Port must match server

    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.connect((HOST, PORT))
        s.sendall(b"Hello from client!")
        data = s.recv(1024)
        print(f"Received from server: {data.decode()}")
    ```
2.  **Run Client on your laptop or RDK-X5:**
    ```bash
    python3 client.py
    ```
3.  **Verify Data Transfer:** Observe the output of the server and client to confirm that data was successfully sent and received.

## Chapter 4: Network Diagnostics and Advanced Tools

This chapter will introduce some more advanced network diagnostic tools to help you analyze network traffic and connection status in depth.

### 4.1 Route Tracing and Path Analysis (traceroute)

'''bash
sudo apt-get install traceroute
'''

The `traceroute` command displays the routing path packets take from RDK-X5 to a target host.

**Task 4.1.1: Use the `traceroute` Command**
1.  **Trace the route to an external host:**
    ```bash
    traceroute google.com
    ```
    Observe the output to understand which routers the packets pass through to reach the destination.
2.  **Explain Principle:** Briefly explain how `traceroute` discovers the routing path by incrementing the TTL (Time To Live) value.

### 4.2 Port Scanning and Service Probing (netcat, telnet)

`telnet` and `netcat` are powerful tools that can be used to test port connectivity, perform simple communication, and even transfer files.

**Task 4.2.1: Use `telnet` to Probe Ports**
1.  **Test if a remote service is reachable:**
    ```bash
    telnet <Target_IP_Address> <Port_Number>
    ```
    For example, to test if you can connect to `google.com` on port 80:
    ```bash
    telnet google.com 80
    ```
    If the connection is successful, connection information will usually be displayed; if it fails, a connection timeout or refusal will be shown.

**Task 4.2.2: Use `telnet` to Simulate a Web Browser**
1.  Install telnet: 
    ```bash
    sudo apt install telnet
    ```
2.  Connect to port 80 of a non-encrypted website: 
    ```bash
    telnet example.com 80
    ```
3.  After the screen goes blank, you are connected. Manually type the following HTTP request. After typing the `Host:` line, **press Enter twice**.
    ```
    GET / HTTP/1.1
    Host: example.com
    ```
    The server will respond with the raw HTTP headers and HTML of the webpage. This is what your browser does behind the scenes.

**Task 4.2.3: Use `netcat` for Port Listening and Data Transfer**
1.  **Listen on a port as a server:** Run on RDK-X5:
    ```bash
    nc -lvp 12345
    ```
    (`-l` listen, `-v` verbose output, `-p` specify port)
2.  **Connect and send data as a client:** Run on your laptop:
    ```bash
    echo "Hello RDK-X5!" | nc <RDK-X5_IP_Address> 12345
    ```
3.  **Observe:** Observe if the `netcat` server on RDK-X5 receives the message. This demonstrates `netcat`'s flexibility in network debugging and simple data transfer.

### 4.3 View Network Connections and Statistics (ss)

`ss` (socket statistics) is a faster and more powerful alternative to `netstat`, used to display current system network connections, routing tables, interface statistics, and more.

**Task 4.3.1: Use the `ss` Command**
1.  **View all TCP connections:**
    ```bash
    ss -tuna
    ```
    (`-t` TCP, `-u` UDP, `-n` numeric addresses, `-a` all)
2.  **View all listening ports:**
    ```bash
    ss -lntu
    ```
    (`-l` listening)
3.  **Filter specific ports or IPs:**
    ```bash
    ss -tuna | grep 22 # View SSH connections
    ```
    Through these commands, you can quickly understand which services are running and which connections are established on RDK-X5.

#### **Question:** Run traceroute google.com. How many hops does it take to get there? Can you identify the point where the traffic leaves the university's network?

#### **Question:** Based on the output of ss -ltn, is your SSH server running? On which port?

---

## Group Assignment - Building a Simple IoT Gateway

You will build a simple but functional IoT Gateway that collects system information from the RDK-X5 and reports it to an application running on your laptop. A monitoring system is created for remote IoT device RDK-X5. A central station (your laptop) needs to automatically collect key metrics from remote device every minute to ensure they are running correctly. Your RDK-X5 is the "IoT device" that runs a server to provide this data upon request.

### Part 1: Setup

1.  **Network Setup:**: Before you begin coding, ensure your network is correctly configured. You must be able to `ping` your RDK-X5 from your laptop and vice versa.

    ```bash
    ping 192.168.127.10
    ping 192.168.127.12
    ```

2. **Fork the template repo:**: https://github.com/siyanhu/ISDN3000C_Lab04_template

### Part 2: The Gateway Server (on RDK-X5)

The python script [gateway_server.py](https://github.com/siyanhu/ISDN3000C_Lab04_template/blob/master/gateway_server.py) is a boilerplate for a multi-threaded server. This design allows the server to handle multiple clients simultaneously, which is a standard practice for robust network services.

Your task is to **complete the `handle_client` function**.

1.  Copy this code into a file named `gateway_server.py` on your RDK-X5.
2.  Fill in the `## --- TODO --- ##` section.

### Part 3: The Monitoring Client (on Laptop)

Your task is to write a client script from scratch that will run on your laptop.

1.  `monitoring_client.py` is a template to get you started on your **laptop**.
2.  Write the code to perform the following actions in a loop:
    *   Connect to the server on the RDK-X5.
    *   Send a request message (e.g., `b"GET_DATA"`).
    *   Receive the data response from the server.
    *   Print the received data in a clean, readable format.
    *   Close the connection.
    *   Wait for 60 seconds before repeating the process.

### Part 4: Data Structure Pointers

You have the freedom to choose how you structure the data sent from the server to the client. However, using a standard, self-describing format is highly recommended.

**Recommendation: JSON (JavaScript Object Notation)**
JSON is the de-facto standard for APIs and data exchange. It is human-readable and easy to parse in almost any programming language.

The `get_system_info` function already creates a Python dictionary. You can convert it to a JSON string like this:
`json_string = json.dumps(info_dictionary)`

The client can then parse it back into a dictionary:
`info_dictionary = json.loads(json_string)`

### Part 5: Submission

One submission per each group. Submit your forked github repo link, full names and student number of all group memebers. You are required to submit the following to your forked Github repo:

1.  **Your completed `gateway_server.py` file.**
2.  **Your completed `monitoring_client.py` file.**
3.  **A screenshot of your client's terminal.** The screenshot must show the client running and successfully receiving **at least 3 responses** from the RDK-X5 (proving the 60-second loop works).
4.  add `.gitignore`, add `readme.md`, add `requirements.txt`.
5.  **A brief text description** of your network setup (IP addresses used) and the data format you chose.

---

## Appendix: Common Issues and Troubleshooting

*   **RDK-X5 cannot SSH login?**
    *   Check if RDK-X5 is powered on.
    *   Confirm that RDK-X5 and your laptop are on the same network and can `ping` each other.
    *   Check if RDK-X5's IP address is correct.
    *   Ensure the SSH service (`sshd`) is running on RDK-X5 (`sudo systemctl status sshd`).
    *   Check firewall rules (`sudo ufw status` or `sudo iptables -L`) to ensure port 22 is open.
*   **No network after IP address change?**
    *   **First, try to reconnect via Wi-Fi SSH.**
    *   Check if the new IP address, subnet mask, and gateway configuration are correct.
    *   Use `ip addr show` to confirm that changes have taken effect.
    *   Check for conflicts with other network services (e.g., DHCP server).
*   **DHCP service fails to start or client cannot obtain IP?**
    *   Check if `INTERFACESv4` in `/etc/default/isc-dhcp-server` correctly specifies the listening interface.
    *   Check if the `/etc/dhcp/dhcpd.conf` configuration file has syntax errors (`sudo dhcpd -t`).
    *   Ensure the interface the DHCP server listens on has a static IP address, and that this IP address is within the DHCP configured subnet.
    *   Check if the firewall is blocking DHCP ports (UDP 67/68).
    *   View system logs (`journalctl -u isc-dhcp-server`) for detailed error messages.
*   **How to check if RDK-X5 wireless card supports AP mode?**
    *   Log into RDK-X5 and use the following command:
        ```bash
        iw list
        ```
    *   Look for the `Supported interface modes` section in the output. If it includes `AP`, then your wireless card supports Access Point mode.