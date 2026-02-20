# WireGuard VPN Server & Client Setup on AWS (macOS, Ubuntu)

We'll set up a WireGuard VPN server on AWS EC2, connect clients (Mac & Ubuntu), use DNS instead of raw IPs, debug common issues, configure security groups, detect variable network interface names, place client config files in the correct paths, and walk through every step in detail.

---

## **1. WireGuard Server Setup on AWS EC2**

### **1.1. Launch an Ubuntu 24.04 EC2 Instance**
- **EC2 Dashboard** → **Launch Instance**
- Select **Ubuntu 24.04 LTS**
- **t3.micro** (x86) or **t4g.micro** (ARM) recommended
- **Allocate an Elastic IP and attach it to the instance** (prevents the public IP from changing)

### **1.2. AWS Security Group Settings**
Open the following ports in your EC2 security group:
```
# Inbound Rules
Port 22  (SSH)       → YOUR_IP/32 (restrict to your IP only)
Port 51820 (WireGuard, UDP) → 0.0.0.0/0  (or restrict to specific IPs)

# Outbound Rules
Allow ALL Traffic (enabled by default)
```

### **1.3. Connect to the Server and Install WireGuard**
```bash
ssh -i ~/.ssh/aws-ec2.pem ubuntu@<YOUR_EC2_PUBLIC_DNS>
sudo apt update && sudo apt install wireguard -y
```

### **1.4. Find the Correct Network Interface**
On AWS, the network interface name **may not be `eth0`**. Check with:
```bash
ip link show
```
Example output:
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 ...
2: enX0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 ...
```
Note whatever interface name appears (e.g., `enX0`, `ens5`) - you'll use it below.

### **1.5. Generate WireGuard Keys**
```bash
umask 077
wg genkey | tee privatekey | wg pubkey > publickey
cat privatekey   # Copy this
cat publickey    # Copy this
```

### **1.6. Create the Server `wg0.conf`**
```bash
sudo nano /etc/wireguard/wg0.conf
```
```ini
[Interface]
Address = 10.0.0.1/24
ListenPort = 51820
PrivateKey = <SERVER_PRIVATE_KEY>
PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -A FORWARD -o wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o <NETWORK_INTERFACE> -j MASQUERADE
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -D FORWARD -o wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o <NETWORK_INTERFACE> -j MASQUERADE
```

> **Note:** `SaveConfig = true` is intentionally omitted here. When enabled, WireGuard overwrites your config file on shutdown - which can erase manually added `[Peer]` blocks. Add peers manually and keep `SaveConfig` off for predictable behavior.

Replace `<NETWORK_INTERFACE>` with the interface name you found in step 1.4.

```bash
sudo systemctl enable wg-quick@wg0
sudo systemctl start wg-quick@wg0
sudo wg show  # Verify it's running
```

### **1.7. Enable IP Forwarding and Persist iptables Rules**
```bash
# Enable IP forwarding
echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

# Install iptables persistence (if not already present)
sudo apt install netfilter-persistent iptables-persistent -y

# Apply NAT and forwarding rules
sudo iptables -t nat -A POSTROUTING -o <NETWORK_INTERFACE> -j MASQUERADE
sudo iptables -A FORWARD -i wg0 -o <NETWORK_INTERFACE> -j ACCEPT
sudo iptables -A FORWARD -i <NETWORK_INTERFACE> -o wg0 -m state --state RELATED,ESTABLISHED -j ACCEPT

# Save rules so they persist across reboots
sudo netfilter-persistent save
sudo netfilter-persistent reload
```

---

## **2. Client Setup**

### **2.1. macOS Client Setup**
```bash
brew install wireguard-tools
mkdir -p ~/.wireguard
cd ~/.wireguard
wg genkey | tee client_privatekey | wg pubkey > client_publickey
cat client_privatekey  # Copy and save
cat client_publickey   # Copy and save
```

#### **macOS `client.conf`**
```bash
nano ~/.wireguard/client.conf
```
```ini
[Interface]
PrivateKey = <CLIENT_PRIVATE_KEY>
Address = 10.0.0.2/24
DNS = 8.8.8.8

