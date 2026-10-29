[Back to project overview](../README.md)

## 6G RAN Semantic Data modelling (__*Work In Progress*__)

* RAN Semantic Data in 6G shifts the paradigm of wireless transmission from shannonian bit-level delivery to the delivery of the meaning and intent of data.
* By transmitting only the task-relevant information needed by the receiver, 6G Radio Access Networks (RAN) may reduce transmitted data volume or latency and support edge reasoning when the application, channel, and endpoint models make semantic processing beneficial.
* Next-generation wireless networks, including 5G-Advanced and 6G, will connect with large numbers of bandwidth-constrained devices such as vehicles, surveillance cameras, and drones. Transmitting raw or conventionally compressed images can use valuable spectrum on details irrelevant to a particular task. Semantic communication instead aims to transmit task-relevant information—such as detected objects and their locations—while preserving what is needed for downstream decisions.
* ?__Semantic communication is fundamentally a rate-allocation problem__: a limited transmission budget is split between describing task-relevant content and protecting it against the channel. The practical bottleneck is often not compression efficiency alone but model interoperability, because the neural encoder on the device and the neural decoder in the network must behave as a compatible pair. Shared codebooks, semantic equalization, and a classical fallback mode all start to look less like isolated codec tricks and more like the same two-sided AI management problem that 3GPP already faces in areas such as AI-assisted channel-state feedback.
* ? Placement decides where the inference can run. Accelerator contention decides how many semantic sessions the RAN can sustain. relation between semantic session and UE session, does UE session know about every semantic data and semantic session? how memory footprint will be impacted with AI/ML Ops for semantic data transmission and/or reception, OTA impact as well? whether Semantic Communication turns RAN Scheduling into a Radio Compute Problem?
* A Semantic Aware Radio Access Network (S-RAN) uses AI and ML Ops to compress data into a latent representation (a "semantic data layer").
* How it works?: For example instead of sending an entire high resolution(HD) image over a crowded cellular/Radio link, a semantic encoder strips away redundant data and extracts only the essential features needed for the receiving end to understand or reconstruct the message.
* Why it matters?: It can improve spectrum efficiency or task performance in suitable scenarios. The RAN may account for task value alongside conventional channel, QoS, and fairness constraints rather than treating all traffic identically.
* Potential benefits, which require task- and system-level validation, include lower transmitted data volume, better task quality under a constrained link, and more efficient allocation of radio or edge-compute resources.

### Background
* Need to understand how to model and integrate a **Semantic Layer** and a **Knowledge Plane** parallel to the data and control planes for 6G.
**Why a need of Semantic layer and data for 6G, and how AI could play in that modelling? Also, the lifecycle of Semantic Models in the RAN.**
* Semantic aware MAC Scheduler: how MAC Scheduler will be semantic aware and what features and controls are needed for that, full design with signaling, benefits in terms user and RAN perspective. 

### Literature review: gaps and design implications

The three semantic-communication PDFs in `references/papers/` provide complementary perspectives: S-RAN architecture and multi-user resource management [7], a broader Semantic RAN survey across radio, data-link, network, and security functions [9], and semantic protocols, protocol learning, and AI-enabled protocols in O-RAN/6G [8]. Together they identify several areas that this draft introduces but does not yet specify.

| Gap in this draft | What the papers highlight | Design implication |
|---|---|---|
| Encoder/decoder and knowledge-base compatibility | Semantic quality depends on the matched capabilities of both endpoints, their channel conditions, and aligned background knowledge. Knowledge bases may be service-specific, local, or personalized [7], [9]. | Identify the task, model/profile version, and relevant knowledge context before enabling semantic transmission; detect mismatch and provide a conventional or hybrid fallback. |
| Semantic service and UE-session relationship | The papers describe terminal-to-terminal, cross-RAN, and terminal-to-server scenarios, which may coexist with ordinary traffic [7]. | Treat a semantic session as application/task context associated with existing UE connectivity and QoS flows, not as a new UE session for each semantic object. The exact mapping and signaling remain an open design item. |
| Hybrid operation and mode selection | Semantic and conventional bit-oriented communication will coexist; the communication mode depends on service needs, knowledge/model availability, and RAN resources [7], [9]. | Add admission and mode selection, and retain a bit-oriented path when semantic processing is unavailable, unsuitable, or not worth its cost. |
| Compute and model lifecycle | Edge inference, model memory/compute demand, deployment, and model interoperability are practical constraints, not just radio concerns [8], [9]. | Track compute capacity and model residency alongside radio state; define model/profile onboarding, version alignment, update, rollback, and retirement. |
| Task-aware scheduling objective | Radio allocation must account for semantic utility, channel conditions, knowledge matching, and competition with conventional users; suitable semantic metrics remain an open research problem [7], [9]. | Define a measurable application utility and optimize it subject to radio, compute, latency, reliability, and fairness constraints. Do not equate semantic priority with an unconditional grant or retransmission. |
| Evaluation and standardization | Semantic KPIs, reproducible system-level evaluation, interoperability, and standardization are still developing [8], [9]. | Report task-level quality together with conventional QoS, radio/compute cost, fairness, and interoperability results. Treat proposed interfaces and metrics in this document as conceptual unless a standard specifies them. |
| Security and privacy | Semantic context, knowledge bases, model updates, and learned protocols add privacy and integrity risks [8], [9]. | Include authorization, data minimization, model/update integrity, and privacy review in the design and evaluation. |

