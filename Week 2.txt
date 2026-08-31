#Physical Lab

# Boot to kali on nuc

- Put USB into USB slot. 
- Restart nuc and get to boot menu by spamming f10
- boot USB

# Configuring Network On kali

- Run `sudo nmtui`
- Select your network connection
- set IPv4 to manual and then configure
- save the config

# Configuring Router on serial connection

## To get into the correct CLI
enable
configure terminal

## To configure the first interface
interface FastEthernet 0/0
ip address 192.168.1.1 255.255.255.0
no shutdown
exit

## To configure the 2nd interface
interface FastEthernet 0/1
ip address 192.168.2.1 255.255.255.0
no shutdown
exit

end

## Verify connection between systems
show interfaces
Test connectivity
ping 192.168.1.2
ping 192.168.2.2


# Erasing work setting back to 0
## back to config
enable
configure terminal

## Unconfiguring 0/0
interface FastEthernet 0/0
no ip address
shutdown
exit

## Unconfiguring 0/1
interface FastEthernet 0/1
no ip address
shutdown
exit

end
