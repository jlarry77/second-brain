# Static IP Configuration for Ubuntu

This guide walks you through configuring a static IP address on Ubuntu using Netplan and disabling Cloud-Init’s default network configuration.

---

## Prerequisites

- This guide assumes you are using **Ubuntu 18.04+** with Netplan enabled.
- You need `sudo` access to the system.
- Know the name of your network interface (e.g., `ens18`, `eth0`, etc.).

---

## Step-by-Step Guide

### 1. Backup Your Current Netplan Config

```bash
sudo cp /etc/netplan/50-cloud-init.yaml /etc/netplan/50-cloud-init.bak
```

---

### 2. Verify Backup Was Created

```bash
ls -l /etc/netplan/
```

You should see both:

```
50-cloud-init.yaml
50-cloud-init.bak
```

---

### 3. Identify Default Gateway and Network Interface

```bash
ip route
```

Look for a line like:

```
default via <GATEWAY_ADDRESS> dev <DEVICE_NAME> proto dhcp src <IP_ADDRESS>
```

Take note of:

- `<GATEWAY_ADDRESS>`
- `<DEVICE_NAME>`
- `<IP_ADDRESS>`

---

### 4. Edit Netplan Configuration

Open the file:

```bash
sudo nano /etc/netplan/50-cloud-init.yaml
```

Replace with something like:

```yaml
network:
  version: 2
  ethernets:
    <DEVICE_NAME>:
      addresses:
        - <IP_ADDRESS>/24
      routes:
        - to: default
          via: <DEFAULT_GATEWAY>
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
```

> Replace placeholders:
> - `<DEVICE_NAME>`: e.g., `ens18`
> - `<IP_ADDRESS>`: Desired static IP address
> - `<DEFAULT_GATEWAY>`: Gateway address from step 3

---

### 5. Apply the Netplan Configuration

```bash
sudo netplan apply
```

---

### 6. Disable Cloud-Init Network Configuration (Optional but Recommended)

Create the file:

```bash
sudo nano /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
```

Add this content:

```yaml
network: {config: disabled}
```

---

### 7. Verify Network Configuration and Reboot

```bash
ip a | grep <DEVICE_NAME>
ip route
sudo reboot now
```

After reboot, run `ip a` and `ip route` again to ensure your static IP is active.

---

## Example Output

```bash
ip route
```

```
default via 192.168.1.1 dev ens18 proto static
192.168.1.0/24 dev ens18 proto kernel scope link src 192.168.1.100
```

---

## Resources

- [Netplan.io Documentation](https://netplan.io/reference/)
- [Cloud-Init Docs](https://cloudinit.readthedocs.io/en/latest/)
- [Ubuntu Static IP Wiki](https://help.ubuntu.com/community/Netplan)

---

## Suggestions?

Feel free to open a pull request or issue if you have suggestions or corrections.

---
