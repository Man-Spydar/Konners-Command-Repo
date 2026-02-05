# DNS & IP Tools

## Purpose
Quick-reference commands for **DNS lookups**, **IP configuration**, and **network troubleshooting**.

This page prioritizes **commands first**, with short notes and tips where useful.

---

## DNS Tools

### `nslookup` (Primary DNS Tool)

Basic lookup (uses default DNS server):

```powershell
nslookup example.com
```

Query a specific DNS server:

```powershell
nslookup example.com 8.8.8.8
```

Reverse lookup (IP → name):

```powershell
nslookup 8.8.8.8
```

Record type queries:

A record:

```powershell
nslookup -type=A example.com
```

AAAA (IPv6):

```powershell
nslookup -type=AAAA example.com
```

MX (mail):

```powershell
nslookup -type=MX example.com
```

TXT (SPF, DKIM, verification):

```powershell
nslookup -type=TXT example.com
```

SRV (common for AD/services):

```powershell
nslookup -type=SRV _ldap._tcp.example.com
```

!!! tip
    If results differ between DNS servers, you are likely dealing with replication, forwarding, or split-brain DNS.

### `Resolve-DnsName` (PowerShell DNS)

Modern replacement for `nslookup`.

Basic lookup:

```powershell
Resolve-DnsName example.com
```

Specific record type:

```powershell
Resolve-DnsName example.com -Type MX
```

Query a specific DNS server:

```powershell
Resolve-DnsName example.com -Server 8.8.8.8
```

!!! note
    `Resolve-DnsName` provides structured output and is preferred in scripts.

---

## IP Configuration

### View IP Configuration

Full details:

```powershell
ipconfig /all
```

Quick summary:

```powershell
ipconfig
```

### Release / Renew DHCP

Release IP:

```powershell
ipconfig /release
```

Renew IP:

```powershell
ipconfig /renew
```

!!! warning
    Releasing an IP on a remote machine may disconnect your session.

### Flush DNS Cache

Clear local DNS resolver cache:

```powershell
ipconfig /flushdns
```

!!! tip
    Flush DNS after changing DNS records or network settings.

---

## Connectivity Testing

### `ping`

Basic connectivity test:

```powershell
ping example.com
```

Ping by IP:

```powershell
ping 8.8.8.8
```

Continuous ping:

```powershell
ping example.com -t
```

Stop with `Ctrl + C`.

!!! note
    Ping success does not guarantee application-level connectivity.

### `tracert` (Route Tracing)

Trace route to destination:

```powershell
tracert example.com
```

Limit hop count:

```powershell
tracert -h 10 example.com
```

!!! tip
    Timeouts near the destination often indicate firewall or ISP filtering, not local issues.

### `pathping` (Advanced Routing Test)

Combines ping and tracert:

```powershell
pathping example.com
```

!!! note
    `pathping` can take several minutes to complete.

---

## Network Adapter Tools

### View Network Interfaces

```powershell
Get-NetAdapter
```

View IP info per interface:

```powershell
Get-NetIPAddress
```

### Disable / Enable Adapter

Disable:

```powershell
Disable-NetAdapter -Name "Ethernet" -Confirm:$false
```

Enable:

```powershell
Enable-NetAdapter -Name "Ethernet"
```

!!! warning
    Disabling the active adapter will drop network connectivity.

---

## DNS Client Configuration

### View DNS Servers

```powershell
Get-DnsClientServerAddress
```

Per interface:

```powershell
Get-DnsClientServerAddress -InterfaceAlias "Ethernet"
```

### Set DNS Servers (IPv4)

```powershell
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 8.8.8.8,8.8.4.4
```

!!! warning
    Changing DNS servers may violate policy. Use only when authorized.

---

## Connection & Port Testing

### `netstat` (Connection Inspection)

View all active connections and listening ports:

```powershell
netstat -ano
```

Show listening ports only:

```powershell
netstat -ano | findstr LISTENING
```

Filter by specific port:

```powershell
netstat -ano | findstr :443
```

Show connections with executable mapping (admin required):

```powershell
netstat -ab
```

!!! tip
    Match the PID column to a process via Task Manager or `Get-Process`.

Match PID to process:

```powershell
Get-Process -Id 1234
```

!!! warning
    `-b` requires elevation and may be blocked by endpoint protection.

### `Test-NetConnection` (PowerShell Connectivity Test)

Preferred PowerShell-native alternative to ping and telnet-style tests.

Basic connectivity test:

```powershell
Test-NetConnection example.com
```

Test a specific port (TCP):

```powershell
Test-NetConnection example.com -Port 443
```

Test by IP:

```powershell
Test-NetConnection 8.8.8.8 -Port 53
```

Show only key results:

```powershell
Test-NetConnection example.com -Port 443 | Select TcpTestSucceeded
```

!!! tip
    `TcpTestSucceeded : True` confirms the port is reachable, even if the app still fails.

Test using a specific interface:

```powershell
Test-NetConnection example.com -InterfaceAlias "Ethernet"
```

Trace route with `Test-NetConnection`:

```powershell
Test-NetConnection example.com -TraceRoute
```

!!! note
    `Test-NetConnection` is script-friendly and can replace several legacy checks.

---

## Quick Troubleshooting Flow

Check IP:

```powershell
ipconfig /all
```

Test DNS resolution:

```powershell
nslookup example.com
```

Flush cache:

```powershell
ipconfig /flushdns
```

Test connectivity:

```powershell
ping example.com
```

Trace route:

```powershell
tracert example.com
```

---

## Common Tips & Gotchas

!!! tip
    If ping by IP works but hostname fails, the issue is usually DNS.

!!! tip
    If internal names resolve externally, check split-DNS configuration.

!!! tip
    Always verify which DNS server is being queried before escalating.

---

## Status

This page is actively expanding.

Planned additions:

- `IPv6` troubleshooting
- Common AD DNS SRV lookups
