# Oncall Monitor
This program utilizes Pulse Audio to monitor an input device, outputting an alarm if sound is detected.

## Installation
Run install.sh without root.

## Usage
The included `oncall` script eases the handling of the service. This is a particularly useful shorthad for times where you are awoken by a false,
or non-critical alarm while oncall and just want to return to bed. Additional checks are also performed to alert in cases where the service fails
immediately on start.

    Usage: /home/luke/.local/bin/oncall: [-s seconds] COMMAND
    COMMAND   DESCRIPTION
    start     Start the service.
    stop      Stop the service.
    restart   Restart the oncall service. Use [-s seconds] flag for a delayed restart.
    config    View or set configuration.
 
## Service
The script which monitors the input runs in an systemd user service file. This is started via `systemctl --user start oncall-monitor.service` or `oncall start`.

Options for the service are set via the oncall config command:

* oncall config set device DEVICE
    * Name of a pulse audio input device which will be used for monitoring. Example: `alsa_input.usb-046d_HD_Pro_Webcam_C920_8BBC58AF-02.analog-stereo`. Use `pactl list short sources` to get device names
    * oncall config set device alsa_input.pci-0000_00_1b.0.analog-stereo
* oncall config set endtime ENDTIME
    * Time to stop the service. This would likely be just before your alarm to wake up. Example: `07:00`
    * Must be in 24 hour format `HH:HH`
    * oncall config set endtime "08:00"
* oncall config set wav WAV
    * Path to the file that will play as an alarm. Example: `Downloads/siren.wav`. The file is copied into the program's config directory, so you may remove the original file after this config is set.
    * Must be playable by `aplay`. Will play on a loop until the service is stopped.
    * oncall config set wav ./mysoundfile.wav
* oncall config set inputvol VOLUME
    * Sets the input volume on the input device via PulseAudio.
    * Value must be between 1 and 100, with lower values having lower input sensitivity.
    * oncall config set inputvol 40

## Timer
A timer file is also provided for convenience. May be useful so monitoring is automatically started every night at a certain time.

## Troubleshooting
Review the `oncall-monitor` service status and logs: `systemctl --user status oncall-monitor`
If there is issues with playing the sound file, try playing it directly with aplay: `aplay -r 48000 "$soundfile" 2> /dev/null` 
