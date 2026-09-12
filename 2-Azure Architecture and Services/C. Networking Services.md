# AZ-900 — Describe Azure Architecture and Services (35–40% of exam)
## Module: Describe Azure Networking Services

> **Exam context:** This module **completes the official "Describe Azure compute and networking services" skill bucket** — combined with your last module (VMs, containers, Functions, App Service), you've now covered every officially listed item in that section, including the "public and private endpoints" bullet that showed up explicitly here. Verified against Microsoft's current study guide — no gaps this time, everything pasted maps to an official bullet point.
>
> **What's next in Domain 2:** "Describe Azure storage services," then "Describe Azure identity, access, and security" — the two remaining pieces of this domain.
>
> This is a **trap-dense module** — lots of "which of these three similar-sounding things do I use" decisions. I've built comparison tables for each cluster rather than treating them as isolated facts, since that's how the real exam tends to test this material.

---

## 1. Virtual Networks and Subnets

**Core idea:** An Azure Virtual Network (VNet) lets your Azure resources (VMs, web apps, databases) talk to each other, to the internet, and to your on-premises network — think of it as an extension of your own network into Azure.

**What a VNet gives you:**
- Isolation and segmentation (multiple separate private networks)
- Internet communication
- Communication between Azure resources
- Communication with on-premises resources
- Traffic routing and filtering
- The ability to connect VNets to each other

**Subnets:** You define a private IP address range for your VNet (not internet-routable on its own), then divide it into named subnets, allocating part of that address space to each.

