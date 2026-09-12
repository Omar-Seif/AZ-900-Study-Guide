# AZ-900 — Describe Azure Architecture and Services (35–40% of exam)
## Module: Describe Azure Storage Services

> **Exam context:** Verified against the current AZ-900 study guide — this module's 6 learning objectives match the official skills list exactly (compare storage services, storage tiers, redundancy options, storage account options/types, file movement tools, migration options). No gaps this time.
>
> **What's next:** "Describe Azure identity, access, and security" — the last piece of Domain 2 (35–40%), covering Microsoft Entra ID, authentication methods, Conditional Access, RBAC, Zero Trust, defense-in-depth, and Microsoft Defender for Cloud.
>
> This module is **extremely number-and-detail-dense** — durability percentages, retention minimums, character limits, device capacities. Cheat-box at the end, as usual.

---

## 1. Azure Storage Accounts

A **storage account** gives you a unique namespace for your data, accessible worldwide over HTTP/HTTPS.

**Naming rules (concrete and testable):**
- **3 to 24 characters**, lowercase letters and numbers only.
- Must be **globally unique across all of Azure** — not just unique within your subscription or resource group.

**🚩 Exam trap:** People assume storage account name uniqueness only needs to be unique *within their own subscription*. It's global — no two storage accounts anywhere in Azure can share a name, because the name becomes part of a public endpoint URL (e.g., `https://<name>.blob.core.windows.net`).

### Storage Account Types

| Type | Supported services | Redundancy options | Best for |
|---|---|---|---|
| **Standard general-purpose v2** | Blob, Queue, Table, Files | LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS (**all** options) | Recommended default for most scenarios |
| **Premium block blobs** | Blob only | LRS, ZRS | High transaction rates, small objects, low latency needs |
| **Premium file shares** | Files only | LRS, ZRS | High-scale/high-performance file shares, supports both SMB and NFS |
| **Premium page blobs** | Page blobs only | LRS only | Specialized page blob workloads |

**🚩 Exam trap — a genuinely easy one to miss:** **None of the three Premium account types support any geo-redundant option (GRS/GZRS/RA-GRS/RA-GZRS).** Only **Standard general-purpose v2** offers geo-redundancy. If a scenario needs both premium performance *and* cross-region disaster recovery, that combination isn't directly available — worth remembering as a real limitation, not just a trivia point.

### Endpoints
Each service gets its own subdomain, built from your account name:

| Service | Endpoint pattern |
|---|---|
| Blob Storage | `https://<name>.blob.core.windows.net` |
| Data Lake Storage Gen2 | `https://<name>.dfs.core.windows.net` |
| Azure Files | `https://<name>.file.core.windows.net` |
| Queue Storage | `https://<name>.queue.core.windows.net` |
| Table Storage | `https://<name>.table.core.windows.net` |

---

## 2. The Five Core Storage Services

| Service | What it stores | Typical use |
|---|---|---|
| **Blobs** | Unstructured object data (text/binary) | Images, video/audio streaming, backups, analytics datasets, big data (via Data Lake Storage Gen2) |
| **Files** | Managed file shares (SMB/NFS) | Cloud or on-prem file shares, replacing traditional file servers |
| **Queues** | Messages for async processing | Reliable messaging between app components — commonly paired with **Azure Functions** as an event trigger (direct callback to your compute module) |
| **Disks** | Block-level storage volumes | Attached storage for Azure VMs |
| **Tables** | NoSQL structured, non-relational data | Large volumes of simple structured data |

**Shared benefits across all of Azure Storage:** durable/highly available (redundancy options), secure (encrypted by default + fine-grained access control), scalable, fully managed (Azure handles hardware/patching), and accessible globally via REST APIs, SDKs, CLI, PowerShell, portal, or Storage Explorer.

**Direct callback to Domain 1:** "Managed" here is the PaaS-style benefit you first met in the service-types module — Microsoft handles the underlying infrastructure so you only manage your data.

### Blob Storage Tiers — Cost vs. Retrieval Speed Tradeoff

