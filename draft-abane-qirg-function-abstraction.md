---
title: "On Architecture Classification and Functional Abstraction in Quantum Networks"
abbrev: "Architecture Classification and Functional Abstraction"
category: info

docname: draft-abane-qirg-function-abstraction-latest
pi: [toc, sortrefs, symrefs]

submissiontype: IRTF

number:
date:
# date: 2022-02-02 -- date is filled in automatically by xml2rfc if not given
consensus: true
v: 3
area: "IRTF"
workgroup: "Quantum Internet Research Group"
keyword:
  - network architecture
  - quantum repeater
venue:
  group: "Quantum Internet Research Group"
  type: "Research Group"
  mail: "qirg@irtf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/qirg"
  github: "amar-ox/draft-abane-qirg-function-abstraction"
  latest: "https://amar-ox.github.io/draft-abane-qirg-function-abstraction/draft-abane-qirg-function-abstraction.html"

author:
 -
    fullname: "Amar Abane"
    organization: National Institute of Standards and Technology
    email: "amar.abane@nist.gov"

normative:

informative:
  I-D.draft-hajdusek-qirg-timing-physics:
  I-D.draft-cacciapuoti-qirg-quantum-native-architecture:
  kozlowski-qn-prot: DOI.10.1145/3386367.3431293
  van-meter-qi-arch: DOI.10.1109/QCE53715.2022.00055
  bacciottini-redip: DOI.10.1109/OJCOMS.2023.3345166
  cacciapuoti-beyond-layering: DOI.10.1109/TNSE.2026.3679795
  kumar-instr-set: DOI.10.1109/qcnc69040.2026.00176
  delle-os-qn-nodes: DOI.10.1038/s41586-025-08704-w


...

--- abstract

TBD


--- middle

# Introduction
Quantum networking research has produced a growing number of architectural proposals for the distribution of entanglement across multi-node networks. These proposals span different hardware platforms, protocol mechanisms, and operational models, ranging from connection-oriented architectures based on pre-established paths to more dynamic approaches relying on continuously generated resources. Despite these differences, many proposals share common assumptions regarding entanglement generation, entanglement swapping, resource management, and network coordination.

At the same time, there is currently no widely accepted framework for describing and comparing routing architectures across the quantum networking literature. Similar concepts are often described using different terminology, while architectural assumptions such as proactive versus reactive operation, centralized versus distributed control, or synchronous versus asynchronous execution are not always distinguished consistently. As a result, comparing architectures and identifying common building blocks remains challenging.

Another emerging trend is the separation between control-plane and data-plane functions, inspired in part by concepts from Software-Defined Networking (SDN). Several architectural proposals distinguish between processes responsible for path computation, resource coordination, policy decisions, and request handling, and processes responsible for executing quantum operations such as entanglement generation, entanglement swapping, purification, and state management. However, unlike classical packet networks where the data plane primarily performs forwarding, quantum-network data-plane processes may themselves involve complex coordination, scheduling, resource allocation, and local decision-making. Consequently, the boundary between control-plane and data-plane responsibilities remains an active area of research.

This document argues that a network-function-oriented view provides a useful abstraction for reasoning about quantum networking architectures. Quantum networking operations are not instantaneous actions but coordinated processes involving distributed resources, timing constraints, and interactions between multiple network entities. Identifying these operations as network functions, together with the interfaces through which they interact, provides a common vocabulary that can be applied across different architectural approaches.
The goal of this document is therefore not to propose a new architecture, protocol, or protocol stack. Instead, it aims to identify and classify recurring architectural patterns in quantum routing systems, and to derive a minimal set of common network functions and interfaces that appear across existing proposals. These abstractions are intended to facilitate comparison of routing architectures, support future standardization discussions, and provide a framework for describing both existing and future quantum networking systems.


# Requirements Language

{::boilerplate bcp14-tagged}


# Goals and Non-Goals of this Document

## Goals
* To identify classes of routing approaches used in quantum networks.
* To identify a minimal set of common quantum-network functions required to support end-to-end entanglement distribution across various routing classes.
* To identify the interfaces and interactions between these functions that appear across different architectural proposals.
* To provide a common terminology and framework that enables comparison of existing and future architectures.

