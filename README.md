## 6G RAN Semantic Data modelling (__*Work In Progress*__)

* RAN Semantic Data in 6G shifts the paradigm of wireless transmission from shannonian bit-level delivery to the delivery of the meaning and intent of data.
* By transmitting only the essential semantic attributes required to reconstruct meaning at the receiver, 6G Radio Access Networks (RAN) can reduce bandwidth consumption, ultra-low latency, and intelligent edge reasoning
* ?__Semantic communication is fundamentally a rate-allocation problem__: a limited transmission budget is split between describing task-relevant content and protecting it against the channel. The practical bottleneck is often not compression efficiency alone but model interoperability, because the neural encoder on the device and the neural decoder in the network must behave as a compatible pair. Shared codebooks, semantic equalization, and a classical fallback mode all start to look less like isolated codec tricks and more like the same two-sided AI management problem that 3GPP already faces in areas such as AI-assisted channel-state feedback.
* ? Placement decides where the inference can run. Accelerator contention decides how many semantic sessions the RAN can sustain. relation between semantic session and UE session, does UE session know about every semantic data and semantic session? how memory footprint will be impacted with AI/ML Ops for semantic data transmission and/or reception, OTA impact as well?
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
__*WIP*__

## References

1. Sagduyu, Y. E., & Erpek, T. (2026). When Semantic Communication Meets Queueing: Cross-Layer Optimization of Latency and Task Fidelity. arXiv:2605.05514. https://arxiv.org/abs/2605.05514

2. O-RAN ALLIANCE. dApp Architecture and Interfaces. Research report on real-time AI-based observability and programmability in O-RAN. https://www.o-ran.org/research-reports/dapp-architecture-and-interfaces

3. O-RAN ALLIANCE. dApps for Real-Time RAN Control: Use Cases and Requirements. https://www.o-ran.org/research-reports/dapps-for-real-time-ran-control-use-cases-and-requirements

4. Santhi, N. N., et al. (2026). ARCHES: Adaptive Real-Time Switching of AI Models for the RAN. arXiv:2604.23397. https://arxiv.org/abs/2604.23397

5. NVIDIA Technical Blog. Deploy AI-RAN at Cell Sites with NVIDIA ARC-Compact. https://developer.nvidia.com/blog/deploy-ai-ran-at-cell-sites-with-nvidia-arc-compact/

6. Task-Oriented Communication with Hybrid-Precision Models. arXiv:2607.16766. https://arxiv.org/abs/2607.16766

