# Part 4 — Installing the Splunk Universal Forwarder

The target machine is on the network, but nothing is leaving it yet. That's the job of
the **Splunk Universal Forwarder** — a small agent that watches whatever log sources
you point it at and ships those events to the indexer. No web interface, no searching,
no indexing of its own. It reads and it forwards.

This part gets the agent installed on `target-PC` and pointed at `192.168.10.10`.
Choosing *which* logs it sends comes later.

## Downloading

I took the forwarder from Splunk's download page (a free account is needed). Two things
to get right: **Windows 64-bit `.msi`**, and **the same version as the server** — 10.4.3
in my case. A forwarder is allowed to be older than its indexer, but matching versions
avoids a whole category of odd problems, and there's no reason not to in your own lab.

![Downloading the Splunk Universal Forwarder for Windows](images/32-download-universal-forwarder.png)

## Running the installer

License agreement first.

![Accepting the Splunk Universal Forwarder license agreement](images/33-uf-license-agreement.png)

Then the deployment type — **An on-premises Splunk Enterprise instance**, since my
indexer is a VM on the lab network rather than Splunk Cloud.

![Choosing an on-premises Splunk Enterprise deployment](images/34-uf-deployment-type.png)

## Credentials

The installer asks for a username and password. These are **local to the forwarder**,
not the login for Splunk's web interface — they're there so you can administer this
agent later from the command line. Easy to confuse the first time. I set both and kept
a note of them.

![Setting the local administrator credentials for the forwarder](images/35-uf-credentials.png)

## Deployment server

A deployment server pushes configuration out to a fleet of forwarders. Useful at a few
hundred endpoints, pointless with one, so I left it blank and configured this forwarder
directly.

![Leaving the deployment server field empty](images/36-uf-deployment-server.png)

## Receiving indexer

This is the screen that matters:

- **IP** — `192.168.10.10`, the static address from Part 1. This is exactly why it had
  to be static: the forwarder stores what you type here, and if the server moved, the
  forwarder would keep talking into the void.
- **Port** — `9997`, the standard Splunk-to-Splunk port.

![Configuring the receiving indexer at 192.168.10.10 port 9997](images/37-uf-receiving-indexer.png)

The installer doesn't test these values. It won't warn you that nothing is listening on
`9997` yet, because receiving has to be switched on separately on the server side. Until
then the forwarder just retries quietly in the background — normal, not broken.

![Installing the Splunk Universal Forwarder](images/38-uf-installing.png)

## Verifying

The forwarder installs as a Windows service and starts on its own, so `services.msc` is
the quickest confirmation — **SplunkForwarder Service**, *Running*, *Automatic*.

![The SplunkForwarder service running in services.msc](images/39-uf-service-running.png)

Or from an admin prompt:

```cmd
sc query SplunkForwarder
```

`STATE : 4  RUNNING` is what you want. If it's stopped, that points at the install
rather than the network — an unreachable indexer doesn't stop the service from running.

## Result

`target-PC` has the forwarder installed, running automatically, and aimed at
`192.168.10.10:9997`. What it doesn't have is anything to send.

Next comes Sysmon, which is what turns ordinary Windows logging into the detailed
process, network and file telemetry that makes this lab worth building.

**Next:** [Part 5 — Deploying Sysmon](05-sysmon.md)
