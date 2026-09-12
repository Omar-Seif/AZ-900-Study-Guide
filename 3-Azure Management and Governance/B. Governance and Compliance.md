# AZ-900 — Describe Azure Management and Governance (30–35% of exam)
## Module: Describe Features and Tools for Governance and Compliance

> **Exam context:** Checked against the current official skills list — this section has **3 official bullets**: Microsoft Purview, Azure Policy, and resource locks. **The Service Trust Portal is NOT on the official list.** It's covered below anyway (briefly, ⏭️ SKIP-flagged) since Microsoft's own disclaimer notes "related topics may be covered," but don't spend real study time memorizing its menu structure.
>
> **The real payoff of this module** is a direct follow-up to your last one: you now have **four different governance mechanisms** (RBAC, tags, Policy, locks) that all apply across the same Management Group → Subscription → Resource Group → Resource hierarchy — but they don't all behave the same way. I've built a consolidated comparison at the end specifically to lock that in.

---

## 1. Microsoft Purview

A family of **data governance, risk, and compliance** solutions giving you one unified view across on-premises, multicloud, and SaaS data. Core capabilities: automated data discovery, sensitive data classification, and end-to-end data lineage.

**Two solution areas — a real trap pair since both sound like "manage your data":**

| | Risk and Compliance | Unified Data Governance |
|---|---|---|
| Built on | Microsoft 365 (Teams, OneDrive, Exchange as core components) | Broader — Azure, SQL/Hive databases, on-premises, other clouds (e.g., Amazon S3) |
| Focus | **Protecting** sensitive data and **managing regulatory compliance** requirements | **Mapping and understanding** your entire data estate |
| What it helps you do | Protect sensitive data across clouds/apps/devices; identify data risks; manage regulatory compliance | Build a data map (classification + lineage); locate sensitive data; create a secure catalog for data consumers; generate usage insights; manage access at scale |

**🧠 Memory trick:** *"Risk and Compliance = M365-rooted, protects and satisfies regulators. Unified Data Governance = broader estate-wide map, helps people find and use data safely."*

---

## 2. Azure Policy

Lets you **create, assign, and manage policies** that control or audit your resources — enforcing rules so configurations stay compliant with your standards.

**Core behaviors:**
- Evaluates resources and flags noncompliant ones.
- Can **prevent** noncompliant resources from being created in the first place.
- **Evaluates existing resources too — not just new ones.** A policy created today still gets checked against VMs that existed before the policy did.
- Comes with **built-in** policy/initiative definitions for Storage, Networking, Compute, Security Center, and Monitoring.
- Can **auto-remediate** in some cases — e.g., automatically adding a missing `AppName` tag. **This is the direct payoff of last module's preview:** you learned tags don't inherit automatically; Azure Policy is *how* you enforce them anyway.
- **Exceptions are supported** — you retain full control by marking specific resources as exceptions rather than being locked into a rigid all-or-nothing rule.
- Integrates with **Azure DevOps** CI/CD pipelines (pre- and post-deployment phases).
- **Applies regardless of how a change was proposed** — including Copilot recommendations or agent-like automation. Policy still enforces allowed locations, required tags, approved SKUs, and security baselines no matter the source of the change.

**🚩 Exam trap:** A scenario describing "a VM size restriction that gets enforced both when creating a new VM AND when resizing an existing one, and also flags VMs that already existed before the rule was written" is testing whether you know Policy applies **retroactively**, not just at creation time.

### Policy Inheritance

**Policies are inherited** — set one at a high level (e.g., a resource group), and everything within it automatically receives that policy, same downward-flowing pattern as the Management Group → Subscription → Resource Group → Resource hierarchy you've seen before.

### Initiatives

An **initiative** groups related policies together to track compliance toward a larger goal. Example: the built-in **"Enable Monitoring in Azure Security Center"** initiative bundles over 100 individual policy definitions, including:
- Monitor unencrypted SQL Database in Security Center
- Monitor OS vulnerabilities in Security Center
- Monitor missing Endpoint Protection in Security Center

**🧠 Memory trick:** *"A policy is one rule. An initiative is a folder of rules aimed at one bigger compliance goal."*

**🚩 Exam trap — Policy vs. RBAC:** These solve *different* problems, and mixing them up is a common mistake. **RBAC controls WHO can do WHAT** (permissions). **Azure Policy controls WHAT configurations are ALLOWED**, regardless of who's doing it. A user could have full RBAC permission to create a VM of any size, and Azure Policy could still block that VM if its size violates a policy — the two systems operate independently and both apply simultaneously.

---

## 3. Resource Locks

Prevent resources from being **accidentally deleted or changed** — a safety net that exists even on top of RBAC, because someone with the right permissions can still make a costly mistake.

**Two types:**

| Lock type | Can modify? | Can delete? | Equivalent to |
|---|---|---|---|
| **Delete (CanNotDelete)** | ✅ Yes | ❌ No | — |
| **ReadOnly** | ❌ No | ❌ No | Restricting everyone to the **Reader** role's permissions |

