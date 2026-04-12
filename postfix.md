
install postfix 
During installation, select “Internet Site” when prompted. 
This tells Postfix your default mail server will handle emails for domains you own.
```bash
sudo apt update -y && sudo apt install postfix
```

back up the postfix main configuration file before proceeding.
```bash
sudo cp /etc/postfix/main.cf /etc/postfix/main.cf.backup
```
Empty postfix's main.cf file
```bash
sudo truncate -s 0 /etc/postfix/main.cf
```

Open the postfix cofiguration file
```bash
sudo nano /etc/postfix/main.cf
```

Configure core postfix's settings
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

Restart postfix
```bash
sudo systemctl restart postfix
```

Check the service's status.
```bash 
sudo systemctl status postfix
```

Check for Errors.
```bash
#using tail -f
sudo tail -f /var/log/mail.log

#using journalctl -fu
sudo journalctl -fu postfix

```

Verify your DNS Records.
```bash 
#install dnsutils package
sudo apt install dnsutils

#Verify your DNS mx Record
dig MX yourdomain.com +short

#output should look like this below
10 mail.yourdomain.com
```

Send a Test Email.
```bash
echo "check mail server" | mail -s "Server Test" yourname@example.com
```
Check Mail delivery If the email doesn’t arrive within 5 minutes
```bash 
# 1. Inspect the mail queue
sudo postqueue -p

# 2. Review logs for errors
sudo grep 'status=sent' /var/log/mail.log
```

Troubleshooting Tip
```bash 
# Test locally first! Send an email between two users on your server
echo "Local test" | mail -s "Internal Test" user@yourdomain.com  

# Check delivery with
sudo tail -f /var/log/mail.log
```


