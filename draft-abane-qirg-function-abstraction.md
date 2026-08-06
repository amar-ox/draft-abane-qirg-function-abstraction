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
  RFC9340:
  I-D.draft-hajdusek-qirg-timing-physics:
  I-D.draft-cacciapuoti-qirg-quantum-native-architecture:
  I-D.draft-kaws-qirg-advent-02:
  kozlowski-qn-prot: DOI.10.1145/3386367.3431293
  van-meter-qi-arch: DOI.10.1109/QCE53715.2022.00055
  bacciottini-redip: DOI.10.1109/OJCOMS.2023.3345166
  cacciapuoti-byd-lay: DOI.10.1109/TNSE.2026.3679795
  kumar-instr-set: DOI.10.1109/qcnc69040.2026.00176
  delle-os-qn-nodes: DOI.10.1038/s41586-025-08704-w
  pompili-network-stack: DOI.10.1038/s41534-022-00631-2
  dahlberg-netqasm: DOI.10.1088/2058-9565/ac753f
  abane-survey: DOI.10.1109/TQE.2025.3541123
  shi-etg-routing: DOI.10.1109/TNET.2023.3343748
  lui-qbgp: DOI.10.1109/INFOCOM52122.2024.10621359
  cicconetti-req-sched: DOI.10.1109/TQE.2021.3090532
  yang-async-etg-rtg: DOI.10.1116/5.0172819
  li-drm-etp: DOI.10.1109/TNET.2024.3507785
  xiao-conn-less: DOI.10.1109/MNET.2023.3321044




...

--- abstract

RFC 9340 {{RFC9340}} establishes the architectural principles of the Quantum Internet and identifies the fundamental responsibilities required to support entanglement-based networking. This document builds upon those principles by introducing a systematic classification of routing architectures and deriving a technology-independent reference model composed of reusable network functions and interfaces. Rather than proposing new architectural principles, it operationalizes those introduced in RFC 9340 into a functional abstraction suitable for comparing, describing, and implementing diverse quantum-network architectures.


--- middle

# Introduction
Quantum networking research has produced a growing number of architectural proposals for distributing entanglement across multi-node networks. These proposals differ in hardware platforms, protocol mechanisms, and operational models, yet they share common responsibilities such as entanglement generation, entanglement swapping, resource management, and network coordination. Despite this convergence, there is currently no common framework for describing and comparing routing architectures. Similar concepts are often expressed using different terminology, while architectural choices are not consistently distinguished.

Recent architectural proposals also increasingly separate control-plane and data-plane responsibilities. However, unlike classical packet networks, quantum-network data-plane operations may themselves require distributed coordination, scheduling, and resource management, making the boundary between control and execution an active area of research.

RFC 9340 establishes the architectural principles of entanglement-based quantum networking and identifies remote entanglement generation as the fundamental network service {{RFC9340}}. Building interoperable quantum networks requires implementation-independent and reusable service abstractions that express networking capabilities independently of the specific protocols and architectural mechanisms used to realize them.

This document builds upon the architectural principles introduced in RFC 9340 by introducing a classification framework for entanglement-routing architectures and deriving a technology-independent reference model composed of reusable network functions and interfaces. Rather than proposing a new architecture, protocol, or protocol stack, it identifies recurring architectural patterns, abstracts their common responsibilities into reusable functions, and defines the interfaces through which those functions interact. These abstractions provide a common vocabulary for describing, comparing, and composing diverse quantum networking architectures while supporting future standardization efforts.


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
Building on this direction, the Beyond Layering architecture {{cacciapuoti-byd-lay}} advocates moving beyond strict layered networking models in favor of architectures centered on quantum-specific abstractions and programmable coordination mechanisms. Rather than prescribing rigid protocol layers, it introduces meta-protocols responsible for orchestrating lower-level quantum operations across the network. From the perspective of this document, these meta-protocols can be interpreted as realizations of network functions and the interfaces through which they interact. This provides further evidence that a function-oriented abstraction can serve as a common framework across a wide range of architectural designs, independent of the specific protocol structure or implementation.

# Architectures Classification

## Classification Dimensions

Defining network functions and interfaces requires abstractions that are sufficiently general to accommodate both current architectural approaches and future developments. Since routing architectures differ in how routing decisions are made, where control logic is placed, and how network operations are coordinated, this document first introduces a classification of entanglement-routing architectures based on a set of orthogonal dimensions. The purpose of this classification is not to favor particular approaches, but to distinguish architectural choices from the underlying responsibilities that recur across architectures. The network functions and interfaces identified in the remainder of this document are therefore derived from capabilities that appear across multiple architectural classes, rather than from any single architectural proposal.