**🚩 Exam trap:** Don't mix up the two — **Delete lock still allows modification**, it only blocks deletion. **ReadOnly blocks both** modification and deletion. If a scenario says "users can still update the resource but can't delete it," that's the Delete lock, not ReadOnly.

**Scope and inheritance:** Locks apply to individual resources, resource groups, or entire subscriptions, and **they inherit** — a lock on a resource group automatically applies to every resource inside it (same inheriting pattern as Azure Policy, opposite of tags).

**🚩 Exam trap — the sharpest one here:** **Resource locks apply regardless of RBAC permissions.** Even a resource **Owner** cannot delete or modify a locked resource without first removing the lock — being the highest-privilege role doesn't bypass a lock. It's a genuine two-step process: (1) remove the lock, (2) then perform the action you already had permission for.

**Management:** portal, PowerShell, CLI, or ARM templates — same tool set pattern you've seen for tags and other Azure Resource Manager-governed features.

---

## 4. Service Trust Portal ⏭️ SKIP (not on official skills list — light overview only)

A portal (`https://servicetrust.microsoft.com/`) providing documentation about Microsoft's own security, privacy, and compliance practices/controls. Some compliance materials require signing in with a Microsoft Entra work/school account and accepting an NDA.

Three main menu areas: **Service Trust Portal** (home), **My Library** (pin documents, get update notifications), **All Documents** (central repository you pin from).

⏭️ Since this isn't on the current official skills list, don't invest study time memorizing the menu structure — know it exists and what it's broadly for, in case a "related topic" question touches on it.

---

## Master Comparison: What Inherits Down the Hierarchy?

This is the single most valuable table across this whole domain so far — four governance mechanisms, same hierarchy, four different behaviors:

| Mechanism | Inherits down the hierarchy? | What it controls | Overridable by high privilege (e.g., Owner)? |
|---|---|---|---|
| **RBAC** (Domain 2) | ✅ Yes | WHO can do WHAT (permissions) | N/A — RBAC *is* the permission system |
| **Tags** (Domain 3, last module) | ❌ **No** | Metadata for organization/cost/automation | N/A — not an access control |
| **Azure Policy** (this module) | ✅ Yes | WHAT configurations are allowed (compliance) | ❌ No — applies regardless of who's making the change |
| **Resource locks** (this module) | ✅ Yes | Whether a resource can be deleted/modified at all | ❌ No — even Owners must remove the lock first |

**🧠 Memory trick:** *"Tags are the one odd exception that doesn't inherit. Everything else that's actually a control mechanism (RBAC, Policy, Locks) flows downhill — and none of them can simply be overridden by having a high-privilege role; Policy and Locks specifically ignore RBAC standing entirely."*

---

## Scenario Table

| Scenario | Correct tool |
|---|---|
| Need a unified map of sensitive data across Azure, on-prem SQL, and Amazon S3 | Microsoft Purview (Unified Data Governance) |
| Need to protect sensitive data across Teams/OneDrive/Exchange and manage regulatory compliance | Microsoft Purview (Risk and Compliance) |
| Need to block creation of any VM larger than a certain size, and flag existing oversized VMs too | Azure Policy |
| Need a missing tag automatically added to noncompliant resources | Azure Policy (auto-remediation) |
| Need to group 100+ related compliance policies under one tracked goal | Azure Policy initiative |
| Need to prevent a critical storage account from being deleted, while still allowing updates | Delete (CanNotDelete) lock |
| Need to fully freeze a resource so nothing can be read... wait, read AND changed at all | ReadOnly lock |
| An Owner needs to delete a locked resource | Must remove the lock first — Owner status alone doesn't bypass it |
| Need documentation on Microsoft's own compliance certifications and controls | Service Trust Portal |

---

## Key Takeaways

- **Microsoft Purview**: Risk and Compliance (M365-rooted, protect + regulatory) vs. Unified Data Governance (broader estate map, classification, lineage, secure access at scale).
- **Azure Policy** controls *what configurations are allowed*, independent of RBAC's *who can do what*. It inherits downward, evaluates existing resources retroactively, can auto-remediate, supports exceptions, and applies even to AI-assisted/Copilot-proposed changes.
- **Initiatives** group related policies toward one larger compliance goal.
- **Resource locks**: Delete (blocks deletion only) vs. ReadOnly (blocks both deletion and modification, like the Reader role). They inherit downward and **apply regardless of RBAC** — even Owners must remove a lock before acting.
- **The big-picture pattern**: RBAC, Policy, and Locks all inherit down the same hierarchy and none can be bypassed by high privilege alone; **tags are the deliberate exception** that doesn't inherit at all.
- **Service Trust Portal** isn't officially tested — light awareness only.

---

## Practice Questions

**Q1 (Easy).** Which Microsoft Purview solution area is built primarily on Microsoft 365 services like Teams, OneDrive, and Exchange?

A) Unified Data Governance
B) Risk and Compliance
C) Azure Policy
D) Service Trust Portal

<details>
<summary>Answer</summary>