#### Semantic service context and model alignment

A semantic session is an application-scoped context for a task or stream. It may be carried over an existing UE/PDU session and one or more QoS flows; multiple semantic tasks can share a UE session, and a task may use conventional communication instead. This is a proposed modeling convention, not a standardized 3GPP session type.

Before using a semantic profile, the communicating endpoints should agree on the task and compatible encoder/decoder profile, model version, and any required knowledge-base or context version. A conceptual service context can include a task/profile identifier, direction, requested task quality, deadline or freshness limit, and fallback policy. Avoid exposing payload meaning to the MAC through deep packet inspection; pass only authorized, validated metadata through an explicit interface. The metadata format and signaling procedure are not standardized by the cited papers.

The model and knowledge lifecycle should cover onboarding and compatibility checks, version distribution, runtime monitoring, controlled update/rollback, and retirement. Model placement may be at the device, RAN edge, or service/task server according to latency, compute, memory, energy, and privacy constraints. A model that is not resident or whose knowledge context is incompatible must not be assumed available to the scheduler.

#### Hybrid operation and semantic-aware scheduling

The scheduler should retain conventional QoS, channel, buffer, and reliability handling. A semantic-aware policy can rank eligible transmission actions by expected marginal task-utility improvement, then allocate resources subject to radio, latency, compute, energy, and fairness budgets. A conceptual objective is to maximize aggregate expected task utility; the utility function and constraints must be defined and measured for each service rather than assumed to be universal.

Candidate actions may include semantic encoding/profile selection, transmission size, radio resources, or conventional fallback. The policy must account for model/knowledge match, channel state, task age or deadline, and inference queue/cost. Heavy semantic inference and model adaptation belong outside the slot-critical scheduler unless their worst-case latency and compute budget are demonstrated. HARQ, DTX, carrier aggregation, and other MAC/PHY procedures remain subject to the applicable radio protocol; semantic value may inform eligible choices but must not silently override protocol-required behavior or service reliability.

#### Evaluation and security requirements

Evaluate semantic operation against a conventional bit-oriented baseline and a hybrid baseline under comparable service requirements. Report task-specific quality (for example, task accuracy or semantic similarity/distortion) alongside end-to-end latency, reliability, goodput, spectrum and energy efficiency, fairness, control overhead, inference latency, compute utilization, and model/knowledge mismatch. Test different channels, loads, user counts, mobility, and compute contention; do not present gains from an individual simulation or survey as guaranteed system-wide improvements. There is not yet a single standardized semantic KPI that covers all tasks [8], [9].

Semantic metadata, task intent, personalized knowledge, and model updates can reveal sensitive information or be manipulated. Apply least-privilege access, data minimization, integrity and provenance checks for model/profile updates, and privacy review for any stored or shared context. Include interoperability and failure/fallback behavior in test plans, not only semantic reconstruction quality.

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
       |  +-------------------------+                            |
       +--+-------------------------+----------------------------+
                       | N3S (Semantic User Plane Interface)
                       |
       +---------------------------------------------------------+
       |                                                         |
       |               6G Radio Access Network (gNB/aNB)         |
       |                                                         |
       |  +---------------------------------------------------+  |
       |  |            Semantic Central Unit (gNB-CU)         |  |
       |  |         - Semantic PDCP / SDAP                    |  |
       |  |         - Local Knowledge Base (L-KB)             |  |
       |  +---------------------------------------------------+  |
       |                           | F1-C / F1-U                 |
       |  +---------------------------------------------------+  |
       |  |            Distributed Unit (gNB-DU)              |  |
       |  |        - Semantic-aware MAC Scheduler             |  |
       |  +---------------------------------------------------+  |
       +---------------------------------------------------------+
