# AZ-900 — Azure Architecture and Services (35–40% of exam)
## Module: Describe the Core Architectural Components of Azure

> **Exam context:** Welcome to Domain 2 — the biggest domain on the exam (35–40%). This first module lays the physical and organizational map of Azure that every later module builds on. Verified against Microsoft's current AZ-900 study guide: this module's 7 learning objectives (regions/region pairs/sovereign regions, availability zones, datacenters, resources/resource groups, subscriptions, management groups, and the hierarchy) match the official skills list exactly — no gaps or surprises this time.
>
> This module is **dense with specific numbers** (minimum zone counts, distance requirements, nesting limits). I've pulled those into a dedicated cheat-box near the end because number-based trivia is a favorite AZ-900 question style.

---

## 1. What Is Azure? (Quick Context)

Azure is Microsoft's cloud platform, offering services across roughly 10 categories: Compute, Networking, Storage, Databases, AI + ML, Identity + Security, DevOps + Management, IoT, Analytics, and Integration.

---

## 2. Azure Accounts and Subscriptions (Getting Started)

**Account vs. subscription — a distinction worth being precise about:**
- An **Azure account** is your identity — tied to Microsoft Entra ID (the identity service you first met as a preview back in Module 1's shared responsibility discussion).
- A **subscription** is created under that account and is what actually lets you provision resources and get billed.
- One account can have multiple subscriptions (e.g., separate subscriptions for Dev/Test, Production, and Sandbox), but you need at least one to do anything.

**The Azure free account (verified current details from Microsoft, since the source module didn't give exact figures):**
- **$200 credit**, usable within the first **30 days**.
- **12 months** of free access to popular services.
- **55+ services that are always free**, indefinitely.
- Requires a phone number, credit card (for identity verification only — you're not charged unless you upgrade), and a Microsoft or GitHub account.

**The Azure for Students offer** gives **$100 in credit** over 12 months, free developer tools, and — notably — **no credit card required**. This is almost certainly where your own $100 in Azure credits came from, so worth knowing your credit is on the 12-month student clock, not the 30-day standard-account clock.

**🚩 Exam trap:** Don't mix up the standard free account ($200 / 30 days) with the student account ($100 / 12 months). They're both real, current offers, and a question could ask you to match the right credit amount and time window to the right account type.

**Connection back to Module 1:** This whole account/subscription setup is just the practical, real-world front door to the **consumption-based model** — you're literally handed a pool of pay-as-you-go credit and billed only for what you use beyond it.

---

## 3. Azure Physical Infrastructure

The physical hierarchy, top to bottom:

**Geography → Region → Availability Zone → Datacenter**

### Datacenters
Facilities full of racked servers with dedicated power, cooling, and networking — same basic idea as an on-premises datacenter (Module 1), just operating at a much larger scale. You never interact with an individual datacenter directly.

### Regions
A **region** = a geographical area containing one or more datacenters, networked together with low-latency connections. When you deploy most resources, you pick a region. A few services are **global/non-regional** and don't require picking one — Microsoft Entra ID, Azure Traffic Manager, and Azure DNS are the named examples.

**🚩 Exam trap:** Not every service or VM size is available in every region. A question might describe a deployment failing or being unavailable in a specific region — that's testing whether you know regional availability varies, not a bug or exception to some other rule.

### Availability Zones (AZs)
**Physically separate datacenters within the same region**, each with independent power, cooling, and networking, connected via high-speed private fiber. If one zone goes down, the others keep running.

**Key number: a minimum of 3 separate availability zones** exist in every zone-enabled region. **But not all regions support availability zones at all** — this is a real gap, not a memorization trap; some regions genuinely don't have them.

**Three categories of AZ-aware services** (a strong scenario-mapping opportunity):

| Category | What it means | Example |
|---|---|---|
| **Zonal** | You pin the resource to one specific zone yourself | VMs, managed disks, IP addresses |
| **Zone-redundant** | The platform automatically replicates across zones for you | Zone-redundant storage, SQL Database |
| **Non-regional** | Resilient to both zone-wide *and* region-wide outages by design | Global services like Microsoft Entra ID |

**🧠 Memory trick:** *"Zonal = you pin it. Zone-redundant = it copies itself. Non-regional = it's above the whole region/zone system entirely."*

**Connection back to Module 2:** This is the concrete mechanism behind "reliability" (Module 2's benefit of recovering from failure) — availability zones are literally *how* Azure delivers that resilience, not just an abstract promise.

### Region Pairs
Most regions are paired with another region in the **same geography** (e.g., US, Europe, Asia), at least **300 miles apart**. Purpose: protect against events big enough to take out multiple availability zones at once (natural disasters, civil unrest, widescale power/network failures).

**Region pair benefits:**
- One region in each pair is **prioritized for faster recovery** during a widescale Azure outage.
- Planned updates roll out to paired regions **one at a time**, reducing risk of simultaneous downtime.
- Data stays within the same geography as its pair (data-residency/compliance) — **except Brazil South**.

**🚩 Exam trap (a genuinely tricky exception):** Most region pairs are **bidirectional** (West US and East US each back each other up). **Brazil South is a named exception** — it's paired with South Central US, but South Central US's *own* secondary isn't Brazil South (one-directional pairing), and Brazil South is unique for being paired *outside* its own geography. A few regions (Italy North, Poland Central, Israel Central) have **no traditional region pair at all** and instead rely on availability zones + geo-redundant storage.

**🚩 Second exam trap:** Not all Azure services automatically fail over to the paired region during an outage — some require **you** to configure replication/recovery yourself. Don't assume region pairing is a fully automatic safety net for every single service.

### Sovereign Regions
Isolated instances of Azure, separate from the main Azure instance, used for legal/compliance reasons:
- **US Gov regions** (e.g., US DoD Central, US Gov Virginia, US Gov Arizona) — physically and logically isolated, screened U.S. personnel, extra compliance certifications, for U.S. government agencies/partners.
- **China regions** (China East, China North, etc.) — operated through a partnership with 21Vianet; Microsoft doesn't directly run these datacenters.

---

## 4. Azure Management Infrastructure

The management hierarchy, top to bottom:

**Management Groups → Subscriptions → Resource Groups → Resources**

Everything below inherits policies and access settings from everything above it.

### Resources and Resource Groups
A **resource** is anything you create/provision (a VM, a virtual network, a database — anything). A **resource group** is a container for resources, with firm rules:
- Every resource belongs to **exactly one** resource group at a time (you can move it, but never have it in two at once).
- Resource groups **cannot be nested**.
- Resource groups **cannot be renamed** after creation — pick a naming convention up front.
- Deleting a resource group **deletes everything inside it**.
- Access permissions applied to a resource group apply to every resource inside it.

**🚩 Exam trap — a big one:** People sometimes assume resource groups can be nested "for organization," the same way folders can contain subfolders. **They cannot.** Only *management groups* can nest (see below) — resource groups are flat.

### Subscriptions
A **subscription** is a unit of billing, access control, and scale. Two boundary types:
- **Billing boundary** — each subscription generates its own separate invoice.
- **Access control boundary** — access policies and spending limits are applied at the subscription level (e.g., different rules for a Dev subscription vs. a Production subscription).

Common reasons to create additional subscriptions: separating environments (sandbox/dev/test/production), separating teams or workload cost tracking, and separating billing.

### Management Groups
Sit **above** subscriptions, for when you have many subscriptions across teams or geographies and need governance/policy applied at a higher level than any single subscription.
- **Can be nested up to 6 levels deep** (not counting the root level or the subscription level itself).
- Every Microsoft Entra tenant has exactly **one Tenant Root Group** — the top of the whole hierarchy, everything else folds up into it.
- Policies applied at a management group **inherit downward automatically** to every subscription, resource group, and resource beneath it — and the resource/subscription owner **cannot override** an inherited policy.
- **Each management group or subscription has only one parent** (no shared/multiple-parent structures).
- A single directory supports **up to 10,000 management groups**.

**🧠 Memory trick:** *"Resource groups are flat (never nest). Management groups stack like a wedding cake, up to 6 tiers, all folding up to one Tenant Root Group at the very top."*

**🚩 Exam trap:** A question describing "applying one policy that automatically limits VM regions across dozens of subscriptions, with no ability for individual teams to override it" is testing **management groups**, not resource groups or subscriptions individually — the giveaway is the *inheritance across multiple subscriptions* and the *no-override* detail.

**Connection back to Module 2:** Management groups are the formal mechanism behind the "governance" benefit you previewed there (templates, auditing, standards enforcement) — this is the actual structural tool that makes enforced-and-inherited governance possible at scale.

---

## Key Numbers Cheat-Box

| Fact | Number |
|---|---|
| Minimum availability zones in a zone-enabled region | 3 |
| Minimum distance between paired regions | 300 miles |
| Maximum management group nesting depth | 6 levels (excluding root and subscription levels) |
| Tenant Root Groups per Microsoft Entra tenant | 1 |
| Maximum management groups per directory | 10,000 |
| Parents per management group/subscription | 1 (always) |
| Resource groups a single resource can belong to | 1 (always) |
| Standard Azure free account credit / window | $200 / 30 days |
| Standard Azure free account free-services window | 12 months |
| Azure for Students credit | $100 / 12 months, no credit card |

---

## Scenario Table

| Scenario | Correct concept |
|---|---|
| Delete an entire temporary test environment in one action | Resource group (deleting it deletes everything inside) |
| Apply one policy across 40 subscriptions with no team able to override it | Management group |
| Get separate invoices for Marketing's cloud spend vs. Engineering's | Separate subscriptions (billing boundary) |
| A VM needs to be pinned to a specific physical zone | Zonal service |
| A SQL Database needs to automatically replicate across zones with no manual setup | Zone-redundant service |
| Microsoft Entra ID stays available even during a region-wide outage | Non-regional service |
| A U.S. federal agency needs an isolated, screened-personnel Azure instance | Sovereign region (US Gov) |
| A disaster takes out an entire region; workloads need to fail over elsewhere in the same country/geography | Region pair |
| A team wants folders-within-folders to organize resources by sub-project | Not possible — resource groups can't nest; consider naming conventions or separate resource groups instead |

---

## Key Takeaways

- Physical hierarchy: **Geography → Region → Availability Zone → Datacenter.**
- **Availability zones** = physically separate, independently powered datacenters within a region; minimum 3 where supported, but not all regions support them. Zonal / zone-redundant / non-regional are the three service categories.
- **Region pairs** = same-geography backup regions, 300+ miles apart, usually bidirectional — except Brazil South, and a few regions have no pair at all.
- **Sovereign regions** = isolated Azure instances for legal/compliance needs (US Gov, China).
- Management hierarchy: **Management Groups → Subscriptions → Resource Groups → Resources**, with policy/access inheriting downward.
- **Resource groups cannot nest, cannot be renamed, and each resource belongs to exactly one.** Deleting a group deletes everything in it.
- **Management groups CAN nest** (up to 6 levels), fold up to one Tenant Root Group per tenant, and each has exactly one parent.
- **Subscriptions** are billing + access control boundaries; one account can hold several.
- Your own $100 Azure credit matches the **Azure for Students** offer (12-month window) — distinct from the standard $200/30-day free account.

---

## Practice Questions

**Q1 (Easy).** What is the minimum number of availability zones present in any Azure region that supports availability zones?

A) 2
B) 3
C) 5
D) It varies with no minimum

