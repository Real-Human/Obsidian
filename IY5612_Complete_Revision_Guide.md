# IY5612 — Critical Infrastructure Security
## Complete Revision Guide

*Based exclusively on the syllabus, lecture units 1–8, and the 2019 final exam paper. No external content has been added.*

*This document combines the narrative revision guide with the second-pass detailed addendum. Treat it as the single comprehensive reference; specific facts, figures, and formulas sit in the section they belong to.*

---

## How to use this guide

The 2019 exam paper has **5 questions, of which you must answer 3**. The questions span the full module:

| Q | Topic area | Units |
|---|---|---|
| Q1 | Control Systems Security (Modbus, OPC UA, state estimation) | 6, 7 |
| Q2 | CI Interdependencies (graph fragmentation, flows, cascades) | 3, 5 |
| Q3 | Network Robustness (BGP, RPKI, route diversity, power grid) | 4, 5 |
| Q4 | Cyber Security Analysis (APTs, kill chain, supply chains, attribution) | 1, 2 |
| Q5 | Attack/Adversary Modelling (attack trees, AD-trees, game theory) | 8 |

Pick three strongest "themes". Conceptually-distinct safe combinations: **Q2 + Q4 + Q5** (graphs, attacks, modelling) or **Q1 + Q3 + Q4** (control systems, network/power, attacks).

Each unit section contains: **definitions → core content → likely exam framing → things to memorise**.

Tips for using this guide:
1. First pass: read end-to-end to absorb the structure.
2. Second pass: drill the cheat sheet and quick-fire facts at the end.
3. Third pass: write a 30-minute mock answer to a 2019 question; compare against the worked answers in Part 9.

---

# Part 1 — Unit 1: Introduction & National Strategies

## 1.1 Sunburst, Colonial Pipeline, xz

### Why supply chains matter
Software updates are released on fast cycles and installed automatically. Integrity is validated via digital signatures — but only as far as the signing keys, certificates, and the signed code itself are trustworthy. Trust relationships are **transitive** across components used to develop and distribute others. PCI-DSS requires critical patches within 30 days; the supply chain is fast-moving and its visibility is limited.

### Sunburst (SolarWinds Orion) — specifics
- **October 2019**: adversary penetrated the Orion build system after finding credentials on the build-system repository: **`SolarWinds123`**.
- ~**18,000 sites** received the trojanised update.
- Active **March → December 2020** undetected.
- Discovered only because **FireEye** suffered a downstream breach in December 2020 and traced it back; FireEye's red-team tool database was stolen in the process.
- During investigation a **second** implant ("**Supernova**") was identified — apparently from a different actor "further East".
- Communication via **encoded DNS queries**; exfiltration **steganographically hidden in .NET XML assemblies**.
- Two-week dormant period to evade sandboxes; anti-forensics blacklist.
- SolarWinds had previously advised customers to **whitelist** Orion in antivirus.
- Resulted in **US Executive Order 14028** ("Improving the Nation's Cybersecurity").

### xz utilities backdoor (2024)
- Three-year social-engineering campaign by personas culminating in maintainer handover.
- Backdoor constructed only at **release-build** stage; not visible in the Git repository.
- Triggered by a **steganographic code in an RSA authentication key**.
- Steganographic key generated **on-the-fly** using "odd features of the x86 instruction set" to evade memory analysis.
- Discovered by an admin who noticed unusual SSH timing — the lecture calls this "reminiscent of Cliff Stoll's *Cuckoo's Egg*".

### Colonial Pipeline (May 7 2021)
- **DarkSide** ransomware, based in the Russian Federation; described as "relative newcomers" who "had not expected the severity of impact despite their Big Game Hunting tactic".
- Only **IT systems** were directly affected; OT systems were precautionarily shut down because Colonial was unsure whether they had been breached. Restart took until 12 May.

### Other supply-chain incidents
- **log4j** — vulnerable downloads continued >2 years after the critical CVE.
- **Shai-Hulud (2025)** — self-propagating NPM worm.
- **JFrog Artifactory** — Microsoft was a victim ("ironically a tool for securing software supply chains").
- **AcidRain (Feb 2022)** — Viasat attack via firmware update server compromised through a misconfigured VPN; bricked **30,000 Ka-Sat modems** including **thousands of German wind-turbine controllers**.

## 1.2 What is Critical Infrastructure?

### Authoritative definitions
- **CI definition** (verbatim, EU Council Directive 2008/114/EC): "Asset, system, or part thereof located in a nation which is essential for the maintenance of vital societal functions, health, safety, security, economic, or social well-being of people, and the disruption of which would have a significant impact in that nation as a result of the failure to maintain those functions."
- **CII definition** (ENISA): "Interconnected information systems and networks, the disruption or destruction of which would have a serious impact on the health, safety, security, or economic well-being of citizens, or on the effective functioning of government or the economy."
- **Modern CI concept** dates to the **1997 US PCCIP report** *Critical Foundations: Protecting America's Infrastructures*. PCCIP identified 8 sectors; current US **HSPD-7** uses **17 sectors**.

### UK CI sectors (the lecture's list — 9 sectors, 14 sub-sectors)
**Communications, Financial Services, Government, Emergency Services, Health, Water, Energy, Food, Transportation.**

Sub-sector breakdowns:
- **Communications**: telecoms, post, broadcast media (cloud not yet explicitly included).
- **Government**: central + other (incl. elections).
- **Emergency Services**: police, fire, ambulance (cross-link Health), Coast Guard (cross-link Transport).
- **Health**: hospitals, GP offices, supply chains for med equipment, pharma, vaccines.
- **Water**: supply, sourcing, purification, distribution, sewers, waste-water treatment.
- **Energy**: electric / oil / gas (renewables not formally included yet).
- **Food**: production + distribution.
- **Transportation**: rail, ports, road, aviation.

### Why CI is hard to secure
- **Long lifetimes** (20–40 years for ICS components vs. 3–5 years for IT).
- **Legacy systems** with insecure protocols never designed for adversarial environments.
- **Continuous availability** requirements — patching windows are minimal.
- **Cyber-physical coupling** — software faults produce real-world damage.
- **Strong interdependencies** across sectors (electricity feeds telecom, telecom feeds finance, etc.).
- **Mixed ownership** (private operators, public oversight) → fragmented responsibility.

### Threat sources vs threat actors (HMG IA Standard No. 1)
- **Threat Source**: a person/organisation that desires to breach security and benefits from the breach.
- **Threat Actor**: the person who actually performs the attack (or causes the accident — does *not* require malice).
- **Threat Level** = capabilities × motivations.

### Threat actor classification matrix

| | **Economic impact** | **National security** |
|---|---|---|
| **State** | Industrial Espionage | Cyber Warfare |
| **Non-state** | Cyber Crime | Cyber Terrorism |

Plus "amateurs / script kiddies" — opportunistic users of off-the-shelf tools.

### Cyber war / cyber conflict
- **Clarke (2010) definition**: "Actions by a nation-state to penetrate another nation's computers or networks for the purposes of causing damage or disruption."
- **Arquilla & Ronfeldt (1993)**: distinguish **cyber-war** (military operations on info systems) from **netwar** (influence on populations — now usually called **PSYOPS**).
- NATO confirmed in **June 2016** that a cyber attack can trigger **Article 5**.
- **Tallinn Manual** on International Law Applicable to Cyber Warfare: 1st edition 2009-2012, 2nd 2017, 3rd in progress.

### Ukraine 2022 — what did and didn't happen
- The expectation was that Russia would deploy major cyber operations alongside kinetic operations. **It largely did not happen.**
- One success: **AcidRain** wiper on Viasat KA-SAT modems (Feb 2022).
- Defensive response: **>10 PB** of Ukrainian government data physically moved out via **AWS Snowball Edge** suitcases; **Microsoft Azure** + **AWS** hosted critical functions.

### Mirai (2016)
- IoT botnet exploiting default credentials. **Default credential to memorise: `root:xc3511`** — Hangzhou XiongMai webcams.
- Attacked the DNS provider **Dyn** in October 2016, taking down Twitter, Pinterest, GitHub, Spotify, PayPal.
- Botnet size cited as **≈0.5 million devices** (later variants reached 2.5m).
- A French ISP saw **>1 Tbit/s**; security consultancy **620 Gbps**.
- **IoT attacks quadrupled 2019–2022**; in 2019 IoT was 16% of infected devices.

### 2025 AWS US-EAST-1 outage
HMRC, Lloyds, Halifax all went down in the UK because they back-end on US-East-1 — a CI cascade example.

## 1.3 UK and US Strategy

### UK Cyber Strategy lineage
- **2011**: first UK Cyber Security Strategy (3 pillars: Defend, Deter, Develop).
- **2016**: revised strategy with **active cyber defence** emphasis.
- **2022**: rebranded as a **Cyber Power** strategy with **5 pillars**:
  1. Strengthening the UK cyber ecosystem.
  2. Building a resilient and prosperous digital UK.
  3. Taking the lead in technologies vital to cyber power.
  4. Advancing UK global leadership for a more secure, prosperous, and open international order.
  5. **Detecting, disrupting, and deterring** adversaries to enhance UK security in and through cyberspace.

### NCSC
- **Established October 2016 as a sub-entity of GCHQ.**
- **Merged the activities of CESG, CPNI, CCA, and CERT-UK.**
- Provides advice for government cyber security threat intelligence and information assurance.
- Public-facing, liaises with industry, academia, overseas partners.

### NCF
- **Joint MoD / GCHQ activity established in 2020.**
- Draws staff from **GCHQ, MoD, SIS (MI6), and DSTL**.
- Replaces the older National Offensive Cyber programme.
- 2022 strategy explicitly authorises NCF for tactical-to-strategic operations "in accordance with international law, including the law of armed conflict where applicable".
- Useful name to drop: **Ciaran Martin** (former head of NCSC).

### UK National Security Strategy (2015)
- **Cyber attacks are listed as a Tier 1 risk**, on par with major international military crisis or CBRN terrorism.
- **National Security Risk Assessment (NSRA)** is the formal mechanism.
- Threat sources named: **Criminals, Terrorists, Hacktivists, State Actors**, plus amateurs.
- **Single Intelligence Account** funds intelligence agencies.
- **Budapest Convention on Cyber Crime** is the relevant cross-border treaty.

### William Gibson / "cyberspace"
Term coined by Gibson in **Burning Chrome (1982)**. He admitted: "It seemed evocative and essentially meaningless." The 2016 UK strategy defined cyberspace as "an interactive domain made up of digital networks that is used to store, modify, and communicate information."

### UUNET 2002 outage
- Routine BGP misconfig — a **misplaced bracket** in a filter rule added EBGP routes to internal routers.
- ~**20% of US Internet connectivity** disrupted.
- A single character outdid the August 2003 North American power blackout in Internet impact.

### Subsea cables — exam-grade specifics
- **~99% of intercontinental data** travels over fibre-optic subsea cables.
- **SEA-ME-WE 3**: ~39,000 km, world's longest, 960 Gbit/s, reaches Indonesia, Australia, Japan, China, South Korea.
- **SEA-ME-WE 4**: ~18,800 km, on-stream since 2005, US$500m, 1.28 Tbit/s, links Marseille/Palermo through Egypt, Arabia, India, SE Asia.
- **SEA-ME-WE 5** (2016): 66 fibres × 100 Gbps.
- **January 2008** Alexandria anchor incident: SEA-ME-WE 3 and 4 both severed → **70% loss of regional connectivity**.
- **NY–London cable model**: 0.5 repairs/year, 6 days outage/year ⇒ **98.5% availability** with one cable.
- **Route diversity for 99.999% availability**: ~**3 routes** for NY–London, ~**8 routes** for Shanghai–Jakarta (geologically active with heavy shipping → 4.6 repairs/year vs 0.5/year on Tokyo–LA).
- **~200 cable breaks per year** (2023–24).
- **~80 vessels worldwide** handle repairs.
- Cable repair costs **US$1–3m** each.
- US has **two cable layers on retainer**.
- **1884 Cable Convention** — jurisdiction for cable damage lies with the **flag country of the vessel**.

### Cable tapping / sabotage history
- **Operation SHAMROCK** (NSA, since 1945) — targeted cable companies.
- **Operation IVY BELLS** (1971) — Soviet sub-sea cable in **Sea of Okhotsk** at **120 m / 400 ft depth**, carrying unencrypted C2. Interception device: **6 m long, 6 tons, radionuclide battery**, magnetic-tape retrieval by submarine divers. Signal captured via **inductive coupling** (induction coils around the cable). Now in former KGB museum.
- **November 2024 Baltic incidents**: Chinese-flagged freighter cut cable Finland–Germany, then Gotland (Sweden)–Lithuania a day apart. Hong Kong-flagged freighter cut Finland–Estonia gas pipeline a year earlier.