## Non-Goals
* This document does not define a new routing protocol, control protocol, or data-plane protocol.
* It does not prescribe a particular implementation or architectural realization of any network function.
* This document does not propose a layered reference architecture or protocol stack for quantum networks. The identified functions and interfaces are intended as abstractions that can be realized in different ways by different architectural approaches.


# Related Work

## Standardization and Industry Efforts
Several standardization bodies and industry organizations have initiated efforts to define architectures, abstractions, and interfaces for quantum networks. These efforts provide important foundations for the field, but have largely focused on quantum key distribution (QKD) networks or early-stage quantum networking deployments.

Within the IETF, the Quantum Internet Research Group (QIRG) has developed architectural principles for entanglement-based networks and introduced concepts such as control-plane and data-plane separation, timing regimes, and quantum-native networking. These efforts provide an important conceptual foundation, although routing architectures and control abstractions remain active research topics.

In contrast, ETSI and ITU efforts have primarily focused on QKD networks, defining functional architectures, interfaces, orchestration mechanisms, and management frameworks for key generation and distribution. Similarly, IEEE initiatives such as IEEE P1913 focus on device management and control abstractions, extending established networking paradigms such as YANG and NETCONF to quantum communication systems. Industry initiatives, including GSMA, have proposed component-based and service-oriented frameworks for integrating quantum technologies into existing telecommunications infrastructures.

These efforts demonstrate growing convergence around concepts such as software-defined control, orchestration, and service abstractions. However, a common, technology-agnostic framework for classifying routing architectures and identifying reusable routing, control, and data-plane functions in entanglement-based quantum networks remains largely absent. This document builds upon these foundations by proposing elements of a general classification and functional abstraction framework for entanglement-based quantum networking.

## Architectural Proposals for Entanglement-Based Quantum Networks
Several architectural proposals have explored how entanglement-based quantum networks can be organized and operated. Although these works differ in their protocol mechanisms and architectural abstractions, they collectively highlight recurring network functions, control interactions, and design assumptions that motivate the abstractions developed in this document.

- {{kozlowski-qn-prot}} proposal focuses on end-to-end entanglement distribution through elementary entanglement generation and entanglement swapping. The architecture assumes a connection-oriented model in which an end-to-end path is established before entanglement operations are executed. Routing decisions are therefore made proactively, while the resulting instructions are communicated to the data plane through control-plane mechanisms. The work distinguishes centralized and distributed control-plane realizations, but largely treats path computation as an external function whose outputs populate data-plane structures.
A notable contribution of this proposal is the identification of the core operational capabilities required at quantum nodes, including elementary entanglement generation and entanglement swapping. These functions closely align with the network-function abstractions considered in this document. The proposal also introduces concepts such as entangled-pair identifiers and control-plane parameters for memory lifetime management, illustrating the need for explicit interfaces between routing, resource management, and quantum-operation execution.

- {{van-meter-qi-arch}} develops a programmable architecture based on RuleSets that coordinate distributed quantum operations. While RuleSets are intended to support distributed execution, they may also be generated by a centralized controller, allowing multiple control architectures to be realized. The architecture employs a two-pass request mechanism that is consistent with a proactive, connection-oriented routing model in which resources are prepared before end-to-end operations are executed.
The proposal identifies several fundamental network tasks, including link-level entanglement generation, entanglement extension through swapping, error management through purification, and control and management functions. These tasks correspond closely to the network-function perspective adopted in this document. Furthermore, RuleSet actions, conditions, and execution logic provide an implicit abstraction of network functions and their interactions. From the perspective of this I-D, RuleSets can be viewed as one possible mechanism for orchestrating network functions to realize different routing architectures.

- {{bacciottini-redip}} introduces a configurable protocol framework for scheduling purification and swapping operations. The protocol supports arbitrary swapping orders and purification strategies, enabling flexible realization of end-to-end entanglement distribution processes. Similar to previous architectures, REDiP follows a two-pass procedure in which requests are installed and resources reserved before execution, making it primarily applicable to proactive and connection-oriented routing models.
The protocol distinguishes between operating modes referred to as slotted and asynchronous, which correspond to different execution strategies for entanglement generation and processing. While these concepts overlap with the timing-related classifications discussed in this document, they illustrate the broader need for a consistent terminology separating routing decisions, execution context, and synchronization mechanisms. REDiP also moves much of the configurability from rule definitions into protocol data structures, allowing swapping and purification schedules to be expressed directly. The qubit lifecycle model developed in this document is influenced by concepts introduced in REDiP, particularly its treatment of configurable entanglement-processing workflows.

