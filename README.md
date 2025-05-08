# Cisco-ASA-VPN-setup
# Cisco ASA Firewall & VPN Setup Guide

This guide walks you through the steps to configure a Cisco ASA firewall, set up Network Address Translation (NAT), and configure a Remote Access VPN for Windows clients.

## Prerequisites

- **Cisco ASA Firewall**
- **PuTTY or any terminal emulator** for SSH or Console access
- **Cisco AnyConnect VPN Client** installed on your Windows machine
- **Admin access** to the Cisco ASA device

## Steps

### 1. Access the Cisco ASA Device

- **Via Console Access** (For Initial Setup):
    1. Connect to the ASA using a console cable.
    2. Open **PuTTY** (or any terminal emulator) and use the **serial** connection type.
    3. Connect to the ASA.

    ![Console Access](path-to-screenshot-console.png) 

- **Via SSH** (For Remote Access):
    1. Use the following command in **PuTTY** or your terminal:
    ```bash
    ssh admin@<ASA_IP_Address>
    ```

    ![SSH Access](path-to-screenshot-ssh.png)

### 2. Basic Configuration of Cisco ASA

1. **Set Hostname and IP Address**:
    ```bash
    ciscoasa(config)# hostname ASA
    ciscoasa(config)# interface GigabitEthernet0/0
    ciscoasa(config-if)# ip address 192.168.1.1 255.255.255.0
    ciscoasa(config-if)# no shutdown
    ```

    ![Set Hostname](path-to-screenshot-hostname.png)

2. **Set Enable Password**:
    ```bash
    ciscoasa(config)# enable password yourpassword
    ```

    ![Set Password](path-to-screenshot-password.png)

### 3. Configure NAT (Network Address Translation)

1. **Configure NAT to Allow Internet Access**:
    ```bash
    ciscoasa(config)# object network obj_any
    ciscoasa(config-network-object)# subnet 0.0.0.0 0.0.0.0
    ciscoasa(config)# object network obj_any
    ciscoasa(config-network-object)# nat (inside,outside) dynamic interface
    ```

    ![NAT Configuration](path-to-screenshot-nat.png)

### 4. Configure VPN (Remote Access VPN)

1. **Create VPN Pool** (IP Range for VPN Clients):
    ```bash
    ciscoasa(config)# object network vpn_pool
    ciscoasa(config-network-object)# subnet 192.168.100.0 255.255.255.0
    ```

    ![VPN Pool](path-to-screenshot-vpn-pool.png)

2. **Enable SSL VPN on ASA**:
    ```bash
    ciscoasa(config)# webvpn
    ciscoasa(config-webvpn)# enable outside
    ```

    ![Enable VPN](path-to-screenshot-vpn-enable.png)

3. **Define VPN Group Policy**:
    ```bash
    ciscoasa(config)# group-policy VPN-Policy internal
    ciscoasa(config)# group-policy VPN-Policy attributes
    ciscoasa(config-group-policy)# vpn-tunnel-protocol ssl-client
    ciscoasa(config-group-policy)# split-tunnel-policy tunnelspecified
    ciscoasa(config-group-policy)# split-tunnel-network-list value VPN-Split-Tunnel
    ```

    ![Group Policy](path-to-screenshot-group-policy.png)

4. **Create VPN Tunnel Group**:
    ```bash
    ciscoasa(config)# tunnel-group VPN-Users type remote-access
    ciscoasa(config)# tunnel-group VPN-Users general-attributes
    ciscoasa(config-tunnel-general)# address-pool vpn_pool
    ciscoasa(config-tunnel-general)# default-group-policy VPN-Policy
    ```

    ![Tunnel Group](path-to-screenshot-tunnel-group.png)

5. **Create User Credentials**:
    ```bash
    ciscoasa(config)# username vpnuser password vpnpassword
    ```

    ![User Credentials](path-to-screenshot-user-credentials.png)

### 5. Test VPN Connectivity from Windows Machine

1. **Install Cisco AnyConnect VPN Client** on your Windows machine.
2. **Connect to the ASA VPN** using the Cisco AnyConnect client:
    - Open the VPN client, and enter the ASA IP address (e.g., `https://192.168.1.1`).
    - Login with your **username** and **password** (`vpnuser` / `vpnpassword`).

    ![AnyConnect Client](path-to-screenshot-anyconnect.png)

3. **Test VPN Access** by pinging an internal server:
    ```bash
    ping 10.0.0.1
    ```

    ![Ping Test](path-to-screenshot-ping-test.png)

### 6. Monitor VPN Session in ASA

1. **View VPN Sessions**:
    ```bash
    ciscoasa# show vpn-sessiondb detail anyconnect
    ```

    ![VPN Session](path-to-screenshot-vpn-session.png)

## Conclusion

You've successfully configured a Cisco ASA firewall and remote access VPN! This setup allows secure remote access to your internal network from Windows clients. Always verify your configuration, test the connection, and monitor the VPN session logs.

Feel free to follow along, make changes, and adapt the configuration to suit your network requirements.

---

**Notes**:
- Replace `path-to-screenshot-<name>.png` with the actual path to your images in your GitHub repository.
- Each screenshot is a visual representation of the configuration steps.

