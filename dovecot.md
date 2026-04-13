**Dovecot configuration**

Configure Maildir storage in /etc/dovecot/conf.d/10-mail.conf:
```bash 
# Ensure to comment out all other lines starting with keyword 'mail'
# then add these lines

mail_driver = maildir
mail_path = ~/Maildir
```

Secure dovecot via /etc/dovecot/conf.d/10-ssl.conf:
```bash
# Enable TLS encryption 
ssl = yes
ssl_min_protocol = TLSv1.2

# link Dovecot Linux to your Let’s Encrypt certificates:
ssl_server_cert_file = /etc/letsencrypt/live/mail.yourdomain.com/fullchain.pem
ssl_server_key_file = /etc/letsencrypt/live/mail.yourdomain.com/privkey.pem 

# allow plain text auth
auth_allow_cleartext = yes
```

Restart and check dovecot service:
```bash 
sudo systemctl restart dovecot.service && sudo systemctl status dovecot.service

# check for errors in dovecot configuratios with:
#
sudo doveconf -n
#
# This will indicate the errors and the configuration lines causing them.
```
