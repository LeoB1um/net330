## 1. Mode Navigation & Prompts

* **User EXEC Mode** (`Switch>`) 
  * *Purpose:* View basic status. 
  * *Command to enter:* Default mode when logging in.
  * *Command to exit/move up:* Type `enable` to move to Privileged EXEC.
* **Privileged EXEC Mode** (`Switch#`) 
  * *Purpose:* View detailed configurations and run `show` commands. 
  * *Command to enter:* `enable`
  * *Command to exit/move up:* Type `configure terminal` (or `config t`) to move to Global Config. Type `disable` or `exit` to go back to User EXEC.
* **Global Configuration Mode** (`Switch(config)#`) 
  * *Purpose:* Make global changes to the device. 
  * *Command to enter:* `configure terminal`
  * *Command to exit/move up:* Type `interface [name]` to enter specific sub-modes. Type `exit` to go back one level, or `end` to drop straight back to Privileged EXEC.

---

## 2. Creating VLANs

```text
Switch(config)# vlan 100
Switch(config-vlan)# name FacStaff
Switch(config-vlan)# exit
```

---

## 3. Setting Access & Trunk Ports

### Access Port (For end devices like PCs or printers)
```text
Switch(config)# interface fastEthernet 0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 100
```

### Trunk Port (For connections between switches or routers)
```text
Switch(config)# interface gigabitEthernet 0/1
Switch(config-if)# switchport mode trunk
```

---

## 4. Configuring Interface Ranges

```text
Switch(config)# interface range fastEthernet 0/4-12
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 100
```
