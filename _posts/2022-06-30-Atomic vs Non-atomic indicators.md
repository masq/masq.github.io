---
title: Atomic vs Non-Atomic Indicators (of Compromise)
date: 2022-06-22 00:00:00 UTC
categories: [Cyber Threat Intelligence - Theory]
tags: [cyber-threat-intelligence, cti, atomic, ioc, indicator, non-atomic, iocs, indicators, observables]
---

# Atomic vs Non-Atomic Indicators (of Compromise)

Within Cyber Threat Intelligence (CTI), analysts often talk about indicators of compromise (IoCs) as a means of detecting threat actors within a network–i.e., if a defender should find one of these IoCs within their network, it should indicate to the defender that their network is likely compromised. When they talk about these IoCs, they often refer to hashes (SHA256, SHA1, MD5) of a malicious file, IP addresses, domains, URLs, etc. These are sometimes refered to as "observables" 

to what are sometimes called observables... but that I call Atomic IoCs, or simply atomics. The fact that I make this distinction, implies that there is such a thing as a non-atomic IoC. 

## Atomic IoCs

The classics, of course:

* Hashes
	* SHA512
	* SHA256
	* SHA1
	* MD5
* Domains
* Fully Qualified Domain Names (FQDNs)
* IPv4 Addresses
* IPv6 Addresses
* URLs (doesn't have to be http(s) necessarily)

## Non-Atomic IoCs

* Sigma (log patterns)
* Snort/Suricata (network traffic patterns)
* YARA (file patterns)
* Other Domain Specific Languages (DSLs); e.g., SIEM language
	* Splunk's SPL
	* Microsoft Defender's KQL
	* Sumologic
	* Greylog
	* ... etc.
