# Dynamic-Host-Configuration-Protocol-DHCP-Setup

# Lab Project: Dynamic Host Configuration Protocol (DHCP) Setup

## Project Overview
This lab project focuses on setting up a Dynamic Host Configuration Protocol (DHCP) server on R4 (eth2) to dynamically allocate IP addresses to an Ubuntu virtual machine (VM). The DHCP server will lease IP addresses from the `10.10.11.X/28` subnet. You will configure and verify the DHCP setup, ensuring proper communication between the server and the client.

---

## Objectives
- Configure a DHCP server on R4 to manage IP leases within the specified subnet.
- Assign the first usable IP address in the subnet as the static address for R4.
- Verify the DHCP functionality by observing IP allocation to the Ubuntu VM.
- Use Wireshark to monitor and analyze DHCP message exchanges.

---

## Lab Tasks

### 1. **Subnet Calculation**
   - Determine the network, broadcast, and usable IP address range for the `10.10.11.X/28` subnet.
   - Assign the first usable IP address as the static IP for R4 (eth2).
   - Reserve the remaining usable addresses for the DHCP pool.

---

### 2. **Configuring the DHCP Server on R4**
   - **Edit the DHCP Configuration File**:
     - Locate the DHCP configuration file at `/etc/dhcp/dhcpd.conf`.
     - Define the subnet, pool range, and default lease time. For example:
       ```plaintext
       subnet 10.10.11.X netmask 255.255.255.240 {
           range 10.10.11.Y 10.10.11.Z; # Replace Y and Z with the DHCP pool range
           option routers 10.10.11.A;   # Replace A with R4's static IP
           default-lease-time 300;
       }
       ```
   - **Specify the Listening Interface**:
     - Update `/etc/default/isc-dhcp-server` to indicate the interface the server should listen on:
       ```plaintext
       INTERFACESv4="eth2"
       ```
   - **Restart the DHCP Service**:
     - Apply the configuration changes by restarting the DHCP server:
       ```bash
       sudo systemctl restart isc-dhcp-server.service
       ```

---

### 3. **Configuring the Ubuntu VM**
   - Update the `/etc/network/interfaces` file on Ubuntu to ensure it uses DHCP for IP configuration:
     ```plaintext
     iface eth0 inet dhcp
     ```
   - Reboot the Ubuntu VM to apply the changes.

---

### 4. **Verification and Testing**

#### 4.1 **Monitor DHCP Traffic Using Wireshark**
   - Launch Wireshark on Ubuntu:
     ```bash
     sudo wireshark
     ```
   - Monitor incoming traffic on the relevant interface (e.g., eth0).
   - Apply the following filter to view DHCP messages:
     ```plaintext
     udp.port == 67
     ```

#### 4.2 **Verify IP Allocation**
   - Confirm that Ubuntu has obtained an IP address from the DHCP server by running:
     ```bash
     ifconfig
     ```

#### 4.3 **Ping Test**
   - Verify connectivity by pinging R4 from Ubuntu.

#### 4.4 **Check DHCP Server Status**
   - Ensure the DHCP server is running properly on R4:
     ```bash
     sudo systemctl status isc-dhcp-server.service
     ```

#### 4.5 **Inspect Leases File**
   - Examine the DHCP leases file on R4:
     ```bash
     cat /var/lib/dhcp/dhcpd.leases
     ```

---

### 5. **Analyze Wireshark Captures**
   - Observe and analyze the four types of DHCP messages:
     - **Discover**: Sent by the client to find a DHCP server.
     - **Offer**: Sent by the server offering an IP address.
     - **Request**: Sent by the client to request the offered IP.
     - **ACK**: Sent by the server to acknowledge the request.

---

## Deliverables
1. Configured DHCP settings in `/etc/dhcp/dhcpd.conf` on R4.
2. Updated `/etc/network/interfaces` file on Ubuntu for DHCP.
3. DHCP lease file from `/var/lib/dhcp/dhcpd.leases` on R4.
4. Wireshark capture showing DHCP message exchange.
5. Verification of successful IP allocation and network connectivity.

---

## Notes
- Ensure that all configurations are saved and services are restarted to reflect changes.
- Use precise subnet calculations to avoid errors in IP allocation.
- Familiarize yourself with DHCP concepts by reading RFC 2131.
- Perform all testing in a controlled lab environment.

Enjoy exploring DHCP functionality in this hands-on lab project!