| Tier | Access pattern | Minimum retention |
|---|---|---|
| **Hot** | Frequently accessed (e.g., website images) | None specified |
| **Cool** | Infrequently accessed | **30 days** |
| **Cold** | Infrequently accessed, colder than Cool | **90 days** |
| **Archive** | Rarely accessed, flexible latency OK (e.g., long-term backups) | **180 days** |

**🚩 Exam trap — the sharpest one in this section:** **Hot, Cool, and Cold tiers can be set at the account level. The Archive tier CANNOT be set at the account level** — it's only available at the individual blob level, during or after upload. If a question asks "which tier can't be applied account-wide," the answer is Archive.

**🧠 Memory trick:** *"Hot/Cool/Cold = whole-account dial. Archive = per-blob only — too extreme a setting to apply account-wide."*

Cool/Cold tiers trade lower storage cost for higher access cost and lower availability SLA; Archive has the lowest storage cost but the highest retrieval latency ("rehydration") and access cost.

---

## 3. Redundancy Options

**Core idea:** Azure Storage always replicates your data **3 times within the primary region**, no matter which option you choose. The redundancy option determines *how* and *where* those (and additional) copies exist.

### Primary-region-only options

| | Locally Redundant Storage (LRS) | Zone-Redundant Storage (ZRS) |
|---|---|---|
| Replication scope | 3 copies within **one datacenter** | 3 copies across **three Availability Zones** in the region |
| Durability | At least **11 nines** (99.999999999%) | At least **12 nines** (99.9999999999%) |
| Protects against | Server rack/drive failures | Datacenter-level failure (an entire zone going down) |
| Cost | Lowest | Higher than LRS |

**Direct callback to Domain 2, Module 1:** ZRS is the storage-layer application of Availability Zones — same underlying concept (physically separate datacenters within a region) you already learned, just applied to data replication specifically. Microsoft recommends ZRS for high-availability in-region scenarios and when data residency rules require replication to stay within a specific country/region.

### Secondary-region options (for disaster recovery)

When you create a storage account, Azure automatically assigns a secondary region based on **region pairs** — another direct callback to Domain 2, Module 1.

| | Geo-Redundant Storage (GRS) | Geo-Zone-Redundant Storage (GZRS) |
|---|---|---|
| Primary region | LRS (3 copies, 1 datacenter) | ZRS (3 copies, 3 zones) |
| Secondary region | LRS | LRS |
| Durability | At least 16 nines | At least 16 nines (with more precision) |
| Recommended for | Solid disaster recovery | Maximum consistency, availability, **and** DR resilience |

**🚩 Exam trap:** GZRS does **not** use ZRS in the secondary region — the secondary is always LRS in *both* GRS and GZRS. The "Z" in GZRS refers only to the primary region's replication method.

### Read Access to the Secondary Region

By default, **secondary-region data isn't readable at all unless a failover has occurred.** To read it beforehand, you need the **read-access** variants:

| | Without read access | With read access |
|---|---|---|
| GRS-based | GRS | **RA-GRS** |
| GZRS-based | GZRS | **RA-GZRS** |

**🧠 Memory trick:** *"RA- prefix = Read Access before failover. No prefix = secondary is a black box until you fail over."*

### Recovery Point Objective (RPO)

Replication to the secondary region is **asynchronous** (unlike the synchronous replication within LRS/ZRS in the primary region). This creates a gap: the **RPO** is the interval between the most recent write to the primary and the last write successfully replicated to the secondary.

- Azure Storage typically has an RPO of **less than 15 minutes**.
- **There's currently no SLA guaranteeing replication time** — worth remembering as a real limitation, not just a number to memorize.

**🚩 Exam trap:** A question might ask what happens if the primary region fails right after a write. Because of asynchronous replication and RPO, **that most recent write could be lost** if the primary can't be recovered — geo-redundancy protects against regional disaster, but doesn't guarantee zero data loss.

---

## 4. Migration Options: Azure Migrate vs. Azure Data Box

