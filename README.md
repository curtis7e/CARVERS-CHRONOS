# CARVER-CLOCK-SYNC

**A Cisco IOS EEM script that automatically sets the router's system clock to a predefined date/time when the device boots up.**

## Overview

This script solves the common problem of incorrect dates on Cisco routers after power cycles or reboots. It's especially useful for:

- Field-deployed router kits  
- Devices operated by non-technical users  
- Systems requiring consistent timestamps without NTP access  

---

## Installation

1. **Enter configuration mode on your Cisco device:**
   ```bash
   enable
   configure terminal
   ```

2. **Copy and paste the entire script:**
   ```bash
   event manager environment _cron_entry "0-59/1 * * * *"
   event manager environment _show_cmd "show clock"

   event manager applet CARVER-CLOCK-SYNC authorization bypass
    event syslog pattern ".%SYS-5-RESTART: System restarted." maxrun 60
    action 0010 cli command "enable"
    action 0020 syslog msg "Setting system date to October 2, 2025"
    action 0030 cli command "configure terminal" pattern "."
    action 0040 cli command "end" pattern "."
    action 0050 cli command "enable"
    action 0060 cli command "clock set 12:00:00 Oct 2 2025" pattern ".*"
    action 0070 syslog msg "Date has been set to October 2, 2025"
   ```

3. **Exit configuration mode and save:**
   ```bash
   end
   write memory
   ```

---

## Customization

To set your desired date and time, modify **line 11** in the script:

```bash
action 0060 cli command "clock set 12:00:00 Oct 2 2025" pattern ".*"
```

Change `12:00:00 Oct 2 2025` to your preferred time and date using the format:  
`HH:MM:SS Month Day Year`

### Examples:
- `08:30:00 Jan 15 2026`
- `23:45:00 Mar 20 2025`
- `16:00:00 Dec 31 2025`

Additionally, update the log messages in **actions 0020** and **0070** to match your new date.

---

## How It Works

- Detects when the router completes the boot process  
- Bypasses normal authorization requirements  
- Sets the clock to the specified date and time  
- Logs the action to the system log  

---

## Troubleshooting

If the script doesn't run:

- Verify the applet is installed:
  ```bash
  show event manager policy registered
  ```
- Check the syslog for errors:
  ```bash
  show logging
  ```
- Ensure authorization bypass is properly configured

---

## Credits

Created by **Curtis7e**  
Based on an original concept by **JCarver**