To classify entanglement routing architectures, we consider several orthogonal dimensions that capture different aspects of routing behavior, control placement, and system coordination. These dimensions are intentionally independent, allowing a routing architecture to be described without imposing a particular implementation model.

- *Routing timing* distinguishes between proactive and reactive routing {{abane-survey}}. In proactive routing, routing information is produced before entanglement generation begins {{shi-etg-routing}} {{li-drm-etp}}. Path computation may rely on physical topology information, expected or collected link characteristics, or other pre-existing knowledge. In reactive routing, routing decisions are made after entanglement generation, typically based on the currently available entanglement resources {{cicconetti-req-sched}} {{yang-async-etg-rtg}}. This distinction therefore captures when routing information becomes available relative to entanglement generation.

- *Path decision model* distinguishes between connection-oriented and connectionless routing. In a connection-oriented model, an end-to-end route is selected and coordinated before entanglement forwarding or swapping begins {{van-meter-qi-arch}}. Resource reservation may be present but is not required; the defining property is that the route is decided before the request progresses through the network.

In a connectionless model, no fixed end-to-end path is imposed before the request progresses through the network {{xiao-conn-less}}. Instead, intermediate nodes may select the next operation, next hop, or route segment using their current local state and any available control-plane information {{lui-qbgp}}. Nodes maintain persistent or transient forwarding entries representing partial instructions rather than complete end-to-end routes.

The scope and persistence of forwarding state are realization characteristics rather than defining properties of the path decision model: both connection-oriented and connectionless architectures may use persistent or transient forwarding information.

Both proactive and reactive routing architectures may employ either connection-oriented or connectionless operation. Consequently, proactive and reactive routing should be viewed as more abstract concepts, while connection-oriented and connectionless routing describe how routing decisions are applied once routing information becomes available.

- *Control placement* distinguishes between centralized and distributed control. This dimension characterizes where routing decisions are computed rather than how much network information is available. In distributed approaches, routing algorithms execute independently on each network node. Nodes may rely on partial network knowledge, as in BGP-like protocols {{lui-qbgp}}, or complete network knowledge, as in OSPF-like link-state protocols {{I-D.draft-kaws-qirg-advent-02}}. In centralized approaches, a logically centralized controller computes routes and coordinates network operations. While the quantum data plane remains inherently distributed, the control plane may follow either model.

Note that connectionless operation does not preclude centralized control. A centralized controller may provide policies, constraints, preferred next hops, or other advisory *hints* without prescribing a complete path {{cacciapuoti-byd-lay}}.

- *Coordination model* distinguishes between synchronous and asynchronous operation. In synchronous architectures, network operations proceed in globally coordinated rounds or time slots shared among participating nodes {{shi-etg-routing}} {{cicconetti-req-sched}}. In asynchronous architectures, operations are triggered by events, such as successful entanglement generation, measurement outcomes, or control messages, without requiring globally synchronized execution {{li-drm-etp}} {{yang-async-etg-rtg}}. Both proactive and reactive routing architectures may operate in either synchronous or asynchronous environments.

The relationship among these dimensions can be viewed hierarchically. Routing timing determines whether routing information is obtained proactively or reactively. Once routing information is available, the architecture may apply it through either a connection-oriented or connectionless path decision model. Control placement and coordination models then describe where routing logic executes and how network operations are coordinated in time.


## Complementary Characteristics

The previous dimensions classify the overall routing architecture. Additional characteristics can further refine the classification by describing how routing processes are executed and what information they rely on.

*Execution context* describes whether a process lies on the critical path of request servicing. It should not be confused with the coordination model (i.e., synchronous and asynchronous operation), which describes *when* processes execute. In contrast, execution context describes whether a process directly contributes to request latency. Some functions, such as physical topology discovery or pre-computation of static routes, typically execute in the background. Other functions, such as reactive path computation, are often part of the critical path and directly influence request latency. Certain processes may operate in either context. For example, link benchmarking can be performed continuously in the background to maintain routing information, given sufficient resources or strategic triggering, or it can be invoked while serving a specific request, making it a critical-path operation. Execution context therefore complements the routing dimensions by identifying which processes directly impact request completion time.

Another useful characteristic is *network visibility*, which describes the scope of network information available to the routing process. Distributed routing does not necessarily imply partial network knowledge. For example, BGP-like routing relies primarily on partial information learned from neighboring nodes, whereas OSPF-like routing distributes complete topology information to all nodes, allowing each node to independently compute routes. Similarly, centralized controllers typically operate with a global view of the network, although architectures with incomplete or hierarchical views are also possible. Network visibility is therefore independent of control placement and characterizes the information available for routing decisions rather than where those decisions are computed.

