---
layout: post
title: "Rotate logs in Symfony using logrotate utility"
date: 2017-07-20T19:48:56+05:00
comments: true
published: true
---

# Rotate logs in Symfony using logrotate utility

During working on some project, I faced a situation where a symfony project in production is taking huge space on server. I dive into the server and saw that the symfony logs was up-to 4 GB. This project was stable and nobody touched it for last 2 year.

So, I figured out some solution to fix this issue. What i wanted to reduce the log file size or delete the logs via some shell script.

Then I found the Linux `logrotate` and `cron` utility these two utility together solve the issue.

```bash
sudo nano /etc/logrotate.d/calendar-service
/var/www/calendar-service/current/app/logs/*.log {
    size 50M
    hourly
    missingok
    rotate 3
    compress
    delaycompress
    maxage 30
    notifempty
    create 0644 www-data www-data
    sharedscripts
    su www-data www-data
}
```

Then `sudo -u root crontab -e`

```bash
# Run at every 50 mnts
50 * * * * /usr/sbin/logrotate -f /etc/logrotate.d/calendar-service
```

Step 1 : Put a logrotate policy script inside `/etc/logrotate.d` directory

Step 2 : Put a cron job under `crontab` to forcefully rotate this log after some time interval.

Job Done.