- The Quantum-Native Architectural Tenets and Philosophy for the Quantum Internet {{I-D.draft-cacciapuoti-qirg-quantum-native-architecture}} introduces the concept of quantum-native control plane, where quantum operations may be used not only for data plane services such as entanglement distribution, but also for control plane tasks including network-state acquisition, topology discovery, and routing. This approach is illustrated in a quantum internet architecture using virtual or continuously maintained entanglement resources, emphasizing long-lived network states rather than purely on-demand operations.
Building on this direction, the Beyond Layering architecture {{cacciapuoti-beyond-layering}} advocates moving beyond strict layered networking models in favor of architectures centered on quantum-specific abstractions and programmable coordination mechanisms. Rather than prescribing rigid protocol layers, it introduces meta-protocols responsible for orchestrating lower-level quantum operations across the network. From the perspective of this document, these meta-protocols can be interpreted as realizations of network functions and the interfaces through which they interact. This provides further evidence that a function-oriented abstraction can serve as a common framework across a wide range of architectural designs, independent of the specific protocol structure or implementation.

# Architectures Classification
Defining network functions and interfaces requires abstractions that are sufficiently general to accommodate both current architectural approaches and future developments. Since routing architectures differ in how routing decisions are made, where control logic is placed, and how network operations are coordinated, this document first introduces a classification of entanglement-routing architectures based on a set of orthogonal dimensions. The purpose of this classification is not to favor particular approaches, but to distinguish architectural choices from the underlying responsibilities that recur across architectures. The network functions and interfaces identified in the remainder of this document are therefore derived from capabilities that appear across multiple architectural classes, rather than from any single architectural proposal.

To classify entanglement routing architectures, we consider several orthogonal dimensions that capture different aspects of routing behavior, control placement, and system coordination. These dimensions are intentionally independent, allowing a routing architecture to be described without imposing a particular implementation model.

- *Routing timing* distinguishes between proactive and reactive routing. In proactive routing, routing information is produced before entanglement generation begins. Path computation may rely on physical topology information, expected link characteristics, or other pre-existing knowledge. In reactive routing, routing decisions are made after entanglement generation, typically based on the currently available entanglement resources and network state. This distinction therefore captures when routing information becomes available relative to entanglement generation.

- *Path decision model* distinguishes between connection-oriented and connectionless routing. In a connection-oriented model, an end-to-end route (or route segment) is selected and coordinated before entanglement forwarding or swapping begins. Resource reservation and path installation may be present but are not required; the defining property is that the route is decided before the request progresses through the network. In a connectionless model, no fixed end-to-end path is established in advance (route can change between rounds/sessions), and forwarding decisions may be taken independently at intermediate nodes. Both proactive and reactive routing architectures may employ either connection-oriented or connectionless operation. Consequently, proactive and reactive routing should be viewed as more abstract concepts, while connection-oriented and connectionless routing describe how routing decisions are applied once routing information becomes available.

- *Control placement* distinguishes between centralized and distributed control. This dimension primarily characterizes the control plane rather than the data plane. In distributed approaches, routing algorithms execute locally on network nodes using partial network information. In centralized approaches, a controller computes routes and coordinates network operations. While most quantum data-plane functions remain inherently distributed, the control plane may be implemented using either model.

- *Coordination model* distinguishes between synchronous and asynchronous operation. In synchronous architectures, network operations proceed in globally coordinated rounds or time slots shared among participating nodes. In asynchronous architectures, operations are triggered by events, such as successful entanglement generation, measurement outcomes, or control messages, without requiring globally synchronized execution. Both proactive and reactive routing architectures may operate in either synchronous or asynchronous environments.

