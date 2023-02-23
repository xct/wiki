---
description: This is a collection of things to check for an easy way to domain admin
---

# Quick Wins

### Top 10

* Password Spraying \(company name + year, season + year, initial passwords,...\), both on premise and vs Azure AD
* Kerberoast & ASREPRoast
* Bloodhound: paths from initial owned users to high value targets
* Credential reuse between low priv and high priv accounts
* Common CVEs that give RCE
  * CVE-2020-0688 \(Exchange\)
  * CVE-2020-0708 \(Bluekeep\)
  * CVE-2020-0144 \(Eternal Blue\)
  * CVE-2020-0796 \(SMBGhost\) 
  * CVE-2020-1472 \(Zerologon\)
* Spoofing Attacks to capture & relay hashes
  * NBTNS & LLMNR Poisoning with responder or ntlmrelayx
  * MitM6
  * SSH-Honeypots \(many companies autodiscover assets\)
* SSH via LDAP into Linux Boxes with normal User privileges
* Abusing the Printerbug \(relaying the authentication\)



