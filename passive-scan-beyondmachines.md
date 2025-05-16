# Passive Scan of beyondmachines.net

In this analysis, I performed a passive scan of the domain beyondmachines.net, with the goal of identifying DNS records associated with it—specifically subdomains that may represent exposed service points. This type of passive scan does not involve direct interaction with the target services, but rather relies on publicly available information such as DNS records and data from open-source intelligence (OSINT) sources.

### Tools Used
- Amass – for passive subdomain enumeration using multiple public sources.
- Subfinder – fast subdomain enumeration tool focused on passive methods.
- Pentest-Tools.com – web-based enumeration for discovering subdomains and basic DNS info.
- nslookup – used to check if a subdomain resolves to an IP address (active vs. inactive).
- dig – to query DNS records (A, MX, NS, TXT) and get detailed DNS responses.
- whois – to identify domain ownership, registrar, registration dates, and possible hosting information.

### Subdomains

A total of 17 subdomains were identified, of which 8 are active (resolving to an IP address) and 9 appear inactive, potentially indicating outdated, forgotten, or misconfigured services.

```
old.beyondmachines.net
yieldhog.beyondmachines.net
damascian.beyondmachines.net
testalb.beyondmachines.net
status.beyondmachines.net
rendertest.beyondmachines.net
www.beyondmachines.net
challenge.beyondmachines.net
yieldcat.beyondmachines.net
trust.beyondmachines.net
clarity.beyondmachines.net
damascian-online.beyondmachines.net
ako.beyondmachines.net
workshop.beyondmachines.net
pubcdn.beyondmachines.net
app.beyondmachines.net
test.beyondmachines.net
```
Active: 
```
old.beyondmachines.net
www.beyondmachines.net
challenge.beyondmachines.net
yieldcat.beyondmachines.net
trust.beyondmachines.net
workshop.beyondmachines.net
pubcdn.beyondmachines.net
app.beyondmachines.net
```
Inactive:
```
yieldhog.beyondmachines.net
damascian-online.beyondmachines.net
damascian.beyondmachines.net
clarity.beyondmachines.net
testalb.beyondmachines.net
status.beyondmachines.net
ako.beyondmachines.net
rendertest.beyondmachines.net
test.beyondmachines.net
```

The `dig ANY beyondmachines.net` command revealed multiple DNS records, including A records pointing to four IP addresses, NS records indicating Amazon Route 53 as the DNS provider, 
MX records showing the use of Google Workspace for email, and SPF/TXT records used for email spoofing protection.
```
┌──(kali㉿kali)-[~]
└─$ dig beyondmachines.net ANY   
;; ANSWER SECTION:
beyondmachines.net.     60      IN      A       108.157.229.2
beyondmachines.net.     60      IN      A       108.157.229.104
beyondmachines.net.     60      IN      A       108.157.229.73
beyondmachines.net.     60      IN      A       108.157.229.125
beyondmachines.net.     3600    IN      NS      ns-496.awsdns-62.com.
beyondmachines.net.     3600    IN      NS      ns-543.awsdns-03.net.
beyondmachines.net.     3600    IN      NS      ns-1025.awsdns-00.org.
beyondmachines.net.     3600    IN      NS      ns-2019.awsdns-60.co.uk.
beyondmachines.net.     900     IN      SOA     ns-543.awsdns-03.net. awsdns-hostmaster.amazon.com. 1 7200 900 1209600 86400
beyondmachines.net.     3600    IN      MX      5 alt1.aspmx.l.google.com.
beyondmachines.net.     3600    IN      MX      5 alt2.aspmx.l.google.com.
beyondmachines.net.     3600    IN      MX      1 aspmx.l.google.com.
beyondmachines.net.     3600    IN      MX      10 alt3.aspmx.l.google.com.
beyondmachines.net.     3600    IN      MX      10 alt4.aspmx.l.google.com.
beyondmachines.net.     60      IN      TXT     "v=spf1 include:_spf.google.com ~all"
beyondmachines.net.     3600    IN      SPF     "v=spf1 include:_spf.google.com ~all"
```

