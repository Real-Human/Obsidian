# IY5612 — Condensed Revision Guide
## The essentials only

*Distilled from the full guide. Use for final-week revision and exam-day reference.*

---

## Exam structure (2019 paper)

5 questions, answer 3. Topic split has been consistent:

| Q | Theme | Units |
|---|---|---|
| 1 | Control systems (Modbus, OPC UA, state estimation) | 6, 7 |
| 2 | CI interdependencies (graphs, flows, cascades) | 3, 5 |
| 3 | Network/power robustness (BGP, RPKI, grid) | 4, 5 |
| 4 | Cyber security analysis (APTs, kill chain, supply chain) | 1, 2 |
| 5 | Attack/adversary modelling (attack trees, game theory) | 8 |

Safe combinations: **Q2 + Q4 + Q5** or **Q1 + Q3 + Q4**.

---

## Definitions to memorise verbatim

**CI (EU 2008/114/EC):** Asset/system essential for vital societal functions, health, safety, security, economic or social well-being, whose disruption would have significant impact.

**CII (ENISA):** Interconnected information systems and networks whose disruption would have serious impact on health/safety/security/economic well-being or government functioning.

**APT (NIST SP 800-39):** "An adversary that possesses sophisticated levels of expertise and significant resources... pursues its objectives repeatedly over an extended period of time; adapts to defenders' efforts to resist it; and is determined to maintain the level of interaction needed to execute its objectives."

**Menger's Theorem:** Min vertex cut between non-adjacent u, v = max vertex-independent paths. Min edge cut between distinct u, v = max edge-independent paths. Corollary: G is k-connected iff every pair has ≥ k vertex-independent paths.

---

## Unit 1 essentials

**UK CI sectors (CPNI):** 9 sectors, 14 sub-sectors — Communications, Financial Services, Government, Emergency Services, Health, Water, Energy, Food, Transportation. (Not 13.)

**Threat actors (HMG IAS1 matrix):**
| | Economic | National security |
|---|---|---|
| State | Industrial espionage | Cyber warfare |
| Non-state | Cyber crime | Cyber terrorism |

**UK strategy:**
- **NCSC** — Oct 2016, sub-entity of GCHQ, merged CESG/CPNI/CCA/CERT-UK.
- **NCF** — 2020, joint MoD/GCHQ offensive cyber.
- **NSS 2015** — cyber as Tier 1 risk.
- **2022 strategy** — 5 pillars, framed as "Cyber Power".

**Subsea cables:** ~99% of intercontinental traffic. ~200 breaks/year. 1884 Cable Convention: jurisdiction = vessel flag country. **IVY BELLS (1971)** = NSA tap on Soviet cable in Sea of Okhotsk via inductive coupling.

**Mirai (2016):** default credential `root:xc3511`; took down Dyn (Twitter, GitHub, Spotify); botnet ~500k–2.5m IoT devices.

**SolarWinds/Sunburst:** repo password `SolarWinds123`; ~18,000 sites; March-Dec 2020; trojanised Orion update; DNS-encoded C2; led to Executive Order 14028.

---

## Unit 2 essentials

### Cyber Kill Chain (Hutchins et al. 2011) — memorise the 7 stages

1. **Reconnaissance** — OSINT target selection.
2. **Weaponisation** — pair exploit with payload.
3. **Delivery** — email, USB, watering hole, insider.
4. **Exploitation** — code execution.
5. **Installation** — persistence, rootkit.
6. **C2** — beacon to attacker infrastructure.
7. **Actions on Objectives** — the goal; possibly recursive.

Defensive matrix: at each stage, **Detect / Deny / Disrupt / Degrade / Deceive / Destroy**. Defenders only need to break one link.

### APT case studies — pick 3-4 to deploy

- **Solar Sunrise (1998)** — 3 teenagers; thought to be Iraqi.
- **Aurora (2009)** — CVE-2010-0249; Google + 20 companies; introduced watering-hole attacks.
- **APT1 / Comment Crew (Mandiant 2013)** — PLA Unit 61398; 141 attacks 2006-2013; Telvent (Schneider) breached.
- **RSA SecurID (2011)** — CVE-2011-0609; phishing email "2011 Recruitment plan.xls"; Poison Ivy RAT; led to Lockheed/Northrop breach.
- **NotPetya (2017)** — GRU Unit 74455; M.E.Doc supply chain; EternalBlue (MS17-010); >$10bn damage; Maersk recovered via Ghana DC offline due to power failure.
- **Storm-0558 (2023)** — inactive MSA signing key forged → Exchange Online + Azure AD tokens; PRC.
- **Sandworm** = GRU 74455 = NotPetya, Industroyer, Olympic Destroyer, Industroyer2, PIPEDREAM.

