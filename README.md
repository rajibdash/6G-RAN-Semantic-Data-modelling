## 6G RAN Semantic Data modelling (__*Work In Progress*__)

* RAN Semantic Data in 6G shifts the paradigm of wireless transmission from shannonian bit-level delivery to the delivery of the meaning and intent of data.
* By transmitting only the essential semantic attributes required to reconstruct meaning at the receiver, 6G Radio Access Networks (RAN) can reduce bandwidth consumption, ultra-low latency, and intelligent edge reasoning

### Background
* Need to understand how to model and integrate a **Semantic Layer** and a **Knowledge Plane** parallel to the data and control planes for 6G.

### Why a need of Semantic layer and data for 6G, and how AI could play in that modelling? Also, the lifecycle of Semantic Models in the RAN.
* Semantic-aware MAC Scheduler: how MAC Scheduler will be semantic aware and what features is needed for that, full design with signaling

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

## References

