# Sharing Files from Ubuntu Server with Samba

This guide walks you through setting up a Samba Share on Ubuntu Server to allow file sharing with Ubuntu Desktop, Windows, and macOS clients.

---

## Prerequisites

- Ubuntu Server with `sudo` privileges
- An existing user account for Samba access
- Static IP recommended for easier network access

---

## Step-by-Step Instructions

### 1. Install Samba

```bash
sudo apt update
sudo apt install samba
```

Verify the installation:

```bash
whereis samba
```

Expected output (may vary slightly):

```
samba: /usr/sbin/samba /usr/lib/samba /etc/samba /usr/share/samba /usr/share/man/man7/samba.7.gz /usr/share/man/man8/samba.8.gz
```

---

### 2. Create a Shared Directory

```bash
mkdir /home/<USER_NAME>/<SHARE_DIR_NAME>
```

> Replace `<USER_NAME>` with your Linux username and `<SHARE_DIR_NAME>` with your desired share folder name.

---

### 3. Configure the Samba Share

Open the Samba config file:

```bash
sudo nano /etc/samba/smb.conf
```

Add the following at the **bottom** of the file:

```conf
[sambashare]
    comment = Samba on Ubuntu
    path = /home/<USER_NAME>/<SHARE_DIR_NAME>
    read only = no
    browsable = yes
```

Save and exit (`Ctrl+X`, then `Y`, then `Enter`).

Restart the Samba service:

```bash
sudo service smbd restart
```

Allow Samba through the firewall:

```bash
sudo ufw allow samba
```

---

### 4. Set a Samba Password for the User

```bash
sudo smbpasswd -a <USER_NAME>
```

> ⚠️ This user **must** already exist on the system. Please note the password—you won’t be able to retrieve it later.

---

### 5. Connect to the Share

#### a. Ubuntu Desktop

- Open the file manager.
- Click **Other Locations** > **Connect to Server**.
- Enter:

```
smb://<SERVER_IP_ADDRESS>/<SHARE_DIR_NAME>
```

---

#### b. macOS

- In the Finder, click **Go > Connect to Server**.
- Enter:

```
smb://<SERVER_IP_ADDRESS>/<SHARE_DIR_NAME>
```

---

#### c. Windows

- Open **File Explorer**.
- Enter in the address bar:

```
\\<SERVER_IP_ADDRESS>\<SHARE_DIR_NAME>
```

> You’ll be prompted for the username and Samba password created earlier.

---

## 🛠️ Troubleshooting Tips

- Check the status of the Samba service:

```bash
sudo systemctl status smbd
```

- View Samba logs for errors:

```bash
sudo journalctl -xe | grep smbd
```

---

## Resources

- [Ubuntu Samba Docs](https://ubuntu.com/server/docs/samba-introduction)
- [Samba Wiki](https://wiki.samba.org/index.php/Main_Page)

---

## Suggestions?

Feel free to open a pull request or issue with corrections or ideas.

---





