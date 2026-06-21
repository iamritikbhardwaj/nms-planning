# Project Roadmap: Custom Network Management System (NMS)

This roadmap outlines the **4-month (16-week) enterprise implementation timeline** to deploy a high-performance Network Management System utilizing **Telegraf** for metric data ingestion, **TimescaleDB** for unified relational inventory and time-series storage, and **Grafana** for visualization and metric threshold alerts. Complex edge business logic (such as real-time SNMP Trap parsing, concurrent ICMP reachability checks, and notification webhook dispatching) will be executed by a lightweight, concurrent **Custom Go Middleware Application**.

---

## 🏗️ System Architecture Overview

The system architecture is structured as a decoupled data pipeline divided into four distinct layers to ensure high processing throughput, strict separation of concerns, and reliable horizontal scalability:

```
    [ Network Devices ] (Switches, Routers, Firewalls)
          │
          ├───(SNMP Polling & Syslog)──► [ Telegraf Ingestion Layer ] ──┐
          │                                                             ▼
          └───(SNMP Traps & ICMP Ping)──► [ Custom Go Middleware ] ──► [ TimescaleDB ]
                                                                             │
                                                                       (Live Querying)
                                                                             ▼
                                                                     [ Grafana UI & Alerts ]

```

---

## 🗓️ Phase-by-Phase Roadmap

### 🏁 Phase 1: Environment Provisioning, TimescaleDB Schema & Core Ingestion

**Timeline:** Month 1 (Weeks 1 – 4)

**Focus:** Core infrastructure setup, foundational data modeling, and core network availability/reachability tracking.

* **Milestone 1:** Core Pipeline Operational & Availability Metrics Storing Cleanly.
* **Tasks & Deliverables:**
* **TimescaleDB Provisioning:** Install PostgreSQL and enable the TimescaleDB extension on designated hosting servers.
* **Schema Design:** Design the primary relational tables for corporate network inventory, device metadata, and active alarm states. Configure TimescaleDB `Hypertables` partitioned by time vectors to handle rapid time-series metrics.
* **Telegraf SNMP Engine Setup:** Deploy Telegraf agents and configure the `inputs.snmp` plugin. Import foundational MIBs (`MIB-II`, `IF-MIB`) to parse interface states.
* **Availability Polling Core (Alarms 1, 2, 4, 5):**
* Map regular polling intervals for interface state transitions (`ifOperStatus` vs `ifAdminStatus`) and SNMP connection handshakes.
* Set up the base framework to evaluate and trigger **Device Down**, **Interface Down**, and **SNMP Communication Failure** conditions.


* **Optional Syslog Ingestion (Alarm 10):** Spin up Telegraf's `inputs.syslog` network socket listeners to parse and centralize raw device operating system logs into an indexed DB table.



---

### 🧠 Phase 2: Custom Go Middleware (ICMP Engine, Trap Receiver & Advanced Alerts)

**Timeline:** Month 2 (Weeks 5 – 8)

**Focus:** Coding the concurrent Go microservice to handle high-frequency pinging, instant SNMP trap interception, and complex state alerts.

* **Milestone 2:** Go Core Engine Live & Real-Time Event Trap Parsing Verified.
* **Tasks & Deliverables:**
* **Go Architecture Deployment:** Initialize the custom Go daemon. Set up optimized connection pooling via `pgxpool` to safely write parallel records into TimescaleDB.
* **High-Speed ICMP Engine (Alarm 5):** Build a highly concurrent, goroutine-backed ping processor to scan all network IP nodes at short intervals for precise **Device Reachability Monitoring** (latency/packet loss) without bottlenecking.
* **Asynchronous SNMP Trap Receiver:** Program the Go service to listen for immediate inbound traps pushed by devices for real-time anomalies:
* **STP Topology Change Alert (Alarm 11):** Parse TCN trap events to flag spanning-tree realignments.
* **Loop Detection & Broadcast Storms (Alarms 12, 13):** Intercept MAC flapping traps or high-velocity bandwidth traps indicating switch loop states.
* **Port Security & Access Violations (Alarms 14, 15, 16, 18):** Capture unauthorized MAC traps, Rogue DHCP Offer flags (Snooping), Dynamic ARP Inspection drops, and consecutive AAA local login failures.





