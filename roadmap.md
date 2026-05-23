# Project Roadmap: Custom Network Management System (NMS)

This roadmap outlines the **4-month (16-week) enterprise implementation timeline** to deploy a high-performance Network Management System utilizing **Telegraf** for data ingestion, **TimescaleDB** for unified relational and time-series storage, and **Grafana** for data visualization. Complex business logic (such as sliding-window loop detection, metric correlation, and custom webhook alerting) will be executed by a lightweight **Custom Go Middleware Application**.

---

## 🏗️ System Architecture Overview

The system architecture is structured as a decoupled data pipeline divided into four distinct layers to ensure high processing throughput, strict separation of concerns, and reliable horizontal scalability:

---

[ Network Devices ] (Switches, Routers, Firewalls, UPS)
│
▼ (SNMP Get / Bulk / Traps / Syslog)
┌────────────────────────────────────────────────────────┐
│ Telegraf Ingestion Layer (Go-Native SNMP Plugin)       │
└───────────────────────────┬────────────────────────────┘
│
▼ (High-Velocity Writes)
┌────────────────────────────────────────────────────────┐
│ TimescaleDB (Unified Relational & Time-Series Engine)  │ ◄─── [ Custom Go Middleware ]
└───────────────────────────┬────────────────────────────┘      • Link Flap Windowing
│                                   • MAC Flapping Loops
▼ (Live Querying)                   • Telegram / Email Bots
┌────────────────────────────────────────────────────────┐      • Jira/ServiceNow Webhooks
│ Grafana Visualization UI (Graphs, Dashboards, Alerts)  │
└────────────────────────────────────────────────────────u

---

## 🗓️ Phase-by-Phase Roadmap

### 🏁 Phase 1: Environment Provisioning, TimescaleDB Schema & Ingestion Core

**Timeline:** Month 1 (Weeks 1 – 4)  
**Focus:** Core infrastructure setup, foundational data modeling, and basic network reachability discovery.

* **Milestone 1:** Ingestion Pipeline Operational & TimescaleDB Base Metrics Capture.
* **Tasks & Deliverables:**
  * **TimescaleDB Provisioning:** Install PostgreSQL and enable the TimescaleDB extension on designated hosting servers.
  * **Schema Design:** Design the primary relational layout for corporate network inventory tables, hardware device configurations, and metadata pools. Enable and model TimescaleDB `Hypertables` partitioned by time vectors to buffer intensive SNMP time-series variables.
  * **Telegraf Core Orchestration:** Deploy Telegraf agents and map the core `inputs.snmp` plugin configuration. Import standard MIB definitions (MIB-II, IF-MIB) to safely parse fundamental counter metrics.
  * **Availability Metrics Deployment (Track A Foundations):** Establish continuous high-velocity polling queues for device reachability checks (ICMP Ping sweeps) and interface up/down states using standard `ifOperStatus` matrices.
  * **Syslog Target Ingestion:** Spin up Telegraf's `inputs.syslog` network socket listeners to aggregate incoming asynchronous operating logs straight into indexed tables within TimescaleDB.

---

### 🧠 Phase 2: Custom Go Middleware Development & Advanced Network Logic

**Timeline:** Month 2 (Weeks 5 – 8)  
**Focus:** Backend programming in Go to calculate sophisticated metric combinations, sliding-window anomalies, and compliance triggers.

* **Milestone 2:** Custom Go Alerting Logic & Algorithmic Thresholds Verified.
* **Tasks & Deliverables:**
  * **Go Application Architecture:** Initialize the custom Go microservice. Implement optimized connection pooling configurations utilizing the native `pgxpool` cluster management driver to interface with TimescaleDB.
  * **Complex Event Processors (Track B Implementation):**
    * **Link Flap Detection:** Program sliding-window database queries in Go to identify rapid interface state cycles over custom time increments, implementing damping flags to suppress noisy devices.
    * **MAC & Topology Analysis:** Code lookup routines to isolate immediate MAC address table fluctuations across access layers and flag prospective loop states.
    * **Telemetry Parsing:** Configure deep state threshold triggers parsing hardware environmental matrices (CPU spikes, memory leakage, fan performance drops, internal temperature limits, and PoE power constraints).
  * **Interface Optical Diagnostics:** Map specific vendor-dependent OID data arrays to capture digital diagnostic metrics, tracking SFP Tx/Rx optical laser power performance in real-time.
  * **Edge Security Integration:** Set up capture mechanisms for active SNMP authentication failures, port-security violations, and unauthorized DHCP Snooping / Dynamic ARP Inspection (DAI) block anomalies.