**Public vs. private endpoints (officially named in this module's skills list):**

| | Public endpoint | Private endpoint |
|---|---|---|
| IP type | Public IP address | Private IP address, from within the VNet's address space |
| Reachable from | Anywhere on the internet | Only from within the VNet (or connected/peered networks) |

**🧠 Memory trick:** *"Public = anyone, anywhere. Private = only inside your own fence."*

---

## 2. Connecting Azure Resources to a VNet — The Three-Way Trap

This is the **single most-confused cluster of terms** in Azure networking fundamentals — three different mechanisms that all sound like "connecting things privately":

| | What it connects | How | Key detail |
|---|---|---|---|
| **VNet Peering** | Two entire **VNets** to each other | Direct connection over Microsoft's private backbone, never touching the public internet | Can span **different regions** — builds a global interconnected network |
| **Service Endpoint** | A VNet to specific **PaaS resource types** (e.g., Azure SQL Database, Storage accounts) | Extends your VNet's identity to the service over the Microsoft backbone | Improves security and routing between the VNet and that resource type generally |
| **Private Endpoint** | A VNet to **one specific instance** of a resource | Assigns that resource a private IP *inside* your VNet's own address space | Most granular — a dedicated private IP mapped to one exact resource |

**🧠 Memory trick:** *"Peering = network-to-network. Service Endpoint = network-to-service-type. Private Endpoint = network-to-one-specific-resource, with its own private IP."*

**🚩 Exam trap:** A scenario describing "two entire virtual networks need to communicate directly, including across regions" is testing **peering**, not service/private endpoints — those two are about reaching *specific PaaS resources*, not connecting whole VNets together.

---

## 3. Connecting to On-Premises Networks

Three ways to link Azure back to your own infrastructure — another comparison-heavy cluster:

| | Connects | Typical use |
|---|---|---|
| **Point-to-site VPN** | A single external device/computer → Azure VNet | An individual remote user connecting in |
| **Site-to-site VPN** | An entire on-premises network (via VPN device/gateway) → Azure VPN Gateway | Whole office/datacenter network appearing as if it's local to Azure |
| **Azure ExpressRoute** | On-premises network → Azure, via a dedicated private connection (not over the internet) | High-bandwidth, high-security, low-latency needs |

**🧠 Memory trick:** *"Point-to-site = one point (device). Site-to-site = one whole site (network) to another. ExpressRoute = skip the internet entirely."*

**Direct callback to Domain 1:** All three of these are literally *how* the hybrid cloud deployment model gets implemented technically — this is the concrete mechanism behind that abstract concept from Module 1.

---

## 4. VPN Gateway Deep Dive

A **VPN Gateway** is a type of virtual network gateway, deployed in a dedicated subnet, enabling:
- Site-to-site (on-prem datacenter ↔ VNet)
- Point-to-site (individual device ↔ VNet)
- Network-to-network (VNet ↔ VNet)

**🚩 Key cardinality trap:** You can deploy **only one VPN Gateway per virtual network** — but that single gateway **can connect to multiple locations** (multiple on-prem sites and/or multiple other VNets) at once. Don't confuse "one gateway" with "one connection."

**Authentication:** Regardless of VPN type, Azure uses a **preshared key**.

### Policy-based vs. Route-based Gateways

| | Policy-based | Route-based |
|---|---|---|
| How it decides encryption | Statically matches packet IP addresses against defined lists | Models IPSec tunnels as network interfaces; IP routing (static or dynamic) decides which tunnel to use |
| Resilience to topology changes | Lower | **Higher** — preferred for most on-premises connectivity |
| Required for | — | VNet-to-VNet connections, point-to-site, multisite, ExpressRoute coexistence, high-availability scenarios |

**🧠 Memory trick:** *"Policy-based = static list-checking. Route-based = dynamic, interface-based, and the more flexible/preferred default."*

### VPN Gateway High Availability — Four Options

| Option | How it works |
|---|---|
| **Active/standby (default)** | Two instances always deployed even though you see one resource; standby takes over automatically on failure — restored in seconds (planned maintenance) or ~90 seconds (unplanned disruption) |
| **Active/active** | Requires BGP; each instance gets its own public IP, with separate tunnels from the on-prem device to each |
| **ExpressRoute failover** | A VPN Gateway configured as a backup path if the ExpressRoute circuit itself fails |
| **Zone-redundant gateways** | Deployed across Availability Zones for resilience against zone-level failures — requires different gateway SKUs and **Standard** (not Basic) public IPs |

**Direct callback to Domain 2, Module 1:** Zone-redundant gateways are the concrete networking payoff of the Availability Zones concept you learned earlier — physically and logically separating gateway instances across zones, same underlying principle applied to network infrastructure specifically.

**🚩 Exam trap:** Don't assume "one VPN gateway resource visible in the portal" means there's no redundancy — active/standby with two instances is the *default* behind the scenes, even for a single-looking gateway resource.

---

## 5. Routing and Filtering Traffic

### Routing
Azure routes traffic between subnets/VNets/on-prem/internet automatically by default. You can override this with:

| Mechanism | What it is |
|---|---|
| **Route tables** | The container/resource holding custom routing rules |
| **User-Defined Routes (UDR)** | The specific custom routes you add *inside* a route table, manually overriding Azure's default system routes |
| **BGP** | A protocol (via VPN Gateway, Route Server, or ExpressRoute) that **automatically propagates** on-premises routes into Azure — the dynamic alternative to manually maintaining UDRs |

**🧠 Memory trick:** *"UDR = you write the map by hand. BGP = the map updates itself automatically."*

### Filtering

| Mechanism | What it is |
|---|---|
| **Network Security Groups (NSGs)** | Inbound/outbound rules (allow/block) based on source/destination IP, port, and protocol |
| **Network Virtual Appliances (NVAs)** | Specialized VMs performing a dedicated network function (e.g., firewall, WAN optimization) |

**🧠 Memory trick:** *"NSG = a rulebook. NVA = a dedicated bouncer VM doing one specialized job (firewall, WAN optimization)."*

**🚩 Exam trap:** A scenario describing "block traffic from a specific IP range on a specific port" is testing **NSGs**. A scenario describing "a dedicated appliance running full firewall or WAN optimization functionality" is testing **NVAs**. Both filter traffic, but NSGs are simple rule sets; NVAs are full specialized VMs.

---

## 6. Azure ExpressRoute

A **private, dedicated connection** from on-premises to the Microsoft cloud (Azure and Microsoft 365 both), via a connectivity provider, over an **ExpressRoute Circuit** — bypassing the public internet entirely for that traffic.

⏭️ **SKIM:** The source module itself says to focus on *when* to use ExpressRoute rather than memorizing the detailed connectivity models (any-to-any IP VPN, point-to-point Ethernet, virtual cross-connection at a colocation facility) — so treat those three as background awareness, not something to drill.

**Key benefits:**
- Reliability, speed, and consistent latency (no public internet)
- **ExpressRoute Global Reach** — connects your own ExpressRoute circuits together (e.g., an Asia office and a Europe datacenter) so they communicate without touching the public internet
- Dynamic routing via BGP
- Built-in redundancy at every peering location

**When to choose ExpressRoute:**
- Need private, consistent connectivity
- Strict compliance/data-transfer requirements
- Need predictable latency and high throughput
- Want to avoid sending critical traffic over the public internet

**🚩 Exam trap — a genuinely tricky exception:** Even with ExpressRoute active, **DNS queries, certificate revocation list (CRL) checks, and Azure CDN requests still travel over the public internet.** Don't assume ExpressRoute means *zero* traffic ever touches the internet — it's specific to the traffic routed through the circuit itself.

---

## 7. Azure DNS

A hosting service for DNS domains using Azure's infrastructure — managed with the same credentials, billing, and tools as your other Azure resources.

**Benefits:**
- **Reliability/performance** — hosted across Azure's global DNS network, using **anycast** routing so the closest available server answers each query.
- **Security** — built on Azure Resource Manager, so it gets Azure RBAC, activity logs, and resource locks (direct preview of governance concepts coming in Domain 3).
- **Ease of use** — manage via Portal, PowerShell, CLI, REST API, or SDKs, alongside your other Azure billing/support.
- **Private DNS domains** — use your own custom domain names inside private VNets instead of Azure-provided names.
- **Alias records** — a record set that points to an Azure resource (public IP, Traffic Manager profile, CDN endpoint) and **automatically updates itself** if that resource's underlying IP address changes.

**🚩 Exam trap:** **Azure DNS cannot be used to purchase a domain name.** You still need App Service domains or a third-party registrar to *buy* the domain; Azure DNS only hosts and manages the records once you own it.

**🧠 Memory trick:** *"Alias records = smart records that follow the resource around. Regular records = fixed, you update them yourself if the IP changes."*

---

## Master Comparison: Which Connectivity Option?

| Scenario | Correct option |
|---|---|
| A remote employee needs to connect their laptop securely to the company VNet | Point-to-site VPN |
| An entire branch office network needs to connect to Azure as if it were local | Site-to-site VPN |
| A company needs the highest bandwidth, lowest latency, and wants to completely avoid the public internet for compliance reasons | ExpressRoute |
| Two VNets in different regions need to talk directly to each other | VNet Peering |
| A VNet needs secure, optimized access to an Azure SQL Database as a resource type | Service Endpoint |
| A VNet needs a dedicated private IP mapped to one specific storage account instance | Private Endpoint |
| A company wants to block traffic from a specific IP range on a specific port | Network Security Group |
| A company needs a dedicated VM running full firewall functionality | Network Virtual Appliance |
| A company wants on-premises routes automatically propagated into Azure without manual configuration | BGP |
| A company wants full manual control over which subnet traffic uses which path | User-Defined Routes |
| A VPN gateway needs to survive a zone-level failure | Zone-redundant gateway |
| A record needs to auto-update when the underlying resource's IP changes | Alias record (Azure DNS) |

---

## Key Takeaways

- **Peering** (VNet ↔ VNet) vs. **Service Endpoint** (VNet ↔ PaaS resource type) vs. **Private Endpoint** (VNet ↔ one specific resource instance, with its own private IP) — the top trap triangle in this module.
- **Point-to-site** (device), **site-to-site** (whole network), **ExpressRoute** (dedicated private line, no internet) — three on-premises connectivity tiers, all concrete implementations of Domain 1's hybrid cloud concept.
- **Only one VPN Gateway per VNet, but it can connect to multiple locations.** Route-based gateways are generally preferred over policy-based, and are required for VNet-to-VNet, point-to-site, multisite, ExpressRoute coexistence, and HA.
- VPN Gateway HA: **active/standby (default)**, **active/active (BGP)**, **ExpressRoute failover**, and **zone-redundant** (direct callback to Availability Zones).
- **NSG** = rule-based filtering; **NVA** = dedicated appliance VM for specialized functions (firewall, WAN optimization).
- **Route tables/UDR** = manual routing control; **BGP** = automatic route propagation.
- **ExpressRoute** avoids the public internet for its traffic — but DNS, CRL checks, and CDN requests still use the internet regardless.
- **Azure DNS** can't sell you a domain name — only host/manage records once you own one. Alias records auto-update as underlying resource IPs change.

---

## Practice Questions

**Q1 (Easy).** Which connectivity option connects two entire virtual networks directly to each other, potentially across different regions, without touching the public internet?

A) Service endpoint
B) Private endpoint
C) VNet peering
D) Network security group

