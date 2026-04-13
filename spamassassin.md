**Spam Elimination With Spamassassin**
Install required packages:
```bash
sudo apt update && sudo apt install spamassassin spamc  
```

Create a dedicated user for the service:
```bash
sudo adduser spamd --disabled-login  
```
Configure Filter Rules:
Customize your SpamAssassin configuration by editing /etc/spamassassin/local.cf
```bash
required_score 5.0  
use_bayes 1  
bayes_auto_learn 1  
header RBL_CHECK eval:check_rbl('zen.spamhaus.org')
```

Restart spamassassin:
```bash
sudo systemctl restart spamassassin-maintenance.service

# confirm its running with:
sudo systemctl status spamassassin-maintenance.service
```


