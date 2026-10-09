## 6G RAN Semantic Data modelling (__*Work In Progress*__)

* RAN Semantic Data in 6G shifts the paradigm of wireless transmission from shannonian bit-level delivery to the delivery of the meaning and intent of data.
* By transmitting only the essential semantic attributes required to reconstruct meaning at the receiver, 6G Radio Access Networks (RAN) can reduce bandwidth consumption, ultra-low latency, and intelligent edge reasoning
* ?__Semantic communication is fundamentally a rate-allocation problem__: a limited transmission budget is split between describing task-relevant content and protecting it against the channel. The practical bottleneck is often not compression efficiency alone but model interoperability, because the neural encoder on the device and the neural decoder in the network must behave as a compatible pair. Shared codebooks, semantic equalization, and a classical fallback mode all start to look less like isolated codec tricks and more like the same two-sided AI management problem that 3GPP already faces in areas such as AI-assisted channel-state feedback.
* ? Placement decides where the inference can run. Accelerator contention decides how many semantic sessions the RAN can sustain. relation between semantic session and UE session, does UE session know about every semantic data and semantic session? how memory footprint will be impacted with AI/ML Ops for semantic data transmission and/or reception, OTA impact as well? whether Semantic Communication turns RAN Scheduling into a Radio Compute Problem?
* A Semantic Aware Radio Access Network (S-RAN) uses AI and ML Ops to compress data into a latent representation (a "semantic data layer").
* How it works?: For example instead of sending an entire high resolution(HD) image over a crowded cellular/Radio link, a semantic encoder strips away redundant data and extracts only the essential features needed for the receiving end to understand or reconstruct the message.
* Why it matters?: It dramatically increases spectrum efficiency and reduces latency. The RAN schedules and weights radio resources based on the importance and semantic value of the data rather than treating every bit identically.
* Benefits?:

### Background
* Need to understand how to model and integrate a **Semantic Layer** and a **Knowledge Plane** parallel to the data and control planes for 6G.
**Why a need of Semantic layer and data for 6G, and how AI could play in that modelling? Also, the lifecycle of Semantic Models in the RAN.**
* Semantic aware MAC Scheduler: how MAC Scheduler will be semantic aware and what features and controls are needed for that, full design with signaling, benefits in terms user and RAN perspective. 

### Architecture and stack (High level)

```text
       +---------------------------------------------------------+
       |                                                         |
       |                  6G CORE NETWORK (6GC)                  |
       |                                                         |
       |  +-------------------------+   +---------------------+  |
       |  |  Knowledge Mgmt Function|---| Policy Control (PCF)|  |
       |  |          (KMF)          |   +---------------------+  |
       |  +-------------------------+              |             |
       |               |                           |             |
       |  +-------------------------+              |             |
       |  |  Semantic UPF (S-UPF)   |--------------+             |
       |  +-------------------------+
       +--+-------------------------+----------------------------+
                       | N3S (Semantic User Plane Interface)
                       |
       +---------------------------------------------------------+
       |                                                         |
       |               6G Radio Access Network (gNB)             |
       |                                                         |
       |  +---------------------------------------------------+  |
       |  |            Semantic Central Unit (gNB-CU)         |  |
       |  |  - Semantic PDCP / SDAP                           |  |
       |  |  - Local Knowledge Base (L-KB)                    |  |
       |  +---------------------------------------------------+  |
       |                           | F1-C / F1-U                 |
       |  +---------------------------------------------------+  |
       |  |            Distributed Unit (gNB-DU)              |  |
       |  |  - Semantic-aware MAC Scheduler                   |  |
       |  +---------------------------------------------------+  |
       +---------------------------------------------------------+
```
## Semantic-aware MAC Scheduler design details
### Functional Architecture & Design

The details the internal architectural components and data flows of a **Semantic-Aware Medium Access Control (MAC) Scheduler** residing within the gNodeB Distributed Unit (gNB-DU). 

#### 1. Functional Design Block Diagram

The block diagram below details the architecture, component boundaries, and internal interfaces of the Semantic-Aware MAC Scheduler.