Finally, it is useful to distinguish between knowledge of the *physical topology* and knowledge of the *logical topology*. Physical topology information describes the underlying network graph together with relatively static properties such as connectivity and expected link characteristics. Logical topology information reflects the currently available network state, including established entangled links, fidelity values, quantum memory occupancy, and other dynamic resources. Many routing proposals differ primarily in whether routing decisions rely on physical-topology information, logical-topology information, or a combination of both. This distinction often aligns with, but is not equivalent to, the proactive/reactive classification. Likewise, network visibility may apply to either topology representation, describing whether routing decisions are made using a local or global view of the physical or logical network state.


| Dimension           | Options                               | Meaning                                                                                     |
| ---------------     | ------------------------------------ | ------------------------------------------------------------------------------------------- |
| Routing timing      | Proactive / Reactive                 | When is routing information obtained?                                                       |
| Path decision model | Connection-Oriented / Connectionless | Is the route fixed before forwarding?                                                       |
| Control placement   | Centralized / Distributed            | Where is routing logic executed?                                                            |
| Coordination model  | Synchronous / Asynchronous           | How are operations coordinated in time?                                                     |


| Process                                                          | Timing        | Context                                    |
| ---------------------------------------------------------------- | ------------- | ------------------------------------------ |
| Physical topology discovery / Precomputed paths                  | Async         | Background                                 |
| Quantum Link Benchmarking                                        | Async         | Background (with strategy) / Critical path |
| Reactive path computation (including logical topology discovery) | Sync or Async | Critical path (with effect on fidelity)    |
| Proactive path computation                                       | Sync or Async | Critical path (without effect on fidelity) |



# Derivation of Network Functions from Architectural Responsibilities
Although the architectural proposals discussed in above differ in their protocol structures, control mechanisms, and operational assumptions, they exhibit a common set of responsibilities required to support end-to-end entanglement distribution. These responsibilities appear regardless of whether an architecture is centralized or distributed, proactive or reactive, connection-oriented or connectionless.

At a high level, an entanglement-routing system must first acquire and maintain information about the network. This includes both knowledge of the physical topology and knowledge of the logical topology. These responsibilities correspond to routing-state management and topology-information functions.

The system must then determine how end-to-end requests should be realized using available resources. Depending on the architectural model, this may involve path computation, resource selection, policy enforcement, admission control, or route installation. While different proposals distribute these tasks differently between centralized controllers and network nodes, they collectively represent a routing and control responsibility.

Once a routing decision has been made, the network must execute the corresponding quantum operations. This requires creating elementary entanglement, extending entanglement through swapping, improving resource quality through purification or error-management procedures, and coordinating the associated signaling. These responsibilities form the operational core of the quantum data plane.

In parallel, the network must manage the quantum resources on which these operations depend. Architectures therefore require mechanisms for memory allocation, resource tracking, entanglement-state management, qubit lifecycle management, and mapping logical requests onto physical resources. While the details vary across proposals, resource management emerges as a recurring architectural responsibility.

Finally, all reviewed architectures require coordination functions that enable distributed operation. These include classical communication, synchronization, timing services, and interactions with underlying communication infrastructure used to establish quantum connectivity. Such functions do not directly create or consume entanglement, but provide the supporting capabilities necessary for other network functions to operate correctly.

These recurring responsibilities motivate the function-oriented abstraction adopted in this document. Rather than defining network behavior in terms of specific protocols or architectural layers, the following sections identify a minimal set of network functions that collectively realize these responsibilities and can be composed in different ways by different routing architectures.


# Network Functions and Interfaces

## Motivation
Recent work on programmable quantum repeater nodes and quantum network operating systems suggests that quantum networks should be described in terms of reusable functions and the interfaces used to invoke and coordinate them, rather than as monolithic protocols or hardware-specific operations. Kumar et al. {{kumar-instr-set}} propose a programmable execution model for repeater nodes, where higher-layer protocols invoke node capabilities through well-defined execution interfaces. Similarly, Delle Donne et al. {{delle-os-qn-nodes}} demonstrate the importance of separating application logic, network services, and node-local execution mechanisms, enabling applications to remain independent of underlying hardware implementations.

This perspective motivates the identification of reusable network functions such as elementary entanglement generation, entanglement swapping, purification, memory management, state preparation, and measurement and herald handling. These functions are generally not instantaneous, not always deterministic, and may operate on different timescales. They therefore require coordination, scheduling, and synchronization across nodes and resources. Consequently, a quantum network architecture should identify not only the functions themselves but also the interfaces through which they are invoked, coordinated, and monitored.