The domain beyondmachines.net is registered through Amazon Registrar, Inc. since September 2019, with registration valid until September 2025. 
It uses Amazon Route 53 nameservers for DNS hosting. The registrant’s personal information is protected by an Identity Protection Service based in the UK, 
and the domain has standard domain status protections enabled.

``` 
┌──(kali㉿kali)-[~]
└─$ whois beyondmachines.net   
   Domain Name: BEYONDMACHINES.NET
   Registry Domain ID: 2430791830_DOMAIN_NET-VRSN
   Registrar WHOIS Server: whois.registrar.amazon
   Registrar URL: http://registrar.amazon.com
   Updated Date: 2024-08-03T22:52:06Z
   Creation Date: 2019-09-07T14:53:47Z
   Registry Expiry Date: 2025-09-07T14:53:47Z
   Registrar: Amazon Registrar, Inc.
   Registrar IANA ID: 468
   Registrar Abuse Contact Email: trustandsafety@support.aws.com
   Registrar Abuse Contact Phone: +1.2024422253
   Domain Status: clientDeleteProhibited https://icann.org/epp#clientDeleteProhibited
   Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
   Domain Status: clientUpdateProhibited https://icann.org/epp#clientUpdateProhibited
   Name Server: NS-1025.AWSDNS-00.ORG
   Name Server: NS-2019.AWSDNS-60.CO.UK
   Name Server: NS-496.AWSDNS-62.COM
   Name Server: NS-543.AWSDNS-03.NET
   DNSSEC: unsigned

   The Registry database contains ONLY .COM, .NET, .EDU domains and
   Registrars.
   Domain Name: beyondmachines.net
   Registry Domain ID: 2430791830_DOMAIN_NET-VRSN
   Registrar WHOIS Server: whois.registrar.amazon
   Registrar URL: https://registrar.amazon.com
   Updated Date: 2024-08-03T22:52:06Z
   Creation Date: 2019-09-07T14:53:47Z
   Registrar Registration Expiration Date: 2025-09-07T14:53:47Z
   Registrar: Amazon Registrar, Inc.
   Registrar IANA ID: 468
   Registrar Abuse Contact Email: trustandsafety@support.aws.com
   Registrar Abuse Contact Phone: +1.2024422253
   Domain Status: clientUpdateProhibited https://icann.org/epp#clientUpdateProhibited
   Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
   Domain Status: clientDeleteProhibited https://icann.org/epp#clientDeleteProhibited
   Registry Registrant ID: Not Available From Registry
   Registrant Name: On behalf of beyondmachines.net owner
   Registrant Organization: Identity Protection Service
   Registrant Street: PO Box 786
   Registrant City: Hayes
   Registrant State/Province: Middlesex
   Registrant Postal Code: UB3 9TR
   Registrant Country: GB
   Registrant Phone: +44.1483307527
   Registrant Phone Ext:
   Registrant Fax: +44.1483304031
   Registrant Fax Ext:
   Registrant Email: 04d958d6-a8e9-450e-b4fa-cd0038ec9773@identity-protect.org
   Registry Tech ID: Not Available From Registry
   Tech Name: On behalf of beyondmachines.net owner
   Tech Organization: Identity Protection Service
   Tech Street: PO Box 786
   Tech City: Hayes
   Tech State/Province: Middlesex
   Tech Postal Code: UB3 9TR
   Tech Country: GB
   Tech Phone: +44.1483307527
   Tech Phone Ext:
   Tech Fax: +44.1483304031
   Tech Fax Ext:
   Tech Email: 04d958d6-a8e9-450e-b4fa-cd0038ec9773@identity-protect.org
   Name Server: NS-543.AWSDNS-03.NET
   Name Server: NS-496.AWSDNS-62.COM
   Name Server: NS-1025.AWSDNS-00.ORG
   Name Server: NS-2019.AWSDNS-60.CO.UK
   DNSSEC: unsigned
```
### Summary

To enhance security, inactive subdomains must be removed or secured to eliminate attack vectors. 
Testing and staging environments should be isolated from public access. Proper configuration and monitoring of SPF, TXT, and MX records are essential to prevent email spoofing and limit information disclosure. 
Regular DNS record cleanup and avoiding exposure of internal IPs or sensitive subdomains help maintain a strong security posture.