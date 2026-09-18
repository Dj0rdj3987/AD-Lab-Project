# Part 5 — Configuring Splunk to Receive Endpoint Telemetry

At the end of Part 4 the forwarder was installed but silent. Getting data to actually
flow takes two agreements: the forwarder has to be told *what* to collect and *where*
to send it, and the server has to be told to *listen* and *where to put* what arrives.
This part sets up both ends.

## Telling the forwarder what to collect

The forwarder reads its instructions from a file called `inputs.conf`. This is where I
declared which Windows and Sysmon logs to pick up, and crucially which index to
send them to. I pointed everything at an index named `endpoint`:

![The inputs.conf on the forwarder, sending data to the endpoint index](images/38-inputs-conf.png)

I saved the file with the new configuration. A forwarder only reads `inputs.conf` at
start, so the change means nothing until the service restarts — hence the next step.

## Restarting the forwarder service

The Universal Forwarder runs as a Windows service, so I restarted **SplunkForwarder**
so it would pick up the new `inputs.conf`:

![Restarting the SplunkForwarder service](images/39-restart-forwarder-service.png)

## Creating the endpoint index on the server

The forwarder is now trying to send everything to an index called `endpoint` — but
that index has to exist on the server first, or the data has nowhere to land. On the
Splunk server I created it with exactly that name:

![Creating the endpoint index on the Splunk server](images/40-create-endpoint-index.png)

The name isn't arbitrary — it has to match the index I named in `inputs.conf`. A
mismatch here is the classic reason data "disappears": the forwarder is sending fine,
but to an index the server doesn't have.

## Enabling receiving on the server

By default a Splunk instance doesn't accept forwarded data on any port; it has to be
switched on. Under **Settings → Forwarding and receiving**, I went to **Configure
receiving** and added a receiving port so the server would listen for the forwarder.

![Settings → Forwarding and receiving](images/41-forwarding-and-receiving.png)

![Configure receiving — adding the listening port](images/42-configure-receiving.png)

## Verifying the data is arriving

With both ends configured, I searched the `endpoint` index on the server to confirm
events were actually coming in:

![Searching the endpoint index and seeing live events from the target](images/43-data-arriving-search.png)

Events from the target were landing in Splunk. That closes the loop — the endpoint
generates Sysmon telemetry, the forwarder ships it, and the server stores and indexes
it where I can search it. From here on, anything I do to the target machine is
visible from the SOC side.

## Result

Endpoint telemetry now flows end to end into the `endpoint` index. The monitoring half
of the lab is complete. Next comes the other half: standing up the Active Directory
environment the target will eventually join.

**Next:** [Part 6 — Installing Windows Server and Active Directory Domain Services](06-windows-server-ad.md)