The execution of many network functions depends on timing relationships between lower-layer physical processes and higher-layer networking services {{I-D.draft-hajdusek-qirg-timing-physics}}. This suggests that timing and synchronization should be treated as supporting architectural capabilities that facilitate the orchestration of network functions across multiple timescales.

Programmable node architectures may also expose operational capabilities such as calibration, diagnostics, performance verification, quality estimation, and fidelity witnessing. While such functions are not directly involved in entanglement delivery and are not further decomposed in the present model, they represent important architectural elements and may benefit from standardized interfaces in future work.

The realization of several network functions, particularly the Entanglement Generation Function (EGF), Entanglement Swapping Function (ESF), and Entanglement Purification Function (EPF), may rely on a common hardware abstraction layer that exposes the capabilities of the underlying quantum processor through implementation-independent interfaces. As illustrated by programmable node architectures such as QNodeOS {{delle-os-qn-nodes}}, such an abstraction may provide access to physical operations including qubit initialization and measurement, state movement, quantum gates, and physical-layer entanglement-generation attempts, while exposing hardware capabilities such as qubit topology, supported operations, coherence properties, and expected operation quality. Although the realization of this abstraction is hardware dependent and outside the scope of this document, it provides a common execution substrate upon which multiple network functions may be implemented.

The network functions defined in this document should be viewed as architectural abstractions rather than individual software modules or protocols. One possible realization is through a network-oriented execution environment, where network functions are implemented as coordinated quantum and classical programs executing on a quantum network processing unit (QNPU) {{dahlberg-netqasm}}. In such systems, a program consists of quantum code blocks, containing quantum operations interleaved with classical control, together with purely classical code blocks responsible for coordination and communication. The QNPU executes hardware-dependent quantum operations through lower-layer execution interfaces. This separation enables network functions to remain portable across hardware platforms while allowing hardware-specific optimizations within the underlying execution environment.


## Network Function Definitions
- Quantum Application Function (QAF): Originates end-to-end entanglement requests and consumes the resulting quantum networking service. Could represent a higher-level orchestrator or independent end-user applications.
- Quantum Controller Function (QCF): Responsible for path computation or routing guidance, policy enforcement, resource coordination, admission control, and, where applicable, installation of forwarding state. Depending on the routing architecture, the QCF may provide complete path instructions, partial route or action instructions, or advisory hints that guide node-local forwarding decisions. It may be centralized or distributed.
- Quantum Forwarding Function (QFF): Coordinates the progression of entanglement requests and resources through the network. It invokes operations such as entanglement generation, swapping, and purification according to available forwarding state, local entanglement state, and applicable control-plane instructions or policies. Depending on the path decision model, it may follow an installed end-to-end path or make node-local decisions for the next hop, route segment, or quantum operation.
- Forwarding Information Base (FIB): Maintains forwarding information used by the Quantum Forwarding Function during request and entanglement-resource processing. Entries may contain complete path instructions, partial route or next-hop instructions, operation dependencies, policies, or other forwarding context. Depending on the architecture, entries may be persistent, soft-state, request-scoped, or created transiently from local network state.
- Quantum Multiplexing Function (QMF): Maps logical requests and paths onto available quantum resources and maintains the association between paths and qubits.
- Quantum Memory Management Function (QMMF): Manages the lifecycle of quantum memories, including allocation, release, ownership, operational state tracking, and entanglement metadata. The function may virtualize qubit addressing by mapping logical qubit identifiers to physical quantum memories, and may maintain implementation-specific information about available quantum resources, such as qubit capabilities, lifetime, or placement, to support resource allocation and scheduling.
  - Entanglement State Repository Function (ESRF): Maintains a consistent view of entanglement resources, including EPR identifiers, qubit mappings, connectivity information, fidelity metrics, and operational state. This function may be implemented as part of the QMMF.
  - Qubit Lifecycle Management Function (QLMF): Maintains a finite state machine (FSM) of each qubit. This function may be implemented as part of the QMMF.
