# Fixing Silent HTTPS & Connection Failures Caused by Upstream ISP "Fortinet" Filtering

## Context & Symptoms
While helping my girlfriend troubleshoot why she couldn't launch Roblox on her PC, we ran into a stubborn 3-hour connection issue that even stumped her stepdad (who holds a specialization in IT diploma and previously worked in the IT industry).

* **Roblox Launcher:** Failed to connect with a generic connection/HTTPS error (no specific reason given).
* **Most Web Browsers:** Failed to load the Roblox website without displaying a clear error message.
* **Opera GX Browser:** Explicitly flagged an HTTPS/SSL certificate error mentioning **Fortinet**, even though Fortinet/FortiClient was **not** installed anywhere on her local Windows machine.
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/63d3c01a-4b21-4e03-bdcd-40741eb43c56" />


## Root Cause
* **Carrier-Level Network Filtering (T-Mobile):** Testing via a mobile hotspot confirmed her local Windows installation was completely clean and isolated the block to the ISP's upstream infrastructure.
* Even with local router firewall settings disabled, the ISP's upstream FortiGate gateway performs transparent DNS interception and SSL inspection (commonly tied to carrier-level web security filters).
* Standard Windows DNS overrides failed because unencrypted DNS queries (Port 53) were still intercepted before leaving the ISP network.

## Troubleshooting Steps Attempted
1. **Cross-Browser Isolation:** Tested across multiple browsers on her PC and uncovered the hidden "Fortinet" SSL intercept clue via Opera GX's error details.
2. **Local Certificate Audit:** Checked the Windows Certificate Store (`certmgr.msc`) and verified no rogue or third-party Fortinet root certificates were installed locally.
3. **Router & Physical Layer Checks:**
   * Logged into the T-Mobile router administrator panels and confirmed firewall rules were not blocking traffic.
   * Power-cycled the router (unplugged/replugged) and tested different physical Ethernet LAN ports.
4. **Network Isolation (Hotspot Test):** Connected her PC to a mobile hotspot—both the Roblox launcher and website loaded normally, proving the issue was isolated to the home ISP connection.
5. **Local DNS Reset & Manual Override**: Ran `ipconfig /flushdns` in Command Prompt and manually configured IPv4 DNS servers in Windows *(Note: Still intercepted by the ISP's transparent DNS proxy).*

## Working Solution & Download Link (For anyone who has the same issue :D)
* Installed and enabled the official [Cloudflare 1.1.1.1 (WARP)](https://one.one.one.one) client.
* **Why it worked:** Encrypts DNS queries (DoH/DoT) and tunnels traffic securely, preventing the ISP's upstream FortiGate filter from intercepting the TLS handshake or injecting its block certificate—getting her back online immediately!
* **Startup Tip:** Once you toggle the switch **ON** for the first time, WARP automatically runs in the background whenever Windows starts up.
