# AZ-900 — Describe Azure Architecture and Services (35–40% of exam)
## Module: Describe Azure Identity, Access, and Security

> **Exam context:** This is the **last module in Domain 2** — once you're solid here, you've covered the biggest domain on the exam (35–40%) in full.
>
> **One scope gap to flag:** I checked this module's 9 learning objectives against the official skills-measured list, and **"Describe encryption concepts and key management options in Azure" is not on the official list as its own bullet.** Nothing about encryption at rest/in-transit or Azure Key Vault appears as a standalone tested item anywhere in the current AZ-900 skills document. That said, Key Vault does get a **supporting mention inside the officially-tested defense-in-depth model** (application and data layers reference it directly), so I haven't cut it entirely — I've marked the deep-dive Key Vault details (rotation policies, managed identities, separation of duties) ⏭️ SKIP, but kept the core "what is Key Vault + at-rest vs. in-transit" framing since it supports a topic that *is* tested.
>
> **What's next:** Domain 3, "Describe Azure management and governance" (30–35%) — the final domain, covering cost management, governance/compliance tools, resource management/deployment tools, and monitoring.

---

## 1. Microsoft Entra ID

Microsoft's cloud-based identity and access management service — lets users sign into Microsoft cloud apps and apps you build yourself.

**If you know on-premises Active Directory, this will feel familiar** — the key difference: you still control the identity accounts, but Microsoft ensures the service itself is globally available. Connecting the two (via **Microsoft Entra Connect**) adds a real benefit: on-prem AD alone doesn't monitor sign-in behavior, but once connected to Entra ID, Microsoft can detect suspicious sign-ins (unexpected locations, unknown devices) at no extra cost.

**Direct callback to Domain 1:** Microsoft Entra Connect — bridging on-premises AD with cloud Entra ID so changes flow **both ways** — is identity's version of the hybrid cloud deployment model.

**Who uses it:** IT admins (access control), app developers (SSO, credential integration), users (self-service password reset), and online service subscribers (Microsoft 365, Azure, Dynamics 365 already authenticate through it).

**What it provides:** Authentication (including self-service password reset, MFA, banned password lists, smart lockout), SSO, application management (Application Proxy, SaaS integration, My Apps portal), and device management (via Intune, enabling device-based Conditional Access).

### Microsoft Entra Domain Services

Provides **managed** domain services — domain join, group policy, LDAP, Kerberos/NTLM — **without you deploying or maintaining domain controllers**. Especially useful for legacy applications that can't use modern authentication; you can lift-and-shift them into a managed domain instead of running your own AD DS in the cloud.

- Creating a managed domain deploys **two Windows Server domain controllers** into your chosen region (called a **replica set**) — Microsoft handles all DC management, backups, and encryption at rest.
- Existing users/groups from your Entra tenant carry over, so users sign in with existing credentials.

**🚩 Exam trap — sync direction:** Microsoft Entra Domain Services performs a **one-way sync from Entra ID to Domain Services only.** Resources created directly in the managed domain are **not** synced back to Entra ID. Contrast this with Microsoft Entra Connect above, which flows changes in both directions between on-prem AD and Entra ID. **Two different sync directions for two different services — easy to mix up.**

**🧠 Memory trick:** *"Entra Connect = two-way bridge (on-prem ↔ cloud). Entra Domain Services = one-way waterfall (Entra ID → Domain Services only)."*

---

## 2. Authentication Methods

**Security vs. convenience spectrum:** Passwords alone = low security, high convenience. MFA = higher security, somewhat less convenient. **Passwordless = high on both** — the sweet spot Microsoft is pushing toward.

### Single Sign-On (SSO)
One identity, access to multiple trusted apps. Reduces password sprawl (fewer credential incidents, less lockout/reset overhead) and simplifies access lifecycle management (one identity to update/remove).

**🚩 Exam trap:** **"Single sign-on is only as secure as the initial authenticator."** Since every subsequent app access rides on that first sign-in's security, a compromised initial authenticator exposes everything connected to it. This is a genuinely testable nuance, not just a warning — SSO isn't automatically "more secure," it concentrates risk onto one entry point.

