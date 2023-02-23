# Oncall Monitor
This program utilizes Pulse Audio to monitor an input device, outputting an alarm if sound is detected.

## Usage
The included `oncall` script eases the handling of the service. This is a particularly useful shorthad for times where you are awoken by a false,
or non-critical alarm while oncall and just want to return to bed. Additional checks are also performed to alert in cases where the service fails
immediately on start.

    Usage: /home/ldavidson/oncall.sh: [-s seconds] COMMAND
    COMMAND   DESCRIPTION
    start     Start the service.
    stop      Stop the service.
    restart   Restart the oncall service. Use [-s seconds] flag for a delayed restart.
 
## Service
The script which monitors the input runs in an systemd user service file. This is started via `systemctl --user start oncall-monitor.service` or `oncall start`.

Options for the service are set via environment variables:

* ONCALL_INPUT_DEV
    * Name of a pulse audio input device which will be used for monitoring. Example: `alsa_input.usb-046d_HD_Pro_Webcam_C920_8BBC58AF-02.analog-stereo`
* ONCALL_END_TIME
    * Time to stop the service. This would likely be just before your alarm to wake up. Example: `7:00`
    * Must be in 24 hour format `HH:HH`
* ONCALL_SOUND_FILE
    * File to play as an alarm. Example: `Downloads/siren.wav`
    * Must be playable by aplay. Will play on a loop until the service is stopped.

Environment variables can be exported or set via systemctl --user edit `oncall-monitor.service`

    [Service]
    Environment="ONCALL_INPUT_DEV=alsa_input.usb-046d_HD_Pro_Webcam_C920_8BBC58AF-02.analog-stereo"
    Environment="ONCALL_END_TIME=7:00"
    Environment="ONCALL_SOUND_FILE=Downloads/siren.wav"

## Timer
A timer file is also provided for convenience. May be useful so monitoring is automatically started every night at a certain time.

