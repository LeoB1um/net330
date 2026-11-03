# Without Authentication (security issues)

So why is unauthenticated OSPF bad? The main problem is losing control of your LSA(Link State Advertisement). If a bad actor gains control they are able to create a crafted LSA, updating each router in a cascading attack (CERT-EU, 2013). The consequences that could form from this attack are false routes, black-holed traffic, and traffic redirected to an attacker-controlled destination (CERT-EU, 2013). The reason these are trusted by default stems from what OSPF sets by default. OSPF has three authentication types, none (0), simple (1), and MD5 (2) (ComputerNetworkingNotes, 2020), and type 0 is what is set default in a OSPF network (Cisco Engineers, 2025).

# How it works and common methods

OSPF has three types of authentication, none (default, type 0), simple (type 1), and MD5 (type 2) (Cisco Engineers, 2025; ComputerNetworkingNotes, 2020). By configuring authentication with type 1 or 2 you can prevent simple accidental route injection as it adds a password layer (Mooney-Perrotta, n.d.). However there are caveats. With type 1: simple, your password is sent as plaintext over your network (Cisco Engineers, 2025). With type 2: MD5, the passwords never crosses your network. Instead of plaintext passwords, MD5 uses a hash derived from packet contents and the key you use and sends it with a key ID and a non-decreasing sequence number (Cisco Engineers, 2025). This allows you to have a key ID that references multiple passwords, making it easier to migrate passwords (Cisco Engineers, 2025). Authentication can be enabled per interface or for a whole area (TCC_2, 2009). So in summary, while plain text authentication prevents accidental route injections from unauthorized routers, it exposes the key to attackers, making MD5 a more secure alternative (Mooney-Perrotta, n.d.).

Opinion: This is probably outdated in 2026 in my opinion.

# References

CERT-EU. (2013, August 2). *OSPF LSA MANIPULATION VULNERABILITY IN MULTIPLE CISCO products*. CERT-EU Security Advisory. <https://cert.europa.eu/publications/security-advisories/2013-0063/pdf>

Cisco Engineers. (2025, March). *Configure authentication in open shortest path first*. Cisco. <https://www.cisco.com/c/en/us/support/docs/ip/open-shortest-path-first-ospf/13697-25.html>

ComputerNetworkingNotes. (2020, January 12). *OSPF authentication password and MD5 explained*. ComputerNetworkingNotes. <https://www.computernetworkingnotes.com/ccna-study-guide/ospf-authentication-password-and-md5-explained.html>

Mooney-Perrotta, J. (n.d.). *Did you know that OSPF has it's own security features?* Westermo. Retrieved November 3, 2026, from <https://www.westermo.com/support/did-you-know/secure-dynamic-routing>

TCC_2. (2009, June 23). *OSPF authentication*. Community.Cisco.Com. <https://community.cisco.com/t5/networking-knowledge-base/ospf-authentication/ta-p/3131640>