```
The diagram is a conceptual target architecture, not a description of standardized 3GPP functions or interfaces. In particular, **Semantic UPF**, **N3S**, and the placement of semantic PDCP/SDAP are proposals in this draft, not interfaces or network functions defined by the cited PDFs. Preserve the existing RAN/core protocol path as the baseline and introduce semantic metadata or processing only through an agreed, authorized extension. O-RAN components such as SMO and RIC may support model/policy lifecycle functions where the deployment and timing allow; they are not prerequisites for every semantic service [8], [9].

## Semantic-aware MAC Scheduler design details
### Functional Architecture & Design

The details the internal architectural components and data flows of a **Semantic-Aware Medium Access Control (MAC) Scheduler** residing within the gNodeB Distributed Unit (gNB-DU). 

#### 1. Functional Design Block Diagram

The block diagram below details the architecture, component boundaries, and internal interfaces of the Semantic-Aware MAC Scheduler.

```
+---------------------------------------------------------------------------------+
|                                                                                 |
|                              [SEMANTIC MAC LAYER]                               |
|                   - Channel State Information (CSI) Estimation                  |
|          - Semantic Information Bottleneck / Compression Ratio Control          |
|     - Adaptive Power and Bandwidth Allocation based on Semantic Importance      |
+---------------------------------------------------------------------------------+
                                       |
                                       v
+---------------------------------------------------------------------------------+
|                                                                                 |
|                            [SEMANTIC PHY LAYER]                                 |
|                                                                                 |
|   [Source Data] ---> [Semantic Encoder] ---> [Power Norm] ---> [AWGN/Rayleigh]  |
|                             |                                        |          |
|   [Reconstructed Data] <-- [Semantic Decoder] <----------------------+          |
+---------------------------------------------------------------------------------+

```
* **Semantic Encoder (Transmitter)**
  * Maps raw data (e.g., an image, text tokens, or telemetry vector) directly to complex valued channel symbols (\(x \in \mathbb{C}\)), completely bypassing digital modulation schemes like QAM.
 
* **Channel Layer**
  * which simulates real world physical impairments: Additive White Gaussian Noise (AWGN), Rayleigh fading, and phase shifts.

* **Semantic Decoder (Receiver)**
  * It takes the corrupted channel symbols (y) and reconstructs the data.
  * Instead of minimizing bit-error rate (BER), it optimizes semantic similarity (e.g., MS-SSIM for images, BLEU for text, or classification accuracy for edge AI).

The direct-to-symbol path above is one Deep-JSCC-style research option, not a requirement for every semantic system and not a claim that deployed RANs should bypass standard channel coding or modulation. Semantic coding may also operate with conventional bit-level PHY/MAC mechanisms. The example channel models and CSI assumptions below are simulation choices; they do not represent full multi-user, end-to-end RAN channel behavior. Semantic metrics must be selected for the task and reported with radio reliability and resource costs [7], [9].

**Draft code(PyTorch blueprint with Deep JSCC (Joint Source-Channel Coding) Semantic PHY layer designed...)**

```python

import torch
import torch.nn as nn
import torch.optim as optim

class SemanticEncoder(nn.Module):
    """
    Semantic PHY Transmitter: Extracts structural features from raw data 
    and directly maps them to a set of continuous channel symbols.
    """
    def __init__(self, latent_dim=16):
        super(SemanticEncoder, self).__init__()
        # Feature extraction and semantic abstraction
        self.feature_extractor = nn.Sequential(
            nn.Conv2d(3, 32, kernel_size=5, stride=2, padding=2), # B, 32, 16, 16
            nn.ReLU(),
            nn.Conv2d(32, 64, kernel_size=5, stride=2, padding=2), # B, 64, 8, 8
            nn.ReLU(),
            nn.Flatten(),
            nn.Linear(64 * 8 * 8, 256),
            nn.ReLU(),
            nn.Linear(256, latent_dim * 2) # *2 to represent real and imaginary parts
        )
        self.latent_dim = latent_dim

    def forward(self, x):
        # Generate raw semantic symbols
        symbols = self.feature_extractor(x)
        
        # Power Normalization Constraint: Ensures the transmitter adheres to 
        # average power constraints E[||x||^2] <= 1
        symbols = symbols / torch.sqrt(torch.mean(symbols ** 2))
        return symbols