**Correct: B — Risk and Compliance.** This solution area is explicitly rooted in Microsoft 365 services for protecting sensitive data and managing regulatory compliance.
- A is wrong: Unified Data Governance spans a broader estate (Azure, SQL/Hive, on-prem, other clouds), not specifically M365.
- C and D are unrelated tools entirely, not Purview solution areas.
</details>

---

**Q2 (Easy).** Which resource lock type allows a user to modify a resource but prevents them from deleting it?

A) ReadOnly
B) Delete (CanNotDelete)
C) Contributor lock
D) Owner lock

<details>
<summary>Answer</summary>

**Correct: B — Delete lock.** It specifically blocks deletion while still allowing modification.
- A is wrong: ReadOnly blocks both modification and deletion.
- C and D aren't real resource lock types — Contributor and Owner are RBAC roles, not locks.
</details>

---

**Q3 (Medium).** A user has the Owner role on a resource that also has a ReadOnly lock applied. What happens if they try to delete the resource?

A) The deletion succeeds because Owner overrides any lock.
B) The deletion is blocked; the user must remove the lock first, regardless of their Owner role.
C) The deletion succeeds only if they also have Global Administrator rights.
D) ReadOnly locks only affect Reader-role users, not Owners.

<details>
<summary>Answer</summary>

**Correct: B.** Resource locks apply regardless of RBAC permissions — even an Owner must remove the lock before performing a blocked action.
- A and D both incorrectly assume high-privilege roles bypass locks — they don't.
- C is wrong: no role, however elevated, bypasses a resource lock without first removing it.
</details>

---

**Q4 (Medium).** A company creates an Azure Policy restricting VM sizes to a specific set. Six months later, they discover VMs deployed *before* the policy existed that violate it. What happens?

A) Nothing — Azure Policy only evaluates resources created after the policy exists.
B) Azure Policy evaluates and flags those pre-existing VMs as noncompliant too.
C) The VMs are automatically deleted.
D) The policy silently ignores VMs it can't enforce at creation time.

<details>
<summary>Answer</summary>

**Correct: B.** Azure Policy evaluates and monitors all current resources, including those created before the policy existed — it isn't limited to gatekeeping only new creations.
- A and D both incorrectly assume Policy only applies going forward.
- C is wrong: Policy flags noncompliance; it doesn't automatically delete resources (though in some cases it can auto-remediate specific configuration issues like missing tags, not delete entire resources).
</details>

---

**Q5 (Medium).** Which statement best distinguishes Azure Policy from Azure RBAC?

A) RBAC controls what configurations are compliant; Policy controls who has access.
B) RBAC controls who can perform actions; Policy controls what configurations are allowed, independent of who's acting.
C) They are functionally identical, differing only in which portal blade you use.
D) Policy is a subset of RBAC and cannot function independently.

<details>
<summary>Answer</summary>

**Correct: B.** This is the core distinction: RBAC is about permissions (who), Policy is about compliance/configuration rules (what), and a user's RBAC permissions don't exempt them from Policy enforcement.
- A reverses the definitions.
- C is wrong: they serve genuinely different purposes and are managed/evaluated separately.
- D is wrong: Policy operates independently of RBAC, not as a subset of it.
</details>

---

**Q6 (Hard — cumulative, pulls in the cost management module).** A company wants every new resource to automatically receive an `Environment` tag, since tags don't inherit on their own. Which tool should they use, and how does its inheritance behavior compare to tags?

A) Azure RBAC; RBAC also doesn't inherit, matching tags' behavior
B) Azure Policy; Policy inherits down the hierarchy and can enforce/auto-add tags, unlike tags themselves
C) Resource locks; locks can add tags automatically
D) Microsoft Purview; Purview enforces tagging as part of its data governance features

<details>
<summary>Answer</summary>

**Correct: B.** Azure Policy is exactly the tool previewed at the end of your cost management module — it inherits down the hierarchy (unlike tags, which don't inherit at all) and can automatically add missing tags to noncompliant resources.
- A is wrong: RBAC does inherit (it's tags that don't), and RBAC has nothing to do with tag enforcement.
- C is wrong: resource locks control delete/modify permissions, not tag enforcement.
- D is wrong: Purview is about data governance/classification/lineage, not resource-level tag enforcement.
</details>

---

**Q7 (Hard — cumulative, ties together RBAC, tags, Policy, and locks).** Which of the following governance mechanisms does NOT automatically inherit down the Management Group → Subscription → Resource Group → Resource hierarchy?

A) Azure RBAC
B) Azure Policy
C) Resource locks
D) Resource tags

<details>
<summary>Answer</summary>

**Correct: D — Resource tags.** Tags are the deliberate exception among these four mechanisms — RBAC, Policy, and locks all inherit downward automatically, but tags must be applied explicitly at each level where you want them.
- A, B, and C all inherit down the hierarchy, making them incorrect answers to "which does NOT inherit."
</details>

---

*Next up: "Describe features and tools for managing and deploying Azure resources" — covering the Azure portal, Azure Cloud Shell/CLI/PowerShell, Azure Arc (a full payoff of the Domain 1 preview), infrastructure as code, and Azure Resource Manager (ARM) templates. Paste whenever you're ready.*
