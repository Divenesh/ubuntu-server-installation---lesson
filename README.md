# 🖥️ Setting Up Your Own Ubuntu Server

## Why Ubuntu Server?

When setting up a personal server, it's important to choose an OS that is flexible, well-supported, and capable of running a wide range of services. **Ubuntu Server** is a powerful, lightweight, and stable operating system based on Debian, and it’s widely adopted by professionals and beginners alike.

While platforms like **TrueNAS** are great for dedicated file storage (NAS) systems, they are more specialized. I chose **Ubuntu Server** because I wanted to build a **general-purpose server**—something that can handle:
- File hosting,
- Web applications,
- Docker containers,
- Databases,
- Remote access,
- and more.

Ubuntu Server gives you full control, a rich ecosystem, and strong community support—perfect for custom setups and future expansion.

---

## 🧰 1. Install Ubuntu Server

- **Download** the latest [Ubuntu Server LTS](https://ubuntu.com/download/server) ISO.
- **Flash the OS** onto your machine using a USB stick. You can follow any good guide like [this YouTube tutorial](https://youtu.be/n7aEcfDNULc?si=eTXHZYjg71VrRUbx).

---

## ⚙️ 2. Real Setup of Ubuntu

### 🔌 Connect to the Network

#### If you're using **Ethernet**:
- Simply plug in the Ethernet cable — it should connect automatically.

#### If you're using **Wi-Fi (WLAN)**:
1. Temporarily connect via **Ethernet** or **USB tethering**.
2. Run the following commands to enable Wi-Fi support:

   ```bash
   sudo apt update
   sudo apt install network-manager
   sudo systemctl stop systemd-networkd
   sudo systemctl disable systemd-networkd
   sudo systemctl enable NetworkManager
   sudo systemctl start NetworkManager
   ```

3. Scan for Wi-Fi networks:

   ```bash
   nmcli device wifi list
   ```

4. Connect to your Wi-Fi network:

   ```bash
   nmcli device wifi connect "YourSSID" password "YourPassword"
   ```

   Or use the **BSSID** (MAC address of the access point):

   ```bash
   nmcli device wifi connect <BSSID> password "YourPassword"
   ```

5. **Verify the connection:**

   ```bash
   ip a
   ```

---

## 🔐 3. Enable Remote Access via SSH

1. **Install OpenSSH:**

   ```bash
   sudo apt update
   sudo apt install openssh-server
   sudo systemctl enable ssh
   sudo systemctl start ssh
   ```

2. **Check if SSH is running:**

   ```bash
   sudo systemctl status ssh
   ```

3. **Allow SSH through the firewall:**

   ```bash
   sudo ufw allow ssh
   sudo ufw enable
   ```

4. **Find your server IP:**

   ```bash
   ip a
   ```

5. **Connect from another machine:**

   ```bash
   ssh yourUsername@yourServerIP
   ```

---

## 🔒 4. Hardening SSH (Highly Recommended)

### ✏️ Basic SSH Protections

1. Edit the SSH configuration:

   ```bash
   sudo nano /etc/ssh/sshd_config
   ```

2. Add or modify the following lines:

   ```bash
   PermitRootLogin no
   PasswordAuthentication no
   ChallengeResponseAuthentication no
   Port 3333
   ```

3. **Apply new firewall rules:**

   ```bash
   sudo ufw allow 3333/tcp
   ```

4. Restart SSH to apply changes:

   ```bash
   sudo systemctl restart ssh
   ```

> 🔐 Now SSH will work **only via keys** and on port **3333**, making it harder to brute-force.


5. **Connect from another machine:**

   ```bash
   ssh yourUsername@yourServerIP -p 3333
   ```


---

### ⚡ SSH Rate Limiting (Optional, but helps)

```bash
sudo ufw limit 3333/tcp
```

---

### 🚫 Automatically Ban IPs After Failed Attempts

1. **Install Fail2Ban:**

   ```bash
   sudo apt install fail2ban
   ```

2. **Configure Fail2Ban:**

   ```bash
   sudo nano /etc/fail2ban/jail.local
   ```

3. Add the following configuration:

   ```ini
   [sshd]
   enabled = true
   port = 3333
   maxretry = 5
   findtime = 10m
   bantime = 1h
   ```

4. **Restart Fail2Ban:**

   ```bash
   sudo systemctl restart fail2ban
   ```

---

## ✅ Conclusion

This guide walks you through the process of turning an old machine into a **secure and functional Ubuntu server**. With basic networking, remote SSH access, and hardening techniques in place, your server is ready to host applications or services from anywhere.

> 🔁 Always stay updated and regularly patch your server:

```bash
sudo apt update && sudo apt upgrade
```
