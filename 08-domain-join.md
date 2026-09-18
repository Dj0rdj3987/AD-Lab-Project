# Part 8 — Joining the Target Machine to the Domain

Everything so far has been building toward this: the Windows 10 target, which has been
a standalone machine until now, joins `adlab.local` and starts authenticating against
the domain controller. Once it's a domain member, a login on the target is a login the
DC sees — and that's what makes the later attack visible in Splunk.

## Starting the join

On the target I went to **This PC → Properties → Advanced system settings → Computer
Name → Change**, selected **Domain**, and entered the domain name, `adlab.local`.

![System Properties → Computer Name → Change](images/56-system-properties.png)

![Entering the domain name adlab.local](images/57-computer-name-change-domain.png)

## The DNS gotcha and the fix

This is where the machine threw an error — and it's exactly the one Part 3 warned
about. A Windows machine finds a domain by asking DNS for it. The target's DNS was
still pointed at Google's `8.8.8.8`, which knows nothing about a private `adlab.local`,
so it couldn't resolve the domain and the join failed.

![The domain-join failing because adlab.local can't be resolved](images/58-domain-join-dns-error.png)

The fix is to point the target's DNS at the machine that *does* know the domain — the
domain controller. Under **Network settings → Change adapter options → Ethernet →
IPv4 Properties**, I replaced the DNS server with the DC's address.

![Repointing the target's DNS to the domain controller](images/59-repoint-dns-to-dc.png)

With DNS repointed, the join went through. Windows asked for the credentials of an
account allowed to add machines to the domain, which I supplied.

![Supplying domain credentials to complete the join](images/60-domain-credentials.png)

## Logging in as a domain user

The join completes with a reboot. At the login screen I chose **Other user** and
signed in with one of the domain accounts created in Part 7 — not a local account,
but a user that lives in Active Directory and is authenticated by the domain
controller.

![Logging in as a domain user via Other user](images/61-other-user-domain-login.png)

## Result

The lab is now complete as a monitored Active Directory environment: a domain
controller serving `adlab.local`, departmental OUs with real user accounts, and a
domain-joined Windows target whose activity streams into Splunk through Sysmon and the
Universal Forwarder. Every domain authentication on the target is now something the DC
records and something I can go find in the SIEM.

That's the foundation. With a real domain to attack and full telemetry to watch it
with, the next phase is the interesting one: using Kali to run a brute-force attack
against the domain accounts, then going into Splunk to see exactly what that looks
like from the SOC side (failed-logon Event ID 4625, the successful 4624 that follows),
with Atomic Red Team runs planned after that to cover a broader set of MITRE ATT&CK
techniques.