- Entanglement Generation Function (EGF): Establishes elementary entanglement with neighboring nodes and reports successful entanglement creation.
- Entanglement Swapping Function (ESF): Performs entanglement swapping operations and, sends and receives swapping signalings, and (may directly) update connectivity information resulting from successful swaps.
- Entanglement Purification Function (EPF): Executes purification protocols and maintains the associated operation state.
- Classical Communication Function (CComF): Provides transport of classical protocol messages required for coordination, heralding, routing, swapping, and purification.
- Quantum Communication Function (QComF): Interfaces with quantum channels and physical hardware to transmit and receive quantum states.
- Quantum Timing Function (QTF): Provides logical synchronization services required by time-slotted or phase-based protocols.
- Quantum Interconnect Function (QICF): Provides dynamic optical connectivity between quantum-neighbor nodes and shared quantum resources, such as sources, Bell-state measurement devices, switches, or wavelength/time-bin resources. It enables elementary entanglement generation by establishing, reserving, or scheduling the physical quantum connectivity required by the EGF. The QICF may be realized through centralized control (on-demand or schedule-based), or distributed signaling (on-demand).


## Functional Interfaces
The following interfaces illustrate the interactions between the network functions introduced in the previous section. They describe the information exchanged between functions rather than prescribing protocol messages or APIs. Different implementations may realize these interactions through procedure calls, local software interfaces, distributed protocols, or other mechanisms while preserving the same architectural responsibilities.


- 0a: End-to-end entanglement request
  - From: QAF (initiator) or QCF (at intermediate node, in distributed)
  - To: QCF

This interface initiates the provisioning of an end-to-end entanglement service. It conveys the communication requirements of an application, such as the communicating end nodes, the requested number of EPR pairs, fidelity and rate objectives, and other quality-of-service constraints. The Quantum Controller Function processes the request according to the routing architecture and available network resources. In centralized architectures, this interaction typically follows a request-response model, whereas in distributed architectures it may participate in a multi-pass reservation or coordination procedure before entanglement distribution begins.

The interaction between QAF and QCF may use entanglement request (ER) sockets {{delle-os-qn-nodes}} {{dahlberg-netqasm}}, providing a persistent communication context over which multiple end-to-end entanglement requests may be issued. Such abstractions are compatible with, but not required by, the reference model defined in this document.

- 0b: End-to-end entanglement ready
  - From: QCF
  - To: QAF

This interface notifies the application of the outcome of an end-to-end entanglement request. The notification may indicate the completion of the entire request, with all requested end-to-end entangled pairs successfully established, or may report individual end-to-end entangled pairs as they become available. The response provides the information required by the application to access and consume the delivered entangled resources, or indicates that the request could not be fulfilled. After consuming an entangled qubit, the application is expected to release it, allowing the associated qubit to return to the available resource pool.

- 1: slot/phase signal
  - From: QTF
  - To: EGF/ESF/EPF/QFF/QCF

This interface provides logical timing information required by synchronized network operations. It informs participating network functions of the beginning of a communication slot, protocol phase, or synchronization interval, allowing them to coordinate operations such as elementary entanglement generation, swapping, purification, or forwarding. The interface is primarily applicable to synchronous architectures, while asynchronous systems may instead rely on event-driven triggering mechanisms.

- 2: Routing or forwarding instructions
  - From: QCF (local or central)
  - To: QFF

This interface provides information used by the QFF to progress an entanglement request. Depending on the routing architecture, it may install or remove a complete end-to-end path, provide instructions for a route segment or next-hop decision, or convey policies and advisory hints used during node-local forwarding.
In connection-oriented approaches, it typically installs complete forwarding instructions, including path (or request) identifiers with forwarding policies such as swapping order, purification strategy, or other execution instructions.
In connectionless architectures, the information need not determine a fixed path and may instead constrain or optimize decisions made from the current local entanglement and resource state.
Invoking this interface typically results in an update of the Forwarding Information Base (FIB), which maintains the forwarding state used during network operation, including the information required to process purification and swap-heralding messages. The persistence of FIB entries is architecture dependent, with some approaches maintaining forwarding state throughout the lifetime of a connection while others install or update entries on a more transient basis.

- 3: qubit-path multiplexing
  - From: QFF
  - To: QMF

This interface associates logical paths or service requests with available quantum resources. It allows the Quantum Forwarding Function to create, update, or retrieve multiplexing information describing how requests are mapped onto physical qubits. The Quantum Multiplexing Function may compute these mappings according to routing instructions, resource availability, or implementation-specific allocation policies.

- 4: Tag/untag qubits (soft)
  - From: QMF
  - To: QMMF

This interface reserves or releases logical associations between qubits and service requests without allocating the underlying physical resources. It allows the Quantum Multiplexing Function to record ownership or intended usage of qubits while postponing physical allocation until network operations are about to begin. This distinction enables routing, multiplexing, and resource planning to be performed independently from the execution of quantum operations.

- 5: activate/deactivate channel
  - From: QFF
  - To: EGF

