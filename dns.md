**DNS Configuration**

Create an MX Record
```bash
mail.yourdomain.com
```
Sender Policy Frame work (SPF):
```bash
v=spf1 mx ipv4:address ipv6:address -all
```

DomainKeys Identified Mail (DKIM):
``` bash 
# install the required packages
sudo apt install opendkim opendkim-tools

# Generate key:
sudo opendkim-genkey -s default -d yourdomain.com 

# Add the public key to your DNS as a TXT DKIM record with this syntax
default._domainkey v=DKIM1; k=rsa; p=[Your Public Key]
```
Create a reverse DNS record (PTR):
```bash
# This is added to cloud service provider's side
```
Domain-based Message Authentication (DMARC):
```bash
# sample Record
v=DMARC1; p=quarantine; rua=mailto:admin@yourdomain.com
```
DNS Records verification:
```bash
# 1.vist the mxtool online
https://mxtoolbox.com

# 2. use the dig command 
dig MX mail.yourdomain.com +short

# change MX to an apropriate record your verifying.