<details>
<summary>Answer</summary>

**Correct: C — VNet peering.** This is the definition: connecting two whole VNets directly over Microsoft's private backbone, even across regions.
- A and B connect a VNet to specific PaaS resources, not to another whole VNet.
- D is a traffic-filtering mechanism, unrelated to connecting VNets together.
</details>

---

**Q2 (Easy).** How many VPN Gateways can be deployed within a single virtual network?

A) As many as needed
B) Exactly one, though it can connect to multiple locations
C) Exactly two, for redundancy
D) Zero — VPN Gateways attach to subscriptions, not VNets

<details>
<summary>Answer</summary>

**Correct: B.** Only one VPN Gateway can be deployed per virtual network, but that single gateway can connect to multiple remote locations (other VNets and/or on-premises sites).
- A and C are incorrect counts.
- D is wrong: VPN Gateways are deployed within a dedicated subnet of a specific VNet.
</details>

---

**Q3 (Medium).** A company needs a dedicated private connection between its on-premises datacenter and Azure that never travels over the public internet, with predictable low latency and high throughput for compliance reasons. What should they use?

A) Site-to-site VPN
B) Point-to-site VPN
C) Azure ExpressRoute
D) VNet peering

<details>
<summary>Answer</summary>

**Correct: C — ExpressRoute.** This scenario matches ExpressRoute's defining traits exactly: private connectivity that bypasses the public internet, predictable latency, high throughput, ideal for compliance-driven requirements.
- A and B are wrong: both site-to-site and point-to-site VPNs are encrypted, but they still travel over the public internet (through an encrypted tunnel), unlike ExpressRoute.
- D is wrong: peering connects VNets to each other, not on-premises networks to Azure.
</details>