| | Azure Migrate | Azure Data Box |
|---|---|---|
| Mode | **Online**, real-time | **Offline**, physical shipping |
| What it does | Unified hub for assessing and migrating on-prem infrastructure, apps, and data to Azure | Ships you a physical storage device (up to **80 TB** usable capacity) to transfer data without using network bandwidth |
| Best for | Ongoing/active migration projects with network connectivity available | Very large datasets (often tens of TB+) when bandwidth is limited or migration would take too long online |
| Tools included | Discovery/assessment, server migration, database migration, web app migration; integrates with ISV tools | Rugged case, local web UI for setup, tracked end-to-end in the Azure portal |

**Azure Data Box specifics worth knowing:**
- Common scenarios: one-time bulk migration, periodic large uploads where online transfer is too slow, or exporting large datasets out of Azure for recovery/regulatory needs.
- After data is confirmed uploaded (import) or the device reaches the Azure datacenter (export), disks are wiped per **NIST 800-88r1** standards.

**🧠 Memory trick:** *"Migrate = online migration hub for infrastructure. Data Box = 'sneakernet' for when the network pipe is too small."*

---

## 5. File Movement Tools (Smaller-Scale, Ongoing)

Different from the large-scale migration tools above — these move individual files or smaller groups, often on an ongoing basis.

| | AzCopy | Azure Storage Explorer | Azure File Sync |
|---|---|---|---|
| Interface | Command-line | Graphical (GUI) | Background service |
| Direction | **One-directional only** — you specify source and destination, it copies that way | Same as AzCopy (uses it on the backend) | **Bi-directional** — stays continuously synced both ways |
| Best for | Scripted uploads/downloads, even between cloud providers | Visual management of blobs/files across platforms (Windows/macOS/Linux) | Centralizing file shares in Azure while keeping a local Windows Server file share in sync |

**🚩 Exam trap — the sharpest one here:** **AzCopy synchronization is strictly one-directional**, based on the source/destination you specify — it does **not** synchronize bidirectionally based on timestamps or metadata. Don't confuse this with Azure File Sync, which genuinely keeps a local server bi-directionally synced with Azure.

**🧠 Memory trick:** *"AzCopy = one-way copy, just automated. File Sync = a living, two-way mirror."*

**Azure File Sync extra capabilities:**
- Access data locally via SMB, NFS, or FTPS.
- Deploy as many local caches worldwide as needed.
- **Disaster recovery**: replace a failed local server by installing Azure File Sync on a new server in the same datacenter.
- **Cloud tiering**: frequently accessed files stay cached locally; infrequently accessed files live in the cloud until requested.

---

## Key Numbers Cheat-Box

| Fact | Number |
|---|---|
| Copies of data always made in the primary region | 3 |
| LRS durability | ≥ 11 nines (99.999999999%) |
| ZRS durability | ≥ 12 nines (99.9999999999%) |
| GRS / GZRS durability | ≥ 16 nines |
| Storage account name length | 3–24 characters, lowercase + numbers only |
| Cool tier minimum retention | 30 days |
| Cold tier minimum retention | 90 days |
| Archive tier minimum retention | 180 days |
| Typical Azure Storage RPO | < 15 minutes (no SLA) |
| Azure Data Box maximum usable capacity | 80 TB |
| Queue message maximum size | 64 KB |

---

## Scenario Table

| Scenario | Correct choice |
|---|---|
| Lowest-cost option, only need protection against a single drive/rack failure | LRS |
| Need protection against an entire datacenter going down, staying within one region | ZRS |
| Need protection against a full regional disaster, reading secondary data not required | GRS |
| Need protection against a full regional disaster AND need to read secondary data before failover | RA-GRS (or RA-GZRS if also zone-redundant in primary) |
| Maximum consistency, availability, and disaster recovery resilience | GZRS |
| Storing website images accessed constantly | Hot tier |
| Storing customer invoices, accessed occasionally, kept 30+ days | Cool tier |
| Long-term backup, rarely accessed, kept 180+ days, latency not critical | Archive tier |
| Want to apply Archive tier to an entire storage account at once | Not possible — Archive is blob-level only |
| Migrating an entire on-prem datacenter's VMs and apps with active network connectivity | Azure Migrate |
| Transferring 50 TB of data where internet bandwidth is too limited | Azure Data Box |
| Need a script to copy blobs one-way into a storage account | AzCopy |
| Need a local Windows file server to stay continuously, bidirectionally synced with Azure Files | Azure File Sync |
| Need a visual GUI to browse and manage blobs across Windows/macOS/Linux | Azure Storage Explorer |