---

### 📊 Phase 3: Traffic Flow Analytics, Backup Automation & UI Configuration

**Timeline:** Month 3 (Weeks 9 – 12)  
**Focus:** Traffic parsing engines, automated maintenance modules, and advanced UI dashboards.

* **Milestone 3:** Full Integration of Flow Engines, Configuration Management, and Dashboards.
* **Tasks & Deliverables:**
  * **High-Capacity Traffic Ingestion:** Finalize precise interface bandwidth calculation formulas using 64-bit high-capacity counters (`ifHCInOctets` and `ifHCOutOctets`) to prevent counter overflow tracking anomalies on high-speed lines.
  * **Flow Analytics Collection:** Deploy NetFlow/sFlow collection mechanisms to classify conversational traffic streams by application, source IP, and protocol distribution arrays.
  * **Automated Configuration Backup Engine:** Code standalone secure Go worker routines that periodically log into active equipment (via SSH/SFTP/TFTP), extract running configuration files, record them in the relational archive, and run file diff metrics to alert on unauthorized adjustments.
  * **Grafana Dashboard Panel Assembly:** Create functional visualization panels across the environment:
    * *NOC Operations Dashboard:* High-level health indicators, real-time alert matrices, and device reachability maps.
    * *Interface Engineering View:* Live graphs reflecting port utilization, error tracking indices (CRC/alignment drops), QoS queue performance, and latency spikes.

---

### 🚀 Phase 4: Notification Orchestration, Scale Testing & Production Go-Live

**Timeline:** Month 4 (Weeks 13 – 16)  
**Focus:** Notification channel integration, performance optimizations, load simulation runs, and complete handover.

* **Milestone 4:** Production Handover Sign-off & System Deployment.
* **Tasks & Deliverables:**
  * **Notification Routing Microservices:** Wire up the Go alerting middleware to outbound channels, verifying markdown templates over:
    * **Telegram API Integration:** Immediate notification delivery for high-severity infrastructure failures.
    * **SMTP Mail Relays:** Scheduled system reports, non-urgent warnings, and long-term diagnostic summaries.
  * **Ticketing Automation Integration:** Establish outbound webhook integrations mapping to target service management ecosystems (Jira Service Desk / ServiceNow APIs) to automatically generate operational tickets upon specific alarm triggers.
  * **TimescaleDB Optimization Tuning:** Apply data compression configurations on historical hypertables to dramatically lower structural storage footprints without compromising query performance.
  * **Scale Simulation & Benchmarking:** Stress-test the unified platform against high-load network simulation environments to guarantee poll stability, tuning Go concurrency worker pools to safely handle multi-thousand node configurations.
  * **UAT & Production Provisioning:** Conduct extensive User Acceptance Testing (simulating network breaks, topology tracking scenarios, and verification alerts). Package final documentation sheets, deployment books, and environment configuration scripts for deployment sign-off.

---

## 📊 Summary Timeline Tracker

| Month | Weeks | Primary Objectives | Deliverable Checkpoint |
| :---: | :---: | :--- | :--- |
| **Month 1** | **1 - 4** | Platform VM installations, relational tables, TimescaleDB Hypertables, and foundational SNMP reachability collection. | Time-series metrics and Syslog events storing cleanly in the database layers. |
| **Month 2** | **5 - 8** | Development of custom Go middleware application logic. Building out Link Flap damping, MAC loops, and hardware sensor thresholds. | Go microservice actively logging structural alert triggers based on database polling data. |
| **Month 3** | **9 - 12** | NetFlow engine bindings, automated configuration backup automation scripts, and Grafana dashboard generation. | Multi-vendor traffic graphs, live NOC screens, and automatic config archives fully functional. |
| **Month 4** | **13 - 16** | Notification configurations (Telegram/Email), ServiceNow/Jira webhooks, platform compression policies, and scale benchmarking. | Production system validated under heavy load testing and environment handover complete. |
