# Domain Name Service (DNS)
DNS is a service for translating a domain name into an IP address. It has a complex hierarchical structure with many servers which enables scalability.

## Reasons for Hierarchical Architecture
* Having only one server would leave DNS vulnerable to attacks by a bad actor - DNS is a critical server!
* Scalability. There are ~341 million domains (large data volume) with every internet device requiring access = large hardware requirements! Isn't scalable to replicate a single server (monolith) containing the full DNS dataset as it is too large.
* Requirement to delegate control of parts of DNA dataset to certain organisations - e.g. UK domains to be managed by a UK entity. This requires a hierarchical structure.

## Key Terms
A **DNS zone** is a specific portion of the DNS dataset (e.g. *.google.com). A DNS zone contains **DNS records** that which are stored as a **zone file** on a **name server** which hosts them. **Authoritative** zones/records are considered to be the single source of truth and can be trusted, whereas **non-authoritative** are cached for performance reasons (e.g. by ISP).

## DNS Architecture
The DNS root server is the starting point of all DNS requests. There are currently 13 root server IP address (each distributed over multiple physical servers by anycast) globally. The root zone only stores info on **top-level domains (TLDs)** (e.g. .com, .co.uk), which are managed by TLD registries that manage that particular TLD. The function of the root zone is to point to the correct registry for the TLD of the requested domain. The TLD registry is considered authoritative.

**TLD registries** contain information on domains within that particular TLD. This info is high level for the root domain (e.g. google.com), and doesn't contain info on any subdomains (e.g. maps.google.com). Instead, it points to the name server containing lower level info for that particular domain, incl. subdomains. 