This interface controls the elementary entanglement generation for a given neighbor, channel, or generation context. The request identifies the *communication endpoint*, such as a neighboring node or communication interface, while the specific qubit addresses or the realization of the corresponding physical operations remain internal to the Entanglement Generation Function. The interface may be invoked for a single request, a communication session, or as part of a persistent forwarding policy.

In an event-driven realization, the QFF directly instructs the EGF to start or stop generation. Alternatively, the EGF may periodically poll the QFF for pending generation requests or updated activation state. In both cases, the QFF provides the generation intent and relevant communication context, while the EGF manages the execution of the elementary entanglement-generation process.

- 6: send entanglement connectivity (link state)
  - From: QFF (aggregated from 8 or 21)
  - To: QCF

This notification interface reports newly established elementary entanglement resources to the Quantum Controller Function. The information contributes to the controller's view of the logical network topology and may be used by routing algorithms operating on currently available entanglement resources. In synchronous architectures, multiple updates may be aggregated and reported periodically, whereas asynchronous architectures typically report new entanglement resources as they become available.

- 7: allocate/deallocate qubits (hard)
  - From: EGF, ESF, EPF
  - To: QMMF

This interface performs the physical allocation and release of quantum memory resources required to execute network operations. Unlike the logical reservation performed through Interface 4, this interaction acquires or frees actual qubits that will participate in entanglement generation, swapping, purification, or related operations. The Quantum Memory Management Function validates resource availability, updates ownership information, and returns the outcome of the allocation request.

- 8: new elementary EPR
  - From: EGF
  - To: QFF

This notification interface informs the Quantum Forwarding Function that a new elementary entangled pair has been successfully established with a neighboring node. The notification typically identifies the newly created EPR resource together with the associated neighbor and local qubit. The forwarding function uses this information to update the logical view of available entanglement resources and determine whether subsequent operations, such as purification or swapping, can proceed. In implementations where the Quantum Memory Management Function already reports qubit state changes (Interface 21), this interface may be optional or merged with those notifications.

- 9: start purification - Initiator node
  - From: EGF (if L2 only), QFF (if L3 involved)
  - To: EPF

This interface initiates an entanglement purification procedure. Depending on the architectural realization, purification may be triggered immediately after elementary entanglement generation (by the EGF) or for any entanglement segment as part of an end-to-end forwarding process (by the QFF). The request identifies the EPR to be purified together with the corresponding neighboring node. The Entanglement Purification Function is responsible for selecting the required entangled pairs (via Interface 11), coordinating the purification protocol with the remote node, and updating the operational state of the participating qubits (via Interface 18). Local details, such as qubit addresses, remain internal to each node and are resolved through interactions with the Quantum Memory Management Function (via Interface 11). Assigning qubit state management to the EPF, rather than the QFF, reflects the separation of responsibilities adopted in this model: purification modifies the state and quality of quantum resources but does not alter the network connectivity. Since the EPF already manages the participating qubits throughout the purification procedure, it is the natural function to update their lifecycle state, keeping the QFF focused on connectivity management and independent of purification-specific implementation details.

- 10a: Do swap
  - From: QFF
  - To: ESF

This interface triggers an entanglement swapping operation at an intermediate node. The request specifies the local qubits participating in the Bell-state measurement together with the corresponding neighboring entanglement segments. The Entanglement Swapping Function performs the required local operations and handles the associated classical signaling with adjacent nodes. The Quantum Forwarding Function is responsible for deciding when swapping should occur, while the detailed execution and management of the local quantum operations remain internal to the Entanglement Swapping Function. The outcome of the operation, including success or failure, is reflected through updates to the qubit operational state (via Interface 18) rather than through this interface itself. This separation allows the forwarding function to remain concerned with the evolution of network connectivity, while the lifecycle of the local qubit is managed independently by the resource management mechanisms.

- 10b: New segment EPR
  - From: ESF
  - To: QFF

This notification interface reports the successful completion of an entanglement swapping operation and the creation of a longer entanglement segment. The notification allows the Quantum Forwarding Function to update its view of the logical connectivity and determine whether additional swapping/purification operations are required. If the Entanglement Swapping Function has direct access to the FIB, these updates may be performed internally, making this notification optional.

- 11: find qubits
  - From: EGF, QFF, EPF
  - To: QMMF

This interface provides lookup services for quantum resources maintained by the Quantum Memory Management Function. Rather than referencing physical qubits directly, requesting functions may specify higher-level selection criteria, such as EPR identifiers, neighboring nodes, paths, communication channels, or request identifiers. The Quantum Memory Management Function resolves these logical references into the corresponding physical qubit addresses while hiding implementation-specific details of resource organization.