### Multifactor Authentication (MFA)
Requires an extra factor beyond a password, from three categories:
- **Something you know** (password, challenge question)
- **Something you have** (a code sent to your phone)
- **Something you are** (biometric — fingerprint, face scan)

An attacker with a password alone still needs the second factor.

### Passwordless Authentication
Eliminates the password entirely — replaced by a trusted device + biometric/PIN. Three Microsoft-supported options:

| Method | Form factor | Best-fit scenario |
|---|---|---|
| **Windows Hello for Business** | Biometric/PIN tied to a specific Windows PC | Information workers with their own designated work PC; includes PKI integration and built-in SSO |
| **Microsoft Authenticator app** | Any iOS/Android phone | Users who want their personal phone as the sign-in factor (notification + number match + biometric/PIN) |
| **FIDO2 security keys** | Physical hardware (USB, Bluetooth, or NFC) | Scenarios needing **unphishable** hardware-based authentication with no username/password at all |

**🚩 Exam trap:** Don't confuse MFA with passwordless — MFA still *includes* a password as one factor; passwordless removes the password from the equation entirely, replacing it with device possession + biometric/PIN as the *whole* authentication event.

**🧠 Memory trick:** *"Windows Hello = tied to one specific PC. Authenticator = tied to your phone, anywhere. FIDO2 = tied to a physical key you carry, unphishable by design."*

---

## 3. External Identities

An **external identity** = a person, device, or service outside your own tenant (partners, suppliers, contractors). **Microsoft Entra External ID** covers secure interaction with these users.

Three distinct capabilities — another comparison-heavy trap cluster:

| | B2B Collaboration | B2B Direct Connect | Entra External ID for Customers (formerly Azure AD B2C) |
|---|---|---|---|
| Who it's for | Partners/suppliers/contractors accessing your internal apps | Mutual two-way trust between two Microsoft Entra tenants | Consumers/customers of apps *you publish* |
| Represented in your directory? | **Yes** — typically as guest users | **No** — only visible within the specific collaboration surface (currently Teams shared channels) | N/A — different identity model entirely, for external-facing apps |
| Current scope | Broad (SaaS apps, custom apps, internal resources) | Currently limited to Teams shared channels | Publishing your own SaaS/custom apps (not Microsoft apps) to the public |

**🚩 Exam trap:** The single most testable detail here: **B2B collaboration users show up as guest users in your directory. B2B direct connect users do NOT appear in your directory at all** — they're only visible/monitorable within the Teams shared channel itself (via Teams admin center reports). If a question asks "which one creates guest user objects," the answer is B2B collaboration.

**Guest access lifecycle:** Access reviews let you (or the guests themselves) periodically recertify whether continued access is still needed, with Entra ID surfacing suggestions — then you can revoke access for guests who no longer need it.

---

## 4. Microsoft Entra Conditional Access

A tool that allows/denies access **based on signals** — who the user is, where they are, what device they're using.

**Flow:** Signal → Decision → Enforcement.
- **Signal** — user location, device, application being accessed.
- **Decision** — allow fully, block entirely, or require an extra factor.
- **Enforcement** — the action carried out (allow, block, or challenge for MFA).

**Common use cases:**
- Require MFA based on role, location, or network (e.g., always for admins, or only from outside trusted networks).
- Require access only through approved client applications.
- Require access only from managed (compliant) devices.
- Block access from untrusted/unexpected sources.

**Direct callback:** This is what makes MFA "granular" rather than all-or-nothing — a known location might skip the second factor entirely, while an unusual one triggers it. This is the practical mechanism that operationalizes the "verify explicitly" principle you're about to meet in Zero Trust.

---

## 5. Zero Trust

A security model that **assumes breach from the start** and verifies every request as if it originated from an uncontrolled network — regardless of whether it came from inside or outside your traditional network perimeter.

**Three guiding principles:**
1. **Verify explicitly** — always authenticate/authorize using all available data points.
2. **Use least privilege access** — Just-In-Time (JIT) and Just-Enough-Access (JEA), risk-based adaptive policies, data protection.
3. **Assume breach** — limit blast radius, segment access, verify end-to-end encryption, use analytics for visibility/detection.