- *Execution context* describes whether a process lies on the critical path of request servicing. Execution context of a process should not be confused with the coordination model (i.e., synchronous and asynchronous operation). Some functions, such as physical topology discovery or pre-computation of static routes, typically execute in the background. Other functions, such as reactive path computation, are often part of the critical path and directly influence request latency. Certain processes may operate in either context. For example, link benchmarking can be performed continuously in the background to maintain routing information (given enough resources and/or strategic triggering), or it can be invoked as part of servicing a specific request and therefore become a critical-path operation.

The relationship among these dimensions can be viewed hierarchically. Routing timing determines whether routing information is obtained proactively or reactively. Once routing information is available, the architecture may apply it through either a connection-oriented or connectionless path decision model. Control placement and coordination models then describe where routing logic executes and how network operations are coordinated in time. Execution context complements these dimensions by identifying whether a given process contributes directly to request latency.

Finally, it is useful to distinguish between knowledge of the physical topology and knowledge of the logical topology. Physical topology information describes the underlying network graph and relatively static link characteristics. Logical topology information reflects the currently available entanglement resources, including active Bell pairs, fidelity values, memory occupancy, and other dynamic state. Many routing proposals differ primarily in whether routing decisions are based on physical-topology information, logical-topology information, or a combination of both. This distinction often aligns with, but is not equivalent to, the proactive/reactive classification.

Some architectures employ quantum operations to gather routing information. Although these operations involve the quantum network itself, their purpose is control-plane information acquisition rather than data forwarding. Such mechanisms are therefore classified as quantum-native control-plane functions. For example, in Quantum BGP-like architectures, link benchmarking may be viewed as a quantum-native control-plane process that provides routing information used by path computation, rather than as part of the data plane. This interpretation preserves a clean separation between control-plane and data-plane functions while accommodating routing architectures that rely on quantum measurements to obtain routing state.


| Dimension           | Options                               | Meaning                                                                                     |
| ---------------     | ------------------------------------ | ------------------------------------------------------------------------------------------- |
| Routing timing      | Proactive / Reactive                 | When is routing information obtained?                                                       |
| Path decision model | Connection-Oriented / Connectionless | Is the route fixed before forwarding?                                                       |
| Control placement   | Centralized / Distributed            | Where is routing logic executed?                                                            |
| Coordination model  | Synchronous / Asynchronous           | How are operations coordinated in time?                                                     |
| Execution context   | Critical-path / Background           | Does the operation affect request latency? (i.e., does it occupy time from the data plane?) |


| Process                                                          | Timing        | Context                                    |
| ---------------------------------------------------------------- | ------------- | ------------------------------------------ |
| Physical topology discovery / Precomputed paths                  | Async         | Background                                 |
| Quantum Link Benchmarking                                        | Async         | Background (with strategy) / Critical path |
| Reactive path computation (including logical topology discovery) | Sync or Async | Critical path (with effect on fidelity)    |
| Proactive path computation                                       | Sync or Async | Critical path (without effect on fidelity) |



# Derivation of Network Functions from Architectural Responsibilities
Although the architectural proposals discussed in above differ in their protocol structures, control mechanisms, and operational assumptions, they exhibit a common set of responsibilities required to support end-to-end entanglement distribution. These responsibilities appear regardless of whether an architecture is centralized or distributed, proactive or reactive, connection-oriented or connectionless.

At a high level, an entanglement-routing system must first acquire and maintain information about the network. This includes both relatively static information, such as physical topology and link characteristics, and dynamic information, such as available entanglement resources, memory occupancy, and resource quality. These responsibilities correspond to routing-state management and topology-information functions.

The system must then determine how end-to-end requests should be realized using available resources. Depending on the architectural model, this may involve path computation, resource selection, policy enforcement, admission control, or route installation. While different proposals distribute these tasks differently between centralized controllers and network nodes, they collectively represent a routing and control responsibility.

Once a routing decision has been made, the network must execute the corresponding quantum operations. This requires creating elementary entanglement, extending entanglement through swapping, improving resource quality through purification or error-management procedures, and coordinating the associated signaling. These responsibilities form the operational core of the quantum data plane.

In parallel, the network must manage the quantum resources on which these operations depend. Architectures therefore require mechanisms for memory allocation, resource tracking, entanglement-state management, qubit lifecycle management, and mapping logical requests onto physical resources. While the details vary across proposals, resource management emerges as a recurring architectural responsibility.

