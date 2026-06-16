# Day 19 — Quantum Cryptography — Unhackable by Physics

> **Today's one idea:** Quantum Key Distribution (QKD) uses quantum mechanics to detect eavesdropping — any attempt to intercept the key disturbs the quantum states and leaves a measurable trace — making security a physical law rather than a computational assumption.
> **Reading time:** ~35 min · **Prereqs:** Days 4, 10
> **Primary source for today:** John Gribbin, *Computing with Quantum Cats*, Chapter 8 (Bantam Press, 2013)
> **Before you start:** Recall Day 18's load-bearing idea — one sentence, no looking. What is the difference between quantum supremacy and quantum advantage — and which one has been demonstrated?

---

## The hook

Every encrypted message on the internet is secured by a computational assumption: that certain math problems — like factoring large numbers — are too hard to solve in any useful time. This assumption has held for 40 years. But it's just an assumption. Shor's algorithm (Day 13) would shatter it the moment a large-scale quantum computer exists.

Quantum cryptography takes a completely different approach. Instead of relying on computational hardness, it relies on the *laws of physics* — specifically, the quantum mechanical fact that measuring a quantum state disturbs it. An eavesdropper cannot intercept a quantum key without leaving a detectable trace.

This is a guarantee, not an assumption. No computational advance — classical or quantum — can break it. The security follows from quantum mechanics, and if quantum mechanics is wrong, we have much bigger problems.

---

## Building the intuition

### The key distribution problem

Encryption requires two parties (call them Alice and Bob, following the cryptography tradition) to share a secret key — a random string of bits that both know and no one else does. Once they have the key, encrypting and decrypting messages is easy and secure (one-time pad encryption is provably unbreakable if the key is truly random and shared secretly).

The problem: how do Alice and Bob share the key securely in the first place? If they've never met and all channels are monitored, any classical transmission of the key could be intercepted.

This is the key distribution problem. Classical cryptography solves it with mathematical assumptions (RSA, Diffie-Hellman). Quantum cryptography solves it with physics.

### BB84 — the first quantum key distribution protocol

Bennett and Brassard invented the BB84 protocol in 1984. The key insight: photons can be prepared in quantum states, and measuring a photon in the wrong basis gives a random result and disturbs the photon's state.

**Step 1:** Alice sends photons to Bob, each prepared in one of four states encoding 0 or 1:

| Bit | Basis | States used |
|-----|-------|------------|
| 0 | Rectilinear (+) | →  (horizontal) |
| 1 | Rectilinear (+) | ↑  (vertical) |
| 0 | Diagonal (×) | ↗  (45°) |
| 1 | Diagonal (×) | ↘  (135°) |

Alice randomly chooses which basis to use for each photon.

**Step 2:** Bob randomly chooses a basis to measure each photon — rectilinear (+) or diagonal (×).

- If Bob chooses the *same* basis as Alice: he gets the correct bit.
- If Bob chooses the *wrong* basis: he gets a random result (50/50) — useless.

**Step 3 (public channel):** Alice and Bob publicly announce *which basis* they used for each photon (not the actual bits). They keep only the photons where they used the same basis — about 50% of all photons. These form the raw key.

**Step 4 (eavesdropper detection):** If an eavesdropper (Eve) intercepted any photons, she would have had to measure them — using a randomly chosen basis, getting wrong results half the time, and inevitably sending Bob disturbed states. Alice and Bob sacrifice a portion of their shared bits and compare them publicly. If the error rate is above the expected noise level, they know Eve was listening.

If no eavesdropper is detected: Alice and Bob perform error correction and privacy amplification to distill a secure shared key. Security is guaranteed by physics — the no-cloning theorem and the disturbance-measurement link.

### What quantum cryptography can and cannot do

**Can do:**
- Distribute cryptographic keys with information-theoretic security (not just computational security)
- Detect any active eavesdropper on the quantum channel
- Operate today — QKD networks are commercially deployed