**The shift it represents:** Traditional perimeter-based security assumed "inside the network = safe." Zero Trust removes that assumption entirely — access is granted based on **continuous, context-aware verification**, never just on network location.

**🚩 Exam trap:** Don't confuse Zero Trust (a set of guiding *principles/mindset*) with defense-in-depth (a *structural, layered* model, covered next) — they're related and complementary, but they answer different exam-question shapes. A question about "assume breach, verify explicitly, least privilege" → Zero Trust. A question about "layers from physical to data" → defense-in-depth.

---

## 6. Azure Role-Based Access Control (RBAC)

Implements the **principle of least privilege**: grant only the access needed for a task, nothing more — without the unsustainable overhead of managing individual permissions person-by-person.

**How it works:** Azure provides built-in roles (or you define custom ones); each role bundles a set of permissions. Assign people/groups to a role, and they get all its permissions — automatically extending to new resources as they're added to that scope.

### Scope Hierarchy — Direct Callback to Domain 2, Module 1

RBAC is applied to a **scope**, and permissions **inherit downward** — this is the *exact same hierarchy* you learned for management groups/subscriptions/resource groups back in your very first Domain 2 module, just now carrying access permissions instead of governance policies:

**Management Group → Subscription → Resource Group → Resource**

Example: assign **Owner** at the management group level, and that user can manage everything in every subscription underneath. Assign **Reader** at the subscription level, and that group can view everything in every resource group/resource beneath it.

**🧠 Memory trick:** *"Same ladder as before — Management Group → Subscription → Resource Group → Resource — just carrying permissions downhill instead of policies this time."*

**Enforcement:** RBAC is enforced on any action passing through **Azure Resource Manager** (accessed via portal, Cloud Shell, PowerShell, or CLI). **🚩 Exam trap: RBAC does NOT enforce access at the application or data level** — that's your application's own responsibility to handle.

**RBAC uses an allow model:** if one role assignment grants read on a resource group and a different assignment grants write on that same resource group, you end up with **both** — permissions combine additively.

---

## 7. Defense-in-Depth

A layered security strategy — if one layer is breached, the next layer is already there to slow the attacker down further. **Seven layers, outer to inner**, with data at the very center:

| Layer (outer → inner) | Focus | Key actions |
|---|---|---|
| **1. Physical security** | Protecting the datacenter hardware itself | Physical access controls to buildings/hardware |
| **2. Identity and access** | Securing identities, granting only needed access | SSO, MFA, audit sign-ins/changes |
| **3. Perimeter** | Blocking network-based attacks from outside | DDoS protection, perimeter firewalls |
| **4. Network** | Limiting connectivity between resources | Segmentation, deny-by-default, restrict inbound/outbound, secure on-prem connectivity |
| **5. Compute** | Securing VMs and endpoints | Endpoint protection, patching, secure VM access |
| **6. Application** | Reducing vulnerabilities in app code | Secure-by-default development, storing secrets in **Azure Key Vault** |
| **7. Data** | Protecting the actual data | Encryption at rest and in transit |

**🚩 Exam trap:** This ordering is genuinely testable — if a scenario describes "filtering large-scale attacks before they reach users," that's the **perimeter** layer (DDoS), not the network layer, which is instead about limiting connectivity *between* your own resources once traffic is already inside.

**Direct callbacks across your whole track:**
- **Perimeter layer (DDoS)** → Domain 1, Module 2's mention that cloud providers are well-suited to handle DDoS attacks.
- **Network layer (NSGs, restricting inbound/outbound)** → your Domain 2 networking module, concretely.
- **Data layer (encryption at rest/in transit)** → conceptually complements (but is distinct from) the storage *durability* redundancy options (LRS/ZRS/GRS/GZRS) from your last module — durability protects against hardware failure, encryption protects against unauthorized access. Different threats, both "protecting data."

---

## 8. Encryption and Key Management ⏭️ Mostly SKIM (see scope note at top)

- **Encryption at rest** — protects stored data (databases, disks, storage accounts).
- **Encryption in transit** — protects data moving between services/apps/users.
- A strong posture uses both.

**Azure Key Vault** — securely stores secrets (connection strings, passwords), encryption keys, and certificates, centralizing management instead of hard-coding sensitive values into app code.