### Russian and Allied seabed assets
- **USS Jimmy Carter** — Seawolf-class SSN with extra **30 m UUV/ROV section**, seabed operations.
- **Russian Yantar** (Class 22010) — "Seabed Research Ship", intelligence ship with submersible hangar.
- **Russian Belgorod** (Project 09852) — modified Oscar SSGN with deep-diving mini-submarine and a nuclear reactor for subsea sensor arrays.
- **AS-31 Losharik** (Project 10831) — capable of >1000 m diving; suffered fatal fire in **2019** killing 14 sailors.
- **RFA Proteus** (UK, commissioned **October 2023**) — multi-role ocean surveillance vessel for subsea-infrastructure protection.
- **Hornsea offshore wind farm**: **5 GW**, completed **2025**, largest UK offshore installation.

### US strategy
- **CISA** as civilian CI security lead under DHS.
- **US Cyber Command**: established 2009 under USSTRATCOM, IOC 2010, **2022 strength: 6,200 personnel**.
- **Stuxnet** developed from **2008**; acknowledged as US/Israeli activity in **June 2012**.
- **Sector Risk Management Agencies (SRMA)** for each of 16 sectors.
- US doctrine post-2018: **persistent engagement / defend forward**.
- **Executive Order 14028 (2021)** — *Improving the Nation's Cybersecurity*. Created a **Cyber Safety Review Board** and IoT cyber-security capability labels.

### Other states with published cyber strategies
EU (2020), NATO (2011), Australia, Canada, Denmark, Estonia, Finland, France, Germany, Netherlands, Russian Federation.

---

# Part 2 — Unit 2: Complex Attacks (Q4 territory)

## 2.1 The Cyber Kill Chain (Hutchins et al., 2011)

**Memorise these 7 stages — they appear directly in the 2019 paper:**

1. **Reconnaissance** — Research, identification, and selection of targets, including OSINT via web sites, mailing lists, social media (LinkedIn, Facebook, etc.) to identify social relationships or technology information.
2. **Weaponisation** — Coupling of remote access mechanism with an exploit into a deliverable payload (frequently documents), usually via an automated tool ("weaponiser").
3. **Delivery** — Transmission to the target environment via electronic channels (messages, drive-by downloads), removable media (to access air-gapped systems), or insider threat actors (cooperative or unwitting).
4. **Exploitation** — After delivery, the adversary's code is triggered. May involve multiple stages for evasion/obfuscation and to escalate access.
5. **Installation** — Once compromised, the attacker attains persistency and obfuscates presence (root-kit tasks).
6. **Command & Control (C2)** — Compromised systems beacon (directly or indirectly) along a C2 chain for updates, instructions, exfiltration, or manual interaction.
7. **Actions on Objectives** — Attackers pursue principal objectives. May include lateral movement and re-applying the chain internally.

> **Exam tip (Q4 2019 lifted this directly):** quote the chain stages and then explain *defensive* opportunities at each stage — the original paper's whole point is that defenders only need to break **one** link.

### Defensive matrix (kill chain × defence action)
At each kill-chain stage you can: **Detect, Deny, Disrupt, Degrade, Deceive, Destroy**. Useful to mention in any kill-chain question — shows you've grasped the *intelligence-driven defence* idea.

### MITRE ATT&CK
- Formulated **beginning 2013**.
- A knowledge base of *tactics* (the why) and *techniques* (the how).
- Multiple matrices: **Mobile, Enterprise, Industrial Control Systems**.
- **TTPs** = Tactics, Techniques, and Procedures.
- Each technique entry includes: identifier, associated tactics, applicable platforms, preconditions, defences bypassed, data sources, mitigations, detection methods.
- Use cases: **Threat intelligence mapping, Gap identification, Incident support, Threat hunting, Red teaming**.
- **MITRE ATT&CK Navigator** is the tool.
- **MITRE Engage framework** complements ATT&CK for organising defence/deception.

### Battlefield Terrain Analysis (defensive analogy)
Five elements: **Observation and Fields of Fire, Cover and Concealment, Obstacles, Key Terrain, Avenues of Approach**. Useful framing for "how should defenders respond to APT threat" answers.

### Deception defences — five categories
**Honeypots, Honeynets, Honeyfiles, Honey records, Honey users** (e.g. fake credentials/hashes for pass-the-hash detection).

### Deception detection rates
- University students: **57%** detection rate.
- Trained law-enforcement professionals: **54%**.

Implication: humans are barely better than chance at spotting deception; deception in defence is therefore high-leverage.

## 2.2 APTs — Advanced Persistent Threats

### Definition (NIST SP 800-39 — verbatim, the lecture quotes it)
> "An adversary that possesses sophisticated levels of expertise and significant resources which allow it to create opportunities to achieve its objectives by using multiple attack vectors (e.g., cyber, physical, and deception). These objectives typically include establishing and extending footholds within the information technology infrastructure of the targeted organizations for purposes of exfiltrating information, undermining or impeding critical aspects of a mission, program, or organization; or positioning itself to carry out these objectives in the future. The advanced persistent threat: (i) pursues its objectives repeatedly over an extended period of time; (ii) adapts to defenders' efforts to resist it; and (iii) is determined to maintain the level of interaction needed to execute its objectives."

### Characteristics list to deploy in a Q4-style answer
- Targeted (not opportunistic).
- Multi-stage operations following a kill-chain pattern.
- Custom malware, often signed or living-off-the-land.
- Long dwell times (months to years).
- Use of zero-days where needed but prefers the cheapest tool that works.
- Operational security: false flags, attribution noise, infrastructure rotation.

### APT case studies

#### Solar Sunrise (1998)
- Late 1998 — attacks on US defence agencies, Lawrence Livermore, and Israeli Defence Force systems.
- Initially thought to be Iraqi state-sponsored; the actual attackers were **three teenage recreational hackers**.
- The lecture connects this to **Eligible Receiver 1997** — an NSA Red Team exercise that had already showed CI vulnerability.

#### Moonlight Maze (1998–9)
- Detected when DoD satellite control systems were penetrated.
- Maps, troop configurations, military hardware designs exfiltrated.
- Traced to hosts at the Russian Academy of Sciences (likely staging proxies).
- Initial attribution to Russian state-sponsored attackers could not be maintained.

#### Titan Rain (2003+)
- Incursions against US DoD, UK and German systems.
- Code-named by US investigators; traced to Chinese systems.
- Targets: Lockheed Martin, Sandia National Laboratories.
- Briefed publicly in 2005 with **Chinese PLA Computer Network Operations**.

#### Sykipot (2006–2011) and GhostNet (2009)
- Sykipot: defence, IT, telecoms, energy, chemical/pharma in US and UK.
- GhostNet: diplomatic systems; **Gh0st RAT** deployed.

#### Operation Aurora (2009)
- Google + 20+ companies in US and Canada.
- IE 0-day **CVE-2010-0249**.
- **Hydraq / Aurora Trojan**.
- Targeted Adobe Flash Player, IE, XML Core Services.
- The lecture introduces **watering-hole attacks** in this context.
- Attributed to the **Elderwood platform** by Symantec.

#### APT1 / Comment Crew (Mandiant report Feb 2013)
- Linked to **PRC PLA Unit 61398**.
- **141 attacks 2006–2013**, longest undiscovered presence **4 years 10 months**, average ~1 year.
- "Comment Crew" name from C2 hidden in **HTML comments**.
- **WEBC2** backdoor (since 2004) — uses HTML "comments" or covert tags from a webpage as the C2 channel.
- C2 disguises: **MSN Messenger, Jabber/XMPP, GMail Calendar**, with TLS/SSL transport.
- **Telvent (Canadian subsidiary of Schneider Electric)** was an APT1 target — relevant because Telvent makes SCADA/smart-grid components.
- Standard lateral movement: pass-the-hash transitive vulnerability.
- Late-2023 update: average APT dwell time has dropped to **~90 days** down from years.

#### RSA SecurID breach (March 2011)
- Two emails to a small group of RSA employees; one read: *"I forward this file to you for review. Please open and view it."*
- Attached: **`2011 Recruitment plan.xls`** with embedded SWF exploiting **CVE-2011-0609** (Adobe Flash 10.2.154.13 and earlier — cross-platform).
- Backdoor was a **Poison Ivy** RAT variant (~10 kB), modified for **reverse connection**.
- SecurID footprint: **>40m hardware tokens, up to 250m soft tokens**.
- Exfiltrated information enabled cloning of SecurID tokens — used in **March/April 2011** to penetrate VPNs of **Lockheed Martin, Northrop Grumman, L-3 Communications**.
- Connected to a wider intelligence operation McAfee called **Shady RAT**.
- Subsequent supply-chain attacks from 2017: **NetSarang/ShadowPad**, **Avast CCleaner**.

#### Storm-0558 (2023)
- Detected May 2023 via unusual ClientAppID/AppID entries in Microsoft 365 audit logs.
- Attackers obtained an **inactive Microsoft Account consumer signing key** (inactive since 2021).
- A flaw in the **GetAccessTokenForResource** API allowed forged MSA tokens to be exchanged for **Exchange Online + Azure AD** tokens.
- Cross-ecosystem access including SharePoint, Teams, OneDrive, and any third-party app using "Login with Microsoft".
- Microsoft blocked old tokens and invalidated MSA keys in July 2023.
- **Tentatively attributed to a PRC threat actor.**

#### Regin (revealed 2014)
- Active **2008–2011** (Gen 1) and **from 2013** (Gen 2).
- Six-stage architecture (0–5):
  - **Stage 0**: never observed in the wild (initial dropper).
  - **Stage 1**: masquerades as a driver, loads Stage 2 from NTFS extended attributes / Registry data.
  - **Stage 2**: kernel-mode driver, decrypts and loads Stage 3, obfuscates Stage 1.
  - **Stage 3**: kernel-mode DLL, configures modules.
  - **Stage 4**: user + kernel mode, loads payload from encrypted virtual file system containers.
  - **Stage 5**: payload modules.
- Encrypted components stored **outside the file system** (raw sectors, Windows Registry).
- Payloads include: network sniffing, file-system crawling, KB/mouse sniffing/injection, screenshots, password DB retrieval, and uniquely **GSM Base Station Controller (BSC) admin network sniffing**.
- C2 over **raw ICMP, UDP, TCP, HTTP**, with peer-to-peer forwarding.
- Last seen active **late 2014**.
- Assessed as Five Eyes attribution.

#### Operation Triangulation (revealed 2023)
- Detected by **Kaspersky's Russian operations**; targeted Russian entities since **at least 2021**.
- **Five zero-day vulnerabilities** chained.
- 0-click delivery via **iMessage with PDF** exploiting an ancient **TrueType VM** bug from the 1990s.
- **CVE-2023-38606** — bypass of Apple SoC kernel-memory protection (Page Protection Layer) using a GPU MMIO feature undocumented in Apple's own code.
- The lecture mentions **Pegasus and Graphite** as alternative delivery infrastructures for similar attacks.

### Russian APT taxonomy ("A Surfeit of Bears")
- **APT29 / Cozy Bear** — **SVR** (foreign intelligence). SolarWinds/Sunburst (2020), DNC, US DoD/think-tanks, COVID-19 vaccine research (2020). Signature: **Hammertoss** RAT using **Twitter + GitHub** with steganographic messages.
- **APT28 / Fancy Bear / Sandworm / Tsar Team** — **GRU**, units **74455** and **26165**. Targets: 2016 US elections, French elections 2017, OPCW, World Bank, NATO, OSCE, Bundestag, Ukraine, 2018 Winter Olympics. Frameworks: **Sourface / Sofacy**.
- **Energetic Bear** — **FSB unit 71330**.
- **Primitive Bear** — another FSB entity.
- **Conti Group** (cyber crime) — split between Russian and Ukrainian members → leak of code, identities, internal messages.

### Other APTs
- **Iran**: APT33, APT34, APT39.
- **North Korea**: **Lazarus** (largely indistinguishable from organised crime), **APT43**.
- **Mercenary spyware**: **Gamma Group, Hacking Team, NSO**.
- **Chinese division of labour (2023–2025)**:
  - **Salt Typhoon** (2023–24) — major US telco breach, classic counter-intelligence.
  - **Volt Typhoon** — pre-positioning in CNI.
  - **BlackTech** (Aug 2023) — US-Japan targets.

### Longhorn / Lamberts (US/CIA-attributed)
- Active since 2008; 40+ countries, many in Middle East.
- Family of malware: **Black** (active implants), **White** (passive), **Blue/Green** (staging), **Pink** (orchestration/beaconing), **Grey** (user-space passive).
- **Cultural code-words** (e.g. "PIZZA ASSAULT" in a "Green Lambert 3.0.5" config block) suggested origin — but always subject to false-flag interpretation.

