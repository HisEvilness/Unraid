# Unraid User Scripts

This directory contains a curated collection of operational scripts used for managing, maintaining, and troubleshooting **Unraid** server environments.

These scripts were originally maintained in a standalone repository and have since been consolidated into the main **Unraid** repository to centralize infrastructure tooling, dashboards, and automation assets.

---

## Purpose

The scripts in this directory are intended to support real-world Unraid operations, including but not limited to:

- System maintenance and cleanup  
- Storage, cache, and filesystem management  
- Monitoring, diagnostics, and health checks  
- Automation of recurring or manual administrative tasks  
- Operational validation and experimentation  

They are written for **practical use on live systems**, not as generic examples.

---

## Execution Context

**Platform**
- Unraid OS

**Shell**
- `bash`
- `sh`

**Execution Methods**
- Manual execution via terminal  
- Unraid **User Scripts** plugin  
- Scheduled or ad-hoc automation (where applicable)

Some scripts may require elevated privileges depending on the task being performed.

---

## Usage Guidelines

Before executing any script:

- Review the script contents in full  
- Verify paths, disks, and mount points  
- Confirm compatibility with your Unraid configuration  

Scripts may assume:
- Specific mount locations  
- Existing cache pools or array layouts  
- Active Docker or plugin components  

Adjust as necessary for your environment.

---

## Safety Notice

These scripts may perform operations that:

- Modify filesystems  
- Interact with disks, cache pools, or Docker volumes  
- Restart or affect running services  

⚠️ **Use at your own discretion.**  
Whenever possible, validate changes in a non-production environment first.

---

## Maintenance & Scope

- Scripts are maintained on a **best-effort** basis  
- Functionality may change as Unraid, Docker, or monitoring stacks evolve  
- Scripts may be added, refactored, or deprecated over time  

This directory is expected to grow alongside other repository components.

---

## Repository Structure Alignment

This scripts directory complements other parts of the repository, including:

- Monitoring dashboards (Grafana / Prometheus)  
- Exporters and telemetry tooling  
- Infrastructure and configuration documentation  

Together, these components form a cohesive operational toolkit for Unraid systems.

---

## Future Improvements (Optional)

Potential next steps include:

- Adding headers to individual scripts (purpose, scope, risk level)  
- Grouping scripts into subdirectories (e.g. `storage/`, `monitoring/`, `maintenance/`)  
- Cross-referencing scripts with related dashboards or metrics  

These enhancements can be introduced incrementally as the repository evolves.
