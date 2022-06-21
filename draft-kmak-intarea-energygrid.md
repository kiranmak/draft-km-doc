---
title: "Networks for Operating Energy grids"
abbrev: "energy grids"
category: info

docname: draft-kmak-intarea-energygrid-latest
submissiontype: independent
number:
date: 2022-06-14
consensus: false
v: 3
area: "Internet"
workgroup: "Internet Area Working Group"
keyword:
 - next generation
 - unicorn
venue:
  group: "Internet Area Working Group"
  type: "Working Group"
  mail: "int-area@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/int-area/"
  github: "kiranmak/draft-km-doc"
  latest: "https://kiranmak.github.io/draft-km-energygrid/draft-kmak-intarea-energygrid.html"

author:
 -
    fullname: Kiran Makhijani
    organization: Futurewei
    email: kiran.ietf@gmail.com

normative:

informative:

    IEC-SPEC:
     target: https://www.gegridsolutions.com/multilin/journals/issues/spring09/iec61850.pdf
     title: "IEC 61850 Communication Networks and Systems In Substations: An Overview for Users"
     author:
      -
        name: Drew Baigent
        org: GE Digital Energy
      -
        name: Mark Adamiak
        org: GE Digital Energy
     date: 2009

    SYNC: DOI.10.1109/ICIT.2013.6505875
    RT-MSGS: DOI.10.3390/en12122272
    SMV-ATTACKS: DOI.10.3390/en12193731
    FED-ENERGY-MGMT:
      target: https://www.nrel.gov/docs/fy02osti/31570.pdf
      title: Using Distributed Energy Resources, A how-to Guide for Federal Facility Managers.
    POWER-SUBS:
      title: 3rd Edition, Electric Power Substations Engineering
      author:
      -
       name: John D. McDonald
      date: 2012

--- abstract

This document describes the Operations and Control Network (OCN) scenario emerging from the modern or smart energy grid applications. These scenarios are derived to highlighting different behavior than the traditional networking leading to new requirements.
Traditional energy grids been a one way power distribution system, manged centrally. However, new sources of power generation and provisioning for efficient use of energy, requires 2-way communication between different the consuming and consuming entities in the power grid networks.


--- middle

# Introduction

The traditional power grids are a single-soruce centralized power distribution network. Power grid infrastructure involves a large power plant
generator and that power to be distributed over large geographical areas using transmission lines to the utility customer.

Essentially, the energy grid network is a
large scale control system which is evolving with a goal of efficiently utilizing every unit of
power generated.

Although demand for energy continues to grow, it is difficult to setup and expand
energy grids and transmission lines. However, several cases arise when power stations will need to
operate at full capacity or re-distribute energy load across different customer side substations. Different types of surge in power consumption must be done
dynamically with the help of automated controls and tools.

Secondly, infrastructure continues to evolve to connect new sources of energy into the grid. This also requires fine-grained control of switching power-sources based on pricing and on-demand parameters.

Electrical grids and substations present with difficult operating conditions for plant operators due to hazards related to high voltages and current exposure (???) when faults occur.
This document provides role of communication systems to support automation and remote operation scenarios in the electric power grids to facilitate above mentioned challenges.

This document provides a background of the power-grid architecture and discusses new scenarios requiring closer integration with the communication technologies.

# Conventions and Definitions

Protective Relays
 :  are used in the power grids to detect defects and abnormal conditions in lines or apparatus to initiate appropriate control circuit action.

Intelligent Electronic Devices (IEDs)
 : are microprocessor-based devices in the power systems that transmit and receive data or control external device such as microprocessor-based voltage regulators, protection relays, circuit breaker controllers, etc. that can communicate with other devices in the network.

Distributed Energy Resources (DERs)
 : Distributed energy resources are small, modular, energy generation
   and storage technologies that provide electric capacity or energy
   closer to the customer site. Typically producing less than 10 megawatts
   (MW) of power. DER systems may be either connected to the local
   electric power grid or be stand-alone applications
   Examples of DER technologies include wind turbines, photovoltaics
   (PV), fuel cells, microturbines, etc. {{FED-ENERGY-MGMT}}. DER
   involves power electronic interfaces, communications
   and control devices for efficient operation of multiple components
   of power management.

Merging Unit (MU)
: is used to convert voltage and current readings
from potential and current transformers, respectively, into digital data and publish them as Sampled measured values.

# Background


## High Level Overview
The centralized power grid systems is a combination of devices and controllers operating at power generation, transmission, distribution substations and customer premises.

~~~

                                   residential
                                     cutomer
                                       /\
                                      |  |
               transmission           +--+
                       /  distribution  |
                      /T\    +--+       |
  +----+      +---+    |     |  |-------+
  |    |------|   |----|-----+--+
  |    |------|   |          +--+
  +----+      +---+---^------┤  ├-------+
   bulk              /T\     +--+       |
   power             /│\     sub-       |
   generation               station     /\
                                       /  \
                                       |  |
                                       +--+
                                  industrial
                                  customer

