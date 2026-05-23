# Project Roadmap: Custom Network Management System (NMS)

This roadmap outlines the **8-week implementation timeline** to deploy a budget-optimized, high-performance Network Management System utilizing **Telegraf** for data ingestion, **TimescaleDB** for unified storage, and **Grafana** for data visualization. Custom business logic (such as sliding-window loop detection and custom webhook alerting) will be handled by a lightweight **Custom Go Middleware Application**.

---

## 🏗️ System Architecture Overview

```
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

```

---

## 🗓️ Phase-by-Phase Roadmap

### 🏁 Phase 1: Core Core Ingestion & TimescaleDB Database Setup

**Timeline:** Weeks 1 – 2

**Focus:** Infrastructure provisioning, data pipelines, and core reachability monitoring.

* **Milestone 1:** Metric Ingestion & TimescaleDB Core Operational.

* **Tasks & Deliverables:**
* **TimescaleDB Provisioning:** Install PostgreSQL and enable the TimescaleDB extension.

* **Schema Design:** Define relational tables for device inventories and metadata alongside localized `Hypertables` optimized for multi-million row SNMP time-series data retention.

* **Telegraf Setup:** Deploy Telegraf and configure the native `inputs.snmp` plugin to track standard OIDs (chassis health, uptime, interface indexes).

* **Track A Deployment:** Establish foundational polling loops for availability metrics (Device/Interface Up-Down via `ifOperStatus` and ICMP Ping sweeps).

* **Syslog Engine:** Configure Telegraf's `inputs.syslog` plugin to capture incoming device system logs into TimescaleDB.

---

### 🧠 Phase 2: Custom Go Alerting Engine & Advanced Network Logic

**Timeline:** Weeks 3 – 5

**Focus:** Custom backend development in Go to process complex telemetry thresholds and compliance states.

* **Milestone 2:** Custom L2/L3 Alerting Logic Verified.

* **Tasks & Deliverables:**
* **Go Middleware Initialization:** Set up a lightweight Go microservice utilizing the `pgx` driver to interface with TimescaleDB.
* **Algorithmic Event Trackers (Track B):**
* **Link Flap Detection:** Write sliding-window database logic in Go to track interface state cycles over short timeframes, suppressing alerts if criteria are exceeded.

* **MAC/Loop Processing:** Code specific analytics to identify MAC address changes and broadcast loops inside input data tables.

* **Telemetry Triggers:** Implement evaluation thresholds for high-capacity hardware states (CPU spikes, memory exhaustion, rising optical SFP dBm levels, fan failures, and PoE budgeting).

* **Edge Security Hooks:** Configure detection logic for invalid SNMP authentication traps, Port Security triggers, and DHCP Snooping anomalies.

---

### 📊 Phase 3: Analytics, Grafana Panels, and Webhook Integration

**Timeline:** Weeks 6 – 7

**Focus:** User interfaces, outbound communications, and system maintenance automations.

* **Milestone 3:** Core Platform Feature Complete.

* **Tasks & Deliverables:**
* **Traffic Ingestion Setup:** Finalize ingestion handlers for bandwidth accounting metrics utilizing High-Capacity interface counters (`ifHCInOctets`/`ifHCOutOctets`).

* **Grafana Dashboard Layouts:** Build customized visualization grids including:
* *Network Operations Center (NOC) View:* Overall reachability map, critical infrastructure status, and live alert tickers.

* *Interface Performance Panels:* Visual indicators for port utilization graphs, error rates (CRC), drop percentages, and latency metrics.

* **Notification Microservice Integration:** Connect the Custom Go application to external notification channels, configuring:
* **Telegram Bot Webhooks:** Secure API routing for priority alarms.
* **SMTP Mail Relay:** Automated delivery for status digests and audit reports.

* **Service Integration Engine:** Write outbound webhook drivers connecting the custom Go monitoring logic to corporate task software (Jira Service Desk / ServiceNow) for automated ticketing.

* **Automated Backup Routines:** Set up schedule-driven Go workers executing secure SFTP/TFTP routines to safely back up network element configurations with automated differential tracking.

---

### 🚀 Phase 4: Scale Verification, Optimization, and Go-Live

**Timeline:** Week 8

**Focus:** Performance tuning, security configurations, deployment validation, and final project sign-off.

* **Milestone 4:** Final Production Handover & System Go-Live.

* **Tasks & Deliverables:**
* **TimescaleDB Optimization:** Apply data compression rules to older data records to maximize storage efficiency and scale performance.

* **Go Engine Profiling:** Audit concurrent Go routine operations and worker pools under simulated heavy trapping scenarios to eliminate execution bottlenecks.

* **Grafana Alert Routing:** Set up state transition thresholds within Grafana panels to prevent redundant alerting cycles.

* **User Acceptance Testing (UAT):** Run functional verification scenarios across the infrastructure (e.g., pulling test links, simulating configuration adjustments, triggering validation drops) to confirm alert responses.

* **System Handover:** Deliver documentation covers, deployment blueprints, environment environment files, and complete source code to transition the project to production.

---

## 📊 Summary Timeline Tracker

| Week | Phase | Primary Objectives | Deliverable Checkpoint |
| --- | --- | --- | --- |
| **1 - 2** | Phase 1 | Database schemas, Telegraf collectors, and reachability tracking.

 | TimescaleDB | Hypertables | capturing | live metrics.

 |
| **3 - 5** | Phase 2 | Custom Go application for link flapping, loops, and telemetry states.

 | Go backend generating precise event tracking logs.

 |
| **6 - 7** | Phase 3 | Grafana panels | Telegram notification scripts | config tools | and ticket hooks.

 | Interactive dashboard layout and webhook alerts functional.

 |
| **8** | Phase 4 | Scalability tuning, validation runs, technical user review, and project sign-off.

 | Production code deployed and system keys handed over.

 |