```
       +-------------------------------------------------------------+
       |                  gNB-DU Upper Layers (RLC)                  |
       +-------------------------------------------------------------+
                                      |
                                      | RLC SDUs & Packet Metadata
                                      v
+--------------------------------------------------------------------+
| gNB-DU MAC LAYER                                                   |
|                                                                    |
|  +--------------------------------------------------------------+  |
|  | [1] SEMANTIC EXTRACTION & PARSING ENGINE                     |  |
|  |     - Inspects SDAP / GTP-U / RLC Header Extensions          |  |
|  |     - Extracts Raw Contextual Features (Data Urgency, Type)  |  |
|  +--------------------------------------------------------------+  |
|                                 |                                  |
|                                 | Parsed Semantic Features         |
|                                 v                                  |
|  +--------------------------------------------------------------+  |
|  | [2] SEMANTIC INFORMATION VALUATION MODULE (SIVM)             |  |
|  |     - Tracks Age of Information (AoI) State Per Flow         |  |
|  |     - Computes Value of Information (VoI) Utility Metrics    |  |
|  +--------------------------------------------------------------+  |
|                                 |                                  |
|                                 | Unified Semantic Weight (ω)      |
|                                 v                                  |
|  +--------------------------------------------------------------+  |
|  | [3] SEMANTIC-AWARE RESOURCE ALLOCATION ENGINE                |  |
|  |     - Dynamic Metric Assembly Loop:                          |  |
|  |       M_i,k = [ R_i,k / Avg_R_i ] x ω_i                      |  |
|  |     - Maps Semantic Value against Radio PRB Constraints      |  |
|  +--------------------------------------------------------------+  |
|               |                                  ^                 |
|   PRB Blocks  |                                  | Channel/HARQ    |
|   & MCS Data  v                                  | Feedback Loop   |
|  +--------------------------------------------------------------+  |
|  | [4] MAC MODULE INTERFACES & FEEDBACK HANDLER                 |  |
|  |     - Constructs Downlink Control Information (DCI)          |  |
|  |     - Executes Exponential Metric Boosts on Critical NACKs   |  |
|  +--------------------------------------------------------------+  |
|                               |                                    |
+-------------------------------|------------------------------------+
                                |
                                | Transport Channels (TB / DCI)
                                v
       +-------------------------------------------------------------+
       |                      gNB-DU PHY Layer                       |
       +-------------------------------------------------------------+
```

#### 2. Component-Level Specifications/Descriptions

##### [1] Semantic Extraction & Parsing Engine
* **Purpose:** Inspects incoming data boundaries at the sub-millisecond level to determine the contextual priority of a packet.
* **Key Mechanisms:**
  * **Header Inspection:** Decodes deep encapsulation or explicit metadata marks passed from the Service Data Adaptation Protocol (SDAP) or user-plane transport network layers.
  * **Feature Mapping:** Classifies raw bytes into distinct structural states (e.g., control loop state transitions, high-impact changes in automated driving safety margins, or invariant background information).

##### [2] Semantic Information Valuation Module (SIVM)
* **Purpose:** Computes the true objective worth of a data unit based on environmental state dynamics and timing.
* **Key Mechanisms:**
  * **Age of Information (AoI) Registry:** Maintains a highly granular per-flow tracking database of time elapsed since the generation of the last successfully received update.
  * **Utility Transform Functions:** Converts linear delay or age properties into highly customized non-linear application values ($U_i$). For instance, an exponential decay function is used for control parameters where old updates yield zero value.

##### [3] Semantic-Aware Resource Allocation Engine
* **Purpose:** Solves the core resource distribution matrix across the space, time, and frequency domains of the NR air interface.
* **Key Mechanisms:**
  * **Metric Assembly:** Pairs the Channel Quality Indicator (CQI) inputs with the contextual scaling factors calculated by the SIVM. 
  * **PRB Multi-plexing Optimization:** Iterates through available Physical Resource Blocks (PRBs) using a bounded greedy loop, maximizing the total transmitted Value of Information (VoI) rather than traditional raw bit volumes.

##### [4] MAC Module Interfaces & Feedback Handler
* **Purpose:** Directs hardware mapping loops and enforces closed-loop target state corrections.
* **Key Mechanisms:**
  * **HARQ Interaction:** Intercepts Hybrid Automatic Repeat Request (HARQ) ACK/NACK signaling from the Physical Layer.
  * **Priority Adjustment:** If a semantically critical block triggers a NACK, this component overrides baseline metrics, boosting the packet's allocation factor in the immediately following Transmission Time Interval (TTI).