---

**Q4 (Medium).** Which statement about ExpressRoute is TRUE?

A) All traffic, including DNS queries and certificate revocation checks, bypasses the public internet once ExpressRoute is configured.
B) DNS queries, certificate revocation list checks, and Azure CDN requests still travel over the public internet even with ExpressRoute active.
C) ExpressRoute requires all traffic to be manually filtered through a Network Security Group.
D) ExpressRoute replaces the need for a VPN Gateway in all high-availability scenarios.

<details>
<summary>Answer</summary>

**Correct: B.** This is the specifically named exception in the module — certain traffic types (DNS, CRL checks, CDN requests) still use the public internet even with ExpressRoute active.
- A is the trap answer — it sounds intuitive but is factually wrong given the named exceptions.
- C is wrong: NSGs are an independent filtering mechanism, not a requirement tied to ExpressRoute specifically.
- D is wrong: a VPN Gateway can actually serve as a *failover* path for ExpressRoute, meaning the two often coexist rather than one replacing the other.
</details>

---

**Q5 (Medium).** A company wants on-premises network routes to be automatically propagated into their Azure virtual network without manually maintaining route entries. Which mechanism fits?

A) User-Defined Routes (UDR)
B) Border Gateway Protocol (BGP)
C) Network Security Groups
D) Service endpoints