- 12: send/receive classical message:
  - Bidirectional: QEF/EPF/ESF/QCF/QICF - CComF

This interface provides the transport of classical protocol messages required to coordinate distributed quantum networking operations. It supports signaling associated with elementary entanglement generation, heralding, entanglement swapping, purification, routing, resource coordination, and other control procedures. The Classical Communication Function abstracts the underlying communication infrastructure, allowing network functions to exchange protocol information without depending on a particular classical networking technology or transport protocol.

- 13: qubit decohered/released
  - From: QMMF
  - To: EGF, EPF

This notification interface informs that a quantum resource has become unavailable, either because it has decohered beyond an acceptable threshold or because it has been explicitly released following consumption or protocol completion. Such notifications allow higher-level functions to terminate pending operations, release associated resources, or initiate recovery procedures when required. The criteria for generating these notifications depend on the implementation-specific management of quantum memories and resource lifetimes.

- 14: QIC request (on-demand)
  - From: EGF
  - To: QICF

This interface requests the establishment of the optical connectivity required to perform elementary entanglement generation with a neighboring node. Depending on the underlying interconnect architecture, the Quantum Interconnect Function may configure optical switches, reserve shared Bell-state measurement devices or photon sources, allocate wavelength or time-bin resources, or perform other implementation-specific actions required to establish connectivity. The response indicates whether the requested connectivity has been successfully established, failed, or has subsequently been released. This interface is intended for *on-demand* operation initiated directly by the Entanglement Generation Function.

- 15: QIC request (schedule-based)
  - From: QCF (or higher-level orchestrator)
  - To: QICF

This interface reserves quantum interconnect resources according to a planned communication schedule rather than an immediate entanglement request. It enables controllers or higher-level orchestration functions to coordinate optical connectivity in advance. The request may specify reservation windows, participating nodes, resource requirements, or scheduling constraints, while the Quantum Interconnect Function is responsible for translating these requests into the appropriate interconnect configuration and resource reservations.

- 16: send/receive quantum signal
  - Bidirectional: EGF - QComF

This interface provides access to the underlying quantum communication hardware responsible for transmitting and receiving quantum states. It enables the Entanglement Generation Function to initiate the physical exchange of photons required for elementary entanglement generation while abstracting the details of the communication technology. The Quantum Communication Function encapsulates hardware-specific operations, allowing higher-layer network functions to remain independent of the physical transmission medium or qubit platform.

- 17: Route advertisement (distributed control plane)
  - From: QCF
  - To: QCF

This interface exchanges routing or reachability information between Quantum Controller Functions operating in distributed control architectures. It enables controllers to disseminate routing state, topology information, or routing metrics required to compute or update forwarding decisions. The exact information exchanged depends on the routing protocol and may include reachability advertisements, link-state information, routing metrics, or other control-plane data. This interface is not required in architectures relying on a logically centralized controller.

- 18: Update qubit operational state
  - From: EGF, QFF, EPF, ESF
  - To: QMMF

This interface updates the operational state maintained for individual qubits throughout their lifetime. Network functions invoke this interface whenever a protocol operation changes the status of a qubit, such as after successful entanglement generation, purification, swapping, measurement, or resource consumption. The Quantum Memory Management Function maintains a consistent view of the qubit lifecycle, allowing resource management and other network functions to operate using a common representation of resource state.

- 19: Read-write FIB
  - From: QFF, ESF
  - To: FIB

This interface reads, installs, updates, or removes forwarding information. The stored information may represent a complete path, a route segment, a next-hop or operation instruction, or forwarding policies and constraints. Entries may be persistent, soft-state, request-scoped, or transient, depending on the routing and execution model. A connectionless realization may therefore use the FIB without requiring the storage of a fixed end-to-end path.

- 20: Read-write RIB
  - From: QCF
  - To: RIB

This interface provides access to the Routing Information Base (RIB) used by the Quantum Controller Function. It supports the creation, modification, removal, and lookup of routing entries representing network reachability and routing information. The Routing Information Base may contain routes learned through distributed routing protocols, computed by a centralized controller, or derived from other routing mechanisms.

- 21: Qubit operational state updated
  - From: QMMF
  - To: QFF (or any other function)

This notification interface informs network functions that the operational state of a qubit has changed. Such changes may result from successful entanglement generation, resource allocation or release, decoherence, timeout, or other events affecting the qubit lifecycle. These notifications allow dependent network functions to react asynchronously to resource state changes without continuously querying the Quantum Memory Management Function. Depending on the implementation, additional network functions beyond the Quantum Forwarding Function may subscribe to these notifications.


