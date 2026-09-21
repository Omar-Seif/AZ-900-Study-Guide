# AZ-900 Track Summary — Domain 2: Describe Azure Architecture and Services
**Exam weight: 35–40% (the largest domain)** · Covers Modules 4–8 (core architecture, compute, networking, storage, identity/access/security)

Condensed review sheet for the whole second track. This domain is the most detail- and number-heavy — use the cheat-boxes for last-minute cramming.

---

## 1. Core Architectural Components (Module 4)

**Physical hierarchy:** Geography → Region → Availability Zone → Datacenter.
**Management hierarchy:** Management Group → Subscription → Resource Group → Resource. *(Policy/access inherit downward — this ladder resurfaces all over the exam.)*

- **Region** = geographic area with 1+ networked datacenters. Some services are **global/non-regional** (Entra ID, Traffic Manager, DNS).
- **Availability Zones** = physically separate datacenters in one region, independent power/cooling/network. **Min 3** where supported, but not all regions support them.
  - **Zonal** (you pin it) · **Zone-redundant** (platform replicates) · **Non-regional** (survives zone *and* region outages).
- **Region pairs** = same-geography backup, **300+ miles** apart. Usually bidirectional — **Brazil South is the exception** (one-way, paired outside its geography with South Central US). A few regions (Italy North, Poland Central, Israel Central) have no pair.
- **Sovereign regions** = isolated Azure instances for legal/compliance (US Gov, China via 21Vianet).
- **Resource groups:** flat (can't nest), can't be renamed, one resource = one group, deleting the group deletes everything in it.
- **Management groups:** CAN nest (up to 6 levels), one Tenant Root Group per tenant, one parent each, up to 10,000 per directory.
- **Free accounts:** standard = $200 / 30 days + 12 months free services; **Azure for Students = $100 / 12 months, no credit card.**

## 2. Compute (Module 5)

- **VMs** = IaaS. Naming like `Standard_D2s_v5` = family / vCPUs / premium-storage / generation.
- **Scale Sets** = elasticity (auto add/remove identical VMs). **Availability Sets** = resiliency (via **update domains** = planned reboots, **fault domains** = hardware failure points). *Availability Zones are preferred over availability sets where supported.*
  - Resiliency ladder: **Availability Set** (different racks, same building) → **Availability Zone** (different buildings, same region) → **Region Pair** (different regions).
- **Containers** = lighter than VMs (no OS to manage). **Container Instances** (simplest) → **Container Apps** (adds built-in scaling/load balancing) → **AKS** (full orchestration).
- **Azure Functions** = serverless, event-driven, pay-per-execution. Stateless by default; **Durable Functions** = stateful multistep.
- **App Service** = PaaS hosting umbrella (Web apps, API apps, WebJobs, Mobile apps) with built-in scaling/HA/load balancing.
- ⏭️ AI/ML/IoT services in the training module are **not on the AZ-900 skills list**.

## 3. Networking (Module 6)

- **Peering** (VNet ↔ VNet, even across regions) vs. **Service Endpoint** (VNet ↔ PaaS resource type) vs. **Private Endpoint** (VNet ↔ one specific resource, with its own private IP).
- On-prem connectivity: **Point-to-site** (one device) · **Site-to-site** (whole network) · **ExpressRoute** (dedicated private line, skips the internet).
- **VPN Gateway:** only **one per VNet**, but connects to multiple locations. **Route-based** preferred over policy-based. HA modes: active/standby (default), active/active (BGP), ExpressRoute failover, **zone-redundant**.
- **NSG** = rule-based filtering (IP/port/protocol). **NVA** = dedicated appliance VM (firewall, WAN optimization).
- **UDR** = manual routes; **BGP** = automatic route propagation.
- **ExpressRoute** avoids the public internet — **but DNS, CRL checks, and CDN requests still use the internet.**
- **Azure DNS** hosts records (anycast, alias records auto-follow IP changes) — but **can't buy you a domain name.**

## 4. Storage (Module 7)

- **Account names:** 3–24 chars, lowercase + numbers, **globally unique**.
- **Standard general-purpose v2** = only type supporting geo-redundancy; all **Premium** types are LRS/ZRS only.
- **Blob tiers:** Hot / Cool (30d) / Cold (90d) / Archive (180d). **Hot/Cool/Cold can be account-level; Archive is blob-level only.**
- **Redundancy** (always 3 copies in primary region): **LRS** (1 datacenter) → **ZRS** (3 zones) → **GRS** (adds secondary region, LRS+LRS) → **GZRS** (ZRS primary + LRS secondary). **RA-** prefix = readable secondary before failover. Geo-replication is **asynchronous** → **RPO** ~<15 min, no SLA.
- **Migration:** **Azure Migrate** (online) vs. **Azure Data Box** (offline physical, up to 80 TB).
- **File movement:** **AzCopy** (one-way) · **Storage Explorer** (GUI over AzCopy) · **Azure File Sync** (bi-directional + cloud tiering).

## 5. Identity, Access & Security (Module 8)

- **Entra Connect** = two-way sync (on-prem ↔ cloud). **Entra Domain Services** = one-way sync (Entra ID → Domain Services only).
- **Auth:** SSO (convenient, concentrates risk on initial authenticator) → MFA (know/have/are) → Passwordless (Windows Hello / Authenticator app / FIDO2).
- **External identities:** **B2B collaboration** (guest users, in directory) vs. **B2B direct connect** (not in directory, Teams shared channels) vs. **External ID for customers** (consumer apps).
- **Conditional Access** = signal → decision → enforcement (makes MFA context-aware).
- **Zero Trust** = verify explicitly, least privilege, assume breach (a *mindset*).
- **RBAC** = who can do what; inherits down the hierarchy; enforced via Azure Resource Manager; additive allow model; doesn't cover app/data-level security.
- **Defense-in-depth** = 7 layers (Physical → Identity → Perimeter → Network → Compute → Application → Data). A *structural model*, distinct from Zero Trust.
- **Defender for Cloud** = Assess → Secure → Defend. Extends to hybrid/on-prem via **Azure Arc** (full plans); to multicloud AWS/GCP via **CSPM** (agentless assessment).

---

## Cheat-Box: Key Numbers

| Fact | Number |
|---|---|
| Min availability zones (where supported) | 3 |
| Region-pair min distance | 300 miles |
| Management group nesting | 6 levels |
| Management groups per directory | 10,000 |
| Storage copies in primary region | 3 (always) |
| LRS / ZRS / GRS-GZRS durability | 11 / 12 / 16 nines |
| Storage account name | 3–24 chars |
| Cool / Cold / Archive min retention | 30 / 90 / 180 days |
| Typical storage RPO | <15 min (no SLA) |
| Azure Data Box max capacity | 80 TB |
| Queue message max | 64 KB |
| VPN Gateways per VNet | 1 |

## Cheat-Box: Top Trap Pairs

| A | vs. | B |
|---|---|---|
| Resource groups (flat, no nesting) | | Management groups (nest to 6) |
| Scale Sets (elasticity) | | Availability Sets (resiliency) |
| Update domain (planned reboots) | | Fault domain (hardware failures) |
| Peering (VNet↔VNet) | | Endpoints (VNet↔service/resource) |
| Point-to-site (device) | | Site-to-site (whole network) |
| NSG (rules) | | NVA (appliance VM) |
| UDR (manual routes) | | BGP (auto routes) |
| Entra Connect (two-way) | | Entra Domain Services (one-way) |
| B2B collaboration (guest in directory) | | B2B direct connect (not in directory) |
| Zero Trust (mindset) | | Defense-in-depth (layers) |
| Azure Arc (full hybrid protection) | | CSPM (agentless multicloud assessment) |

---

## Consolidated Scenario Table

| Scenario | Answer |
|---|---|
| Delete a whole temp environment in one action | Resource group |
| One policy across 40 subscriptions, no override | Management group |
| SQL DB auto-replicated across zones, no manual setup | Zone-redundant service |
| Fail over to another region in the same geography after a disaster | Region pair |
| U.S. federal agency needs isolated, screened-personnel Azure | Sovereign region |
| Auto add/remove identical VMs based on demand | Scale Sets |
| Simplest way to run one container, no VM management | Container Instances |
| Container workload needing built-in scaling/load balancing | Container Apps |
| Event-triggered code, quick, no idle cost | Azure Functions |
| Two VNets in different regions talk directly | Peering |
| Highest bandwidth, lowest latency, avoid public internet | ExpressRoute |
| Block traffic from an IP range on a port | NSG |
| Premium performance + geo-redundancy needed | Not possible together — must use Standard GPv2 for geo |
| Apply Archive tier to a whole account | Not possible — Archive is blob-level only |
| Move 60 TB with limited bandwidth | Azure Data Box |
| Local Windows server continuously synced with Azure Files | Azure File Sync |
| Passwordless sign-in tied to a specific work PC | Windows Hello for Business |
| Unphishable hardware key, no username | FIDO2 |
| Guest users from a supplier, visible in your directory | B2B collaboration |
| Skip MFA at known locations, require it at unusual ones | Conditional Access |
| Extend full Defender protection to an on-prem server | Azure Arc |

---

## Practice Questions

**Q1 (Easy).** Minimum availability zones in a zone-enabled region?
A) 2 · B) 3 · C) 5 · D) No minimum
<details><summary>Answer</summary>**B — 3.**</details>