---

## Key Takeaways

- Storage account names: 3–24 characters, lowercase/numbers only, **globally unique across all of Azure.**
- **Standard general-purpose v2** is the only account type supporting geo-redundancy; all Premium types are LRS/ZRS only.
- **Hot/Cool/Cold** can be set account-wide; **Archive cannot** — blob-level only.
- **LRS** (1 datacenter) → **ZRS** (3 zones, direct callback to Availability Zones) → **GRS** (adds secondary region, LRS+LRS) → **GZRS** (ZRS primary + LRS secondary) — increasing durability and protection scope.
- **RA-** prefix = readable secondary before failover; without it, secondary is unreadable until failover.
- Geo-replication is **asynchronous**, creating an **RPO** (typically <15 min, no SLA) — a real data-loss risk window during a primary-region disaster.
- **Azure Migrate** = online migration hub. **Azure Data Box** = offline physical transfer, up to 80 TB.
- **AzCopy** = one-directional only. **Storage Explorer** = GUI wrapper around AzCopy. **Azure File Sync** = genuinely bi-directional, plus cloud tiering and DR support.

---

## Practice Questions

**Q1 (Easy).** Which redundancy option replicates data three times across three separate Availability Zones within the primary region?

A) LRS
B) ZRS
C) GRS
D) GZRS

<details>
<summary>Answer</summary>

**Correct: B — ZRS.** Zone-redundant storage specifically replicates synchronously across three Availability Zones in the primary region.
- A (LRS) replicates within a single datacenter, not across zones.
- C and D both involve a secondary region, which isn't what this question describes.
</details>

---

**Q2 (Easy).** Which Blob storage tier CANNOT be applied at the storage account level?

A) Hot
B) Cool
C) Cold
D) Archive

<details>
<summary>Answer</summary>

**Correct: D — Archive.** Archive can only be set at the individual blob level, not account-wide.
- A, B, and C can all be set at the account level.
</details>

---

**Q3 (Medium).** A company needs to read data in the secondary region for reporting purposes, even before any failover occurs, and also wants zone-level resilience in the primary region. Which redundancy option fits?

A) GRS
B) RA-GRS
C) RA-GZRS
D) ZRS

<details>
<summary>Answer</summary>

**Correct: C — RA-GZRS.** This is the only option combining zone-level resilience in the primary region (the "Z") with read access to the secondary region before failover (the "RA-" prefix).
- A is wrong: GRS doesn't include zone-level resilience or read access before failover.
- B is wrong: RA-GRS gives read access before failover but doesn't provide zone-level resilience in the primary (it's LRS-based, not ZRS-based).
- D is wrong: ZRS doesn't include any secondary-region replication at all, so there's no secondary data to read.
</details>

---

**Q4 (Medium).** A company wants to store customer invoices that must be retained for compliance, accessed only occasionally, but need to remain accessible within 30 days minimum. Which tier fits best?

A) Hot
B) Cool
C) Archive
D) Premium page blobs

<details>
<summary>Answer</summary>

**Correct: B — Cool.** This matches the module's own example use case exactly — infrequently accessed data with a 30-day minimum retention.
- A is wrong: Hot is for frequently accessed data, not occasional access.
- C is wrong: Archive requires a 180-day minimum retention and has much higher retrieval latency — overkill for this scenario's needs.
- D is not a blob tier at all — it's an unrelated storage account type.
</details>

---

