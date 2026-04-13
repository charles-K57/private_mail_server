**Postfix configuration**

install postfix, During the installation, select “Internet Site” when prompted:
```bash
sudo apt update -y && sudo apt install postfix
```

back up the postfix main configuration file before proceeding:
```bash
sudo cp /etc/postfix/main.cf /etc/postfix/main.cf.backup
```
Empty postfix's main.cf file:
```bash
sudo truncate -s 0 /etc/postfix/main.cf
```

Open the postfix cofiguration file:
```bash
sudo nano /etc/postfix/main.cf
```

Configure core postfix's settings:
```bash

#Your server’s full domain name
myhostname = mail.yourdomain.com

# primary domain your server manages
mydomain = yourdomain.com

# Define the domain appended to unqualified email addresses 
myorigin = $mydomain

#List domains your server accepts emails for
mydestination = $myhostname, localhost.$mydomain, $mydomain

#Restrict which IPs can send emails without authentication
mynetworks = 127.0.0.0/8 [::1]/128

#support both ipv4 & ipv6 protocol connections
inet_protocols = all

#Define where incoming mail is stored
mail_spool_directory = /var/spool/mail

#Limit postfix's exposure.
smtp_banner = $myhostname ESMTP 

```

Restart postfix:
```bash
sudo systemctl restart postfix
```

Check the service's status:
```bash 
sudo systemctl status postfix
```

Check for Errors:
```bash
#using tail -f
sudo tail -f /var/log/mail.log

#using journalctl -fu
sudo journalctl -fu postfix

```

Verify your DNS Records:
```bash 
#install dnsutils package
sudo apt install dnsutils

#Verify your DNS mx Record
dig MX yourdomain.com +short

#output should look like this below
10 mail.yourdomain.com
```

Send a Test Email:
```bash
echo "check mail server" | mail -s "Server Test" yourname@example.com
```
Check Mail delivery If the email doesn’t arrive within 5 minutes:
```bash 
# 1. Inspect the mail queue
sudo postqueue -p

# 2. Review logs for errors
sudo grep 'status=sent' /var/log/mail.log
```

Troubleshooting Tip:
```bash 
# Test locally first! Send an email between two users on your server
echo "Local test" | mail -s "Internal Test" user@yourdomain.com  

# Check delivery with
sudo tail -f /var/log/mail.log
```

**Security Configuration:**
```bash
# install the required packages:
sudo apt install libsasl2-modules sasl2-bin -y

#open the /etc/postfix/main.cf with a text editor
sudo nano /etc/postfix/main.cf
```

Enable sasl Authentication:
```bash
smtpd_sasl_auth_enable = yes  
smtpd_sasl_type = dovecot  
smtpd_sasl_path = private/auth  
```

SSL/TLS Encryption:
```bash
# start with installing certbort
sudo apt install certbort -y 

# Generate certificates
sudo certbot certonly --standalone -d mail.yourdomain.com
```

Add certificates and allow postfix to use tls:
```bash
smtpd_tls_cert_file=/etc/letsencrypt/live/mail.yourdomain.com/fullchain.pem  
smtpd_tls_key_file=/etc/letsencrypt/live/mail.yourdomain.com/privkey.pem  
smtpd_use_tls=yes 
```

Configure Mail relay:
```bash
relay_domains = yourdomain.com, example.net  
smtpd_relay_restrictions = permit_mynetworks, reject_unauth_destination
```

Integrate spamassassin with Postfix:
Modify /etc/postfix/master.cf with the lines below:
```bash
smtp inet n - - - - smtpd  
  -o content_filter=spamassassin  

spamassassin unix - n n - - pipe  
  user=spamd argv=/usr/bin/spamc -f -e /usr/sbin/sendmail -oi -f ${sender} ${recipient}
```

Restart postfix:
```bash 
sudo systemctl restart postfix
#
sudo systemctl status postfix
#
# confirm that its running and if not check the main.cf for errors.
```