### Russian APT taxonomy
- **APT29 / Cozy Bear** = SVR (SolarWinds, DNC).
- **APT28 / Fancy Bear / Sandworm** = GRU 74455 + 26165.
- **Energetic Bear** = FSB unit 71330.

### Supply-chain risk
- Trust transitivity: defender perimeter only as good as weakest vendor.
- Cite: SolarWinds, RSA → Lockheed, xz-utils (2024), OPM (2013-14 via USIS/KeyPoint).

### Attribution
- Always say "attributed to" / "assessed as", never flat fact.
- False flags (Olympic Destroyer planted Korean code), infrastructure rotation, code reuse.

---

## Unit 3 essentials

### Graph definitions
- **k-connected:** ≥ k vertices must be removed to disconnect.
- **Adjacency matrix A:** A[i,j] = edge count; row-sum = degree; G simple iff A[i,j] ≤ 1, A[i,i] = 0.
- **Diameter** = max shortest-path distance.
- **Betweenness centrality** = fraction of shortest paths through a vertex.

### Random graph models — key contrasts

| Model | Degree distribution | Clustering | Path length | Hub structure |
|---|---|---|---|---|
| **ER(n,p)** | Poisson (homogeneous) | low | short | none |
| **Watts-Strogatz** | ~Poisson | high | short (small-world) | none |
| **Barabási-Albert** | power-law (γ=3) | low | very short | hubs (preferential attachment) |

**BA preferential attachment:** P[select u] = δ(u) / Σ δ(w).

**BA degree distribution:** P[δ(u) = k] = 2m(m+1) / [k(k+1)(k+2)] ∝ 1/k³.

**ER giant component threshold:** p = 1/n. Full connectedness threshold: Θ(n log n) edges.

**Robust-yet-fragile (BA):** robust to random failure, fragile to targeted attack on hubs. The Internet AS-level graph follows this pattern.

---

## Unit 4 essentials

### AS-level Internet
- ~78,000 active AS (Dec 2025).
- 32-bit AS numbers via RFC 4893/6793.
- ToR graph relations: **p2c, c2p, p2p, s2s**.
- Tier-1 ASes form full mesh.