**Q2 (Easy).** Which is TRUE of resource groups?
A) Can be nested · B) A resource can be in two at once · C) Deleting the group deletes its resources · D) Can be renamed anytime
<details><summary>Answer</summary>**C.** Resource groups are flat, one-group-per-resource, and can't be renamed.</details>

**Q3 (Medium).** VMs auto-added/removed based on traffic, with built-in load balancing?
A) Availability sets · B) VM Scale Sets · C) Fault domains · D) Container Instances
<details><summary>Answer</summary>**B — Scale Sets** (elasticity). Availability sets are for resiliency.</details>

**Q4 (Medium).** Which statement about ExpressRoute is TRUE?
A) All traffic bypasses the internet once configured · B) DNS, CRL checks, and CDN still use the public internet · C) It requires NSG filtering · D) It replaces VPN Gateway in all HA scenarios
<details><summary>Answer</summary>**B.** ExpressRoute's circuit traffic avoids the internet, but those three traffic types still use it.</details>

**Q5 (Medium).** Read secondary-region data before failover AND zone resilience in the primary?
A) GRS · B) RA-GRS · C) RA-GZRS · D) ZRS
<details><summary>Answer</summary>**C — RA-GZRS.** "Z" = zone resilience in primary; "RA-" = read access before failover.</details>

