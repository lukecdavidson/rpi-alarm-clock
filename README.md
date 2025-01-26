# RPi Alarm Clock
Raspberry Pi alarm clock, utilizing a simple buzzer, LED, and button. Also 
includes `oncall-monitor`, a script for monitoring the audio output of an oncall
 phone, so that you never miss a notification.

## Hardware setup
The script has the RPi pinout hardcoded:

| GPIO Pin | Function                                                         |
|----------|------------------------------------------------------------------|
| 16       | Pull up reset button. Used to stop the alarm if it is triggered. |
| 17       | Buzzer. Hooked up to an NPN transistor leading to a big buzzer.  |
| 26       | LED. On when *alarm-server* is running.                          |

## Alarm Clock
The main script runs a TCP socket server. The `alarm` command is a simple script
 for writing start and stop commands to the server socket:

```
alarm start # Triggers the alarm
alarm stop  # Stops the alarm if it is triggered
```

The *alarm.service* systemd service calls `alarm start`. This just allows us to 
use the *alarm.timer* to schedule regular alarm times. Edit *OnCalender* in 
*alarm.timer* as you see fit.

## Oncall Monitor
The idea with `oncall-monitor` is that you have a company-provided oncall phone.
 Notifications on the phone must be actioned but you may not hear the quick ding
 of an incoming notification when it is 3AM. To solve this, plug the 3.5mm sound
 output on the oncall phone into the Raspberry Pi as a microphone. I use a 3.5mm
 jack to USB adapter as the RPi doesn't have a mic jack onboard. 

Start or enable the *oncall-monitor.service* or *oncall-monitor.timer* as needed.
 It isn't started by default.

The `oncall-monitor` command is run by the *oncall-monitor.service* and need not run
directly. However, it does take 2 positional arguments which can be edited in 
the systemd service definition:

1. Input volume 
2. Input device to monitor

If not configured, the default pulseaudio source device is used. An example of 
configuring this:

```
pactl list short sources | awk '{print $2}'  # Lists the sources, grab the one you need
systemctl --user edit oncall-monitor  # Opens the editor where we edit ExecStart as shwon next
```

In the editor, write at the top of the file:

```
[Service]
ExecStart=
ExecStart=oncall-monitor 30 alsa_input.usb-C-Media_Electronics_Inc._USB_Audio_Device-00.mono-fallback
```

Note my input volume is 30%. I'd recommend testing with this a bit to confirm the
 percentage you set is reliable. If not specified, the default is 50%.