### NotPetya (2017) — full lecture detail
- **Built on EternalBlue** (MS17-010, leaked from US NSA) — also used by WannaCry.
- **GRU Unit 74455** ("Main Centre for Special Technologies", **GTsST**) attribution.
- Origin was an **implant in M.E.Doc**, Ukrainian accounting software by **Linkos Group**.
- Propagated via **PSEXEC + WMIC + stolen credentials (Mimikatz)** plus **CVE-2017-0144 + CVE-2017-0145**.
- **>US$10 billion damage**; ~**10% of Ukrainian computers** affected; **30% of NotPetya victims were outside Ukraine**.
- Decryption was impossible — the ransom demand was a decoy; NotPetya encrypted files and **MFT** without retaining the key (i.e. it was a wiper).

### Maersk case study — concrete numbers
- Single user with **M.E.Doc** in an Odessa office.
- Maersk had **574 offices in 130 countries** with a flat AD topology (150 domain controllers).
- The "lucky break": a **power failure at the Accra, Ghana DC** at the time of attack meant one DC was offline → the only intact AD copy in the world.
- Recovery required physically flying the DC out: **Lagos relay** because Ghana staff lacked UK visas.
- Bookings during recovery taken via **WhatsApp and personal email** for ~2 weeks.
- Maersk represents **20% of worldwide shipping capacity**.

### Cyber-insurance impact
- **Mondelez vs Zurich** ($100m claim, 1700 servers, 24,000 desktops) — settled only **November 2022**.
- **Merck v. Ace American Insurance Company** ($1.4bn, **January 2022**) — ruling: "war or hostile acts" exclusion does **not** apply.
- **Lloyds 4 cyber-insurance exclusions** from March 2023:
  1. Exclude losses from war (declared or not).
  2. Exclude losses from state-backed cyber attacks that significantly impair a state's ability to function or its security capabilities.
  3. Be clear about coverage of computer systems located outside the affected state.
  4. Set out a robust basis for attributing state-backed cyber attacks.

### Supply-chain attacks (Q4 2019 explicitly asks)
- **SolarWinds / Sunburst** (2020): trojanised Orion update; ~18,000 customers received the update, ~100 confirmed targeted exploitation.
- **xz-utils backdoor** (2024): multi-year social-engineering campaign.
- **RSA → Lockheed** (2011): seed values stolen, then used against downstream defence customers.
- **US OPM breach (2013–2014)**: attackers breached OPM in Nov 2013, exfiltrated info on architecture and suppliers, then targeted **USIS** and **KeyPoint** (contractors) with access to OPM servers. Loss: millions of personnel records, security clearance background data, fingerprints.

**Why supply-chain works:** trust transitivity. The defender's perimeter is only as good as the weakest vendor in the dependency tree.

### Attribution — why it's hard
- Adversaries deliberately use **false-flag** techniques: planted Korean code in Olympic Destroyer, Cyrillic comments, recycled tooling.
- Infrastructure (C2 servers, domains) is cheap and rotated.
- Code reuse is widespread (especially open-source and leaked toolkits).
- Time-zone / language analysis is unreliable in adversarial conditions.
- Therefore attribution is usually **probabilistic** and based on convergent evidence (TTPs, infrastructure, victimology, intercepts), not single artefacts.

### October 2020 US DoJ indictment of GRU 74455 operators
For NotPetya and other activities. Question: whether criminal-justice frameworks are the right response.

### UN Charter Article 2(4)
Coercive cyber operations would constitute "an internationally wrongful act under the UN Charter Article 2(4) equivalent to armed attack" → entitles the attacked state to a proportionate (also military) response.

---

# Part 3 — Unit 3: Models of Large-Scale Networks (Q2 territory)

## 3.1 Graph Theory

### The motivating story: AT&T Bell System #4A (24 Nov 1961)
- US Strategic Air Command lost contact with the **BMEWS Site I radar** at **Thule Air Force Base, Greenland** (UK equivalent: **RAF Fylingdales**).
- SAC tried to call **NORAD** — line dead.
- Most likely scenario: surprise Soviet missile strike. SAC alert force activated, bombers dispatched.
- Actual cause: a single **AT&T Bell System #4A toll crossbar switch in Colorado** had failed.

The canonical "single-point-of-failure across CI" story for any Q2/Q3 introduction.

### Graph definitions to know precisely
**Graph** *G* = (*V*, *E*). For CI: V = entities (substations, ASes, hosts), E = relationships (cables, peering links, control flows).

- **Walk**: sequence of vertices/edges, repeats allowed.
- **Path**: walk with no repeated vertex.
- **Cycle**: closed path.
- **Connected**: every pair of vertices has a path between them.
- **k-connected** (vertex-connectivity ≥ k): need to remove ≥ k vertices to disconnect.
- **k-edge-connected**: need to remove ≥ k edges.
- **Vertex-independent paths** between u, v: paths sharing only u and v.
- **Edge-independent paths**: paths sharing no edges.
- **Graph isomorphism**: G₁ and G₂ are isomorphic iff there exists a one-to-one mapping φ: V₁ → V₂ such that for each edge e ∈ E₁ with e₁ = ⟨v,u⟩ there is a unique edge e₂ ∈ E₂ with e₂ = ⟨φ(v), φ(u)⟩.
- **Adjacency matrix** A: A[i,j] = number of edges between vᵢ and vⱼ. **G is simple iff A[i,j] ≤ 1 and A[i,i] = 0**. Row-sum = degree: Σⱼ A[i,j] = δ(vᵢ).
- **Incidence matrix** M: rows = vertices, columns = edges. For undirected G simple iff M[i,j] ≤ 1.
- **Directed graph (digraph)** D = (V, A) where each arc a = ⟨u→v⟩ joins tail u to head v.
- **Dijkstra's Algorithm**: solves single-source shortest path with non-negative edge costs, building a shortest-path tree.

### Menger's theorem (apex of Q2 2019 — memorise verbatim)
> Let *G* be a graph with two non-adjacent vertices *u*, *v*.
> - The **minimum number of vertices** in a vertex cut that disconnects *u* and *v* equals the **maximum number of pairwise vertex-independent paths** between *u* and *v*.
> - The **minimum number of edges** in an edge cut that disconnects *u* and *v* equals the **maximum number of pairwise edge-independent paths** between *u* and *v*.

**Corollaries:**
- *G* is *k*-connected iff any two distinct vertices are connected by ≥ *k* vertex-independent paths.
- *G* is *k*-edge-connected iff any two distinct vertices are connected by ≥ *k* edge-independent paths.

> **Why this matters for CI:** *redundancy = independent paths.* If your network has only one cut vertex (an "articulation point") or one cut edge ("bridge"), removing it fragments the network. The degree of *k*-connectivity is the operational measure of redundancy.

### Distance / centrality measures
- **Diameter** *d(G)*: max over all pairs of shortest-path distances.
- **Eccentricity** of *v*: max shortest-path distance from *v* to any other vertex.
- **Radius**: min eccentricity.
- **Average eccentricity / average path length**: mean of all eccentricities — more representative of typical behaviour than diameter.
- **Degree centrality**: |neighbours(v)|.
- **Betweenness centrality**: σ_st(v)/σ_st summed over s≠t≠v — fraction of shortest paths passing through v. High betweenness = critical "router" node.
- **Closeness centrality**: 1 / (sum of shortest-path distances from v to all others).
- **Eigenvector centrality**: a node is important if connected to important nodes (basis of PageRank).
- **Katz centrality**: variant addressing pathologies of eigenvector centrality on directed networks.

> **Q2 2019 trick:** *"large diameter but small average eccentricity"* — describes a graph with a long thin tail of remote nodes but a dense core. Attack consequences: removing a core node disproportionately affects the average; the diameter is dominated by a few distant nodes.

## 3.2 Random-graph models — exact specifications

### Erdős–Rényi — *two* definitions
- **ER(n, p)**: undirected; each edge ⟨u,v⟩ (u ≠ v) exists with probability p. **Always simple.**
- **ER(n, M)**: undirected; n vertices, M edges, each incident to randomly chosen vertex pairs. **May have loops and multi-edges.**

**Degree distribution: binomial** ≈ Poisson(λ = (n−1)p) for large *n*. Key word: **homogeneous** — degrees concentrated around the mean.

### Giant component — sharp thresholds (memorise)
For ER(n, p):
- p ≤ (1 − ε)/n: largest component is O(log n) "with high probability".
- p > (1 + ε)/n: a single **giant component** appears, others O(log n).
- p = 1/n: largest component proportional to **n^(2/3)**.
- For *full connectedness* the threshold is much higher: **Θ(n log n)** edges needed.

### ER giant-component summary by average degree k
- k < 1: small isolated clusters, small diameter, short paths.
- **k = 1: giant component arises, diameter peaks sharply, paths are LONG.**
- k > 1: almost all nodes connected, diameter shrinks, paths shorten.

### ER average path length
For large H ∈ ER(n, p):

  **d̄(H) = (ln n − γ) / ln(pn)**

where γ ≈ 0.5772.

ER is a **bad model for the Internet**: real CI networks have heavy-tailed degree distributions, ER does not.

### Watts–Strogatz Algorithm (lecture wording)
Let V = {v₁, …, vₙ}; choose **n ≫ k ≫ ln(n) ≫ 1** with k even.
1. Order vertices in a ring.
2. Connect each vertex to its **k/2** left-hand and **k/2** right-hand neighbours.
3. With probability p, replace edge ⟨u,v⟩ with edge ⟨u,w⟩ (w ≠ u, ⟨u,w⟩ ∉ E(G)).

Result: **WS(n, k, p)**.

For 0 < p ≪ 1: **high clustering coefficient** AND **short average path length** — the small-world phenomenon. Captures social and some infrastructure networks but **still does not produce heavy-tail degree distributions**.

### Barabási–Albert Algorithm (lecture wording — likely tested)
Start: G₀ ∈ ER(n₀, p) with V₀ = V(G₀). At each step s > 0:
1. Add new vertex vₛ : Vₛ ← Vₛ₋₁ ∪ {vₛ}.
2. Add **m ≤ n₀** edges from vₛ to vertex u ∈ Vₛ₋₁ with **preferential attachment probability**:

  **P[select u] = δ(u) / Σ_{w∈Vₛ₋₁} δ(w)**

3. Stop when n vertices have been added.

Result: **BA(n, n₀, m)**.

### BA degree distribution theorem (memorise)
For any BA(n, n₀, m) graph G and u ∈ V(G):

  **P[δ(u) = k] = 2m(m+1) / [k(k+1)(k+2)]  ∝  1/k³**

(So the BA power-law exponent is γ = 3.)

### Scale-free formal definition
A function f is **scale-free** iff f(bx) = C(b) · f(x), where C(b) is a constant dependent only on b. In a scale-free network, P[δ(u) = k] ∝ k^(−α).