Finally, all reviewed architectures require coordination functions that enable distributed operation. These include classical communication, synchronization, timing services, and interactions with underlying communication infrastructure used to establish quantum connectivity. Such functions do not directly create or consume entanglement, but provide the supporting capabilities necessary for other network functions to operate correctly.

These recurring responsibilities motivate the function-oriented abstraction adopted in this document. Rather than defining network behavior in terms of specific protocols or architectural layers, the following sections identify a minimal set of network functions that collectively realize these responsibilities and can be composed in different ways by different routing architectures.


# Network Functions and Interfaces

## Motivation
Recent work on programmable quantum repeater nodes and quantum network operating systems suggests that quantum networks should be described in terms of reusable functions and the interfaces used to invoke and coordinate them, rather than as monolithic protocols or hardware-specific operations. Kumar et al. {{kumar-instr-set}} propose a programmable execution model for repeater nodes, where higher-layer protocols invoke node capabilities through well-defined execution interfaces. Similarly, Delle Donne et al. {{delle-os-qn-nodes}} demonstrate the importance of separating application logic, network services, and node-local execution mechanisms, enabling applications to remain independent of underlying hardware implementations.

This perspective motivates the identification of reusable network functions such as elementary entanglement generation, entanglement swapping, purification, memory management, state preparation, and measurement and herald handling. These functions are generally not instantaneous and often require coordination, scheduling, and synchronization across distributed nodes and resources. Consequently, a quantum network architecture should identify not only the functions themselves but also the interfaces through which they are invoked, coordinated, and monitored.

Programmable node architectures may also expose operational capabilities such as calibration, diagnostics, performance verification, quality estimation, and fidelity witnessing. While such functions are not directly involved in entanglement delivery and are not further decomposed in the present model, they may become important architectural elements as quantum networks mature and may benefit from standardized interfaces in future work.

Finally, the execution of many network functions depends on timing relationships between lower-layer physical processes and higher-layer networking services {{I-D.draft-hajdusek-qirg-timing-physics}}. This suggests that timing and synchronization should be treated as supporting architectural capabilities that facilitate the orchestration of network functions across multiple timescales.

## Network Function Definitions
- Quantum Application Function (QAF): Originates end-to-end entanglement requests and consumes the resulting quantum networking service. Could represent a higher-level orchestrator or independent end-user applications.
- Quantum Controller Function (QCF): Responsible for path computation, policy enforcement, resource coordination, and installation of forwarding state. May be centralized or distributed.
- Quantum Routing Function (QRF): Maintains reachability information and routing state used to determine paths through the quantum network. This function may be implemented as part of the QCF.
- Routing Information Base Function (RIBF): Stores network reachability and routing information learned from controllers or routing protocols. Provides routing information to the Quantum Routing Function and other control-plane functions.
- Quantum Forwarding Function (QFF): Manages active entanglement paths and coordinates network operations such as entanglement generation, swapping, and purification according to installed forwarding state.
- Forwarding Information Base Function (FIBF): Maintains forwarding state used to process entanglement resources and associated signaling. Provides path-specific instructions used by the Quantum Forwarding Function during network operation.
- Quantum Multiplexing Function (QMF): Maps logical requests and paths onto available quantum resources and maintains the association between paths and qubits.
- Quantum Memory Management Function (QMMF): Manages the lifecycle of quantum memories, including allocation, release, operational state tracking, ownership, and entanglement metadata.
- Entanglement State Repository Function (ESRF): Maintains a consistent view of entanglement resources, including EPR identifiers, qubit mappings, connectivity information, fidelity metrics, and operational state. This function may be implemented as part of the QMMF.
- Qubit Lifecycle Management Function (QLMF): Maintains a finite state machine (FSM) of each qubit from raw to released operation states. This function may be implemented as part of the QMMF.
- Entanglement Generation Function (EGF): Establishes elementary entanglement with neighboring nodes and reports successful entanglement creation.
- Entanglement Swapping Function (ESF): Performs entanglement swapping operations and, sends and receives swapping signalings, and (may directly) update connectivity information resulting from successful swaps.
- Entanglement Purification Function (EPF): Executes purification protocols and maintains the associated operation state.
- Classical Communication Function (CCF): Provides transport of classical protocol messages required for coordination, heralding, routing, swapping, and purification.
- Quantum Communication Function (QComF): Interfaces with quantum channels and physical hardware to transmit and receive quantum states.
- Quantum Timing Function (QTF): Provides logical synchronization services required by time-slotted or phase-based protocols.
- Quantum Interconnect Function (QICF): The QICF provides dynamic optical connectivity between quantum-neighbor nodes and shared quantum resources, such as sources, Bell-state measurement devices, switches, or wavelength/time-bin resources. It enables elementary entanglement generation by establishing, reserving, or scheduling the physical quantum connectivity required by the EGF. The QICF may be realized through centralized control (on-demand or schedule-based), or distributed signaling (on-demand).