class WirelessChannel(nn.Module):
    """
    Physical Layer Wireless Channel Simulation.
    Supports dynamic SNR adjustments driven by the MAC layer.
    """
    def __init__(self):
        super(WirelessChannel, self).__init__()

    def forward(self, symbols, snr_db):
        # Calculate noise variance based on desired SNR
        # SNR = P_signal / P_noise. Since P_signal is normalized to 1:
        snr_linear = 10 ** (snr_db / 10.0)
        noise_std = torch.sqrt(torch.tensor(1.0 / snr_linear))
        
        # Generate AWGN matching the symbol shape
        noise = torch.randn_like(symbols) * noise_std.to(symbols.device)
        
        # Received corrupted symbols
        received_symbols = symbols + noise
        return received_symbols

class SemanticDecoder(nn.Module):
    """
    Semantic PHY Receiver: Maps raw corrupted channel symbols back 
    into the semantic reconstruction space.
    """
    def __init__(self, latent_dim=16):
        super(SemanticDecoder, self).__init__()
        self.latent_dim = latent_dim
        self.reconstructor = nn.Sequential(
            nn.Linear(latent_dim * 2, 256),
            nn.ReLU(),
            nn.Linear(256, 64 * 8 * 8),
            nn.ReLU(),
            nn.Unflatten(1, (64, 8, 8)),
            nn.ConvTranspose2d(64, 32, kernel_size=5, stride=2, padding=2, output_padding=1),
            nn.ReLU(),
            nn.ConvTranspose2d(32, 3, kernel_size=5, stride=2, padding=2, output_padding=1),
            nn.Sigmoid() # Bound output pixels to [0, 1]
        )

    def forward(self, x):
        return self.reconstructor(x)

class DeepJSCC_System(nn.Module):
    """
    Unified Semantic PHY-MAC Stack Framework
    """
    def __init__(self, latent_dim=16):
        super(DeepJSCC_System, self).__init__()
        self.encoder = SemanticEncoder(latent_dim)
        self.channel = WirelessChannel()
        self.decoder = SemanticDecoder(latent_dim)

    def forward(self, x, snr_db):
        transmitted_symbols = self.encoder(x)
        received_symbols = self.channel(transmitted_symbols, snr_db)
        reconstruction = self.decoder(received_symbols)
        return reconstruction

# =====================================================================
# Simulation and Training Loop Execution
# =====================================================================
if __name__ == "__main__":
    # Hyperparameters
    batch_size = 4
    latent_symbols = 32  # Number of channel symbols allocated by MAC
    target_snr_db = 10.0 # Current channel quality state
    
    # Initialize the End-to-End Stack
    device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
    semantic_stack = DeepJSCC_System(latent_dim=latent_symbols).to(device)
    
    # Loss objective is defined by semantic fidelity rather than simple bits
    criterion = nn.MSELoss() 
    optimizer = optim.Adam(semantic_stack.parameters(), lr=1e-3)
    
    # Dummy Batch representing an incoming video stream or sensor frame (32x32 pixels)
    mock_input_data = torch.rand(batch_size, 3, 32, 32).to(device)
    
    print(f"--- Starting Semantic PHY/MAC Pipeline Simulation ---")
    print(f"Input Data Shape: {mock_input_data.shape}")
    
    # Forward Pass through the semantic stack
    output_data = semantic_stack(mock_input_data, snr_db=target_snr_db)
    
    loss = criterion(output_data, mock_input_data)
    print(f"Initial Semantic Reconstruction Loss (MSE): {loss.item():.4f}")
    
    # Gradient Descent updating encoder/decoder jointly
    optimizer.zero_grad()
    loss.backward()
    optimizer.step()
    print("Optimization step completed successfully. Models adjusted for wireless impairments.")

```

**Also some Code with Rayleigh Fading & Phase Distortion (RayleighFadingChannel) in PyTorch**

```python

import torch
import torch.nn as nn
import torch.optim as optim