<details>
<summary>Answer</summary>

**Correct: B — BGP.** BGP dynamically propagates on-premises routes into Azure, the automatic alternative to manually configuring UDRs.
- A is the opposite — UDR is the manual, hand-configured approach.
- C and D are unrelated to route propagation — NSGs filter traffic, service endpoints connect to PaaS resource types.
</details>

---

**Q6 (Hard).** A company needs a VPN Gateway configuration that requires BGP support, assigns a unique public IP address to each of two gateway instances, and creates separate tunnels from the on-premises device to each IP. Which HA configuration is being described?

A) Active/standby
B) Zone-redundant
C) ExpressRoute failover
D) Active/active

<details>
<summary>Answer</summary>

**Correct: D — Active/active.** This exactly matches active/active: BGP required, unique public IP per instance, separate tunnels to each.
- A is wrong: active/standby is the default configuration and doesn't require BGP or unique public IPs per instance in the same way.
- B is wrong: zone-redundant is about deploying across Availability Zones with different SKUs/Standard public IPs, not specifically about BGP-based dual-tunnel active/active setups.
- C is wrong: ExpressRoute failover uses a VPN Gateway as a backup path for an ExpressRoute circuit, unrelated to this specific dual-instance configuration.
</details>

---

**Q7 (Hard — cumulative, pulls in Domain 2 Module 1).** A company deploys a VPN Gateway in a region that supports Availability Zones and configures it to survive a zone-level failure, requiring a different gateway SKU and a Standard public IP instead of Basic. Which concept from earlier in Domain 2 is being directly applied here?

A) Region pairs
B) Availability Zones
C) Sovereign regions
D) Management groups

<details>
<summary>Answer</summary>

**Correct: B — Availability Zones.** Zone-redundant VPN Gateways are the direct networking-layer application of Availability Zones — physically and logically separating gateway instances across zones within the same region, exactly as Availability Zones do for other resources.
- A is wrong: region pairs protect against whole-region disasters, a different (larger) scope than zone-level failures.
- C is wrong: sovereign regions are isolated Azure instances for legal/compliance needs, unrelated to zone redundancy.
- D is wrong: management groups are a governance/policy hierarchy concept, unrelated to physical zone redundancy.
</details>

---

**Q8 (Hard — cumulative, pulls in Domain 1).** A retail company connects its on-premises datacenter to Azure using a site-to-site VPN, keeping its core inventory database on-premises while running its customer-facing web app in Azure. Which Domain 1 concept does this networking setup make concrete?

A) Multicloud deployment model
B) Hybrid cloud deployment model
C) SaaS shared responsibility model
D) Consumption-based pricing model

<details>
<summary>Answer</summary>

**Correct: B — Hybrid cloud.** Connecting a private on-premises environment to a public cloud (Azure) via VPN is exactly the hybrid cloud deployment model from Domain 1, now shown as a real networking implementation.
- A is wrong: multicloud means using two or more *public* cloud providers — there's only one public provider (Azure) here, plus a private datacenter.
- C is wrong: nothing in this scenario describes a finished, ready-to-use software product (SaaS) — it describes custom infrastructure and connectivity.
- D is wrong: consumption-based pricing is a billing concept, not a deployment/connectivity concept.
</details>

---

*Next up: "Describe Azure storage services" — covering storage tiers, redundancy options, storage account types, and file-movement/migration tools (AzCopy, Storage Explorer, Azure File Sync, Azure Migrate, Azure Data Box). Paste whenever you're ready.*