**Q5 (Medium).** A company needs to move 60 TB of data into Azure but has limited internet bandwidth that would make an online transfer impractically slow. What should they use?

A) AzCopy
B) Azure Migrate
C) Azure Data Box
D) Azure File Sync

<details>
<summary>Answer</summary>

**Correct: C — Azure Data Box.** With 80 TB of usable capacity and a physical shipping model, Data Box is designed exactly for large datasets when bandwidth is the limiting factor.
- A is wrong: AzCopy still relies on your existing network bandwidth, which is the described bottleneck.
- B is wrong: Azure Migrate is for assessing/migrating infrastructure and apps online, not a bandwidth-bypass tool for bulk data specifically.
- D is wrong: Azure File Sync is for ongoing sync of file shares, not one-time bulk data transfer of this scale.
</details>

---

**Q6 (Hard).** Which statement correctly distinguishes AzCopy from Azure File Sync?

A) AzCopy synchronizes bidirectionally based on timestamps; Azure File Sync only copies one-way.
B) AzCopy only copies one-way in a direction you specify; Azure File Sync keeps a local server bidirectionally synced with Azure.
C) Both tools synchronize bidirectionally, but AzCopy is GUI-based while Azure File Sync is command-line only.
D) AzCopy and Azure File Sync are functionally identical, differing only in supported operating systems.

<details>
<summary>Answer</summary>

**Correct: B.** This is the precise, correct distinction: AzCopy is one-directional (you choose source/destination), while Azure File Sync maintains genuine bidirectional sync between a local server and Azure Files.
- A reverses the facts entirely.
- C is wrong on both counts: neither tool's interface type is described this way, and AzCopy is not bidirectional.
- D is wrong: they serve fundamentally different sync models, not just different platforms.
</details>

---

**Q7 (Hard).** A company wants premium, low-latency block blob storage AND geo-redundancy to a secondary region for disaster recovery. Which storage account type should they choose?

A) Premium block blobs, configured with GZRS
B) Standard general-purpose v2, since Premium account types don't support geo-redundant options
C) Premium page blobs, configured with GRS
D) Premium file shares, configured with RA-GRS

<details>
<summary>Answer</summary>

**Correct: B.** None of the Premium account types (block blobs, file shares, or page blobs) support any geo-redundant option — only LRS and/or ZRS. To get geo-redundancy, you must use Standard general-purpose v2, even though it means giving up the Premium performance tier.
- A and C are wrong: Premium block blobs and Premium page blobs only support LRS (page blobs) or LRS/ZRS (block blobs) — GZRS/GRS aren't available for either.
- D is wrong: Premium file shares also only support LRS/ZRS, not RA-GRS.
</details>

---

**Q8 (Hard — cumulative, pulls in Domain 2 Module 1).** A company's storage account uses GRS. Azure automatically selects the secondary region for replication. Based on your earlier studies, what determines which specific region Azure assigns as the secondary?

A) The company manually selects any region worldwide
B) Azure assigns the secondary based on that primary region's official region pair
C) The secondary is always in a sovereign region
D) The secondary is chosen at random from all Azure regions

<details>
<summary>Answer</summary>

**Correct: B.** This directly connects back to Domain 2, Module 1: Azure automatically assigns the geo-redundant secondary region based on the primary region's official region pair (same geography, 300+ miles apart) — you don't get to manually pick an arbitrary region.
- A is wrong: you choose the *primary* region when creating the storage account, but the secondary is automatically assigned via region pairing, not manually chosen.
- C is wrong: sovereign regions are isolated instances for legal/compliance needs, unrelated to how GRS secondary regions are assigned.
- D is wrong: it's a defined, paired relationship, not random.
</details>

---

*Next up: "Describe Azure identity, access, and security" — the final module in Domain 2, covering Microsoft Entra ID, Entra Domain Services, authentication methods (SSO, MFA, passwordless), external identities, Conditional Access, RBAC, Zero Trust, defense-in-depth, and Microsoft Defender for Cloud. Paste whenever you're ready.*