class SemanticEncoder(nn.Module):
    """
    Semantic PHY Transmitter: Maps source data to normalized complex-valued 
    channel symbols (represented as interleaved Real and Imaginary pairs).
    """
    def __init__(self, latent_dim=16):
        super(SemanticEncoder, self).__init__()
        self.latent_dim = latent_dim
        self.feature_extractor = nn.Sequential(
            nn.Conv2d(3, 32, kernel_size=5, stride=2, padding=2),
            nn.ReLU(),
            nn.Conv2d(32, 64, kernel_size=5, stride=2, padding=2),
            nn.ReLU(),
            nn.Flatten(),
            nn.Linear(64 * 8 * 8, 256),
            nn.ReLU(),
            nn.Linear(256, latent_dim * 2) # Latent dim * 2 (Real, Imaginary components)
        )

    def forward(self, x):
        symbols = self.feature_extractor(x)
        # Reshape to [Batch, Latent_Dim, 2] where dim=2 holds (Real, Imag)
        symbols = symbols.view(-1, self.latent_dim, 2)
        
        # Power Normalization Constraint: E[||x||^2] <= 1 across complex space
        power = torch.sqrt(torch.mean(symbols ** 2))
        symbols = symbols / power
        return symbols

class RayleighFadingChannel(nn.Module):
    """
    Simulates a flat Rayleigh fading channel with phase distortion and AWGN.
    Y = H * X + N, where H is a complex Gaussian variable.
    """
    def __init__(self):
        super(RayleighFadingChannel, self).__init__()

    def forward(self, symbols, snr_db):
        batch_size, latent_dim, _ = symbols.shape
        device = symbols.device

        # 1. Generate Rayleigh Fading Coefficients (H = H_real + j*H_imag)
        # Independent zero-mean Gaussian variables with variance = 0.5 per dimension
        h_real = torch.randn(batch_size, latent_dim, 1, device=device) * 0.707
        h_imag = torch.randn(batch_size, latent_dim, 1, device=device) * 0.707
        
        x_real = symbols[:, :, 0:1]
        x_imag = symbols[:, :, 1:2]

        # 2. Apply Complex Multiplication (H * X) to simulate fading and phase rotation
        # (a + jb)(c + jd) = (ac - bd) + j(bc + ad)
        faded_real = (h_real * x_real) - (h_imag * x_imag)
        faded_imag = (h_imag * x_real) + (h_real * x_imag)
        faded_symbols = torch.cat([faded_real, faded_imag], dim=2)

        # 3. Additive White Gaussian Noise (AWGN)
        snr_linear = 10 ** (snr_db / 10.0)
        # Variance of noise per complex dimension
        noise_std = torch.sqrt(torch.tensor(1.0 / (2.0 * snr_linear), device=device))
        noise = torch.randn_like(faded_symbols) * noise_std

        received_symbols = faded_symbols + noise
        
        # Concatenate H components to pass perfect Channel State Information (CSI) to receiver
        h_complex = torch.cat([h_real, h_imag], dim=2)
        
        return received_symbols, h_complex

class SemanticDecoder(nn.Module):
    """
    Semantic PHY Receiver: Performs Zero-Forcing (ZF) Equalization using CSI,
    then translates the equalized symbols back into source data.
    """
    def __init__(self, latent_dim=16):
        super(SemanticDecoder, self).__init__()
        self.latent_dim = latent_dim
        
        self.reconstructor = nn.Sequential(
            nn.Linear(latent_dim * 2, 256),
            nn.ReLU(),
            nn.Linear(256, 64 * 8 * 8),
            nn.ReLU(),
            nn.Unflatten(1, (64, 8, 8)),
            nn.ConvTranspose2d(64, 32, kernel_size=5, stride=2, padding=2, output_padding=1),
            nn.ReLU(),
            nn.ConvTranspose2d(32, 3, kernel_size=5, stride=2, padding=2, output_padding=1),
            nn.Sigmoid()
        )

    def forward(self, y, h):
        # Perform Zero-Forcing Equalization: X_hat = Y / H
        # Complex division: (y_r + j*y_i) / (h_r + j*h_i)
        y_r, y_i = y[:, :, 0:1], y[:, :, 1:2]
        h_r, h_i = h[:, :, 0:1], h[:, :, 1:2]
        
        denom = h_r**2 + h_i**2 + 1e-6 # Added epsilon to prevent division by zero
        
        eq_real = (y_r * h_r + y_i * h_i) / denom
        eq_imag = (y_i * h_r - y_r * h_i) / denom
        
        equalized_symbols = torch.cat([eq_real, eq_imag], dim=2)
        
        # Flatten the symbols for the neural network layers
        flattenED_symbols = equalized_symbols.view(-1, self.latent_dim * 2)
        return self.reconstructor(flattenED_symbols)