**Cannot do:**
- Authenticate that you're talking to the right person (still needs classical cryptography for identity verification)
- Work over arbitrary distances without quantum repeaters (photon loss limits fiber QKD to ~100–200 km without relay nodes)
- Protect against side-channel attacks (malicious hardware, bugs in the QKD device itself)

### Current QKD deployments

QKD is not science fiction — it's commercially deployed today:

- **China:** The Beijing-Shanghai QKD backbone (2016), ~2,000 km via satellite relay (Micius satellite, 2020). Longest quantum-secured communication link.
- **Europe:** EuroQCI (European Quantum Communication Infrastructure), currently being built across EU member states.
- **US:** NIST and DHS-funded testbeds; several financial institutions piloting QKD for inter-datacenter links.
- **Commercial vendors:** ID Quantique (Switzerland), Toshiba Quantum (UK), QuantumCTek (China).

---

## The formal picture

**The no-cloning theorem** (Wootters & Zurek, 1982) guarantees security: an eavesdropper cannot copy an unknown quantum state. Any measurement Eve makes collapses the photon's state — she cannot "silently" intercept and re-send an identical photon. Her presence is detectable.

**Information-theoretic security:** BB84's security doesn't depend on any computational assumption. Even an adversary with infinite computational power cannot break it without leaving detectable traces. This is proven by the "composable security" framework (Renner, 2005).

**Quantum repeaters:** Classical optical networks use signal amplifiers at regular intervals. Quantum signals cannot be amplified (no-cloning). Instead, *quantum repeaters* — devices that use entanglement swapping and quantum memories — extend QKD range. Currently research-stage; expected to enable intercontinental QKD without satellite relay within 10–15 years.

**QKD vs. post-quantum cryptography:**

| | QKD | Post-quantum cryptography (PQC) |
|---|---|---|
| Security basis | Laws of physics | Computational hardness (quantum-hard) |
| Security level | Information-theoretic (unconditional) | Computational (conditional) |
| Implementation | Requires specialized hardware | Software; runs on existing computers |
| Distance limitation | ~100–200 km (fiber) | None |
| Deployment status | Deployed in specialized networks | NIST standards finalized 2024 |
| Threat model | Protects against any eavesdropper | Protects against quantum computers |

Both are valuable; PQC is easier to deploy widely, QKD offers stronger guarantees in specialized settings.

---

## Where it breaks / what it is not

**"QKD is completely unbreakable."**
The protocol is — but implementations aren't. Real QKD devices have been attacked via: bright-light injection attacks (blinding the photon detectors), timing side channels, and hardware trojans. The physics is unbreakable; the engineering is not. "Device-independent QKD" addresses some of these but is harder to build.

**"QKD will replace all encryption."**
No. QKD only distributes keys — it doesn't encrypt data. It requires specialized fiber or line-of-sight optical links, dedicated hardware, and is expensive. PQC (post-quantum cryptography) runs in software on existing hardware and will be the dominant quantum-safe approach for the internet.

**"QKD protects against Shor's algorithm."**
QKD distributes keys without relying on RSA, so Shor's threat to key exchange doesn't apply. But the messages encrypted with those keys still use classical symmetric encryption (e.g., AES) — which Grover's algorithm addresses (solved by using AES-256). QKD + AES-256 is quantum-safe.

---

## Try it yourself

**1. Retrieval — close the page.** Write down in one sentence: how does BB84 detect an eavesdropper — what physical fact makes that detection inevitable? Open only after writing your answer.

<details>
<summary>Answer</summary>
BB84 detects eavesdroppers because any measurement Eve makes on a photon must choose a basis — and with 50% probability she picks the wrong one, sending Bob a disturbed state. When Alice and Bob compare a sample of their bits over a public channel, Eve's wrong-basis measurements show up as elevated error rates that exceed the expected hardware noise. The no-cloning theorem prevents Eve from copying photons silently before measuring.
</details>

**2. Check understanding.**
In BB84, Alice and Bob announce their basis choices publicly after the photons are sent. Why isn't this a security problem?