## Interfaces
- 0: end-to-end entanglement requests
  - Description: One or multiple requests
  - From: QAF (end-node) or another QCF (distributed)
  - To: QFF
  - Mode: request-response (centralized), two-pass (distributed)
  - Request content: source node, destination node, num pairs, min fidelity, rate, timeout
  - Response content: acknowledgment, other coordination information (distributed/two-pass)

- 1: slot/phase signal
  - Description: Signal beginning of a phase within a slot (synchronous)
  - From: QTF
  - To: EGF/ESF/EPF/QFF/QCF
  - Mode: signal/trigger
  - Content: phase code/duration

- 2: routing instructions
  - Description: Install/uninstall a path. Always leads to a FIB update since routing is stateful and FIB entries are used to guide swap heralding messages. FIB entries persistence may differ between connection-oriented, connectionless.
  - From: QCF (local or central)
  - To: QFF
  - Mode: request-response
  - Request content: pathId, instructions. Some instructions such as swapping order or purification scheme may not be possible in connectionless-proactive or distributed reactive.
  - Response content: acknowledgment

- 3: qubit-path multiplexing
  - Description: Get/Set multiplexing profile for a path. Mux calculates per-qubit assignment (e.g., from routing instructions).
  - From: QFF
  - To: QMF
  - Mode: read-write
  - Request content: pathId, multiplexing instructions or policies
  - Response content: acknowledgment (write), qubit addresses (read)

- 4: Tag/untag qubits (soft)
  - Description: Query/reserve qubit assignment to path/request. Clear qubit assignment.
  - From: QMF
  - To: QMMF
  - Mode: request-response (write)
  - Request content: qubit address, pathId
  - Response content: acknowledgment
  - Note: Soft means it does not allocate physical qubits yet.

- 5: activate/deactivate channel
  - Description: Initiate elementary EPR generation with a neighbor. Stop generating EPRs with a neighbor.
  - From: QFF
  - To: EGF
  - Mode: request-response
  - Request content: neighbor name/address or interface name. Only communication information is passed (no qubit address, etc.)
  - Response content: acknowledgment

- 6: send entanglement connectivity (link state)
  - Description: Send entanglement link state to controller (needed in reactive). Aggregated on synchronous, individual on asynchronous.
  - From: QFF (aggregated from 8 or 21)
  - To: QCF
  - Mode: notification
  - Content: qubit addresses and neighbors

- 7: allocate/deallocate qubits (hard)
  - Description: Physically acquire/release qubit(s) for entanglement attempt, swapping, or purification.
  - From: EGF, QSF, QPF
  - To: QMMF
  - Mode: request-response
  - Request content: qubit address
  - Response content: ack/nack

- 8: new elementary EPR
  - Description: Inform forwarder about newly generated EPR.
  - From: EGF
  - To: QFF
  - Mode: notification
  - Content: EPR name/Id, neighbor, qubit address
  - Note: can be removed and rely on 21

- 9: start purification - Initiator node
  - Description: Initiate purification with a remote node
  - From: EGF (if L2 only), QFF (if L3)
  - To: QPF
  - Mode: trigger/signal
  - Content: qubit address/ID, EPR ID, neighbor ID/address.
  - Notes:
    - Local/private information is not included in external messages (e.g., EPR ID or neighbor address instead of qubit address/ID)
    - QPF at responder node resolves EPR name to qubit local address via 11
    - At initiator: QPF gets the one EPR to purify (+ neighbors/path info) and selects qubits/EPRs (via 11) according to purification circuit/schedule.
    - At responder: QPF receives EPR IDs to purify/use and resolves qubit addresses (via 11)
    - After/during operation: QPF updates qubit FSMs (via 18)
    - Why QPF updates qubit FSM instead of QFF:
      - Purification does not change connectivity
      - QPF already interacts with qubit (knows qubit FSM state)
      - Keeps initiator-agnostic implementation/API
      - Keeps the QFF/EQF clean and updated via FSM updates