### BGP attacks
- **Origin hijack** (announce victim's prefix).
- **More-specific hijack** (longest prefix wins).
- **AS_PATH manipulation** (forge path).
- **Route leak** (policy violation).

**Key incidents:** Pakistan/YouTube (2008), China Telecom (2010, **37,000 prefixes for 15 minutes**), MyEtherWallet/Route 53 (2018).

### Defences
- **RPKI (RFC 6480, 2011):** signed origin only. ROA = (prefix, origin AS, max length). Doesn't protect path.
- **BGPsec:** signs each hop. Heavy crypto + **BGPsec islands** (single non-BGPsec speaker isolates secure region) → not deployed at scale.
- **Real diversity = topological diversity ∩ policy diversity.** Vertex-independent paths necessary but not sufficient — Gao-Rexford funnels override.

### DDoS
- **Mirai (2016)**: IoT botnet, Dyn outage.
- **DNS amplification**: 64-byte query → entire zone file (UDP-based).
- **Memcached**: amplification factor >50,000× (GitHub 1.35 Tbps, 2018).

### IIM (Inoperability Input-Output Model)
- Sector inoperability q ∈ [0,1].
- q = Aq + c → q = (I − A)⁻¹ c.
- **DIIM** adds dynamics.
- **DebtRank (Battiston 2012)** = financial centrality analogue of PageRank.

---

## Unit 5 essentials

### Power grid structure
- **Voltages (UK):** 400 kV / 275 kV transmission → 33 kV → 11 kV → 400/230 V distribution.
- Generators 25-33 kV → step-up to transmission.
- HVDC interconnectors: France, NL, Belgium, Norway, Denmark (Jan 2024), NI, RoI.
- **Apparent power:** S = VI = P + jQ (VA = W + j·VAR).

### Frequency control timescales
- **Primary** (governor, autonomous): seconds.
- **Secondary / AGC** (system-wide): minutes.
- **Tertiary** (economic dispatch): 15 min – hours.
- **Reserves:** primary (online), secondary (synchronised), tertiary (offline standby).
- **Voltage control** = local reactive power (capacitors, FACTS).

### 9 August 2019 UK outage — sequence
1. Lightning strike on Eaton Socon-Wymondley 400 kV line.
2. Little Barford CCGT trips: -150 MW.
3. **Hornsea 1** wind farm voltage control unstable: 799 → 62 MW (-737 MW).
4. Little Barford steam unit trips: -244 MW.
5. >1130 MW lost in <1 second.
6. Frequency drops below **48.8 Hz** → **LFDD** activated.
7. Restored within 45 minutes. Govia Thameslink trains shut down.

### Ukraine attacks — CRITICAL distinction

**2015 (BlackEnergy + manual)**:
- Three distribution operators (Ukrenergo + 2 oblenergos).
- Spear-phish → BlackEnergy 3 + KillDisk → operator credentials → remote HMI → manual breaker opening + system wipe + UPS disable + TDoS on call centres.
- ~200,000 customers, ~6 hours.
- "Rustic" recovery via manual switchgear.

**2016 (Industroyer/CrashOverride + automated)**:
- Pivnichna 330 kV substation; refurbished 2012-14 with ABB/GE/Siemens, IEC 61850, MicroSCADA Pro 9.3.
- Industroyer payloads: **IEC 60870-5-101, -104, IEC 61850, OPC DA**.
- Exploited SIPROTEC relay vulnerability.

**PIPEDREAM (2022):** Sandworm modular ICS toolkit, can target SIS. Used in ELECTRUM breach late 2022.

### Cascade modelling
- **Buldyrev et al. 2010 (Nature):** interdependent networks much more fragile than independent.
- **Ford-Fulkerson:** O(E·max|f|), can fail to terminate. King-Rao-Tarjan: O(VE).
- **Cascading models:** threshold (Watts), load redistribution, sandpile.

---

## Unit 6 essentials

### Process control vocabulary (memorise for Q1)
- **MV** (Manipulated Variable), **FCE** (Final Control Element), **PV** (Process Variable), **SP** (Set Point), **Transmitter** (transducer + standardised output).
- "Control FCEs to change MVs to maintain PVs at SPs."

### Control theory
- **Controllability:** rank[B, AB, A²B, …] = n.
- **Observability:** rank[C; CA; CA²; …] = n.
- **Detectability** (Kalman): unobservable states must be stable.
- **Open-loop:** no feedback. **Closed-loop:** sensor → controller → FCE → measure → repeat.

### ICS components
- **PLC** (real-time control), **RTU** (remote telemetry), **HMI** (Windows operator station = lateral movement target), **SCADA server**, **Historian**, **Engineering Workstation**, **DCS**, **SIS** (Triton target).
- **IEC 61131** = PLC programming languages. **IEC 61499** = function blocks for distributed control.
- Real-time: hard / firm / soft. RTOS provides bounded latency.

### Sensor/actuator attacks
- **Loss of View:** operator sees stale data (Stuxnet replay).
- **Loss of Control:** commands have no effect.
- **Accuracy** (closeness to true) vs **Precision** (repeatability) — independent attack surfaces.
- **Davis-Besse 2003:** Slammer worm reached SPDS via contractor network → 5h offline. Plant was offline so no consequence.

### Stuxnet — exhaustive specifics

**Strategic:** Operation Olympic Games (US/Israel, from 2008, acknowledged June 2012). Target: Natanz FEP, Iran. IR-1 centrifuges (Urenco design stolen by A.Q. Khan 2004).

**Targets:**
- **S7-315** PLC = rotor drives (CP342 PROFIBUS module).
- **S7-417** PLC = cascade protection (PR-4000 pressure controllers, **21 per cascade, 15 on exhaust**).

**Centrifuge attack:**
- IR-1 normal: **63,000 rpm**.
- Over-speed: **84,600 rpm for 15 min**.
- Under-speed: **120 rpm**.
- Frequency converter band: **807-1210 Hz** (export-controlled).
- Stuxnet drives to: **1410 Hz, 2 Hz, 1064 Hz**.
- Recorded normal data for **13 and 27-day periods** before launching; replayed to HMI during attack.

**Cascade attack:** block isolation valves → overpressure in non-isolated centrifuges; pressure sensors report sanitised readings.

**Vectors:**
- **MS10-046** (LNK shortcut) = USB 0-click.
- **MS08-067** (NetAPI, Conficker reuse) = network shares.
- **MS10-061** (print spooler) = privilege escalation.
- **MS10-092** (task scheduler) = SYSTEM escalation.
- **WinCC default password `2WSXcder`** for SQL injection.
- Two stolen certs: **Realtek** (Jun 2010, revoked) → **JMicron** (Jul 2010).
- C2: `mypremierfutbol.com`, `todaysfutbol.com`.

**Family:** Duqu (2011), Flame (2012, 20MB, MD5-collision Microsoft cert), Gauss (2012). Equation Group context: GrayFish, EquationDrug, Fanny.

---

## Unit 7 essentials

### SCADA generations (lecture's split: 1st/2nd/3rd, not 4)
- **1st**: POTS/radio/microwave, limited interaction.
- **2nd**: leased lines, guaranteed timing.
- **3rd**: Ethernet, IP — **cyber security risks arise mainly here**.

### Modbus TCP
- Port **502**.
- Variants: RTU (serial binary), ASCII (serial), TCP.
- **No authentication, no integrity (cryptographic), no confidentiality.**
- Master initiates; slaves identified by Unit ID only.
- **Attacks:** read/write registers, replay, spoof unit ID, MitM, DoS.

### IEC standards
- **60870-5-101/-104** = telecontrol serial/TCP (Industroyer target).
- **60870-6 / TASE.2 / ICCP** = control-centre to control-centre.
- **61850** = substation automation; GOOSE multicast (sub-ms latency, IEC 62351-6 integrity).
- **62541 = OPC UA** (from 2006).

### OPC UA risks (Q1 2019)
- Inherently good crypto (X.509, signing, encryption, can use AD PKI).
- **But:** certificate management failures, default `None` security mode, web-app risks (TLS misconfig, SSRF), discovery endpoint leaks address space, OS-level CVEs, complexity → many lines of code.
- Bridging UA Classic ↔ UA may break trust delegation.
- "Only as secure as the deployment."

### State estimation (Q1 2019)
- Linear: **z = Hx + e**, solved by WLS.
- Bad-data detection: chi-squared on residual r = z − Hx̂.
- **False data injection (Liu, Ning, Reiter CCS 2009):** attacker injects **a = Hc** → x̂' = x̂ + c with **unchanged residual** → unobservable attack. Requires knowledge of H + control of enough measurements.
- **Defence:** cryptographically protect strategic measurement subset (PMUs with authenticated time), topology obfuscation, dynamic estimation cross-checks.

### IDS in control systems
- **Signature** (Snort + Quickdraw rules for Modbus/DNP3).
- **Anomaly** (model normal, flag deviations).
- **Specification-based** (formal model of intended behaviour) — best for ICS due to regular protocol patterns.

### GPS / PMU
- GPS accuracy 14ns. **No authentication on civilian L1.** Spoofing trivial (Tippenhauer CCS 2011).
- Defence: multi-constellation, holdover oscillators, signal direction-of-arrival.

### Triton/Trisis (2017)
- Targeted **Triconex SIS** at Saudi petrochemical plant.
- First malware targeting safety systems. Russian-attributed.
- Triggered unintended SIS shutdown → exposed the attack.

---

## Unit 8 essentials

### Static models
- **FMEA:** bottom-up tabular. RPN = Severity × Occurrence × Detection.
- **FTA:** top-down tree. Cut set = minimal set of basic events causing top event.
- **Attack tree (Schneier 1999):** root = goal, leaves = atomic steps.

### Quantitative attack tree formulas (memorise)

**AND gate (k independent events):**
$$Q_0 = \prod_{j=1}^{k} q_j$$

**OR gate (k independent events):**
$$Q_0 = 1 - \prod_{j=1}^{k}(1-q_j)$$

For two events: Q₀ = q₁ + q₂ − q₁q₂.

**Costs:** AND = sum, OR = min.
**Time:** AND = sum (sequential) or max (parallel); OR = min.

### Attack-Defence Trees (Kordy et al. 2010)
Bipartite: attack nodes have defence sub-nodes; defences may have counter-attack sub-nodes.

### Attack Countermeasure Trees (Roy, Kim, Trivedi 2012)

- No countermeasure: **P_goal = P_A**
- Single detection (NAND): **P_goal = P_A · (1 − P_D)**
- Multiple detections, all must fail: **P_goal = P_A · Π(1 − P_Dᵢ)**
- Detection + mitigation: **P_goal = P_A · (1 − P_D · P_M)**
- Undetected: **P_UD = P_A · (1 − P_D)**
- Detected but unmitigated: **P_DUM = P_A · P_D · (1 − P_M)**

### Game theory
- **Prisoner's Dilemma** — info-sharing analogue.
- **Hawks vs Doves** — V (resource value), C (loss cost), C > V → mixed strategies.
- **Nash equilibrium**: no unilateral improvement; mixed strategies always exist (Nash 1950); 2-player general-sum sum-maximisation is NP-complete.
- **Stackelberg games**: leader commits, follower responds. Defender = natural leader. SSE usually unique (Korzhyk et al. 2011).
- **FlipIt (van Dijk et al. GameSec 2012)**: continuous-time stealthy takeover; informs key refresh / re-image cadence.

### Petri nets
- Bipartite: **places** (state) + **transitions** (events) + **tokens**.
- Transition enabled iff every input place has a token. Firing is atomic: removes 1 token from each input place, adds 1 to each output place.
- Captures **concurrency, synchronisation, conflict, non-determinism** — things AT/AD-trees cannot.
- Extensions: **Hierarchical, Coloured (CPN Tools), Timed**.
- Reference: Chen et al. 2011 IEEE Trans. Smart Grid.

### Critique of SANS Top 20 (Q5 2019 opener)
- Population-level recommendations; sophisticated adversary targets controls *not* on the list.
- Static rankings ignore adversary adaptivity (Stackelberg/FlipIt argue for mixed strategies).
- Cost-benefit is contextual; lists hide dependencies.
- Compliance ≠ effect; doesn't help with already-resident persistent adversary.
- **But:** raises the cost floor for opportunists, board-comprehensible artefact, baseline for benchmarking.

---

## Worked-answer skeletons (2019 paper)

**Q1 (Control systems):** (a) Modbus TCP attacks — read/write/replay/spoof/MitM/DoS, root cause = no auth/encryption/integrity. (b) OPC UA web service — inherent crypto good, but cert mgmt + default `None` mode + web app risks + discovery leaks + OS CVEs. (c) FDI — a = Hc unobservable, defend by protecting strategic measurement subset.

**Q2 (Interdependencies):** (a) Min vertex cut via Menger; (b) dense core / thin periphery — defender hardens core; (c) Ford-Fulkerson on capacitated network → min cut = bottleneck; (d) increase k-connectivity, capacity buffers, geographic diversity, automatic islanding.

**Q3 (Network/power):** (a) generation/transmission/distribution + frequency timescales; (b) BGP hijack types + incidents; (c) RPKI origin only + BGPsec islands; (d) topological diversity ∩ policy diversity.

**Q4 (Cyber):** (a) NIST APT def + characteristics; (b) 7 kill-chain stages + defensive ops; (c) supply chain (SolarWinds, RSA, xz, OPM); (d) attribution probabilistic (false flags, infra rotation); (e) Olympic Destroyer false-flag example.

**Q5 (Modelling):** (a) SANS critique (above); (b) attack tree → minimal cut sets → cheapest first; (c) AD-tree for executive laptop (theft / remote / insider / cryptanalysis branches); (d) game theory adds multi-round, mixed strategies, incomplete info, adaptive adversaries.

---

## Cheat sheet (final-day drill)

| Item | Answer |
|---|---|
| WinCC default password | `2WSXcder` |
| Mirai default credential | `root:xc3511` |
| SolarWinds repo password | `SolarWinds123` |
| Stuxnet C2 domains | mypremierfutbol.com, todaysfutbol.com |
| Stuxnet exploits | MS10-046 (LNK) / MS08-067 (Conficker) / MS10-061 (spooler) / MS10-092 (scheduler) |
| Stuxnet certs | Realtek, JMicron |
| RSA exploit | CVE-2011-0609 |
| Aurora exploit | CVE-2010-0249 |
| EternalBlue | MS17-010 (NotPetya) |
| IR-1 normal/over/under | 63,000 / 84,600 / 120 rpm |
| Frequency converter band | 807-1210 Hz |
| Stuxnet rotor frequencies | 1410, 2, 1064 Hz |
| Stuxnet PLCs | S7-315 (rotor), S7-417 (cascade) |
| PR-4000 controllers | 21/cascade, 15 on exhaust |
| Modbus TCP port | 502 |
| RPKI RFC | 6480 |
| Active AS count (2025) | ~78,000 |
| China Telecom 2010 hijack | 37,000 prefixes / 15 min |
| UK 2019 freq floor | <48.8 Hz |
| 9 Aug 2019 loss in <1s | >1130 MW |
| Hornsea deload | 799 → 62 MW |
| UK transmission backbone | 400 kV (partly 275 kV) |
| Apparent power | S = VI = P + jQ |
| Ukraine 2015 customers | ~200,000 |
| Ukraine 2016 substation | Pivnichna 330 kV |
| Industroyer protocols | IEC 60870-5-101/-104, 61850, OPC DA |
| Triton target | Triconex SIS |
| NCSC formed | Oct 2016 (CESG/CPNI/CCA/CERT-UK) |
| NCF formed | 2020 |
| UK CI sectors | 9 (CPNI), 14 sub-sectors |
| US CI sectors (HSPD-7) | 17 |
| EU CI directive | 2008/114/EC |
| BA exponent γ | 3 |
| BA distribution | 2m(m+1)/[k(k+1)(k+2)] |
| ER giant component | p = 1/n |
| ToR relations | p2c, c2p, p2p, s2s |
| AND gate prob | Π qⱼ |
| OR gate prob | 1 − Π(1−qⱼ) |
| ACT detection only | P_A(1−P_D) |
| ACT det+mit | P_A(1−P_D·P_M) |
| FlipIt year | 2012 (GameSec) |
| Mauw-Oostdijk | 2005 (Attack Trees) |
| Kordy | 2010 (AD-Trees) |
| Roy | 2012 (ACT) |
| APT1 unit | PLA 61398 |
| Sandworm unit | GRU 74455 (GTsST) |
| APT29 / APT28 | SVR / GRU |
| Energetic Bear | FSB 71330 |
| Liu, Ning, Reiter | CCS 2009 (FDI) |
| Maersk offices | 574 / 130 countries |

---

## Common traps that lose marks

- **OR gate**: write `1 − Π(1−qⱼ)` NOT `Σ qⱼ` (double-counts intersection).
- **Kill chain**: don't list and stop — pair each stage with a defensive option.
- **APT ≠ sophisticated malware**: it's about operational planning + persistence + adaptation. Commodity exploits often used.
- **Attribution**: always "attributed to" / "assessed as" — never flat fact.
- **Menger**: vertex version requires non-adjacent u, v; edge version only requires distinct.
- **UK CI sectors**: 9 (CPNI), not 13.
- **Stuxnet**: TWO attack vectors on TWO PLCs. Don't conflate.
- **State estimation defence**: protect *strategic subset* of measurements, not all.
- **BGPsec**: NOT deployed at scale. Don't say it solves prefix hijacking — say it would, but for islands + crypto cost.
- **Ukraine 2015 vs 2016**: 2015 = BlackEnergy + manual + ~200k customers; 2016 = Industroyer + automated + Pivnichna 330 kV.
- **FMEA vs FTA**: bottom-up tabular vs top-down tree.
- **ER vs WS vs BA**: Poisson (no hubs) vs small-world (no power-law) vs scale-free hubs.
- **Stuxnet attribution**: "developed under the joint US-Israeli Olympic Games programme, acknowledged June 2012" — never just "US and Israel".
- **SANS Top 20 critique**: structured argument (population vs adversary, static vs adaptive, no dependencies, compliance vs effect) — then concede they raise the cost floor.

---

## Exam-day strategy

1. Read all 5 questions before committing.
2. Skeleton 5 bullets per candidate; pick the strongest 3.
3. Lead each answer with a precise definition.
4. Anchor every claim to a named case study (Stuxnet / Ukraine / NotPetya / SolarWinds / Triton).
5. Include the formula even when the question is qualitative.
6. End with "therefore the defender should..." synthesis.
7. Time: ~60 min/question (3hr ÷ 3). Don't over-run on the first.