<details>
<summary>Answer</summary>
Announcing the *basis* (rectilinear or diagonal) doesn't reveal the *bit value*. The bit is encoded in which specific state within the basis was used (e.g., horizontal vs. vertical within rectilinear). Eve, who intercepted photons earlier, already made her measurement before hearing the basis announcement. Knowing the basis after the fact doesn't help her recover bits she measured in the wrong basis — those measurements already gave random results. Only Alice and Bob, who compare their bases and keep matching-basis results, extract a meaningful key.
</details>

**3. Apply.**
Alice and Bob run BB84 and sacrifice 200 of their shared bits for eavesdropper detection. With no eavesdropper, they expect about 1% error rate from hardware noise. They observe 8% errors. What does this mean, and what should they do?

<details>
<summary>Answer</summary>
8% error rate is significantly above the 1% hardware baseline. This is a statistical signal that an eavesdropper was present — Eve's random basis choices would introduce approximately 25% errors on the intercepted bits, and the elevated overall error rate reflects her presence. Alice and Bob should abort this key exchange and try again over a different channel or time slot. The protocol is working correctly: eavesdropping has been detected before any compromised key was used.
</details>

**4. Stretch.**
QKD requires a "classical authenticated channel" for the basis announcement step (Step 3). This channel must be authenticated — Alice and Bob must know they're talking to each other, not an impersonator. But authenticating classically requires a pre-shared secret — which is exactly what QKD is trying to provide. Doesn't this create a circular dependency?

<details>
<summary>Answer</summary>
Yes — this is a genuine bootstrapping problem. The resolution: QKD doesn't eliminate the need for an initial small pre-shared secret; it *amplifies* it. If Alice and Bob share a short (say 256-bit) secret to authenticate their first classical channel, they can use QKD to distribute a much longer (megabit-scale) fresh key. They then use part of this new key to authenticate the next QKD session. The bootstrapping cost is a one-time small pre-shared secret (which can be physically couriered or established at initial setup). After that, QKD self-sustains indefinitely. This is a feature: the security is reduced to one small secret that must be established securely once, not to an ongoing computational assumption.
</details>

---

**Transfer — apply it (all levels):** Does your organization transmit data that must remain confidential for 15+ years — medical records, classified contracts, long-term financial commitments? Write one sentence on whether QKD or post-quantum cryptography (PQC) is the more relevant near-term concern for your specific use case, and why.

---

## Connect it back

Quantum cryptography is the most mature quantum technology — commercially deployed today, security guaranteed by physics. Tomorrow (Day 20) covers quantum simulation: Feynman's original motivation for quantum computing, and the nearest-term candidate for genuine quantum advantage over classical computers for useful problems.

**The question you should now be able to answer:** Why does measuring an eavesdropper's photon disturb it — and why does this make QKD secure?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Gribbin, *Computing with Quantum Cats*, Chapter 8 ("Quantum Cryptography"), Bantam Press, 2013. Gribbin's narrative of QKD — from the original BB84 paper to commercial deployment — is the most readable account at this level.

**If you want the deep version:**
- Stephanie Wehner, David Elkouss, and Ronald Hanson, "Quantum Internet: A Vision for the Road Ahead," *Science* 362(6412), 2018. DOI: 10.1126/science.aam9288. The clearest roadmap for quantum networking and QKD at scale — discusses where we are (point-to-point QKD) and where we're going (quantum internet with entanglement-based links).
- ID Quantique white papers (idquantique.com): the leading commercial QKD vendor publishes accessible technical descriptions of their Clavis³ and Cerberis³ systems — real-world QKD implementation for non-physicists.

---

## Navigation

← **Previous:** [Day 18 — Quantum Advantage vs. Quantum Supremacy](../../03-building-quantum/days/day-18-advantage-vs-supremacy.md)
→ **Next:** [Day 20 — Quantum Simulation — The Original Killer App](./day-20-quantum-simulation.md)