- 10a: Do swap
  - Description: Initiate swapping
  - From: QFF
  - To: QSF
  - Mode: trigger/signal
  - Content: 2 qubit addresses, 2 neighbor/peers address
  - Note: success/failure will be reflected in qubit FSM. QFF does not care about what happens to local qubits.

- 10b: New segment EPR
  - Description: Local qubit now entangled with a remote node. Results from receiving a SWAP_UPDATE.
  - From: QSF
  - To: QFF
  - Mode: notification
  - Content: Elementary EPR name, peer address
  - Note: QSF receives swap signaling. Assuming that QSF has access to FIB, it can directly update qubits FSMs (i.e., no need to send notification via 10b to QFF)

- 11: find qubits
  - Description: Find qubit addresses based on selection criteria.
  - From: EQF, QFF, QPF
  - To: QMMF
  - Mode: request-response (like a database search query)
  - Request content: EPR name, neighbor id, path id, channel id, #rounds, request id.
  - Response content: qubit addresses

- 12: send/receive classical message:
  - Description: Classical transport for heralding, purification, swap updates, routing
  - Bidirectional: QEF/QPF/QSF/QCF/QICF - CCF

- 13: qubit decohered/released
  - Description: qubit decohered (cut-off) or released (consumed).
  - From: QMMF
  - To: EQF, QPF
  - Mode: notification
  - Content: qubit address/ID.

- 14: QIC request (on-demand)
  - Description: Request dynamic quantum interconnect resources to establish optical connectivity with a neighbor for elementary entanglement generation.
  - From: EGF
  - To: QICF
  - Mode: request-response
  - Request content: Neighbor address/identifier, generation session identifier, optional resource requirements (e.g., BSM type, source type, wavelength, timeslot).
  - Response content: Connectivity ready, failed, released, and optional connectivity information.

- 15: QIC request (schedule-based)
  - Description: Reserve quantum interconnect resources and optical connectivity according to a planned entanglement service or schedule.
  - From: QCF (or higher-level orchestrator)
  - To: QICF
  - Mode: request-response
  - Request content: Schedule (time window, participating nodes, resource requirements, reservation constraints).
  - Response content: Ack/Nack.

- 16: send/receive quantum signal
  - Description: Quantum transport of photons for elementary entanglement generation
  - Bidirectional: QEF - QComF

- 17: Route advertisement (control plane distributed)
  - Description: link state or route advertisement
  - From: QCF
  - To: QCF
  - Mode: notification
  - Content: RIB entries, reachability advertisement (neighbor address, metric, et.)

- 18: Update qubit operational state
  - Description: update qubit FSM state
  - From: EQF, QFF, QPF, QSF
  - To: QMMF
  - Mode: set
  - Content: qubit address, new state

- 19: Read-write FIB
  - Description: Install/Uninstall/Read/Update FIB entry (or customized FIB lookup).
  - From: QFF, QSF
  - To: FIBF
  - Mode: set/get/delete/update
  - Content: FIB entry (pathId, source, destination, swap/purif instructions, QoS requirement, etc.), FIB entry Id, Lookup parameters, etc.

- 20: Read-write RIB
  - Description: Install/Uninstall/Read/Update RIB entry (or customized RIB lookup).
  - From: QCF
  - To: RIBF
  - Mode: set/get/delete/update
  - Content: RIB entry (routeId, source, destination, next hop, cost, etc.), RIB entry Id, Lookup parameters, etc.

- 21: Qubit operational state updated
  - Description: qubit FSM state changed
  - From: QMMF
  - To: QFF (optional: any other function)
  - Mode: notification
  - Content: qubit address, new state



# Use Cases
TBD

# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Security Considerations

TBD


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TBD