<details>
<summary>Answer</summary>

**Correct: B — 3.** Microsoft guarantees a minimum of three separate availability zones in every zone-enabled region.
- A and C are incorrect numbers.
- D is wrong — there is a defined minimum, it's just not guaranteed in *every* region (some regions don't support AZs at all, which is a separate fact from the minimum-when-supported).
</details>

---

**Q2 (Easy).** Which statement about resource groups is TRUE?

A) A resource group can be nested inside another resource group.
B) A resource can belong to more than one resource group at a time.
C) Deleting a resource group deletes all resources within it.
D) Resource groups can be renamed at any time after creation.

<details>
<summary>Answer</summary>

**Correct: C.** Deleting a resource group deletes everything inside it — this is one of the most consequential facts in this module.
- A is false: resource groups cannot be nested (only management groups can).
- B is false: a resource belongs to exactly one resource group at a time.
- D is false: resource groups cannot be renamed after creation.
</details>

---

**Q3 (Medium).** A company needs to apply a single governance policy restricting VM deployment locations across 25 subscriptions spanning multiple departments, with no department able to override the restriction. What should they use?

A) A single resource group shared across departments
B) A management group
C) Azure Availability Zones
D) A region pair

<details>
<summary>EAnswer</summary>

**Correct: B — Management group.** Policies applied at a management group level inherit downward automatically and can't be overridden by subscription or resource owners — exactly matching this scenario.
- A is wrong: resource groups exist inside subscriptions and can't span or govern multiple subscriptions at once.
- C and D are physical infrastructure concepts (zones/regions), unrelated to policy governance across subscriptions.
</details>