## Semantic-Aware MAC Scheduler with HARQ, DTX, and Carrier Aggregation Constraints 

### System Architecture & Block Diagram
The Semantic-Aware MAC Scheduler inside the gNB Distributed Unit (gNB-DU) shifts the resource block allocation paradigm from classic raw-throughput optimization to the optimization of **Quality of Information (QoI)** and **Value of Information (VoI)**. This architectural extension explicitly models **16-process HARQ management**, **DTX (Discontinuous Transmission) recovery loops**, and **Carrier Aggregation (CA)** across multiple Component Carriers (CCs).

```
+-----------------------------------------------------------------------+
|                       RLC LAYER (Logical Channels)                    |
|       [UE 1 Buffers]          [UE 2 Buffers]          [UE n Buffers]  |
+-----------------------------------------------------------------------+
                               |
                               v (SDUs + Metadata)
+-----------------------------------------------------------------------+
|                          gNB-DU MAC LAYER                             |
|                                                                       |
|  +-----------------------------------------------------------------+  |
|  |           1. SEMANTIC EXTRACTION & PARSING ENGINE               |  |
|  |   - Parse packet context & timestamp payloads                   |  |
|  |   - Compute Instantaneous Age of Information (AoI)              |  |
|  +-----------------------------------------------------------------+  |
|                               |                                       |
|                               v (AoI, Context Urgency)                |
|  +-----------------------------------------------------------------+  |
|  |          2. SEMANTIC INFORMATION VALUATION MODULE (SIVM)        |  |
|  |   - Evaluate VoI based on state deviation & lifetime thresholds |  |
|  +-----------------------------------------------------------------+  |
|                               |                                       |
|                               v (Dynamic Weights: omega_i)            |
|  +-----------------------------------------------------------------+  |
|  |    3. CARRIER AGGREGATION & MULTI-HARQ SCHEDULING ENGINE        |  |
|  |   - Monitor Component Carriers (CC_1 to CC_m)                   |  |
|  |   - Handle 16 parallel HARQ Processes per UE per CC             |  |
|  |   - SPF Optimization Loop under Power & PRB budget limits       |  |
|  +-----------------------------------------------------------------+  |
|                               |                                       |
|                               v (Resource Grants & Modulation Coding) |
|  +-----------------------------------------------------------------+  |
|  |                4. HARQ FEEDBACK & DTX HANDLER                   |  |
|  |   - Process ACK / NACK / DTX feedback per CC                    |  |
|  |   - DTX Blind Re-transmission & Power Adjustments               |  |
|  +-----------------------------------------------------------------+  |
+-----------------------------------------------------------------------+
                               |
                               v (Transport Blocks / DCI via MAC PDU)
+-----------------------------------------------------------------------+
|                      PHY LAYER (Component Carriers)                   |
|         [ CC 1 ]                 [ CC 2 ]                 [ CC m ]    |
+-----------------------------------------------------------------------+
```

---
## References

1. Sagduyu, Y. E., & Erpek, T. (2026). When Semantic Communication Meets Queueing: Cross-Layer Optimization of Latency and Task Fidelity. arXiv:2605.05514. https://arxiv.org/abs/2605.05514
2. O-RAN ALLIANCE. dApp Architecture and Interfaces. Research report on real-time AI-based observability and programmability in O-RAN. https://www.o-ran.org/research-reports/dapp-architecture-and-interfaces
3. O-RAN ALLIANCE. dApps for Real-Time RAN Control: Use Cases and Requirements. https://www.o-ran.org/research-reports/dapps-for-real-time-ran-control-use-cases-and-requirements
4. Santhi, N. N., et al. (2026). ARCHES: Adaptive Real-Time Switching of AI Models for the RAN. arXiv:2604.23397. https://arxiv.org/abs/2604.23397
5. NVIDIA Technical Blog. Deploy AI-RAN at Cell Sites with NVIDIA ARC-Compact. https://developer.nvidia.com/blog/deploy-ai-ran-at-cell-sites-with-nvidia-arc-compact/
6. Task-Oriented Communication with Hybrid-Precision Models. arXiv:2607.16766. https://arxiv.org/abs/2607.16766
7. https://arxiv.org/html/2407.11161v2
8. https://www.sciencedirect.com/science/article/pii/S1389128626001556