~~~
{: #grid title="Power grid architecture"}

The entire grid relies on traditional OT. It requires SCADA or similar setup to monitor and control the voltage and power in each substation associated with the transmission, distribution and customer site substations. Several of these functions are identical focusing on protection and distribution of electric current flowing through.

Time synchronization {{SYNC}} is an important aspect of power grids since accurate timing is used to detect when and where malfunctions or disruption in power distribution occurred. Time synchronized electric signals guarantee that current flowing through lines is in phase (ref and use case??)

TODO: Add more on limitations.

## Communications in Power grid

Communications in electric power systems are at two levels.
    >1. Intra-substation communications: To facilitate connectivity
        and control functions through substation automation.
    >2. Inter-substation communications: To facilitate connectivity
        for synchronization, load redistribution and topology
        related functions.

### Subsystem Automation System (SAS)

Independent operations in a substation are described as Substation automation systems {{}}. The reference design used below is based on IEC 61850 process bus based T1-1 substation automation reference case study {{RT-MSGS}}.

~~~
           control and enterprise applications
          .-.           .-.             .-.
         (   )         (   )           (   )
          '-'           '-'             '-'
           |             |               |
      |----------LAN--or--WAN--layer------------|
      |-------------------.-.-------------------|
                         (   )  Firewall
                          '-'
                +-------------------+     '-----'
                |Interconnecting NE |----( SCADA )
                +--:------:-------:-+     '-----'
            /------|      :       |-----\
           /              :              \
        =====      :   ===:=    :       ====*
      +-|   |---|  : +-|   |-+  :  +----|   |---|
      | =====   |  : | ===== |  :  |    =/===   |
      |    |    |  : |       |  :  |    /       |
     +-+  +-+  +-+ :+-+     +-+ :+-+  +-+  +-+ _|
     +-+  +-+  +-+ :+-+ IEDs+-+ :+-+  +-+  +-+
      │    │    |  : |       │  : │    |    |
    :--------------:─-----------:------------:
    :--------------:------------:------------:
     (protection & safety equipment - voltage, current
      transformers, circuit breaker, over-current protection)

~~~
{: #sas title="Substation Automation System and Architecture"}

### Protocols

The communication requirements are documented in IEC 61850: Part 5 standard (since this requires purchase of standard, the reference is not added to the document. Instead {{RT-MSGS}} provides an indirect reference is used).

Power grid systems use different types of messages listed in {{RT-MSGS}} below.

 * IEC GOOSE protocol is an event-driven protocol designed to exchange messages between the IEDs
   over Ethernet. It supports periodic and event based messages.
   Event-based messages are used for reporting errors and are sent in
   bursts to compensate for any packet loss (since the protocol is
   publisher/subscriber based). The {{grid-proto}} show all the protocol interfaces.

*  Sampled Measured values (SV) protocol is also over Ethernet to connect
   the other side of IEDs interact over the process bus to collect sampled values (SV) from the devices. The SV interfaces between the IEDs and MUs.
   It is used to carry digital values of voltage and current and used
   for control and monitoring applications. SMV messages are
   broadcast messages and an svID field is utilized to distinguish and classify them.

* MMS protocol is an application client/server protocol designed for
  interoperability between different device manufacturers.

One of the problem with Ethernet based payloads on process and station bus
is that the Ethernet is a broadcast medium and without appropriate address validation (or lack of it) can lead to substation susceptible to flooding attacks. see {{SMV-ATTACKS}} for more vulnerabilities in SMV and GOOSE protocols.

~~~~

   +------+          +---+
   |SCADA |  MMS     |RTU|
   |      |--------->|   |
   +------+          +---+
                      |
                      |Station Bus (MMS TCP/IP)
         --+-------+---+-------+
           +<=========>|       |
           | GOOSE   +--+    +-+-+
         +---+       |  |    |   | (IEDs)
         |   |       +-++    +-+-+
         +-+-+         |       |
           |           |       |
        ---+--+----+---+-------+---
              |    |  Process Bus (SV)
              |    --          |
          .---+    .|.    .-.  |
         ( MU)    (MU )  (MU --+
          '-'      '-'    '-'
~~~
{:#grid-proto title="Protocols used in Energy Grid"}

### Addresses

All the protocols use Ethernet addresses for lower layer interface (MUs) and IP addresses for IEDs and above.

### Message types
see table below.

## Other work

Within the IETF technologies, some work on smart-grid is being done.
{{!RFC6272}} provides guidance for smart-grids to use existing IETF protocols.

{{I-D.ietf-6lo-plc}} only relates to metering infrastructure and disucsses how IPv6 packets are transported over constrained PLC networks, such as ITU-T G.9903, IEEE 1901.1 and IEEE 1901.2.

# New Power grid Decentralization Scenarios

This section describes a broad view of use cases requiring different perspective related to the communication aspects of power-grid systems.


Smart grid innovation is leading to use of communication and digital
technologies to perform different power distribution functions.
We cover 3 scenarios:


>1. Connecting Distributed Energy Resources - decentralized energy distribution management of power and load distribution/balancing from different sources to improve overall performance of the grid.
>1. Substation Automation Systems - Remote operation and
and monitoring of SAS to reduce delays due to technician having to fix
problems on-site. also to improve the safety of the technicians.
>1. Advance metering infrastructure to address customer side peak demands
and metering based on different sources of energy.

## Connecting Distributed Energy Resources

Distributed energy resources (DERs) enable mechanisms to store locally
generated energy for example from wind, solar, micro-turbines etc. They
are smaller, cleaner and cheaper sources of energy as compared to bulk
power generation.

The DER mechanisms require precise control and monitoring procedures.

  * DER systems improve reliability when incidence such as
    service interruption happens from main power source.
  * if DER energy source is renewable it reduces cost of
    decreasing consumption from main supplier.



### Requirements and Challenges

The communication between DER to end-units (inverters, batteries) is of type
operations and control functions. i.e. a controlling entity will control
DER in local geographies and end-units on customer premises.
It executes commands that will cause switching of power-source in
either direction and regularly read sensors.

Connecting different types of DERs to the grids leads to a large-scale single deplyoment. Some of the challenges arising due to this are:

- A partial knowledge about any energy resource or requirement may result in the instability of the voltage profile.
- There is a need to redesign systems for 2 way communications in which different DERs will let smrt-grid operator know about available power and its demand requirement


## Substation Automation System

The Substation automation system is responsible for control, monitoring and safety of transmission equipment in substations.
It uses well-known industry control technologies like SCADA system and
process bus to carry out sampled measurements of various operational
parameters such as voltage, current, etc. It also sends command to
several types of switchgear equipment such as transformers, voltage equipment, and circuit breakers over communication network.

Substation automation system uses process buses to control and monitor switchgear equipment to read voltage and frequency values.

The core functions of substations are:

>* Protection of switch gear against transmitted frequency, voltage
fluctuations and changes in environment. This requires synchronizing
sampled measurements from different points in the substation.
>* Interlocking
>* Automatic switching sequences

The messages mentioned above are critical to the operations of the grid.
They require guarantee of in-time and ordered deliveries and high reliability.

### Message latency

Messages for substation communication have been identified based on their
criticality in IEC 61850. The messages types are mapped according to their
communication performance requirements shown in
{{iec-msg}} along with their types. The network is hybrid, it
transmits critical and real-time messsages directly over the link layer.
For example, type1, 1A are time-critical and are carried over
the Ethernet directly. The 'type 2' are medium speed message (type 2) and several others are non-critical (source: {{RT-MSGS}}).


| Message Type  | Example Application | Time Constraint|
|---------------+---------------------+-----------------|
| 1A—Fast messages, trip | Circuit breaker commands<br> and states (GOOSE)|  ≤3 ms|
| 1B—Fast messages, other|   same as above|  ≤20 ms|
| 2—Medium speed <br> messages|  RMS values calculated <br> from type 4 messages|  ≤100 ms|
| 3—Low speed messages| Alarms, non-electrical configurations|  ≤500 ms|
| 4—Raw data messages|  Digital representation <br> electrical measurement (SV)|  ≤3 ms|
| 5—File transfer <br>functions|  Files of data for<br> recording settings | ≤1000 ms|
| 6—Time synchronization messages|  synchronization | none|
{: #iec-msg title="Time constraints for IEC 61850 process bus messages."}


# Requirements

In the current design, potential limitations that prevent complete automation
of substations maybe observed as:

>* The network needs to maintain several states from a variety of protocols
and perform conversion from SVM to GOOSE to MMS. There are opportunities to
simplify and converge MMS and GOOSE protocols into single network layer interface.
>* Moreover, SVM is design to broadcast alarm messages repeatedly (or as
a burst) to compensate for the potential packet loss. A robust and reliable design of network layer protocol can eliminate flooding.
>* Remote operations in substations are pivotal to workers' safety especially in case of accidents and inclement weather conditions. In order to
make remote operations feasible, above mentioned messages need support over
a wide-area networks.


To summarize, automation requires  leveraging IT-grade software and services. Then allowing those services to directly control and monitor the substations.

## Decentralization in Energy Grids

TODO??

## Smart Energy Grids

Support for smart-grids is necessary to interconnect different sources of
energy, have a system level view of where peak-demand or consumption
is changing, where faults are occurring and how energy can be used efficiently overall.

This requires finer granularity of monitoring, control, and data acquisition of the electricity network which will extend down to the
distribution pole-top transformer and perhaps even to individual customers, either through the substation communication network, by means of a
separate feeder communication network (chapter 22, {{POWER-SUBS}}).


### Key Performance Parameters

– Metering
– Wide area monitoring (using synchrophasors)
    -- use of time synchronization for phase matching in distribution network.
– Power conditioning
– Electricity storage

### Requirements


# Security Considerations

TODO


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