---

**Q4 (Medium).** Which Azure service category means the platform automatically replicates the resource across availability zones without manual configuration?

A) Zonal
B) Zone-redundant
C) Non-regional
D) Sovereign

<details>
<summary>Answer</summary>

**Correct: B — Zone-redundant.** This is the exact definition — automatic platform-level replication across zones, e.g., zone-redundant storage or SQL Database.
- A (zonal) is the opposite: you manually pin the resource to one specific zone.
- C (non-regional) means the service transcends regions/zones entirely (e.g., Microsoft Entra ID), not "replicated across zones."
- D (sovereign) refers to isolated Azure instances for legal/compliance reasons, unrelated to zone replication.
</details>

---

**Q5 (Hard).** A company operates in Brazil and wants to understand its region-pair disaster recovery options for its Brazil South deployment. Which statement is accurate?

A) Brazil South is paired bidirectionally with East US, like most region pairs.
B) Brazil South's secondary region is South Central US, but South Central US's own secondary is a different region — making this a one-directional pairing.
C) Brazil South has no region pair at all and relies solely on availability zones.
D) Brazil South is paired with another region within South America for data-residency reasons.

<details>
<summary>Answer</summary>

**Correct: B.** This is the specifically named exception in Microsoft's documentation: Brazil South pairs with South Central US, but the relationship isn't mutual — South Central US doesn't pair back to Brazil South, and the pairing itself crosses geographic boundaries (unusual, since most pairs stay within the same geography).
- A is wrong: Brazil South pairs with South Central US, not East US, and the relationship isn't bidirectional.
- C describes regions like Italy North, Poland Central, or Israel Central — not Brazil South, which does have a named secondary region.
- D is wrong: Brazil South's pair (South Central US) is explicitly outside its own geography, which is exactly why it's called out as an exception.
</details>

