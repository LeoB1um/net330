# 6-1

### 1. Setting NAT Interfaces
Before translating traffic, you must define which router interface connects to the internal private network and which connects to the external public network.

* **Inside Interface (Private LAN):** `FastEthernet 0/0`
* **Outside Interface (Public WAN):** `Serial 0/0/0`

```text
R1(config)# interface fastEthernet 0/0
R1(config-if)# ip nat inside
R1(config-if)# exit

R1(config)# interface serial 0/0/0
R1(config-if)# ip nat outside
R1(config-if)# exit
```

---

### 2. Commands for Static NAT with Example
A static NAT rule creates a permanent 1-to-1 mapping between an internal private IP address and an external public IP address.

* **Syntax:** `ip nat inside source static [private_ip] [public_ip]`
* **Example:** To map the internal Web Server (`10.0.0.2`) to the public IP address (`50.0.0.1`), use:

```text
R1(config)# ip nat inside source static 10.0.0.2 50.0.0.1
```

# 6-2

## TECH Journal Entry: Configuring PAT (Port Address Translation / NAT Overload)

### Objective
Configure **Port Address Translation (PAT)** on a Cisco router to allow multiple private internal hosts (e.g., `192.168.0.0/24`) to share a single public IP address (`30.0.0.120`) for external network access.

---

### Step-by-Step PAT Configuration Blueprint

#### Step 1: Define Inside and Outside Boundaries
The router must know which interface connects to your private network and which interface connects to the public provider network. Without these markers, no traffic will trigger the translation engine.

```text
R1(config)# interface FastEthernet0/0
R1(config-if)# ip nat inside
R1(config-if)# exit

R1(config)# interface Serial0/0/0
R1(config-if)# ip nat outside
R1(config-if)# exit
```
* **`ip nat inside`**: Tells the router this interface processes traffic coming from local private devices.
* **`ip nat outside`**: Tells the router this interface processes traffic heading toward external public networks.

#### Step 2: Create the Public Address Pool
Define the public IP address range given to you by the provider that your internal devices will map onto.

```text
R1(config)# ip nat pool test 30.0.0.120 30.0.0.120 netmask 255.0.0.0
```
* **`test`**: The case-sensitive name of your address pool.
* **`30.0.0.120 30.0.0.120`**: The starting and ending IP addresses of the pool. Because this pool only contains **one single IP**, the start and end values match perfectly.
* **`netmask 255.0.0.0`**: Defines the network mask for the pool range.

#### Step 3: Define the Internal Scope via Access Control List (ACL)
Create a standard access list that acts as a filter to specify exactly which local private subnets are authorized to be translated.

```text
R1(config)# access-list 1 permit 192.168.0.0 0.0.0.255
```
* **`access-list 1`**: Creates a standard access list numbered 1.
* **`permit 192.168.0.0 0.0.0.255`**: Authorizes any device starting with `192.168.0.X` (using the wild-card mask `0.0.0.255`) to pass through the filter.

#### Step 4: Bind the Components and Enable Overload (PAT)
Tie the ACL and the NAT Pool together to activate the final translation rules.

```text
R1(config)# ip nat inside source list 1 pool test overload
```
* **`inside source list 1`**: Directs the router to match source traffic permitted by Access List 1.
* **`pool test`**: Dictates that matched traffic should be translated using the IP space defined in your "test" pool.
* **`overload`**: **The core PAT mechanism.** It tells the router to track translations using unique TCP/UDP port numbers, allowing thousands of internal clients to securely reuse the same public IP address simultaneously.

---

### Key Verification & Troubleshooting Commands

If your translation table looks empty, remember that **NAT only populates dynamically when active traffic is crossing the boundaries.** Always generate network traffic from a client PC (like running a continuous ping or loading a web page) *before* trying to verify:

* **View Active Mappings:** `show ip nat translations`  
  *Shows how internal private IPs and port numbers map directly onto the pool IP.*
* **View Global PAT Metrics:** `show ip nat statistics`  
  *Displays total active translations, hits, misses, interfaces, and pool capacities.*

# 6-3
##TECH Journal Entries for Lab 6-3: PAT & Combined NAT

### 1. Creating a NAT Address Pool
When using dynamic NAT or PAT, you must define the public IP address range (pool) that the router is allowed to use for translating internal traffic.

* **Command:** `ip nat pool [pool_name] [start_ip] [end_ip] netmask [subnet_mask]`
* **Example:** `ip nat pool public-pat 219.93.144.120 219.93.144.120 netmask 255.255.255.0`
* **Explanation:** Creates a pool named `public-pat`. Since this specific lab only requires one public IP address for all clients to share, the start and end IP addresses are identical (`219.93.144.120`).

---

### 2. Allowing Multiple Networks in a Standard Access List (ACL)
To allow multiple distinct private subnets to share the same public NAT address pool, you can add multiple permit lines sequentially under the same Access List number.

* **Commands:**
  ```text
  R1(config)# access-list 1 permit 192.168.1.0 0.0.0.255
  R1(config)# access-list 1 permit 192.168.3.0 0.0.0.255
  ```
* **Explanation:** Both lines are tied to `access-list 1`. The first line permits the Skiff network range, and the second line adds a permit rule for the Foster network range. 

---

### 3. Binding the Access List to the Pool with Overload (PAT)
This command ties your internal network filters (the ACL) to your external public address space (the Pool) and turns on port-level translation.

* **Command:** `ip nat inside source list [acl_number] pool [pool_name] overload`
* **Example:** `ip nat inside source list 1 pool public-pat overload`
* **Explanation:** Tells the router to take any traffic permitted by Access List 1 and translate it using the IP address from the `public-pat` pool. The **`overload`** keyword at the end is what changes standard NAT into PAT (Port Address Translation), enabling thousands of local devices to securely share a single public IP by tracking unique TCP/UDP port numbers.

---

### 4. Verifying Active Mappings
Unlike static configurations, dynamic PAT tables are temporary and only populate when there is live traffic passing through the router.

* **Command:** `show ip nat translations`
* **Explanation:** Run from privileged EXEC mode (`R1#`), this command prints out the active translation table. It lets you visually confirm that internal private IPs (`Inside local`) are successfully converting into the public pool address (`Inside global`) accompanied by unique port numbers when communicating with external web servers.