---

### 📊 Phase 3: Performance Performance Tracking & Grafana Dashboard Assembly

**Timeline:** Month 3 (Weeks 9 – 12)

**Focus:** Tuning utilization formulas, capturing hardware/optical performance bounds, and constructing frontend views.

* **Milestone 3:** Core Alarm Matrix Fully Visualized on Production Dashboards.
* **Tasks & Deliverables:**
* **Utilization Calculations (Alarms 3, 6, 7):** Fine-tune Telegraf delta calculations using 64-bit high-capacity counters (`ifHCInOctets`/`ifHCOutOctets`) to generate accurate **Port Utilization Graphs** and monitor **CRC/Error Rates** without risk of counter overflows.
* **Optical Laser Diagnostics (Alarms 8, 17):** Map vendor-specific Digital Optical Monitoring (DOM) OIDs to track SFP Transmit/Receive laser power ($dBm$). Implement relational logic in Go or DB queries to correlate a sudden loss of optical light with link downs to flag explicit **Fiber Cut Detections**.
* **Configuration Mismatches (Alarm 19):** Code comparison checks using LLDP/CDP cache metrics to cross-reference and flag **Interface Speed/Duplex Mismatches**.
* **Grafana Dashboard Implementation:** Build two major production dashboards:
* *NOC High-Level Matrix:* Device up/down matrices, immediate reachability heatmaps, and a real-time critical alert ticker fed directly from the Go service.
* *Interface Engineering Layout:* Clean historical line/area graphs capturing utilization percentages, error rates, packet drops, and optical power trajectories.





---

### 🚀 Phase 4: Notification Orchestration, Optimization & Final Handover

**Timeline:** Month 4 (Weeks 13 – 16)

**Focus:** Linking data thresholds to outgoing channels, data footprint tuning, and end-to-end stress testing.

* **Milestone 4:** System Validation Under Load & Production Sign-off.
* **Tasks & Deliverables:**
* **Notification Engine Wiring:** Connect the Go middleware and Grafana Alerting engines to external endpoints:
* **Telegram API Integration:** Instant notification payloads for critical events like *Device Down*, *Fiber Cuts*, or *Broadcast Storms*.
* **SMTP Mail Gateways:** Weekly summaries of interface error logs and non-critical configuration alerts (e.g., NTP drifts or duplex mismatches).


* **Ticketing Webhooks:** Configure outgoing JSON webhook payloads to match target ticketing platforms (Jira Service Desk / ServiceNow) to automate ITIL incident generation on critical triggers.
* **TimescaleDB Compression Policies:** Enable native TimescaleDB chunk compression on mature time-series tables to minimize disk footprints while keeping raw historical charts instantly readable.
* **Scale Simulation & Tuning:** Stress-test the unified platform using simulated SNMP/ICMP loads to verify stability, optimizing Go concurrency parameters to guarantee fluid polling during extensive device outages.
* **UAT & Deployment Handover:** Execute formal User Acceptance Testing workflows (simulating link drops, port violations, and loop traps). Package final operational handbooks, environment configuration scripts, and documentation layouts.



---

## 📊 Summary Timeline Tracker

| Month | Weeks | Primary Objectives | Deliverable Checkpoint |
| --- | --- | --- | --- |
| **Month 1** | **1 - 4** | Platform VM installations, TimescaleDB structural tables, and basic Telegraf SNMP configuration. | Base device status and interface metric states logging clean entries to the DB layer. |
| **Month 2** | **5 - 8** | Development of custom Go middleware application. Coding goroutine ping sweeps and the real-time SNMP Trap parsing daemon. | Real-time security, loop, and STP trap anomalies captured and classified instantly. |
| **Month 3** | **9 - 12** | Tuning of utilization formulas, optical transceiver power arrays, and assembling Grafana panels. | Interactive NOC dashboards displaying port graphs, error trends, and fiber degradation lines. |
| **Month 4** | **13 - 16** | Notification webhooks (Telegram/Email), ticketing workflows, database compression, and performance stress testing. | Production monitoring system fully validated against client specification, leading to project sign-off. |
