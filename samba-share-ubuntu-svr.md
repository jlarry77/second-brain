# Sharing files from Ubuntu Server with Samba Share

1. Update and Install Samba Share on your server

```bash
sudo apt update
sudo apt install samba
```
Verify successful installation
```bash
whereis samba
```
You should receive this output:
```samba: /usr/sbin/samba /usr/lib/samba /etc/samba /usr/share/samba /usr/share/man/man7/samba.7.gz /usr/share/man/man8/samba.8.gz```

2.  Create a Shared Directory
```bash
mkdir /home/<USER_NAME>/<SHARE_DIR_NAME>
```

3.  Add the new directory to the Samba Share .conf file.
Add these lines to the bottom of: `/etc/samba/smb.conf'
```bash
sudo nano /etc/samba/smb.conf
```
```conf
[sambashare]
    comment = Samba on Ubuntu                    # A brief Description of the Shared Directory
    path = /home/<USER_NAME>/<SHARE_DIR_NAME>    # The path to the Shared Directory
    read only = no                               # Read Only: 'no' grants write permissions to this folder
    browsable = yes                              # 'yes' enables Ubuntu's file manager to list this share under 'Network'
```
Save and Exit (Ctrl-x, y, <enter>)

Restart the Samba Share Service
```bash
sudo service smbd restart
```
Update firewall rules ot allow Samba Traffic
```bash
sudo ufw allow samba
```

4.  Set up a user account.  Be sure to make note of the password that you are using, you will not be able to retrieve it later!  The user also must be a current user for the system.
```bash
sudo smbpasswd -a <USER_NAME>
```

5.  Connecting to the share:
  a.  Ubuntu Desktop
  Open the default file manager and click 'Connect to Server' then enter:
    smb://<SERVER_IP_ADDRESS>/<SHARE_DIR_NAME>

  b.  MacOS
  In the Finder Menu, clock 'Go>Connecto to Server' then enter:
    smb://<SERVER_IP_ADDRESS>/<SHARE_DIR_NAME>

  c.  Windows
  Open up 'File Explorer' and add this to the file path:
    \\<SERVER_IP_ADDRESS>\<SHARE_DIR_NAME>

All three options will prompt you for the User Name and the Password that you created for the shared directory.