**Q6 (Medium).** Which passwordless method is tied to a specific Windows PC?
A) Authenticator app · B) FIDO2 key · C) Windows Hello for Business · D) Conditional Access
<details><summary>Answer</summary>**C.** Its biometric/PIN credentials are tied to that PC.</details>

**Q7 (Hard).** Premium block blob storage AND geo-redundancy for DR. Which account type?
A) Premium block blobs + GZRS · B) Standard GPv2 (Premium types don't support geo-redundancy) · C) Premium page blobs + GRS · D) Premium file shares + RA-GRS
<details><summary>Answer</summary>**B.** No Premium type supports any geo-redundant option — only Standard GPv2 does.</details>

**Q8 (Hard — cumulative).** App on App Service (PaaS, Microsoft patches) deployed across Availability Zones, configured to fail over to a paired region. Which THREE concepts?
A) IaaS + Scale Sets + sovereign regions
B) PaaS shared responsibility + Availability Zones + region pairs
C) SaaS + fault domains + multicloud
D) Serverless + Container Apps + hybrid
<details><summary>Answer</summary>**B.** App Service = PaaS; zones + region-pair failover are core architecture concepts.</details>

**Q9 (Hard — cumulative).** A user has Owner at management group scope. Effect?
A) Only the management group object itself · B) Everything in every subscription beneath it (inheritance) · C) Nothing until also assigned at subscription level · D) All of Azure globally
<details><summary>Answer</summary>**B.** RBAC inherits down the Management Group → Subscription → Resource Group → Resource ladder.</details>

---

### Key Takeaways
- This domain builds on itself: the **management hierarchy** appears for governance, storage secondary-region logic, *and* RBAC scope.
- **Availability Zones** power reliability across compute (availability sets/zones), networking (zone-redundant gateways), and storage (ZRS).
- **Azure Arc** (first met in Domain 1) pays off here by extending Defender for Cloud to hybrid machines.