~~~ ascii-art

 +------+       0a        +------+                +----------+
 | QAF  |---------------> | QCF  |<------17------>| Peer QCF |
 |      |<--------------- |      |<------0a------>|          |
 +------+       0b        +--+---+                +----------+
                              |
                +-------------+-------------+
                |             |             |
              2 |           15|           20|
                v             v             v
             +------+      +------+      +------+
             | QFF  |      | QICF |      | RIB  |
             +--+---+      +--+---+      +------+
                |            ^
       +--------+--------+   |
       |        |        |   | 14
     3 |     10a|      5 |   |
       v        v        v   |
    +-----+   +-----+  +-----+
    | QMF |   | ESF |  | EGF |
    +--+--+   +--+--+  +--+--+
       |         |        |
     4 |      10b|      8 |
       v         v        v
    +------+    QFF      QFF
    | QMMF |
    +--+---+
       |
     21|
       v
      QFF


 Additional interfaces:

   6    QFF --------------------------> QCF
        Send entanglement-connectivity information

   7    EGF, ESF, EPF ---------------> QMMF
        Allocate or deallocate qubits

   9    EGF, QFF ---------------------> EPF
        Start purification at the initiator node

  11    EGF, QFF, EPF ---------------> QMMF
        Find qubits

  13    QMMF -------------------------> EGF, EPF
        Report that a qubit decohered or was released

  18    EGF, QFF, EPF, ESF ----------> QMMF
        Update qubit operational state

  19    QFF, ESF <-------------------> FIB
        Read or write forwarding state

  20    QCF <------------------------> RIB
        Read or write routing state
~~~

Figure 1: Network functions and their logical interfaces.
Interfaces involving several source or destination functions are listed
below the main diagram to preserve readability.



~~~ ascii-art
                         +---------+
                         | RELEASE |
                         +----+----+
                              |
                              v
                         +---------+
                  +----->|   RAW   |<-----+
                  |      +----+----+      |
                  |           |           |
                  v           v           |
              +--------+   +----------+   |
              | ACTIVE |-->| RESERVED |---+
              +---+----+   +----+-----+
                                |
                                |
                                v
                          +-----------+
                          | ENTANGLED |
                          +-----+-----+
                                |
                                v
                           +---------+
                      +--->|  PURIF  |
                      |    +----+----+
                      |         |
                      v         v
                 +---------+ +----------+
                 | PENDING | | ELIGIBLE |
                 +---------+ +----+-----+
                                   |
                            +------+------+
                            |             |
                            v             v
                       +---------+   +----------+
                       | CONSUME |   | SWAPPING |
                       +---------+   +----------+

   Transitions to RELEASE:

      ENTANGLED, PURIF, PENDING, ELIGIBLE,
      CONSUME, SWAPPING -> RELEASE


Qubit Ownership:
    QMMF:       RAW, RELEASE
    EGF:        ACTIVE, RESERVED
    QFF:        ENTANGLED,  ELIGIBLE
    QPF:        PURIF, PENDING
    QSF:        SWAPPING
    QAF:        CONSUME
~~~

Figure 2: Possible Qubit State Machine used by the Qubit Lifecycle Management Function (QLMF).

# Use Cases
The functions defined in this document may be composed differently depending on network scale, topology, and operational requirements. The following illustrative use cases demonstrate how the reference model can accommodate different architectural settings.

- Quantum multicomputer or small quantum data center: Communication is limited to direct entanglement between nodes connected through the optical interconnect. The QICF may be centralized and schedule access to switches, sources, Bell-state measurement devices, or other shared resources. Since entanglement routing through swapping is not used, QCF and QFF functions are not required.

- Large quantum data center or small QLAN: The architecture may support multi-hop entanglement distribution on top of direct optical connectivity. The QICF may be centralized and provide both scheduled and on-demand connectivity. A centralized QCF computes and coordinates entanglement routes, while the QFF on each node executes forwarding operations.

- Large QLAN or QWAN: Multi-hop entanglement routing is coordinated by a logically centralized QCF and executed by QFF instances at network nodes. The QICF may instead be distributed, with nodes and interconnect devices coordinating on demand to establish the optical connectivity required for elementary entanglement generation.

- Quantum Internet: Entanglement routing and forwarding may be distributed across nodes or administrative domains through distributed QCF and QFF instances. The QICF may not appear as an explicit network-level function because interconnect control is absent, local to individual links, or hidden behind the elementary entanglement-generation service.


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
