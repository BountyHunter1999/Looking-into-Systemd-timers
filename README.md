# Looking-into-Systemd-timers

- we follow [this](https://documentation.suse.com/smart/systems-management/html/systemd-working-with-timers/index.html)

- `systemd` timer units are identified by the `.timer` file name extension and each timer file requires a corresponding service file it controls
- a timer file activates and manages the corresponding service file

- put the files into `/etc/systemd/system/` directory

## Timer File

```sh
[Timer]
OnBootSec=5min
OnUnitActiveSec=24h
OnCalendar=Mon..Fri *-*-* 10:00:*
Unit=helloworld.service

[Install]
WantedBy=multi-user.target
```

- `OnBootSec=5min`: timer should start 5 minutes after the system boots up
  - this delays the start of the service so that other critical services have time to start up first
- `OnUnitActiveSec=24h`: trigger 24hrs after the last activation of the service unit
- `OnCalendar`: calendar based trigger on weekdays
  - `DayOfWeek Year-Month-Day Hour:Minute:Second`
- `Unit=helloworld.service`: service unit to be activated when the timer elapses
- `WantedBy=multi-user.target`: timer should be enabled when the `multi-user.target` is active
  - [`multi-user.target`](https://unix.stackexchange.com/questions/404667/systemd-service-what-is-multi-user-target)

## Verification and use

- `systemd-analyze verify /path/helloworld.*`
- `sudo systemctl start helloworld.timer`: Start the timer
- `sudo systemctl enable helloworld.timer`: Enable the timer to make sure that it is activated on boot
- `systemctl list-timers`: list the timers
- `journalctl -u helloworld.service` see the logs of the service
  - just replace service with timer to get the timer logs