⏭️ **SKIP for AZ-900 specifically:** rotation policies, separation of duties, managed-identity retrieval patterns — genuinely useful real-world practice, but beyond what's tested here. Just know Key Vault's core purpose, since it's directly referenced within the (officially tested) defense-in-depth application/data layers above.

---

## 9. Microsoft Defender for Cloud

A **security posture management and threat protection** service, monitoring Azure, on-premises, hybrid, and multicloud resources from one control plane.

**Direct callback to Domain 1, Module 1:** Remember **Azure Arc**, introduced all the way back in your very first module as a tool for managing resources across environments? Here's its concrete payoff — **Azure Arc extends Defender for Cloud's protections to non-Azure/on-premises machines.** Multicloud coverage (AWS, GCP) is handled differently — via **agentless Cloud Security Posture Management (CSPM)** assessment, rather than the fuller Arc-based extension used for hybrid/on-prem.

**🚩 Exam trap:** Don't conflate the two multicloud/hybrid extension methods — **Azure Arc** = extends full Defender *plans* to non-Azure machines (hybrid/on-prem). **CSPM** = agentless *assessment* of multicloud resources (AWS/GCP), a lighter-weight posture check rather than full protection extension.

### Three Pillars: Assess → Secure → Defend

| Pillar | What it does |
|---|---|
| **Continuously assess** | Vulnerability assessment for VMs, container registries, SQL servers; integrates with Microsoft Defender for Endpoint |
| **Secure** | Builds on **Azure Policy** (a preview of Domain 3!) to evaluate new resources against best practices, using the **Microsoft Cloud Security Benchmark (MCSB)**; produces a **secure score** to prioritize fixes |
| **Defend** | Generates security alerts (with remediation steps), uses **kill-chain analysis** to correlate related alerts into a full attack story, and provides advanced threat protection (e.g., just-in-time VM access) |

**🧠 Memory trick:** *"Assess = know your weak spots. Secure = harden against a benchmark, get a score. Defend = catch and respond to actual attacks in progress."*

---

## Master Scenario Table

| Scenario | Correct concept |
|---|---|
| A supplier's employees need guest access to specific internal collaboration resources | B2B collaboration |
| Two companies want mutual, seamless access within Teams shared channels only, without directory entries | B2B direct connect |
| A company wants to publish a consumer-facing app to the public with its own sign-up/sign-in | Entra External ID for customers |
| Legacy app needs domain join/LDAP without deploying domain controllers | Microsoft Entra Domain Services |
| A user with their own dedicated work PC wants passwordless sign-in tied to that machine | Windows Hello for Business |
| A user wants passwordless sign-in via their personal phone | Microsoft Authenticator app |
| An organization needs unphishable, hardware-based authentication with no username | FIDO2 security key |
| Skip a second authentication factor for known/trusted locations, require it for unusual ones | Conditional Access |
| Apply the same permission automatically across many subscriptions in one action | RBAC at the management group scope (direct callback to Domain 2, Module 1) |
| Filter a large-scale attack before it reaches your users | Defense-in-depth: Perimeter layer (DDoS) |
| Extend full security protection to an on-premises server | Azure Arc + Microsoft Defender for Cloud |
| Get a lightweight security posture check across AWS/GCP resources without deploying agents | CSPM (Microsoft Defender for Cloud) |

---

## Key Takeaways

- **Microsoft Entra Connect** (two-way sync, on-prem ↔ cloud) vs. **Microsoft Entra Domain Services** (one-way sync, Entra ID → Domain Services only) — don't mix up the sync direction.
- **SSO** is convenient but concentrates risk on the initial authenticator. **MFA** adds a second factor. **Passwordless** removes the password entirely — three named methods (Windows Hello for Business, Authenticator app, FIDO2), each suited to a different scenario.
- **B2B collaboration** (guest users, visible in directory) vs. **B2B direct connect** (no directory presence, Teams-shared-channels-only today) vs. **External ID for customers** (consumer-facing apps) — three distinct external identity capabilities.
- **Conditional Access** = signal → decision → enforcement, making MFA context-aware rather than all-or-nothing.
- **Zero Trust** = verify explicitly, least privilege, assume breach — a mindset, distinct from defense-in-depth's structural layers.
- **RBAC** inherits down the **same Management Group → Subscription → Resource Group → Resource** hierarchy from Domain 2, Module 1, enforced via Azure Resource Manager, using an additive allow model. Doesn't cover app/data-level security.
- **Defense-in-depth**: 7 layers, outer to inner — Physical, Identity & Access, Perimeter, Network, Compute, Application, Data.
- **Encryption/Key Vault** — not separately tested on AZ-900, but referenced inside defense-in-depth's app/data layers.
- **Microsoft Defender for Cloud**: Assess → Secure → Defend, extends to hybrid via **Azure Arc** (full protection) and to multicloud via **CSPM** (lighter agentless assessment).

