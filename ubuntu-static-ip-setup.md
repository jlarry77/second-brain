# Static IP Configuration for Ubuntu

#  1.  Create a backup of your current `50-cloud-init.yaml` and edit the existing copy:
`Bash`
```
sudo cp /etc/netplan/50-cloud-init.yaml /etc/netplan/50-cloud-init.bak
```
#  2.  Verify backup has been created, you should see both `50-cloud-init.yaml` and `50-cloud-init.bak`:
`Bash`
```
ls -l /etc/netplan/
```
#  3. Find your default gateway:
`Bash`
```ip route```

The output should start with 'default via <GATEWAY_ADDRESS> dev <DEVICE_NAME> proto dhcp src <IP_ADDRESS> ...

# 4.  Edit `50-cloud-init.yaml`:
`YAML`
```
network:
  version:2
  ethernets:
    <DEVICE_NAME>:  ## Replace DEVICE_NAME with something like 'ens18' or the actual interface name for your VM or Server
      address:
      - <IP_ADDRESS>/24  ## Replace <IP_ADDRESS> with your desired IP Address
      routes:
      - to: default
        via: <DEFAULT_GATEWAY>   ## Replace <DEFAULT_GATEWAY> with your Default Gateway address
      nameservers:
        addresses:
        - 8.8.8.8
        - 8.8.4.4
```
# 5.  Apply the new netplan settings:
`Bash`
```sudo netplan apply```

# 6.  Disable Cloud-Init Network Config
`Bash`
```sudo nano /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg```
```network: {config:disabled}```

#  7.  Make sure that the static IP is correctly set
`Bash`
```
ip a | grep <DEVICE_NAME>
ip route
sudo reboot now
```