---

**Q6 (Hard — cumulative, pulls in Domain 1).** A retail company deploys its e-commerce application across three availability zones within one Azure region to protect against zone-level outages, while also configuring the app to fail over to a paired region in the event of a region-wide disaster. Which TWO cloud concepts from your earlier studies does this scenario directly demonstrate?

A) Consumption-based model + horizontal scaling
B) Reliability (Module 2) + hybrid cloud deployment model (Module 1)
C) Reliability (Module 2) + region pairs as the mechanism delivering it
D) Shared responsibility model + SaaS

<details>
<summary>Answer</summary>

**Correct: C.** This scenario is a direct, concrete example of "reliability" — the ability to recover from failure — first introduced in Module 2 as an abstract benefit. Availability zones and region pairs are the actual Azure mechanisms that *deliver* that reliability, tying the earlier concept to this module's specific infrastructure.
- A is wrong: nothing about scaling resource size or count is described here — this scenario is entirely about failure resilience, not demand-based scaling.
- B is half right (reliability) but wrong on the second half: nothing about private + public cloud combination (hybrid) is described — everything here is happening within Azure itself.
- D is wrong: no service model (IaaS/PaaS/SaaS) is specified, and the scenario is about infrastructure resilience, not the responsibility split for patching/security.
</details>

---

*Next up: "Describe Azure compute and networking services" — covering container instances, VMs, Functions, VM Scale Sets, availability sets, Azure Virtual Desktop, and virtual networking basics (VNets, subnets, peering, DNS, VPN Gateway, ExpressRoute). Paste it whenever you're ready.*
