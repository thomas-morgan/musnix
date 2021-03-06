# musnix

A module for real-time audio in NixOS.

### About:

**musnix** provides a set of simple, high-level configuration options for doing real-time audio work in [NixOS](https://nixos.org/), including optimizing the kernel, applying the [`CONFIG_PREEMPT_RT`](https://rt.wiki.kernel.org/index.php/Main_Page) patch to it, and adjusting various low-level system settings.

### Usage:
Add the following to your `configuration.nix`:
```
  imports = 
    [ <existing imports>
      /path/to/musnix
    ];
    
  musnix.enable = true;

  user.extraUsers.<username>.extraGroups = [ "audio" ];

```

### Options:

`musnix.enable`
* **Description:** Enable musnix, a module for real-time audio.
* **Default value:** `false`
* **Note:** This option must be set to `true` to use other musnix options.
* **Details:** If enabled, this option will do the following:

  * Activate the `performance` CPU frequency scaling governor.

  * Set `vm.swappiness` to 10.

  * Set the following udev rules:
    ```
    KERNEL=="rtc0", GROUP="audio"
    KERNEL=="hpet", GROUP="audio"
    ```

  * Set the `max_user_freq` of `/sys/class/rtc/rtc0` to 2048.

  * Set the `max-user-freq` of `/proc/sys/dev/hpet` to 2048.

  * Set the following PAM limits:
    ```
    @audio  -       memlock unlimited
    @audio  -       rtprio  99
    @audio  soft    nofile  99999
    @audio  hard    nofile  99999
    ```
  
  * Set the following environment variables to default install locations in NixOS:
    * `VST_PATH` 
    * `LVST_PATH` 
    * `LADSPA_PATH`
    * `LV2_PATH`
    * `DSSI_PATH`

  * Allow users to install plugins in the following directories:
    * `~/.vst`
    * `~/.lvst`
    * `~/.ladspa`
    * `~/.lv2`
    * `~/.dssi`

`musnix.kernel.optimize`
* **WARNING:** Enabling this option will rebuild your kernel.
* **Description:** If enabled, this option will configure the kernel to be preemptible, to use the deadline I/O scheduler, and to use the High Precision Event Timer (HPET).
* **Default value:** `false`

`musnix.kernel.realtime`
* **WARNING:** Enabling this option will rebuild your kernel.
* **Description:** If enabled, this option will apply the [`CONFIG_PREEMPT_RT`](https://rt.wiki.kernel.org/index.php/Main_Page) patch to the kernel.
* **Default value:** `false`

`musnix.kernel.latencytop`
* **WARNING:** Enabling this option will rebuild your kernel.
* **Description:** If enabled, this option will configure the kernel to use a latency tracking infrastructure that is used by the "latencytop" userspace tool.
* **Default value:** `false`

`musnix.ffado.enable`
* **Description:** If enabled, use the Free FireWire Audio Drivers (FFADO).
* **Default value:** `false`

`musnix.alsaSeq.enable`
* **Description:** If enabled, load ALSA Sequencer kernel modules.  Currently, this only loads the `snd_seq` and `snd_rawmidi` modules.
* **Default value:** `true`

`musnix.soundcardPciId`
* **Description:** The PCI ID of the primary soundcard. Used to set the PCI latency timer.

  To find the PCI ID of your soundcard:
  ```
  lspci | grep -i audio
  ```
* **Default value:** `""`
* **Example value:** `"00:1b.0"`

### More info:
* http://wiki.linuxaudio.org/wiki/system_configuration
* https://wiki.archlinux.org/index.php/Pro_Audio
