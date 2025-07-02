# Self Hosted Media Hub

This guide provides step-by-step instructions for setting up a secure personal media server using Ubuntu Server, VirtualBox, Samba, Jellyfin, WireGuard, and Duck DNS. It is tailored for intermediate users with basic knowledge of networking and the Linux command line.

---

## Step 1: Set Up the Virtual Machine (VM)

- Enable virtualization in BIOS.
- Install Ubuntu Server in VirtualBox.
- Configure VM settings:
  - Name the VM, set username and password.
  - Allocate memory and disk space.
  - Go to VM settings > Network:
    - Enable network adapter.
    - Set "Attached to" = "Bridged Adapter".
- Boot into the VM, login with your credentials.
- Test network access:
  ```bash
  ping google.com
  ```
  Press Ctrl+C to stop.

---

## Step 2: Assign a Static IP Address

- Use router DHCP reservation (recommended):
  - Access router admin (e.g., 192.168.1.1).
  - Find DHCP/static lease settings.
  - Identify your server (check MAC address with `ip a`).
  - Assign a static IP (e.g., 192.168.1.100).
  - Save settings and reboot router.

---

## Step 3: Connect via SSH

- Use another device to SSH into your server:
  ```bash
  ssh username@192.168.1.100
  ```
  Replace username and IP accordingly.

---

## Step 4: Use Dynamic DNS (Duck DNS) (Optional)

If router static IP reservation is unavailable:

- Go to https://www.duckdns.org, create an account.
- Register a subdomain (e.g., myserver.duckdns.org).
- Follow the install instructions under "linux cron".

---

## Step 5: Set Up Samba File Sharing

- Install Samba:
  ```bash
  sudo apt install samba
  ```
- Create shared folder:
  ```bash
  sudo mkdir /media/myfiles
  sudo chown $USER: /media/myfiles
  ```

---

## Step 6: Configure Samba

- Edit Samba config:
  ```bash
  sudo nano /etc/samba/smb.conf
  ```
- Change:
  map to guest = never
- Add at the bottom:
  [myfiles]
  path = /media/myfiles
  writeable = yes
  public = no
- Save and exit (Ctrl+X, then Y, then Enter).
- Set Samba password:
  ```bash
  sudo smbpasswd -a youruser
  ```
- Restart service:
  ```bash
  sudo systemctl restart smbd
  ```

Access from other devices:
- Windows: \\192.168.1.100\myfiles
- Linux: smb://192.168.1.100/myfiles

---

## Step 7: Port Forwarding for WireGuard

- Log into router admin interface.
- Go to Port Forwarding/NAT settings.
- Add a new rule:
  - Protocol: UDP
  - Port: 51820
  - IP: Your server's static IP
- Save changes.

Help: https://www.reddit.com/r/HomeNetworking/comments/i7ijiz/a_guide_to_port_forwarding/

---

## Step 8: Install WireGuard with PiVPN

- Run PiVPN script:
  ```bash
  curl -L https://install.pivpn.io | bash
  ```
- Select WireGuard when prompted.
- Use your Duck DNS domain (e.g., myserver.duckdns.org).
- Enable unattended upgrades.

---

## Next Steps

- Add clients to WireGuard with `pivpn add`.
- Install Jellyfin for media streaming:
  ```bash
  sudo apt install jellyfin
  ```
- Access Jellyfin at: http://your-server-ip:8096

Enjoy your private, secure home server!
