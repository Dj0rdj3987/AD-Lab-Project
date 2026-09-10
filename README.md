# Building a SOC Home Lab: Splunk, Sysmon, and Active Directory

This project documents the process of building a small but realistic Security Operations Center (SOC) home lab from scratch, entirely on VirtualBox. The goal was simple: create an environment that behaves like a miniature enterprise network, complete with its own domain, endpoint telemetry, and a centralized log-collection platform, so that I could practice the same skills a SOC analyst uses on the job — collecting logs, correlating events, and investigating activity across a Windows environment.

The lab is built around three virtual machines:

- **An Ubuntu server running Splunk Enterprise**, which acts as the central log-collection and analysis platform (the SIEM).
- **A Windows target machine**, which represents a typical end-user workstation, equipped with Sysmon and the Splunk Universal Forwarder so that its activity is captured and shipped to Splunk in near real time.
- **A Windows Server machine**, promoted to a Domain Controller, which provides the Active Directory environment — the organizational units, users, and domain that the target machine ultimately joins.

The high-level architecture of the lab is shown below.

![Lab architecture diagram](images/00-lab-architecture-diagram.png)

## Why this project

I'm transitioning from a background in industrial instrumentation and automation into cybersecurity, with SOC Analyst (L1/L2) roles as my target. Reading about detection engineering and log analysis only gets you so far — I wanted a lab where I could actually generate telemetry, watch it land in Splunk, and get comfortable with the full pipeline: endpoint → forwarder → indexer → search. This repository is both my own build log and, I hope, something useful for anyone else putting together a similar lab.

## How this documentation is organized

Each part below covers one stage of the build, in the order I actually did the work. I'd recommend following them in sequence if you're building this lab yourself, since later parts assume the machines and services set up in earlier ones are already in place.

1. **[Part 1 — Network Configuration for the Splunk Server](01-splunk-server-network-configuration.md)**
   Setting a static IP address on the Ubuntu VM so the Splunk server has a predictable, fixed address on the lab network.

2. **[Part 2 — Installing Splunk Enterprise](02-splunk-installation.md)**
   Setting up a VirtualBox shared folder between the host and the Linux VM, then installing and configuring Splunk to run as its own service user.

3. **[Part 3 — Preparing the Target Machine](03-target-machine-setup.md)**
   Renaming the Windows target VM and giving it a static IP address so it has a stable identity on the network.

4. **[Part 4 — Deploying Sysmon and the Splunk Universal Forwarder](04-sysmon-and-universal-forwarder.md)**
   Installing the Universal Forwarder and Sysmon (with the Olaf Hartong configuration) on the target machine so its process, network, and system events are captured in detail.

5. **[Part 5 — Configuring Splunk to Receive Endpoint Telemetry](05-splunk-data-ingestion.md)**
   Writing `inputs.conf` on the forwarder, creating a dedicated `endpoint` index, and enabling forwarding and receiving on the Splunk server so the data actually flows in.

6. **[Part 6 — Installing Windows Server and Active Directory Domain Services](06-windows-server-ad.md)**
   Adding the AD DS role and promoting the server to a domain controller for a brand-new forest.

7. **[Part 7 — Creating Organizational Units and Users](07-ad-users-and-ous.md)**
   Structuring the directory with IT and HR organizational units and populating them with users.

8. **[Part 8 — Joining the Target Machine to the Domain](08-domain-join.md)**
   Connecting the Windows target VM to the new domain, including the DNS fix needed for the machine to actually find the domain controller, and logging in with a domain account for the first time.

## A note on the architecture diagram

Every step below is illustrated with the actual screenshot taken at the time, already sitting in each part's `images/` folder and ready to publish. The one exception is the architecture diagram referenced at the top of this page (`images/00-lab-architecture-diagram.png`) — that's a separate draw.io export rather than a screenshot, so I'll need to drop that file in myself before publishing.

## Environment

- **Hypervisor:** Oracle VirtualBox
- **SIEM / log platform:** Splunk Enterprise 10.4.3 (Ubuntu Server, `192.168.10.10`)
- **Endpoint telemetry:** Sysmon v15.21 (Olaf Hartong's `sysmon-modular` configuration) + Splunk Universal Forwarder 10.4.3
- **Target machine:** `target-PC`, `192.168.10.100`
- **Directory services:** Windows Server 2022, Active Directory Domain Services (`ADDC01`, `192.168.10.7`)
- **Domain:** `adlab.local`, with **IT** and **HR** organizational units
