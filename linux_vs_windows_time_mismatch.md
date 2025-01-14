# Linux uses UTC while Windows uses local time

Windows reads the hwclock as local, while Linux reads it as UTC. This causes Windows to read the wrong time (if you're not in the UTC time zone) and requires the user to update it in the *Settings* application each time they reboot from Linux to Windows.


Neither OS fully supports the other way, but **Local in Linux is better supported than UTC in Windows**, so let's change it in Linux.

To show the hwclock run the following and look after "RTC time":
```sh
timedatectl
```

To switch to local:
```sh
timedatectl set-local-rtc 1 --adjust-system-clock
```

To revert:
```sh
timedatectl set-local-rtc 0 --adjust-system-clock
```

To correct the time if needed:
```sh
ntpd -qg && sudo hwclock -w
```