---

## Practice Questions

**Q1 (Easy).** Which passwordless authentication method is tied specifically to a user's own designated Windows PC?

A) Microsoft Authenticator app
B) FIDO2 security key
C) Windows Hello for Business
D) Conditional Access

<details>
<summary>Answer</summary>

**Correct: C — Windows Hello for Business.** Its biometric/PIN credentials are directly tied to the user's specific PC.
- A is tied to a phone, not a PC.
- B is a portable hardware device, not tied to any specific machine.
- D isn't an authentication method at all — it's a policy enforcement tool.
</details>

---

**Q2 (Easy).** Which statement about Microsoft Entra Domain Services synchronization is correct?

A) It synchronizes bidirectionally between Entra ID and Domain Services.
B) It synchronizes one-way, from Entra ID to Domain Services only.
C) It doesn't synchronize at all — each system is fully independent.
D) It synchronizes one-way, from Domain Services to Entra ID only.

<details>
<summary>Answer</summary>

**Correct: B.** Resources created in Entra ID flow down to the managed domain, but resources created directly in the managed domain are never synced back up to Entra ID.
- A describes Microsoft Entra Connect, not Domain Services — a common mix-up between the two similarly-named services.
- C is wrong: there is synchronization, just one-directional.
- D reverses the actual direction.
</details>

---

**Q3 (Medium).** A company wants suppliers' employees to access specific internal collaboration apps using their own existing credentials, with those users appearing as guest accounts in the company's directory. Which capability fits?

A) B2B direct connect
B) B2B collaboration
C) Entra External ID for customers
D) Microsoft Entra Domain Services

<details>
<summary>Answer</summary>

**Correct: B — B2B collaboration.** This is the capability where external users are represented in your directory, typically as guest users.
- A is wrong: B2B direct connect users are NOT represented in your directory at all — only visible within Teams shared channels.
- C is wrong: External ID for customers is for publishing your own consumer-facing apps, not internal collaboration with suppliers.
- D is unrelated to external collaboration — it's about managed domain services for legacy apps.
</details>

---

**Q4 (Medium).** Which defense-in-depth layer is specifically responsible for filtering large-scale attacks (such as DDoS) before they affect users?

A) Network layer
B) Perimeter layer
C) Compute layer
D) Application layer

<details>
<summary>Answer</summary>

**Correct: B — Perimeter.** DDoS protection and perimeter firewalls are explicitly the perimeter layer's job.
- A is wrong: the network layer focuses on limiting connectivity *between* your own resources once traffic is already inside, not filtering large-scale external attacks.
- C and D address VM/endpoint security and application code vulnerabilities respectively — unrelated to large-scale attack filtering.
</details>

---

**Q5 (Medium).** A company assigns the Owner role to a user at the management group scope. What is the effect?

A) The user can manage resources only within that specific management group object itself, not any subscriptions under it
B) The user can manage everything in every subscription within that management group, due to downward inheritance
C) The assignment has no effect until also applied at the subscription level
D) The user gains Owner rights across all of Azure globally, regardless of management group boundaries

<details>
<summary>Answer</summary>

**Correct: B.** RBAC permissions inherit downward through the same Management Group → Subscription → Resource Group → Resource hierarchy from Domain 2, Module 1 — Owner at the management group level cascades to everything beneath it.
- A is wrong: inheritance means the effect explicitly extends downward, not just to the top-level object.
- C is wrong: no separate subscription-level assignment is needed — inheritance handles it automatically.
- D is wrong: the scope is bounded by that specific management group's hierarchy, not all of Azure globally.
</details>

