Birdwatch -- Monitoring for the Bird Internet Routing Daemon
============================================================

**Objective:**  
Alert the admin when a BGP session goes down for an extended period of time,
but don't needlessly send alerts for short outages (e.g. < 2 minutes).

Birdwatch will check the state of all sessions of all `INTERESTING_PROTOCOLS`
every `CHECK_INTERVAL` seconds.
When a session changes state, an alert is scheduled `NOTIFICATION_DELAY` time
units in the future.
When the notification time for a connection arrives, Birdwatch sends an email,
*unless* the protocol's state has returned to the state that was last reported
via email.

Mail is sent from `MAIL_FROM` and addressed to `MAIL_TO`.
Birdwatch uses the `sendmail` binary, so you'll need that but won't have to
provide any addresses or authentication credentials for an external mailserver.

Currently, the "next alert time" for each session is reset whenever its state
changes, so flapping session will not trigger any alerts at all.
This is by design (more or less).

Configuration
-------------

Birdwatch comes pre-configured for the default socket paths for a dual-stack
installation of Bird on Debian, so you normally shouldn't need to re-configure
anything.

If you do need to change the configuration, just edit the block of uppercase
variables at the top of the main `birdwatch` script.

Example notifications
---------------------

Birdwatch sends a full report of all current states when it is started:
```
Subject: Birdwatch iphin IPv6: 1 start, 1 down, 9 up
Date: Thu, 03 Aug 2017 14:58:02 +0000

NAME           PROTOCOL       STATE          SINCE          INFO           
*redacted*     BGP            up             2017-06-30     Established    
*redacted*     BGP            up             2017-07-29     Established    
*redacted*     BGP            up             2017-06-28     Established    
*redacted*     BGP            up             2017-08-01     Established    
*redacted*     BGP            up             2017-06-29     Established    
*redacted*     BGP            up             2017-07-24     Established    
*redacted*     BGP            start          09:16:22       Connect        
*redacted*     BGP            up             2017-07-18     Established    
*redacted*     BGP            up             2017-07-13     Established    
*redacted*     BGP            down           2017-08-01                    
*redacted*     BGP            up             2017-07-21     Established    
```

After that, it's only *what* changed, *when* it changed:
```
Subject: Birdwatch iphin IPv4: 1 start
Date: Sat, 05 Aug 2017 23:36:40 +0000

NAME           PROTOCOL       STATE          SINCE          INFO           
*redacted*     BGP            start          23:34:23       Connect        
```

The notification doesn't contain the state it changed *from*. Note that you
cannot reliably infer it from the previous notification, because the state
might have changed several times during the configured notification delay.