class DeepJSCC_Rayleigh_System(nn.Module):
    def __init__(self, latent_dim=16):
        super(DeepJSCC_Rayleigh_System, self).__init__()
        self.encoder = SemanticEncoder(latent_dim)
        self.channel = RayleighFadingChannel()
        self.decoder = SemanticDecoder(latent_dim)

    def forward(self, x, snr_db):
        tx_symbols = self.encoder(x)
        rx_symbols, csi = self.channel(tx_symbols, snr_db)
        reconstruction = self.decoder(rx_symbols, csi)
        return reconstruction

# =====================================================================
# Simulation Pipeline Execution
# =====================================================================
if __name__ == "__main__":
    batch_size = 4
    latent_symbols = 32  
    target_snr_db = 15.0 # Higher SNR typically required to combat fading deep nulls
    
    device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
    semantic_stack = DeepJSCC_Rayleigh_System(latent_dim=latent_symbols).to(device)
    
    criterion = nn.MSELoss()
    optimizer = optim.Adam(semantic_stack.parameters(), lr=1e-3)
    
    # Generate mock tensor data
    mock_input_data = torch.rand(batch_size, 3, 32, 32).to(device)
    
    print("--- Starting Semantic Rayleigh Pipeline Simulation ---")
    
    # Execute end-to-end forward step
    output_data = semantic_stack(mock_input_data, snr_db=target_snr_db)
    
    loss = criterion(output_data, mock_input_data)
    print(f"Reconstruction Loss under Rayleigh Fading: {loss.item():.4f}")
    
    # Backpropagation to allow model to adapt to equalization residuals
    loss.backward()
    optimizer.step()
    print("Pipeline successfully handled fading, phase distortion, and backprop adjustments.")

```

**Semantic MAC Scheduler view**

```
   +-------------------------------------------------------------+
   |                  gNB-DU Upper Layers (RLC)                  |
   +-------------------------------------------------------------+
                                |
                                | RLC SDUs & Packet Metadata
                                v
+--------------------------------------------------------------------+
| gNB-DU MAC LAYER(L2)                                               |
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
  * **Header Inspection:** The original proposal considers deep encapsulation or explicit metadata marks passed from SDAP or user-plane transport layers. For an implementable design, prefer explicit, authorized metadata; do not assume deep payload inspection or a new header extension is standardized.
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
  * **Priority Adjustment:** The original proposal boosts allocation for a semantically critical block after a NACK. Treat this as a research policy to evaluate, not an unconditional override: retransmission and reliability behavior must remain within the applicable HARQ procedure and service contract.

## Semantic-Aware MAC Scheduler with HARQ, DTX, and Carrier Aggregation Constraints 

### System Architecture & Block Diagram
The Semantic-Aware MAC Scheduler inside the gNB Distributed Unit (gNB-DU) shifts the resource block allocation paradigm from classic raw-throughput optimization to the optimization of **Quality of Information (QoI)** and **Value of Information (VoI)**. This architectural extension explicitly models **16-process HARQ management**, **DTX (Discontinuous Transmission) recovery loops**, and **Carrier Aggregation (CA)** across multiple Component Carriers (CCs).

The fixed 16-process count and the proposed DTX recovery behavior in this illustration are not universal semantic-RAN requirements; supported HARQ processes and DTX behavior depend on the radio configuration and implementation. In particular, “blind retransmission” or power adjustment must not be treated as a generic DTX response. Keep CA and HARQ operation compliant with the applicable PHY/MAC procedures, and evaluate any semantic-aware retransmission policy against reliability, latency, and resource constraints.

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
7. Sun, Y., et al. (2024). *S-RAN: Semantic-Aware Radio Access Networks*. arXiv:2407.11161v2. https://arxiv.org/abs/2407.11161
8. Tahenni, A., et al. (2026). *A Survey on 6G and O-RAN Intelligence: Semantic Protocols, Protocol Learning, and AI-Enabled Semantic Protocols*. Computer Networks, 279, 112143. https://www.sciencedirect.com/science/article/pii/S1389128626001556 (https://doi.org/10.1016/j.comnet.2026.112143)
9. Meng, R., et al. (2025). *Semantic Radio Access Networks: Architecture, State-of-the-Art, and Future Directions*. arXiv:2512.20917v1. https://arxiv.org/abs/2512.20917