---

**Q6 (Hard).** A company wants to extend Microsoft Defender for Cloud's full protection capabilities to an on-premises server, and separately wants a lightweight security posture assessment of its AWS resources without deploying agents. Which two mechanisms should it use, respectively?

A) Azure Policy for on-premises; Microsoft Entra Connect for AWS
B) Azure Arc for on-premises; CSPM for AWS
C) CSPM for on-premises; Azure Arc for AWS
D) Microsoft Entra Domain Services for both

<details>
<summary>Answer</summary>

**Correct: B.** Azure Arc extends full Defender plans to non-Azure/on-premises machines; CSPM provides agentless posture assessment for multicloud resources like AWS — exactly matching the two different needs described.
- A is wrong: Azure Policy is a governance tool (previewed here, covered fully in Domain 3), not the mechanism for extending Defender to hybrid machines; Entra Connect is an identity sync tool, unrelated to security posture assessment.
- C reverses the correct pairing.
- D is wrong: Domain Services is about managed domain join/LDAP, entirely unrelated to security posture management.
</details>

---

**Q7 (Hard — cumulative, pulls in Domain 1).** A company connects its on-premises Active Directory to Microsoft Entra ID using Microsoft Entra Connect, enabling users to sign in consistently whether accessing on-premises or cloud resources. Which Domain 1 concept does this directly implement?

A) Multicloud deployment model
B) Hybrid cloud deployment model
C) SaaS shared responsibility model
D) Consumption-based pricing model

<details>
<summary>Answer</summary>

**Correct: B — Hybrid cloud.** Bridging a private, on-premises identity system with a public cloud identity service, so both work together seamlessly, is the identity-layer implementation of the hybrid cloud model you first learned in Domain 1.
- A is wrong: only one public cloud provider (Azure/Entra ID) is involved, alongside a private on-premises system — not multiple public providers.
- C is wrong: no finished, ready-to-use software product is being described — this is infrastructure/identity integration, not a shared-responsibility service-model question.
- D is wrong: nothing about billing or pay-as-you-go pricing is described here.
</details>

---

**Q8 (Hard — cumulative, pulls in Domain 2 Module 1 and this module).** A company grants the Reader role to a security team at the subscription scope, and separately grants the Contributor role to a different team at the resource group scope within that same subscription. Which statement is TRUE?

A) The security team's Reader access at the subscription level overrides the Contributor access at the resource group level, reducing it to Reader only.
B) Both teams' permissions apply independently within their respective scopes; the Contributor team retains write access at their resource group, while the security team can read everything in the subscription, including that resource group.
C) RBAC does not allow two different roles to be active simultaneously within the same subscription.
D) The Contributor role automatically overrides the Reader role wherever their scopes overlap.

<details>
<summary>Answer</summary>

**Correct: B.** RBAC permissions are additive and scope-based — the subscription-level Reader assignment inherits down (so the security team can read the resource group too), while the resource-group-level Contributor assignment independently grants that other team write access at their narrower scope. Neither overrides the other; they simply combine.
- A and D both incorrectly assume one role "overrides" the other — RBAC doesn't work by override, it works by additive, scope-based inheritance.
- C is wrong: multiple role assignments at different (or even overlapping) scopes are completely normal and expected in RBAC.
</details>

---

## Domain 2 Complete ✅

You've now covered all of "Describe Azure architecture and services" (35–40% of the exam) — the largest domain: core architectural components, compute and networking services, storage services, and identity/access/security. Notice how much this domain built on itself: the Management Group → Subscription → Resource Group → Resource hierarchy from your very first module here resurfaced *twice* more (once for storage account context, once for RBAC scope), and Azure Arc's preview from Domain 1 paid off concretely in Defender for Cloud.

*Next up: Domain 3, "Describe Azure management and governance" (30–35%) — the final domain, covering cost management, governance/compliance tools (Microsoft Purview, Azure Policy, resource locks), resource management/deployment tools (Azure portal, Cloud Shell, CLI, PowerShell, Azure Arc, infrastructure as code, ARM templates), and monitoring tools (Azure Advisor, Service Health, Azure Monitor). Paste the first module whenever you're ready.*
