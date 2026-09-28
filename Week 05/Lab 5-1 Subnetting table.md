### Hospital IP Subnet Allocation Table (Base: 10.20.0.0/16)

| Subnet Name | VLAN ID | Hosts Needed | Network ID | Subnet Mask | First Usable IP | Last Usable IP | Default Gateway | DHCP Pool Range |
| :--- | :---: | :---: | :--- | :--- | :--- | :--- | :--- | :--- |
| **Clinic** | 10 | 300 | `10.20.0.0 /23` | `255.255.254.0` | `10.20.0.1` | `10.20.1.254` | `10.20.0.1` | `10.20.0.2` - `10.20.1.254` |
| **Visitor** | 20 | 300 | `10.20.2.0 /23` | `255.255.254.0` | `10.20.2.1` | `10.20.3.254` | `10.20.2.1` | `10.20.2.2` - `10.20.3.254` |
| **Office** | 30 | 300 | `10.20.4.0 /23` | `255.255.254.0` | `10.20.4.1` | `10.20.5.254` | `10.20.4.1` | `10.20.4.2` - `10.20.5.254` |
| **Default Subnet** | 1 | 150 | `10.20.6.0 /24` | `255.255.255.0` | `10.20.6.1` | `10.20.6.254` | `10.20.6.1` | `10.20.6.10` - `10.20.6.254`* |
| **Counseling** | 40 | 150 | `10.20.7.0 /24` | `255.255.255.0` | `10.20.7.1` | `10.20.7.254` | `10.20.7.1` | `10.20.7.2` - `10.20.7.254` |

*\*Note: The DHCP pool for the Default Subnet (VLAN 1) starts at .10 to leave static IP spaces (.2 through .9) available for core infrastructure services like your required DHCP and DNS servers.*