### Scale-free properties summary
- Heavy-tailed degree distribution.
- **Hubs** dominate the topology.
- Small diameter (ultra-small world).
- Self-similar structure.
- **Robust to random failure** (most random nodes have low degree, so removal doesn't fragment) but **fragile to targeted attack** on hubs (the *robust-yet-fragile* property).
- Most real CI networks (Internet AS-level, power grids partially) are scale-free.

> **Q3 2019 hint:** when asked about Internet AS-level graph degree distribution — answer: **power-law**, scale-free, with a small number of high-degree tier-1 ASes acting as hubs.

---

# Part 4 — Unit 4: Internet Robustness (Q3 territory)

## 4.1 Internet structure

### AS/BGP scale (Dec 2025 figures)
- **~78,000 active advertised AS**.
- **1.05m active FIB entries**, **3.11m RIB entries**.
- The "1m route" router certification was crossed in **2025**.
- AS numbers: originally 16-bit; **32-bit AS numbers** standardised in **RFC 4893** (updated by **RFC 6793**).
- Routers use **TCAM (ternary content-addressable memory)** for FIB → prefix size matters because TCAM is expensive.
- Each AS must connect to **≥ 2** other AS.

### Node types within an AS
- **Border routers** — speak EBGP to neighbours.
- **Internal routers** — IGP (OSPF, IS-IS).
- **Route reflectors** — scale IBGP without full mesh.

### Topology classes
- **Tier-1**: full default-free zone, peers with everyone.
- **Tier-2**: pays tier-1, peers with other tier-2.
- **Tier-3 / stub**: customer-only.
- **Customer–Provider** vs **Peer–Peer** edges (Gao-Rexford rules).

### ToR (Type of Relationship) graph model
G = (V, E, R) where **R = {p2c, c2p, p2p, s2s}**:
- **p2c**: provider-to-customer.
- **c2p**: customer-to-provider.
- **p2p**: peer-to-peer.
- **s2s**: sibling-to-sibling.

Tier-1 ASes form a **full mesh** (observed empirically).

### NP-hardness result
Finding the maximum number of vertex-disjoint paths in a general ToR graph (or deciding whether k vertex-disjoint valid paths exist for constant k ≥ 2) is **NP-hard**. This is why "augment the graph for k-fault tolerance" matters: 2-fault tolerance is already considered prohibitively expensive.

### Deng et al. (2011) k-fault tolerance
"k-fault tolerant" informally = any pair of nodes is reachable with fewer than k faults. The augmented AS graph remains broadly scale-free but is no longer random. Tested by removing 100–3,500 nodes (~10% of AS); for **random** failures, 0- and 1-tolerant ToR graphs hold up; for **targeted** attacks on hubs, fragmentation is rapid (the *robust-yet-fragile* signature).

## 4.2 BGP attacks (Q3 2019 directly asks)

### Recap: BGP basics
- **Path-vector** protocol exchanging *AS_PATH* attributes.
- Operates between border routers via **TCP/179**.
- *No built-in authentication of origin or path*. Trust is purely transitive.

### Prefix hijacking
- An AS announces a prefix it does not own (or a more-specific prefix to win longest-prefix match).
- Variants:
  - **Origin hijack** — announces victim's prefix as if attacker owns it.
  - **More-specific hijack** — wins by /24 vs victim's /16.
  - **Path manipulation** — appends victim's AS to AS_PATH to look legitimate ("AS-path forgery").
  - **Route leak** — propagates a route in violation of policy.

### BGP hijack incidents (full lecture list)
- **AS7007 (1997)** — accidental route propagation; vast Internet traffic redirected.
- **Pakistan / YouTube (2008)** — anti-pornography blackhole leaked globally.
- **China Telecom (2010)** — **37,000 prefixes for 15 minutes** (the lecture's specific figure).
- **Iran (January 2017)** — anti-pornography prefix hijack.
- **December 2017** — Google, Apple, Facebook routes redirected to **DV-LINK-AS** (Russia).
- **Amazon Route 53 / MyEtherWallet 2018** — DNS hijack via BGP, theft of cryptocurrency.

### Defences

#### RPKI (Resource Public Key Infrastructure)
- Cryptographically certifies that an AS is authorised to originate a given prefix.
- **RFC 6480 (2011)**.
- IANA → RIRs (e.g. RIPE NCC) → LIRs → ASes: hierarchy of certificates and **ROAs** (Route Origin Authorisations).
- **ROAs contain: address range, originating AS, maximum length**.
- Multiple, even overlapping ROAs may exist for the same range.
- Self-signed root certificates at RIR level.
- A router doing **ROV** (Route Origin Validation) marks routes as Valid / Invalid / Unknown.
- **Limitation**: only covers *origin* — not the AS_PATH. A path-manipulation attack with a forged but plausible path can still pass RPKI.

#### BGPsec
- Extends RPKI to sign each hop in the AS_PATH (path validation).
- Implemented as a **BGP Capability extension**; speakers negotiate willingness to send/receive BGPsec.
- Adds a **BGPsec_Path attribute** to UPDATE messages; each AS along the path signs.
- Prevents: **valid-origin grafting onto path**, **path poisoning**, **cut-and-paste creation of signed paths**.
- Updates **one neighbour at a time** — neighbour identifier in signature.
- **BGP one NLRI per UPDATE** restriction needed (signature semantics).
- Special handling for **route servers** (hidden AS), **AS-prepending**, and migration.
- **Limitations:**
  - *Computational cost* — heavy crypto on every update; not deployed at scale.
  - *Replay protection* requires expirations and re-signing.
  - **BGPsec islands**: until everyone deploys, partial deployment creates "islands" — outside an island all guarantees disappear. A **single non-BGPsec speaker** isolates islands of BGPsec speakers. The lecture's exact phrasing: "the most interesting parameter in the deployment of BGPsec is therefore how large the BGPsec **archipelago** can become."
- **Conclusion:** BGPsec is robust *in principle* but **deployment is the dominant practical concern**; current Internet is not BGPsec-protected.

#### Other defences
- **AS_PATH filtering** at peering points.
- **Maximum prefix limits**.
- **MANRS** (Mutually Agreed Norms for Routing Security).
- **Real-time monitoring services** (BGPmon, Cloudflare Radar).

### Route diversity / vertex independence (Q3 2019 last part)
For an AS *A* to be robust it should have **multiple vertex-independent paths** to its destinations. But **policy** matters — Gao-Rexford rules can force traffic through a specific provider regardless of physical diversity. Therefore route diversity at the *vertex* level **does not guarantee diversity in practice** because of policy filtering. Real diversity = topological diversity ∩ policy diversity.

## 4.3 DDoS and physical concentration

### DDoS chronology
- **1999**: First modern DDoS tools — **Trinoo, Stacheldraht**.
- **2000**: **Mafiaboy** (Michael Calce, age 15) attacked **Yahoo, CNN, eBay, Dell, Amazon**. Sentence: 8 months juvenile detention + CDN$250.
- **2002**: First DDoS against all **13 DNS root zones**.
- **2016**: **Mirai** — botnet sizes up to **2.5m IoT devices** (later variants); attacks against **Dyn, GitHub, Twitter, Reddit, Netflix, AirBnB**; Krebs site at 620 Gbps; French ISP >1 Tbit/s.
- **BASHLITE** also mentioned alongside Mirai.

### Amplification attacks
- Attacker spoofs victim's source IP and queries a public service that returns a much larger reply.
- **DNS amplification**: 64-byte query → entire (large) zone file. UDP-based → no TCP handshake to throttle attack.
- **NTP monlist amplification** — historically up to ×500.
- **Memcached amplification** — record amplification factors >50,000× (2018 GitHub attack peaked ~1.35 Tbps).

### Defences
- BCP-38 / source address validation (rarely deployed end-to-end).
- Anycast DNS.
- Scrubbing centres.
- Provider-side rate limiting.

### Carrier hotels
- **~1000–2000 facilities** cover the US continentally → host **80–90% of routing equipment**.
- **60 Hudson Street, NYC** (formerly Western Union HQ) — major carrier hotel:
  - **300,000 litres of fuel oil** for generators.
  - Building is **5 m above sea level**, **<400 m from the Hudson River**.
- Defence: geographical diversity, redundant peering elsewhere.

## 4.4 Inoperability Input-Output Models (IIM / DIIM)

### Leontief Input-Output equations (1930s, Nobel 1973)
Output of sector i:

  **xᵢ = aᵢ₁x₁ + aᵢ₂x₂ + … + aᵢₙxₙ + dᵢ**

(dᵢ is final demand). Used by the Soviet Union for economic planning.

### IIM (Haimes & Jiang)
- **Inoperability** *q_i* of sector *i* in [0,1]: degree of dysfunction.
- *q* = *A*·*q* + *c*, where *c* is the perturbation; solve *q* = (I − A)⁻¹ *c*.
- **DIIM (Dynamic IIM)** adds a time-rate constant *K* to model recovery.
- **Application:** estimating cascade impact across CI sectors (e.g. how much does a 20% electricity outage degrade telecoms, transport, finance?).

### DebtRank (Battiston et al., 2012, *Nature Scientific Reports*)
- Centrality metric analogous to PageRank for financial networks.
- Identifies institutions with the largest borrowing relative to asset size **and** with the most interconnections.
- Demonstrated empirically with FED data, beginning of the 2008 financial crisis (Aug 2007) vs height (Mar 2009): showed institutions far more fragile than capital ratios alone implied.

---

# Part 5 — Unit 5: CI Interdependencies & Power Systems (Q2/Q3 territory)

## 5.1 Modelling interdependencies

### Why CI is interdependent (Rinaldi-style classification)
- **Physical** — A's output is B's input (electricity → pumps).
- **Cyber** — A and B linked by ICT (control, monitoring).
- **Geographic** — colocation (cable + pipe in same trench).
- **Logical** — policy, market, regulatory coupling.

### Graph models for interdependency
- **Single-layer graphs** — collapse all sectors into one weighted graph.
- **Multi-layer / multiplex graphs** — each layer is one sector; cross-layer edges encode dependencies.
- **Interdependent network models** — Buldyrev et al. (2010, *Nature*) Catastrophic Cascade of Failures. Removing one node from network A removes its dependent in network B, which fragments A further.

### Flow problems and Ford–Fulkerson (Q2 2019)
- **Max-flow / min-cut theorem** — generalisation of Menger's theorem to capacities. Max flow from *s* to *t* = capacity of min *s-t* cut.
- **Ford–Fulkerson algorithm**: while there's an augmenting path in the residual graph, push flow along it.
- Basic complexity: **O(E · max|f|)**.
- Better algorithm: **King, Rao, Tarjan** — **O(VE)**.
- **Ford–Fulkerson can fail to terminate** on some networks (with irrational capacities).
- Variants: Edmonds–Karp (BFS for augmenting paths, O(V·E²)), Dinic's, push-relabel.
- **CI use:** model power flow, water flow, packet flow; identify minimum-cut bottleneck.

### Cascading-failure models
1. **Threshold models** (e.g. Watts) — node fails when fraction of failed neighbours exceeds threshold.
2. **Load-redistribution models** — failed node's load redistributes to neighbours; if any neighbour exceeds capacity it fails too.
3. **Sandpile models** — stochastic failures as a self-organised criticality phenomenon.

### Agent-based models
- Each CI element is an agent with its own state and rules.
- Global behaviour emerges from local interactions.
- Useful when analytic solutions are intractable; weakness = computational cost and validation difficulty.

### Tor as agent-based example
- Tor is the lecture's worked agent-based modelling example.
- Functions: hide which client connects to which host; hide service location / provide alternative paths to blocked services.
- Three-stage flow: **directory of entry nodes (1) → circuit through intermediate nodes (2) → traffic forwarded through circuit (3)**.
- Default circuit: entry/guard, middle, exit (three hops).
- Vulnerabilities: traffic confirmation by global adversary, exit node monitoring, malicious entry guards over time.

### Game-theoretical models (preview of Unit 8)
- Treat defenders and attackers as rational agents with payoffs.
- Note in 5.1: **observations will not be perfect → Stackelberg games are an attractive model**.

## 5.2 Power systems

### Apparent power
**S = VI = P + jQ** (units: VA), where P [W] real, Q [VAR] reactive.

### Structural elements (Q3 2019 directly asks)
- **Generation** — large central (thermal, nuclear, hydro) and increasingly distributed (wind, solar PV).
- **Transmission** — high voltage (≥132 kV in UK), long distance, AC mostly, some HVDC interconnectors.
- **Sub-transmission / distribution** — step-down to industrial/commercial/domestic voltages.
- **Load** — consumers, increasingly with embedded generation/storage (prosumers).

### UK transmission grid voltages (lecture's exact numbers)
- **400 kV** backbone (partly **275 kV**).
- HVDC interconnectors to: **France, Netherlands, Belgium, Norway, Denmark (since Jan 2024), NI, RoI**.
- Plans for: **Germany, Morocco**.
- HVDC also Scotland ↔ England.
- Generators provide **25–33 kV** → step-up transformers.
- Sub-transmission/distribution: **4–33 kV** → distribution transformer → **3-phase 400 V / single-phase 230 V**.
- Other countries commonly use **380 kV+** for transmission.

### HVDC details
- Used for: subsea cables, transmission between unsynchronised AC networks.
- A future Chinese link will transmit **12 GW over 3000 km at 1100 kV (UHVDC)**.
- The 1970s Cahora Bassa HVDC (Mozambique): **1920 MW**.
- The transmission grid is **sparse** — meshed only where critical.

### Frequency and voltage control timescales (Q3 2019)
- **Frequency** must stay near nominal (50 Hz UK, 60 Hz US) — drift indicates active power imbalance.
- **Primary frequency control** — autonomous governor response, **seconds** (droop control, ~30 s).
- **Secondary frequency control / AGC** — system-wide control restoring nominal frequency, **minutes** (~10 minutes).
- **Tertiary control** — economic dispatch, **15 minutes to hours**.
- **Primary reserves** — held by online generators, droop-activated.
- **Secondary reserves** — synchronised but not loaded; activated by AGC.
- **Tertiary reserves** — offline standby.
- **Voltage control** — done locally via reactive power (capacitors, FACTS, generator excitation); not a single global state.

### 9 August 2019 UK outage — exact sequence
1. **Lightning strike** on the **Eaton Socon – Wymondley 400 kV line** (routine fault).
2. **Little Barford CCGT** plant tripped on vector-shift protection: **−150 MW**.
3. **Hornsea 1** offshore wind plant voltage-control system responded unstably: **799 → 62 MW** (de-loading **−737 MW**).
4. **Little Barford** second steam unit tripped: **−244 MW**.
5. Cumulative loss **>1130 MW within <1 second**.
6. Frequency drop tripped distributed generators: another **−350 to −450 MW**.
7. **LFDD (Low Frequency Demand Disconnection)** activated as frequency dropped **below 48.8 Hz**.
8. ESO restored most loads within **45 minutes**.
9. **Govia Thameslink Class 700/717 trains**: frequency-protection systems shut down, drivers couldn't reset.

Highlights: tight coupling, fast cascade (under 90 seconds), reliance on automatic protection schemes.

### Smart grid
- Two-way communication between control centre, substations, and end customers.
- Components: AMI (smart meters), PMUs (Phasor Measurement Units, sub-cycle synchrophasor data), demand-response, microgrid controllers.
- **Attack surface explosion** — millions of internet-attached endpoints, many resource-constrained.

### Power-grid protocols
- **IEC 60870-5-101 / -104** — under-frequency conditions communicated to SCADA. Used by Industroyer.
- **IEC 60870-6 (TASE.2 / ICCP)** — between control centres over WANs.
- **IEC 61850** — substation automation and active management of distribution networks.
- **Active reserve communication is sometimes still by telephone** (the lecture flags this!).

## 5.3 Ukraine attacks

### 2015 — Ukrenergo / Prykarpattyaoblenergo / Kyivoblenergo (23 December)
- **Three regional distribution operators** attacked simultaneously.
- Pre-positioning months earlier via spear-phishing (BlackEnergy 3 — around since **2007**, KillDisk).
- Attackers used legitimate operator credentials, opened breakers via remote HMI, **then wiped systems**, took down call centres with a TDoS, disabled UPS systems.
- **~200,000 customers** without power for ~6 hours.
- Recovery enabled by "rustic" control systems → **manual switchgear operation by phone**.

### 2016 — Kyiv (transmission level, 17 December, 11:53 pm)
- **"Poltava" transmission grid substation at Pivnichna (330 kV)** disconnected.
- Affected parts of Kiev; reconnected at 1:05 am.
- Substation refurbished **2012–2014**, equipped with **ABB, GE, Siemens** equipment using **IEC 61850** and **MicroSCADA Pro 9.3**.
- **Enpaselectro Ltd. had posted detailed designs online** — and security was not a requirement.
- More automated than 2015: **Industroyer / CrashOverride** malware — the first **purpose-built** malware for grid operations.

### Industroyer (Crash Override) — protocols and components
- RAT with C2 + backdoor + ICS attack payloads.
- Payload modules for: **IEC 60870-5-101, IEC 60870-5-104, IEC 61850, OPC DA**.
- Also targeted **ABB MicroSCADA software** to deny operator recovery, then wiped/crashed systems.
- Exploited a vulnerability in **SIPROTEC protective relays** (since patched) that disabled protective breakers — banking on the fact that operators would manually override (as in 2015) and destroy equipment.

### PIPEDREAM / Incontroller (2022)
- Discovered 2022, attributed to **Sandworm**.
- Modular generic ICS toolkit; targets multiple PLC vendors (Schneider, Omron).
- **Capability to target Safety Instrumented Systems (SIS)** even though apparently not exploited.
- First seen used in the **ELECTRUM breach of the Ukrainian power grid in late 2022**.

### October 2025 Polish power grid attack
- Large-scale attack against the Polish grid, attributed to the Russian **FSB** (domestic, not GRU).
- Used **generic wipers** on substations rather than the bespoke Sandworm exploit kit.

### Attribution
- **Sandworm** (GRU Unit 74455 / GTsST) — assessed responsible for BlackEnergy, Industroyer, NotPetya, Olympic Destroyer, Industroyer2.

---

# Part 6 — Unit 6: Cyber-Physical Systems (Q1 + Q4 territory)

## 6.1 Control theory

### Open-loop vs closed-loop
- **Open-loop**: actuator commanded directly; no feedback. Cheap, simple, fragile to disturbance.
- **Closed-loop**: sensor measures output, controller compares to setpoint, drives error to zero. PID is the canonical example.

### State-space view
A system can be written as
- ẋ(t) = A x(t) + B u(t)
- y(t) = C x(t) + D u(t)

where x is state, u is input, y is output.

- **Controllability**: can we drive *x* from any initial state to any desired state in finite time using *u*? Test: rank of [B, AB, A²B, …] = n.
- **Observability**: can the state *x* be reconstructed from measurements *y*? Test: rank of observability matrix [C; CA; CA²; …] = n.
- **Detectability**: weaker condition — unobservable states (modes) must be stable, so non-observable states do not require stabilisation by a control system. Formulation due to **Rudolf Kalman**.

> **Why the exam cares:** to manipulate a CPS an attacker exploits **controllability** of the malicious goal; to detect them defenders need **observability** of the deviation. State-estimation attacks (Q1 2019) exploit gaps in observability.

### Process control vocabulary (memorise — exam-relevant for Q1)
- **MV (Manipulated Variable)**: parameter (usually a resource) being manipulated by the control system.
- **FCE (Final Control Element)**: component changing the value of the MV (e.g. valve, motor).
- **Controller Output**: signal from controller to FCE.
- **PV (Process Variable)**: measurement responding to changes in MV.
- **Set Point (SP)**: value at which the PV is to be maintained.
- **Transmitter**: transducer responding to MV via a sensing element, converting to a standardised transmission signal.
- **Sensor**: may or may not be part of the transmitter.
- **Controller**: device that operates automatically to regulate a controlled variable.

The lecture's one-line definition of process control:
> "The act of controlling Final Control Elements (FCEs) to change Manipulated Variables (MVs) so as to maintain Process Variables (PVs) at desired Set Points."

### LTI (Linear Time-Invariant)
The most benign system class. Time-varying systems have parameters changing over time and must be modelled explicitly.

### Non-linear-system properties
- May have **multiple, isolated equilibrium points** (linear systems have only one).
- Can have **finite escape time** if unstable.
- Periodic input → output may contain harmonics and sub-harmonics with different amplitudes/phases.

## 6.2 ICS components

| Component | Role |
|---|---|
| **PLC** (Programmable Logic Controller) | Real-time controller in the field (ladder logic, structured text). |
| **RTU** (Remote Terminal Unit) | Like PLC but for remote/telemetry use; often serial-link first. |
| **HMI** (Human-Machine Interface) | Operator's view; usually Windows; primary lateral-movement target. |
| **SCADA server** | Aggregates HMIs, historians, alarms; often runs on commercial OS. |
| **Historian** | Time-series database of process variables. |
| **Engineering Workstation** | Where PLC programs are developed and downloaded. Primary supply-chain target. |
| **DCS** (Distributed Control System) | Functionally similar but designed for tightly integrated process control (refineries, power plants); blurred with SCADA over time. |
| **Safety Instrumented System (SIS)** | Independent safety layer (Triton case study). |

### IEC 61131 / IEC 61499
- **IEC 61131** — standard for PLC programming languages (Ladder Logic, Structured Text, etc.).
- **IEC 61499** — function blocks for distributed control, layered on top of IEC 61131.

### Real-time classification
- **Hard real-time** — missing a deadline = system failure (e.g. centrifuge speed control). Determinism is critical.
- **Firm real-time** — occasional misses tolerable but result discarded.
- **Soft real-time** — degraded QoS, no catastrophe (e.g. video).
- **RTOS** (Real-Time Operating System) provides bounded scheduling latency and priority inheritance to support hard real-time tasks.

## 6.3 Attacks on sensors and actuators

### Sensor attacks
- **Replay** — record valid signal, play back during sabotage (Stuxnet's "lying to operator" → Loss of View).
- **Bias / scaling** — small, sustained offset; harder to detect than spike.
- **Spoofing** — synthetic signal indistinguishable from real (GPS spoofing of PMUs).
- **Jamming / DoS** — physical layer.

### Actuator attacks
- **Direct command injection** — overwrite setpoint.
- **Disable safety interlock**.
- **Force resonance** — drive a mechanical system at its natural frequency to damage it.

### Concepts
- **Accuracy** = closeness to true value (systematic).
- **Precision** = repeatability (random).
- An attacker may degrade either independently — biased sensor remains precise; jammed sensor loses precision.

### Loss of View / Loss of Control
- **Loss of View**: operator sees stale or false data; cannot detect the attack.
- **Loss of Control**: operator's commands no longer have intended effect.
- Stuxnet engineered both simultaneously — replay of pre-recorded normal sensor data while malicious commands ran.

### De-synchronisation
- Many ICS protocols rely on **time** (sequence numbers, timestamps).
- Force clock drift between controller and field device → reordered or stale commands accepted as valid.

### Davis-Besse incident (2003)
- Slammer worm reached the safety parameter display system at Davis-Besse nuclear plant via a contractor's network.
- Took the SPDS offline for ~5 hours.
- Plant was already offline so no safety consequence.
- Illustrates: insecure perimeter, contractor network bypassing firewall, no segmentation.

## 6.4–6.6 Stuxnet — the canonical CPS attack

### Strategic context
- Operation **Olympic Games**, joint US/Israel programme.
- Target: Natanz Fuel Enrichment Plant (FEP), Iran.
- Goal: degrade IR-1 centrifuge cascades while maintaining plausible deniability and stealth.

### Stuxnet timeline — exact dates
- **2003**: Some Equation Group malware components date back to here.
- **August 2007**: Malware components later used in Stuxnet and Duqu identified ("**Tilded**" platform).
- **June 2009**: earliest public detection of Stuxnet. Does **not** yet use MS10-046 LNK exploit; not yet signed.
- **June 2009**: Initial infection — **Foolad Technic Engineering Co.**, Isfahan.
- **January 2010**: Stuxnet uses signed driver with valid **Realtek Semiconductors** certificate.
- **April 2010**: Second infection wave — **Foolad Technic** + **Behpajooh Co. Elec. & Comp. Engineering** (most likely source of worldwide propagation), and Iran companies on UN sanctions list.
- **June 2010**: Belarusian firm **VirusBlokAda** first reports W32.Stuxnet (already using MS10-046). **VeriSign revokes the Realtek certificate.**
- **July 2010**: New Stuxnet variant uses **JMicron Technology** certificate.
- **August 2010**: Microsoft issues **MS10-046** (LNK shortcut).
- **September 2010**: Microsoft issues **MS10-061** (printer spooler).
- **September 2010**: Iran admits production issues at nuclear fuel separation centrifuges.

### IR-1 origin
- Iranian variant of an **Urenco** design **stolen by A.Q. Khan in 2004**.
- Developer believed to be **Neda Industrial Group**.

### Target system specifics
- **IR-1 centrifuges** — Pakistani/European-derived 1960s design with two known weaknesses:
  1. Rotor drives can be over-speeded past critical resonance frequencies.
  2. Isolation/exhaust valves can be manipulated to create cascade overpressure.
- **Siemens S7-315** PLC controlled the **rotor drive** system.
- **Siemens S7-417** PLC controlled the **cascade protection system** (valves, pressure controllers PR-4000 with 21 per cascade, 15 controlling exhaust).

### Centrifuge speeds (memorise)
- IR-1 normal operating speed: **63,000 rpm**.
- Attack: accelerate to **84,600 rpm for 15 minutes** (causing severe loads).
- Then spin down rapidly to **120 rpm** — exerting torque and likely causing severe vibrations and cracked rotors.
- Pressure imbalances result if many centrifuges fail.

### Frequency converter targets
- Targeted converters operate at **807–1210 Hz** (export-controlled band; >600 Hz are licensed exports owing to uranium-enrichment use).
- Stuxnet drives them to: **1410 Hz, 2 Hz, 1064 Hz**.
- The **CP342 PROFIBUS module** intermediates between **frequency converter drives** and the **6ES7-315 PLC**.
- Attack monitored "normal" data for **13 and 27-day periods** before launching.
- Implements a state machine to play back legitimate readings to HMI while issuing malicious commands; alerts/fail-safes suppressed.
- Attack waits **hours to months** between iterations to mimic "natural" failures.

### Cascade overpressure attack
- Targets the **S7-417** controller (cascade protection system).
- **PR-4000** pressure controllers — **21 per cascade**, **15 of which control exhaust valves**.
- PR-4000 has no PROFIBUS interface → translation bridge required.
- Attacker blocks isolation valves at certain enrichment stages → overpressure in non-isolated centrifuges.
- Pressure sensors are calibrated and report **sanitised readings** — operator sees normal pressure throughout the attack.

### Stuxnet propagation vectors (the full list)
- **MS10-046** — LNK shortcut auto-execute on USB / network share. The "0-click" element.
- **MS08-067** — NetAPI buffer overflow (reused Conficker vector); a more sophisticated **ROP** variant.
- **MS10-061** — printer spooler. Attack: connect to spooler as guest, print two malicious files into `%SYSTEM%`. First file `winsta.exe` = Stuxnet mothership DLL; second file `wbem\mof\sysnullevnt.mof` registers an event.
- **MS10-092** — task-scheduler privilege escalation on Vista+. Task XML had weak checksum, allowing escalation to SYSTEM.
- **Network-share propagation**: searched for accessible shares (`$ADMIN`, `$C$`, same-user-account hosts), uploaded mothership DLL, scheduled DLL execution.
- **WinCC** payload via hard-coded SQL credential **`Administrator : 2WSXcder`** — the database password (a keyboard zigzag pattern). Payload inserted via `INSERT INTO sysbinlog VALUES (0x...)` and persisted via OLE Automation Stored Procedures (`sp_addextendedproc sp_dumpdbilog`).
- Two stolen code-signing certs: **Realtek** and **JMicron Technology**.

### Stuxnet stealth and operational details
- Injects into trusted processes: **LSASS, WinLogon, SvcHost**, plus AV vendors (**Kaspersky, McAfee, eTrust, Symantec, ESET, F-Secure, AntiVir**).
- Uses **`LoadLibrary` with non-existent file name + NTDLL.DLL hooking** to map files to in-memory locations (counter-IDS).
- C2 via legitimate-looking domains: **www.mypremierfutbol.com** and **www.todaysfutbol.com**.
- Sends basic info (IP, time, host) to C2 over RPC.
- **Filter driver** for files matching Stuxnet payload characteristics → AV/user processes can't see them on removable media.
- USB payload: file **`~WTR4132.tmp`** plus several LNK shortcuts (for different Windows versions).
- Bridged the **air gap** via infected engineer USB sticks and project-file infection at integrators.
- Cryptographic signing of payloads.
- Validation of target environment (it only fired if the specific cascade configuration was matched — otherwise stayed dormant).
- Self-removal beyond a certain date.

### Stuxnet payload size
**~500 kB** (vs Flame's **20 MB**).

### Significance
- First malware to cause **physical destruction** via cyber means.
- First confirmed nation-state CPS attack.
- Established the playbook for Industroyer, Triton, etc.
- Demonstrated the *gap between IT security and OT engineering knowledge* — defenders needed both nuclear engineering and Windows IR expertise.

### The Stuxnet family — the Equation Group context
The lecture connects Stuxnet to a longer programme dating back to **2003**:
- **GrayFish** — bootkit.
- **EquationDrug** — persistent storage on **hard-disk firmware**.
- **Fanny** — mapping tool.
- **Duqu** — espionage variant (2011), shared "Tilded" framework.
- **Flame** — espionage in Middle East (2012). 20 MB. Communications over SSH and TLS, network/keyboard sniffing, document harvesting. Most original feature: **forged certificate for Microsoft and a fake Microsoft Update server** (used MD5 collision).
- **Gauss** — banking/credential theft, Lebanese targets (2012).
- **C2 infrastructure**: 300+ domains, 100+ servers, typically Linux **LAMP**, fraudulent DNS registrations.

---

# Part 7 — Unit 7: Control Systems Security (Q1 territory — direct hits)

## 7.1 Generations of SCADA

The lecture's split (1st/2nd/3rd, not 4):
- **First Generation**: analog POTS (dial-up/leased), radio, microwave for sensor alerts. Limited interaction.
- **Second Generation**: still mostly POTS + microwave for WAN; leased lines guaranteed response times.
- **Third Generation**: Ethernet, IP, open standards. **Cyber security risks arise mainly here.**

Each generation widens the attack surface; legacy components from earlier generations persist.

### DCS hierarchical layering
The lecture frames DCS specifically as a **hierarchy** of layers:
- Bottom: sensors/actuators on **fieldbus** (non-routable specialised protocols).
- Fieldbus → **local PLC / RTU** for fast guaranteed cycle times.
- Intermediate nodes can be **fieldbus aggregators** or **SCADA servers**.
- Higher-level units cannot guarantee timing because of communication delays.

## 7.2 ICS protocols (Q1 directly tests)

### Modbus — the precise variants
- Dating from 1979.
- **Modbus RTU**: serial, binary representation.
- **Modbus ASCII**: serial, ASCII encoding.
- **Modbus TCP**: TCP encapsulation, **port 502**, with a **Transaction Identifier** (because IP may give out-of-order messages).
- All variants: only the master initiates; **no explicit identification/authentication**; non-cryptographic checksums; no confidentiality.
- **Function codes** (e.g. 03 = read holding register, 06 = write single register, 16 = write multiple).
- **Unit Identifier** retained even in Modbus TCP for gateway addressing.
- **Attacks (Q1 2019 explicitly asks):**
  - **Read** any holding/input register from any device on the bus → reconnaissance, IP theft.
  - **Write** to coils/registers → direct command injection (turn pump on/off, change setpoint).
  - **Replay** captured commands.
  - **Spoof** unit ID — slaves only addressed by ID, no source authentication.
  - **MitM** — full process visibility and manipulation.
  - **DoS** — flood with malformed packets.
- **Mitigations** — VPN/IPsec gateway, Modbus/TLS, strict allow-listing of function codes by deep-packet-inspection firewall, network segmentation.

### DNP3 (Distributed Network Protocol)
- Common in North American electric utilities.
- **DNP3 Secure Authentication (SA, IEC 62351-5)** — adds challenge-response authentication using HMAC over critical operations; v5 with key change.
- Still optional in many deployments.

### IEC 60870-5
- European telecontrol standard.
- **5-101** serial, **5-104** TCP/IP variant. Same data model.
- Used heavily for substation–control-centre communication; **targeted by Industroyer**.

### IEC 60870-6 / TASE.2 / ICCP family lineage
- **ELCOM-90 → TASE.1 (Telecontrol Application Service Element-1) → TASE.2 (current ICCP)**.
- Native security added later via **ISO/IEC 62351**.
- TLS-based security defined but historically patchily deployed.

### IEC 61850
- Substation automation; runs over Ethernet with **GOOSE** (Generic Object-Oriented Substation Event, multicast, sub-millisecond latency requirement) and **SV** (Sampled Values).
- GOOSE has integrity hooks (IEC 62351-6) but the latency budget makes asymmetric crypto challenging.

### OPC origin and security
- Originally: **Object Linking and Embedding (OLE) for Process Control**, mid-1990s, based on Microsoft **COM/DCOM**.
- Renamed **2011**: **Open Platform Communications**.
- Three core services: **OPC Data Access (DA)** (real-time data), **OPC Historical Data Access**, **OPC Alarms and Events** (asynchronous notifications).
- DCOM model is "complex, difficult to configure, proprietary to Microsoft only, with very limited control over time-outs". Microsoft itself deprecated COM/DCOM.
- **OPC XML-DA** (from 2003): XML-based encoding to escape DCOM, but still .NET-dependent.
- **OPC UA (IEC 62541)** — from **2006** onwards (current 2011):
  - Two protocol bindings: **binary** (no XML/SOAP) or **web services** (XML/SOAP).
  - **UA Security**: identification, authentication, access control, confidentiality, integrity.
  - Uses **public-key cryptography (X.509 certificates)** and **digital signatures**.
  - Can use existing **Active Directory PKI**.
  - Binary variant uses **WS-SecureConversation**-like primitives transformed to binary form.

### OPC UA risks (the 2019 question targets this directly)
- **Certificate management failures** — self-signed certs, no rotation, no revocation checking → attacker can MitM with a stolen/forged cert.
- **Insecure default profiles** — many vendors ship with `None` security mode enabled by default; first-time setup leaves it weak.
- **Web-facing endpoints** — once OPC UA traverses an organisational boundary as a SOAP/HTTPS-style service, all the usual web-app risks apply (TLS misconfig, server-side bugs, server-side request forgery against internal endpoints).
- **Discovery endpoint exposes information** about the address space — useful reconnaissance.
- **Increased attack surface** — running on a general-purpose OS; vulnerable to OS-level CVEs.
- **Trust delegation problems** — when bridging between OPC UA Classic and UA, certificates and authorisation can be lost.
- **OPC is not strictly deterministic real-time.**
- "Provides rather greater attack surfaces based on the complexity of the implementation" — number of protocols, dependencies, lines of code; OS service dependencies hard to validate.

Strong protocol but **only as secure as the deployment**.

## 7.3 State estimation (Q1 2019 — examined)

### What it is
- Power-grid control centres receive redundant noisy measurements (voltages, currents, real/reactive power) and infer the **state vector** (bus voltage magnitudes and angles) from them.
- Used as the input to contingency analysis, optimal power flow, alarming.

### State-estimation applications (the lecture's full list)
- **Network firewall / IDS** — discrete state estimation (inferring protocol-session state without seeing all messages).
- **GPS navigation** — at start-up, current location is estimated until satellite signal acquired.
- **Power networks** — adjust generation/load instantly.
- **Cyber-physical systems / digital twins** — jet engines, manufacturing.

### Advanced control applications using state estimation
- **Feedforward Control** — anticipates estimated disturbances.
- **Cascade Control** — nested control loops on estimated states.
- **LQ Optimal Control** — Linear Quadratic optimal feedback on estimated states.
- **MPC (Model-Based Predictive Control)** — predicts future behaviour, optimises control.

### Estimator types
- **Linear estimator** — linearised measurement model *z* = *Hx* + *e*; solved by **Weighted Least Squares**.
- **Non-linear estimator** — *z* = *h(x)* + *e*; iterative Newton or Gauss–Newton.
- **Kalman filter** — recursive estimation under Gaussian noise and linear dynamics; minimum-mean-square optimal.
- **Extended / Unscented Kalman Filter** — for non-linear dynamics.

### Bad-data detection
- Standard test: chi-squared on the measurement residual *r* = *z* − *Hx̂*.
- Largest-Normalised-Residual (LNR) test: identify the most suspect measurement.

### False-data injection attacks (Liu, Ning, Reiter CCS 2009)
- Attacker injects *a* = *Hc* (a vector lying in the column space of *H*) → estimator produces *x̂'* = *x̂* + *c*, residual unchanged.
- **Bypasses chi-squared test entirely** — *unobservable attack*.
- Requires:
  - Knowledge of the topology and *H* (often obtainable via leaked documentation or recon).
  - Control of enough measurements to construct *Hc*.
- **Defences:**
  - Protect a strategic subset of measurements with cryptographic integrity (PMU data with authenticated time-stamps).
  - Topology obfuscation / random measurement selection.
  - Anomaly detection on system dynamics rather than instantaneous measurements.
  - Cross-checking with PMU-based **dynamic state estimation**.

### PMU and GPS issues
- PMUs rely on **GPS time** for synchrophasor reference.
- GPS is accurate to **14 ns** (practically 100 ns).
- **Differential GPS** can reduce errors further.
- Civilian GPS: **no authentication or integrity protection** — easily replaced with time-shifted copies (destroys time-of-arrival ranging) or arbitrary messages.
- Lecture cites **Tippenhauer et al., CCS 2011** ("On the Requirements for Successful GPS Spoofing Attacks").
- Defences: multi-constellation receivers (Galileo/GLONASS/BeiDou), spoof detection (signal direction-of-arrival), holdover oscillators.

## 7.4 Intrusion Detection in Control Systems

### Three families
1. **Signature-based** — pattern matching against known malicious payloads. Strong precision, weak recall on novel attacks.
2. **Anomaly-based** — model normal behaviour, flag deviations. Detects novel attacks but suffers from false-positive overload.
3. **Specification-based** — formal model of *intended* behaviour (protocol grammar, process bounds). High precision and good recall *if* the specification is accurate.

### IDS toolset names
- **Snort** with SCADA-specific rules and preprocessors (Modbus, DNP3).
- **Quickdraw** rule sets — adopted by other IDS/IPS vendors; filter scanning, read/write request commands.

### Triton / Trisis (2017)
- Targeted **Triconex Safety Instrumented Systems** at a Saudi petrochemical plant.
- Malware uploaded malicious logic to the SIS PLC.
- Triggered an unintended SIS shutdown — but this *exposed* the attack.
- First malware specifically targeting safety systems.
- **Russian-attributed** (the lecture's phrasing).
- Same actor active in **2019**, scoping target system for **>1 year**.
- Sandworm's PIPEDREAM (2022) extends this trend.

---

# Part 8 — Unit 8: Attack & Adversary Modelling (Q5 territory)

## 8.1 Static models

### Risk = Probability × Loss
The lecture's quantitative formulation: **Risk = (Probability of Incident) × (Expected Loss)** — but "establishing these parameters is not always possible to a satisfactory level".

### Failure Mode and Effects Analysis (FMEA)
- Structured tabular technique: list each component, its failure modes, effects on the system, severity, occurrence likelihood, detectability.
- **Risk Priority Number (RPN)** = Severity × Occurrence × Detection.
- Workflow: Identify failure modes → Identify effects → Determine severity → Determine detectability → Determine risk priority → Identify required actions.
- Strength: comprehensive coverage of single-component failures. Weakness: poor at combinatorial / dependent failures.

### Fault Tree Analysis (FTA)
- Top-down: start from undesired event (top), decompose into intermediate events using **AND** and **OR** gates terminating in basic events.
- Quantitative: compute probability of top event from basic-event probabilities.
- **Cut set** — minimal set of basic events whose simultaneous occurrence causes the top event. **Minimal cut sets** describe failure modes; smallest cut set = weakest path.

### Attack Trees (Schneier, 1999)
- Same tree structure, but root = attacker goal, leaves = atomic attack steps.
- AND/OR gates as in FTA. Some variants add SEQUENCE / PRIORITY-AND.

### Quantitative Attack Trees (Mauw & Oostdijk 2005 — assessable)
Annotate each leaf with a value (cost, time, probability, skill required).

For the **probability of compromise**:

**AND gate** with k independent basic events:

  **Q₀(t) = Π_{j=1..k} qⱼ(t)**

**OR gate** with k independent basic events:

  **Q₀(t) = 1 − Π_{j=1..k} (1 − qⱼ(t))**

For two events under an OR gate:

  **Q₀(t) = q₁(t) + q₂(t) − q₁(t)·q₂(t)**

These are derived from **Pr(E₁ ∪ E₂) = Pr(E₁) + Pr(E₂) − Pr(E₁ ∩ E₂)** and the independence assumption.

For costs, AND gates **sum** child costs, OR gates take the **min**.

For attack time, AND gates **sum** (sequential) or **max** (parallel), OR gates take **min**.

> **Q5 2019 explicitly tests this**: build an attack tree for a stated goal, and use it to allocate defensive resources — defender should target leaves that participate in the *minimal cut sets* with the lowest attacker-cost paths.

## 8.2 Dynamic models

### Attack-Defence Trees (Kordy et al., 2010 — assessable)
- Bipartite extension of attack trees: each attack node may have **defence nodes** that *negate* it; each defence may itself be defeated by counter-attacks.
- Two roles alternate at each level: attacker and defender.
- Useful semantics:
  - **Propositional** — Boolean satisfiability of goal vs defences.
  - **Quantitative** — multi-attribute domains (cost, probability, time) with additional operators for counter-action.

> **Q5 2019 part (10 marks):** *"executive's laptop is sole copy of trade secret; construct an AD-tree."* A skeletal answer:
>
> **Goal (attacker):** Steal the trade secret from the laptop.
>
> **Top-level OR:**
> 1. Physical theft of laptop
>    - **AND:** access the office, defeat physical lock, exit unobserved
>    - *Defence:* CCTV, badge access, security guard
>    - *Counter:* badge cloning, social engineering, after-hours
> 2. Remote compromise
>    - **OR:** spear-phish the executive; compromise corporate network and pivot; supply-chain compromise of laptop OS
>    - *Defence:* MFA, endpoint AV/EDR, security training
>    - *Counter:* MFA fatigue, 0-day, training-evasion
> 3. Insider compromise
>    - **AND:** identify staff with access; coerce/bribe; exfiltrate
>    - *Defence:* DLP, vetting, separation-of-duty
> 4. Cryptanalysis (if encrypted)
>    - *Defence:* full-disk encryption (BitLocker/FileVault); strong passphrase; TPM-bound key
>    - *Counter:* cold-boot attack, evil-maid, weak passphrase
>
> Mark each leaf with cost/probability; identify the cheapest paths the defender should harden first.

### Attack Countermeasure Trees (Roy, Kim, Trivedi 2012 — full formulas)
- ACTs explicitly model **detection events**, **attack events**, and **mitigation events** as alternating sub-trees.

For a leaf attack with no countermeasure:

  **P_goal = P_A**

For an attack with a single detection countermeasure (logical NAND):

  **P_goal = P_A · (1 − P_D)**

For an attack with multiple detection countermeasures, all of which must fail:

  **P_goal = P_A · (1 − P_D₁)·(1 − P_D₂)·…·(1 − P_Dₙ)**

For an attack with a detection AND a separate mitigation step:

  **P_goal = P_A · (1 − P_D + P_D · (1 − P_M)) = P_A · (1 − P_D · P_M)**

Useful derived probabilities:

  **P_UD = P_A · (1 − P_D)**         (undetected attack)

  **P_DUM = P_A · P_D · (1 − P_M)**  (detected but unmitigated)

## 8.3 Game theory for security

### Normal-form / static games
- Players, strategies, payoff matrices.
- **Prisoner's dilemma** — illustrates how individually rational play can be collectively suboptimal. Relevance: information sharing among CI operators.
- **Hawks vs Doves** — two players competing for resource of value V at cost of losing C, with C > V. Hawk = confrontational; Dove = conciliatory. Pure-strategy equilibria depend on whether C > V; mixed strategies often arise. Relevance: adversary populations co-evolving with defences.
- **Pure strategy** — single deterministic action.
- **Mixed strategy** — probability distribution over actions.

### Nash equilibrium
- No player can improve by unilateral deviation. Every finite game has an NE in mixed strategies (Nash 1950).
- Multiple NE possible — exponentially many for many-player games.
- Computing NE: **simple for 2-player zero-sum**; for **2-player general-sum, maximising sum of rewards is NP-complete**; many-player → exponential.
- Players assumed **rational** and **selfish**, always choosing the move with the best expected outcome.

### Stackelberg games
- Leader commits to a (possibly mixed) strategy publicly; follower optimally responds.
- In **security games**, defender is naturally the leader (deploys patrols, IDS configuration); attacker observes and best-responds.
- Equilibrium concept: **Strong Stackelberg Equilibrium (SSE)** — usually unique up to ties.
- Used in real-world systems (airport patrols, transit security).
- Cite: **Korzhyk, Yin, Kiekintveld, Conitzer, Tambe (2011)** — *Stackelberg vs. Nash in Security Games* (JAIR Vol. 41, pp. 297–327).

### FlipIt (van Dijk, Juels, Oprea, Rivest — GameSec 2012)
- Two-player game over control of a shared resource.
- Continuous-time: players can move at any time, not just in rounds.
- Each move incurs a cost.
- Payoff = total time controlled − total move costs.
- Useful for: **how often to refresh keys, re-image, reset** under stealthy adversaries.
- Models stealthy long-running campaigns where defender doesn't know if attacker is currently in control.

> **Q5 2019 explicitly compares game theory to AD-trees.** Key contrasts to deploy:
> - AD-trees are **structural**; games are **strategic**.
> - AD-trees give **point estimates**; games yield **equilibrium strategies** under multi-round play.
> - AD-trees assume static defence; games model **incomplete information** and **mixed strategies**.
> - Games extend naturally to **adaptive adversaries**; AD-trees less so.
> - Computational complexity of game equilibria can be high (especially Bayesian games); AD-trees scale better.

## 8.4 Petri nets (concurrency in adversary models)

### Formal mechanics
- **Bipartite graph**: two node types — **Places** (resources / partial state) and **Transitions** (events / synchronisations). Edges connect different node types only.
- **Tokens** mark places — any finite number per place.
- A transition **t is enabled** for a marking iff for every edge from place p to t there is a distinct token in p's marking.
- An enabled transition can **fire** (atomic operation):
  - One token removed from each place p with edge p → t.
  - One token added to each place p with edge t → p.
- Place capacities can be transformed away by adding extra places.

### Capabilities Petri nets capture (that AD-trees / FTA cannot)
- **Concurrency** — multiple tokens enable parallel execution.
- **Synchronisation** — transitions waiting on multiple input places.
- **Conflict** — multiple transitions sharing input places.
- **Non-determinism** — choice of which enabled transition fires.

### Extensions (memorise the names)
- **Hierarchical Petri Nets** — decompose complex networks into assemblies; hide lower-level detail (good for decision support).
- **Coloured Petri Nets (CPN)** — tokens carry **values** (not just identical markers) → far more compact; tool support: **CPN Tools**.
- **Timed Petri Nets** — explicit time, where ordering and concurrency are insufficient.

### Modelling attacks with Petri nets
- Attacker actions = transitions.
- Security states = places.
- Concurrent attackers / parallel attack stages → multiple tokens.
- Sequencing: extra transitions to enforce pre-conditions.

### Trade-off vs Attack Trees
Petri nets capture more but require deeper system understanding: "more so than in the case of e.g. the Attack Tree (or Attack-Defence Tree etc.) methodology, this results in attacks and adversaries being modelled at different abstraction levels". They are **both visual and computationally tractable** (allowing tool support).

### Lecture reference
**Chen, Sanchez-Aarnoutse, Buford (2011)** — *Petri Net Modeling of Cyber-Physical Attacks on Smart Grid*, IEEE Trans. Smart Grid Vol. 2, No. 4, pp. 741–749.

## 8.5 Critique of best-practice lists (Q5 2019 starts here)

The 2019 paper opens Q5 with: *"The SANS Top 20 Critical Security Controls represent a useful starting point for defensive resource allocation. Critique this approach in the context of sophisticated, persistent adversaries."*

Key arguments to deploy:
- **Best-practice lists are population-level recommendations**; the median organisation benefits but a sophisticated adversary will simply identify which controls are *not* on the list and target those.
- **Static rankings ignore adversary adaptivity** — Stackelberg / FlipIt analysis shows the optimal defender should **mix** strategies, not commit to a deterministic top-N.
- **Cost-benefit is contextual** — control 11 at one site may be control 1 at another.
- **Lists do not encode dependencies** — controls interact (segmentation only works if asset inventory is accurate; backups only work if integrity is verified).
- **Compliance-driven implementation is shallow** — a tick-box approach generates reports without adversarial value.
- **Lists do not address persistence** — after years of pre-positioning, the SANS Top 20 doesn't help you find an adversary already inside.
- **Better: model-based defence allocation** — attack-tree cut-sets indicate which leaves to harden; game-theoretic analysis indicates how to randomise; AD-trees show where counters break attack paths.
- **However:** lists are not worthless — they raise the cost floor for opportunistic attackers, give boards a comprehensible artefact, and provide a baseline against which model-based investments can be benchmarked.

---

# Part 9 — Worked answers using the 2019 paper

## Q1 — Control Systems Security

### (a) Modbus TCP attacks an internal attacker can mount.
- *Read* registers/coils — full process visibility, IP theft, reconnaissance.
- *Write* registers/coils — direct command injection (start pumps, change setpoints, open breakers).
- *Replay* captured legitimate commands — defeats one-time defensive measures.
- *Spoof* unit IDs (no authentication) — masquerade as different RTUs/PLCs.
- *MitM* — modify in flight (no integrity).
- *DoS* — malformed packets, function-code abuse (e.g. force-listen-only mode).
- Cite **lack of authentication, encryption, integrity** as the root cause; mention TCP/502.

### (b) Discuss the security risks of OPC UA exposed as a web service.
- OPC UA *inherently* has good security primitives (mutual cert auth, signing, encryption) **but**:
- Web-facing exposure inherits all standard web-app risks: TLS misconfig, certificate management failures, server-side request forgery, OS-level CVEs.
- Discovery endpoints leak the address space.
- Default profiles often ship with **None** security mode → must be explicitly hardened.
- Bridging UA Classic ↔ UA may break trust delegation.
- Patching is operationally hard — control systems have minimal maintenance windows.
- Recommended: keep OPC UA inside the OT zone, expose only via brokered gateways with explicit policy enforcement; never publish to the public Internet.

### (c) Bad-data injection on state estimation.
- Power-grid state estimator solves *z* = *Hx* + *e* via WLS; chi-squared on residuals detects bad data.
- An attacker injecting *a* = *Hc* leaves the residual unchanged → *unobservable* false-data injection.
- Requires knowledge of *H* (topology, line parameters) and ability to corrupt enough measurements.
- Consequences: erroneous contingency analysis, mis-dispatch, potentially load-shedding errors.
- Mitigations: protect a measurement subset cryptographically (PMUs with authenticated time), topology obfuscation, dynamic estimation cross-checks.
- Cite Liu, Ning, Reiter (CCS 2009).

## Q2 — CI Interdependencies

### (a) Disconnect an undirected graph.
- Find a **vertex cut** of size 1 (articulation point) or in general the minimum vertex cut — Menger's theorem links this to vertex-independent paths.
- For an attacker, the question reduces to a min-cut computation; Ford–Fulkerson on the unit-capacity dual gives an algorithm.
- Discuss connectivity (k-connectivity) as a measure of resistance.

### (b) Large diameter, small average eccentricity.
- Such graphs have a dense core and a long thin periphery.
- Removing core nodes drastically increases average eccentricity; removing periphery has little effect.
- Implication: defender hardens the central nodes; attacker attempting *fragmentation* should also target central nodes (the betweenness-rich ones).

### (c) Flow problems for CI.
- Model as capacitated network; Ford–Fulkerson computes max flow; min-cut tells you the bottleneck.
- Used for power-flow capacity, telecoms throughput, and **interdependency cascade** analysis.
- Cascading failures naturally extend the model: when an edge saturates, redistribute load; if alternative capacity insufficient, edges fail iteratively.

### (d) Defending against cascading attacks.
- Increase k-edge-connectivity / k-vertex-connectivity.
- Add capacity buffers (no edge >70% utilised under N-1 contingency).
- Geographic and policy diversity.
- Real-time monitoring + automatic islanding to limit cascade reach.

## Q3 — Network Robustness

### (a) Power network elements + control timescales.
Generation / transmission / distribution / load. Frequency control: primary (seconds), secondary/AGC (minutes), tertiary (15 min – hours). Voltage: local reactive control. Reserves classification (primary/secondary/tertiary).

### (b) BGP prefix hijacking.
Origin hijack, more-specific hijack, AS_PATH manipulation, route leaks. Famous incidents (Pakistan/YouTube 2008, China Telecom 2010 with 37,000 prefixes for 15 minutes, Amazon Route 53 2018). Effects: blackholing, traffic interception, MITM for HTTP/DNS abuse.

### (c) RPKI and BGPsec defences.
- RPKI validates **origin** only (RFC 6480, 2011); deployed at moderate scale; ROV in many networks.
- BGPsec validates the **path** but heavy crypto cost, not deployed; **BGPsec islands** mean partial deployment leaves attack surface.
- Therefore RPKI alone does not solve path-manipulation attacks; BGPsec needed but unfeasible at scale today.
- Complementary measures: AS_PATH filtering, max-prefix limits, MANRS, real-time monitoring.

### (d) Route diversity via vertex-independent paths + policy.
- Topological vertex diversity is necessary but not sufficient — Gao-Rexford and customer-provider relationships funnel traffic through specific ASes regardless of physical alternatives.
- True diversity requires **policy-aware path selection** and explicitly multi-provider arrangements; otherwise nominal diversity collapses to a single effective path.

## Q4 — Cyber Security Analysis

### (a) APT definition and characteristics.
Quote NIST SP 800-39 verbatim. List characteristics: targeted, multi-stage, custom malware, long dwell, zero-days when needed, OPSEC.

### (b) Cyber kill chain.
Recite the seven stages with one-line description each. Emphasise that defenders only need to break one link.

### (c) Supply-chain attacks.
Examples: SolarWinds (Sunburst — `SolarWinds123`, ~18,000 sites, March-Dec 2020), RSA SecurID → Lockheed (CVE-2011-0609, Poison Ivy), xz-utils, OPM (USIS, KeyPoint). Why effective: trust transitivity.

### (d) Attribution problems.
False flags, infrastructure rotation, code reuse, time-zone unreliability, plausible-deniability deniable assets. Attribution is probabilistic, evidential, multi-source. Cite Longhorn/Lamberts cultural code-words, October 2020 GRU 74455 indictment.

### (e) False-flag operations.
Discuss Olympic Destroyer (planted Korean code), Cyrillic comments in unrelated campaigns, recycled tooling. Defenders should weight TTP / victimology evidence over single artefacts.

## Q5 — Attack & Adversary Modelling

### (a) Critique of SANS Top 20 / best-practice lists for sophisticated adversaries.
See Section 8.5.

### (b) Attack trees for resource allocation / minimal cut sets.
- Build tree, annotate leaves with cost/probability/time.
- Compute minimal cut sets — smallest sets of leaves whose joint protection breaks the attack.
- Allocate budget across the cheapest cut sets first; iterate.

### (c) AD-tree for executive's laptop.
See Section 8.2 worked example.

### (d) Game theory vs AD-trees.
- Multi-round dynamics (FlipIt — GameSec 2012, continuous time, optimal refresh cadence).
- Mixed strategies (don't always patrol the same point).
- Incomplete information (Bayesian games for unknown adversary type).
- Stackelberg framing for defender as leader (cite Korzhyk et al. 2011).
- Trade-offs: AD-trees are decomposable, game models are strategically richer but computationally heavier.

---

# Part 10 — Quick-fire facts cheat sheet

| Item | Answer |
|---|---|
| Mirai default credentials | `root:xc3511` |
| WinCC default password | `2WSXcder` |
| SolarWinds password discovered on repo | `SolarWinds123` |
| Stuxnet C2 domains | mypremierfutbol.com, todaysfutbol.com |
| Stuxnet USB exploit | MS10-046 (LNK) |
| Stuxnet RPC exploit (Conficker reuse) | MS08-067 |
| Stuxnet print spooler exploit | MS10-061 |
| Stuxnet task scheduler exploit | MS10-092 |
| RSA SecurID exploit (Flash) | CVE-2011-0609 |
| Aurora IE exploit | CVE-2010-0249 |
| NotPetya exploits | CVE-2017-0144, CVE-2017-0145 |
| EternalBlue MS bulletin | MS17-010 |
| IR-1 normal speed | 63,000 rpm |
| IR-1 Stuxnet over-speed | 84,600 rpm for 15 min |
| IR-1 Stuxnet under-speed | 120 rpm |
| Frequency converter target band | 807–1210 Hz |
| Stuxnet rotor frequencies | 1410, 2, 1064 Hz |
| Stuxnet rotor PLC | Siemens S7-315 (6ES7-315) |
| Stuxnet cascade PLC | Siemens S7-417 |
| Pressure controllers per cascade | 21 (15 on exhaust) |
| Stuxnet payload size | ~500 kB |
| Flame payload size | 20 MB |
| Modbus TCP port | 502 |
| RPKI origin RFC | RFC 6480 (2011) |
| 32-bit AS numbers RFC | RFC 4893 / 6793 |
| Active AS count (Dec 2025) | ~78,000 |
| 2010 China Telecom hijack | 37,000 prefixes / 15 min |
| 2008 Pakistan-YouTube fault | global blackhole |
| UK 2019 outage frequency floor | <48.8 Hz |
| Cumulative loss in 9 Aug 2019 | >1130 MW in <1 s |
| Hornsea 1 deload | 799 → 62 MW |
| UK transmission backbone | 400 kV (partly 275 kV) |
| Apparent power | S = VI = P + jQ |
| 2015 Ukraine customers affected | ~200,000 |
| 2016 Ukraine substation | Pivnichna 330 kV |
| Industroyer protocols | IEC 60870-5-101/-104, IEC 61850, OPC DA |
| Triton target | Triconex SIS, Saudi petrochemical |
| FlipIt year/venue | 2012, GameSec |
| Mauw–Oostdijk year | 2005 |
| Kordy AD-trees year | 2010 |
| Roy ACT year | 2012 |
| Maersk offices/countries | 574 / 130 |
| Stuxnet Realtek cert revoked | June 2010 |
| US Cyber Command IOC | 2010 |
| NCSC formed (and from) | Oct 2016 (CESG/CPNI/CCA/CERT-UK) |
| NCF formed | 2020 |
| UK CI sectors (CPNI) | 9 sectors, 14 sub-sectors |
| US CI sectors (HSPD-7) | 17 |
| 1997 US CI report | PCCIP "Critical Foundations" |
| EU CI directive | 2008/114/EC |
| Tier-1 mesh | full mesh (empirical) |
| BA degree distribution exponent | γ ≈ 3 |
| BA preferential attachment | P[u] = δ(u)/Σδ(w) |
| BA degree distribution | P(k) = 2m(m+1)/[k(k+1)(k+2)] ∝ 1/k³ |
| ER giant component threshold | p = 1/n |
| ER full connectedness threshold | Θ(n log n) edges |
| ER avg path length | (ln n − γ) / ln(pn) |
| Ford-Fulkerson complexity | O(E·max\|f\|) |
| King-Rao-Tarjan | O(VE) |
| AND gate (attack tree) | Q₀ = Π qⱼ |
| OR gate (attack tree) | Q₀ = 1 − Π(1 − qⱼ) |
| ACT detection (NAND) | P_goal = P_A(1 − P_D) |
| ACT detection + mitigation | P_goal = P_A(1 − P_D·P_M) |
| OPC UA standard | IEC 62541, from 2006 |
| GPS accuracy | 14 ns (practically 100 ns) |
| 1884 Cable Convention | jurisdiction = vessel flag country |
| ~Cables broken per year (2023-24) | ~200 |
| US Cyber Command strength (2022) | 6,200 |
| APT1 unit | PLA Unit 61398 |
| NotPetya / Sandworm unit | GRU Unit 74455 (GTsST) |
| APT29 agency | SVR |
| APT28 agency | GRU (units 74455, 26165) |
| Energetic Bear agency | FSB unit 71330 |
| RPKI ROA contents | address range, originating AS, max length |
| ToR graph relations | p2c, c2p, p2p, s2s |

---

# Part 11 — Exam-day strategy & common traps

## Strategy
1. Read all five questions before committing.
2. For each candidate question, write a 5-bullet skeleton in pencil; pick the three with the strongest bullets.
3. Lead each answer with a clear **definition** of the central concept (CI, APT, kill chain, Menger, etc.) — examiners reward precise vocabulary heavily.
4. Anchor abstract claims to a **named case study** (Stuxnet, Ukraine, NotPetya, SolarWinds, Triton).
5. Where mathematics is asked (attack-tree probabilities, max-flow), write the formula explicitly even if the question seems qualitative.
6. Conclude each answer with a **defensive implication** — a sentence that says *"therefore the defender should …"*. This shows synthesis.

## Common pitfalls to avoid

- **OR-gate probability**: write `1 − (1−q₁)(1−q₂)`, NOT `q₁ + q₂` (that double-counts the intersection).
- **Kill-chain stages**: don't list them and stop. Each stage must be paired with a defensive opportunity (Detect/Deny/Disrupt/...).
- **APT ≠ sophisticated**: the lecture is explicit that APT events often use commodity exploits — *operational planning* is what makes them advanced. Don't conflate "complex malware" with "APT".
- **Attribution claims**: phrase as "assessed as", "attributed to", "linked to" — never as flat fact. Even Sandworm/GRU 74455 should be "attributed to". This is examined.
- **Menger's theorem**: don't mix up vertex- and edge-versions. The vertex theorem requires non-adjacent u, v; the edge theorem only requires distinct.
- **UK CI sectors**: the lecture's own list is **9 (with 14 sub-sectors)**, not 13.
- **Stuxnet attack count**: there are TWO attack vectors (rotor + cascade) on TWO PLCs (S7-315 + S7-417). Don't conflate them.
- **State estimation FDI defence**: don't say "use crypto" — be specific. The lecture's defence is to protect a *strategic subset* of measurements (PMUs with authenticated time stamps), not all of them.
- **BGPsec is not deployed**: this is the entire point. Saying "BGPsec solves prefix hijacking" loses marks. Say "BGPsec would solve... but is not deployed at scale due to BGPsec islands and crypto cost."
- **Ukraine 2015 vs 2016**: don't blur them. 2015 = BlackEnergy + manual remote HMI = ~200,000 customers. 2016 = Industroyer/CrashOverride + automated, IEC 60870/61850/OPC DA payloads, Pivnichna 330kV.
- **FMEA vs FTA**: FMEA is bottom-up tabular; FTA is top-down tree. Don't confuse them.
- **ER vs WS vs BA**: all three are different. ER has Poisson degree (no hubs); WS has small-world but no power-law; BA produces hubs via preferential attachment.
- **"Flat statement" on contested topics**: e.g. "Stuxnet was created by the US and Israel" — say "developed under the joint US-Israeli Olympic Games programme, acknowledged in June 2012". Precise framing earns marks.
- **Q5 SANS Top 20 critique**: don't just say "they're bad". The marks are in the structured argument: population-level vs adversary-specific, static vs adaptive, no dependencies, compliance vs effect, etc. Then concede that they raise the cost floor.

## Final reminders
- Time budget: 5 questions in 3 hours = 36 minutes per question. If you've picked 3, you have ~60 minutes each. **Don't run out of time on the first one.**
- Diagrams help: a quick attack-tree sketch or BGPsec island diagram earns marks faster than prose.
- Final paragraph of every answer: synthesise. "What does this all mean for the defender?" If you can't answer that in one sentence, you haven't understood the question.

Good luck.