[Peer]
PublicKey = <SERVER_PUBLIC_KEY>
Endpoint = <YOUR_EC2_PUBLIC_DNS>:51820
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 25
```

```bash
sudo wg-quick up ~/.wireguard/client.conf
curl ifconfig.me  # Should return your AWS Elastic IP
```

### **2.2. Ubuntu Client Setup**
```bash
sudo apt update && sudo apt install wireguard resolvconf -y
```

> **Note:** Install `resolvconf` so that `wg-quick` can apply the `DNS` setting correctly on Ubuntu.

```bash
cd /etc/wireguard
wg genkey | tee client_privatekey | wg pubkey > client_publickey
cat client_privatekey  # Copy and save
cat client_publickey   # Copy and save
```

#### **Ubuntu `wg0.conf`**
```bash
sudo nano /etc/wireguard/wg0.conf
```
```ini
[Interface]
PrivateKey = <CLIENT_PRIVATE_KEY>
Address = 10.0.0.3/24
DNS = 8.8.8.8

[Peer]
PublicKey = <SERVER_PUBLIC_KEY>
Endpoint = <YOUR_EC2_PUBLIC_DNS>:51820
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 25
```

```bash
sudo systemctl enable wg-quick@wg0
sudo systemctl start wg-quick@wg0
curl ifconfig.me  # Should return your AWS Elastic IP
```

### **2.3. Register Clients on the Server**
After setting up each client, register its public key on the server:

```bash
# Run on the SERVER for each client
sudo wg set wg0 peer <CLIENT_PUBLIC_KEY> allowed-ips <CLIENT_VPN_IP>/32
# Example for macOS client:
# sudo wg set wg0 peer <MAC_CLIENT_PUBKEY> allowed-ips 10.0.0.2/32
# Example for Ubuntu client:
# sudo wg set wg0 peer <UBUNTU_CLIENT_PUBKEY> allowed-ips 10.0.0.3/32

sudo systemctl restart wg-quick@wg0
sudo wg show  # Verify peers appear
```

Alternatively, add `[Peer]` blocks directly to `/etc/wireguard/wg0.conf` before starting the service:
```ini
[Peer]
PublicKey = <CLIENT_PUBLIC_KEY>
AllowedIPs = 10.0.0.2/32
```

---

## **3. Debugging & Troubleshooting**

**Check NAT and routing on the server:**
```bash
sudo iptables -t nat -L -n -v
ip route show
```

**Check DNS and routing on clients (macOS & Ubuntu):**
```bash
ping -c 4 8.8.8.8
netstat -rn
```

**Monitor WireGuard traffic on the server:**
```bash
sudo tcpdump -i <NETWORK_INTERFACE> udp port 51820
```

**Check WireGuard handshake and peer status:**
```bash
sudo wg show
```

**Common issues:**
- No handshake → Check that UDP port 51820 is open in the AWS Security Group
- Traffic not routing → Verify `net.ipv4.ip_forward = 1` is active (`sysctl net.ipv4.ip_forward`)
- DNS not resolving on Ubuntu → Make sure `resolvconf` is installed

---

## **Final: Useful Aliases**

Add these to your shell config for quick VPN control:

```bash
# For bash
echo "alias vpn-up='sudo wg-quick up wg0'" >> ~/.bashrc
echo "alias vpn-down='sudo wg-quick down wg0'" >> ~/.bashrc
source ~/.bashrc

# For zsh (macOS default since Catalina)
echo "alias vpn-up='sudo wg-quick up wg0'" >> ~/.zshrc
echo "alias vpn-down='sudo wg-quick down wg0'" >> ~/.zshrc
source ~/.zshrc
```

---

WireGuard is now running on AWS, clients are connected, the correct network interface is identified, DNS is used instead of raw IPs, and NAT routing is properly configured. **If `curl ifconfig.me` returns your AWS Elastic IP from a client - everything is working!** 🚀
