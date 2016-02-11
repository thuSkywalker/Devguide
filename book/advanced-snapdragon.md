# Snapdragon Advanced

## Wifi-settings

<aside class="todo">These are notes for advanced developers.</aside>

Connect to the Linux shell (see [console instructions](advanced-system-console.html#snapdragon-flight-wiring-the-console)).

### Access point mode

If you want the Snapdragon to be a wifi access point (AP mode), edit the file: `/etc/hostapd.conf` and set:

```
ssid=EnterYourSSID
wpa_passphrase=EnterYourPassphrase
```

Then configure AP mode:

```
/usr/local/qr-linux/wificonfig.sh -s softap
reboot
```

### Station mode

If you want the Snapdragon to connect to your existing wifi, edit the file: `/etc/wpa_supplicant/wpa_supplicant.conf` and add your network settings:

```
network={
    ssid="my existing network ssid"
    psk="my existing password"
}
```

Then configure station mode:

```
/usr/local/qr-linux/wificonfig.sh -s station
reboot
```


## Troubleshooting

### No space left on device

Sometimes make ***-load fails to upload:

```
failed to copy 'mainapp' to '/home/linaro/mainapp': No space left on device
```

This can happen if ramdumps fill up the disk. To clean up, do:

```
rm -rf /var/log/ramdump/*
```

### ADSP restarts

If the mini-dm console suddently shows a whole lot of INIT output, the ADSP side has crashed. The reasons for it are not obvious, e.g. it can be some segmentation fault, null pointer exception, etc..

The mini-dm console output typically looks like this:

```
[08500/02]  20:32.332  Process Sensor launched with ID=1   0130  main.c
[08500/02]  20:32.337  mmpm_register: MMPM client for USM ADSP core 12  0117  UltrasoundStreamMgr_Mmpm.cpp
[08500/02]  20:32.338  ADSP License DB: License validation function with id 164678 stored.  0280  adsp_license_db.cpp
[08500/02]  20:32.338  AvsCoreSvc: StartSvcHandler Enter  0518  AdspCoreSvc.cpp
[08500/02]  20:32.338  AdspCoreSvc: Started successfully  0534  AdspCoreSvc.cpp
[08500/02]  20:32.342  DSPS INIT  0191  sns_init_dsps.c
[08500/02]  20:32.342  INIT DONE  0224  sns_init_dsps.c
[08500/02]  20:32.342  Sensors Init : waiting(1)  0160  sns_init_dsps.c
[08500/02]  20:32.342  INIT DONE  0224  sns_init_dsps.c
[08500/02]  20:32.342  THRD CREATE: Thread=0x39 Name(Hex)= 53, 4e, 53, 5f, 53, 4d, 47, 52  0186  qurt_elite_thread.cpp
[08500/02]  20:32.343  THRD CREATE: Thread=0x38 Name(Hex)= 53, 4e, 53, 5f, 53, 41, 4d, 0  0186  qurt_elite_thread.cpp
[08500/02]  20:32.343  THRD CREATE: Thread=0x37 Name(Hex)= 53, 4e, 53, 5f, 53, 43, 4d, 0  0186  qurt_elite_thread.cpp
[08500/02]  20:32.343  THRD CREATE: Thread=0x35 Name(Hex)= 53, 4e, 53, 5f, 50, 4d, 0, 0  0186  qurt_elite_thread.cpp
[08500/02]  20:32.343  THRD CREATE: Thread=0x34 Name(Hex)= 53, 4e, 53, 5f, 53, 53, 4d, 0  0186  qurt_elite_thread.cpp
[08500/02]  20:32.343  THRD CREATE: Thread=0x33 Name(Hex)= 53, 4e, 53, 5f, 44, 45, 42, 55  0186  qurt_elite_thread.cpp
[08500/02]  20:32.343  Sensors Init : ///////////init once completed///////////  0169  sns_init_dsps.c
[08500/02]  20:32.342  loading BLSP configuration  0189  blsp_config.c
[08500/02]  20:32.343  Sensors DIAG F3 Trace Buffer Initialized  0260  sns_init_dsps.c
[08500/02]  20:32.345  INIT DONE  0224  sns_init_dsps.c
[00053/03]  20:32.345  Unsupported algorithm service id 0  0953  sns_scm_ext.c
[08500/02]  20:32.346  INIT DONE  0224  sns_init_dsps.c
[08500/02]  20:32.347  INIT DONE  0224  sns_init_dsps.c
[08500/02]  20:32.347  INIT DONE  0224  sns_init_dsps.c
[08500/02]  20:32.546  HAP:159:unable to open the specified file path  0167  file.c
[08500/04]  20:32.546  failed to open /usr/share/data/adsp/blsp.config  0204  blsp_config.c
[08500/04]  20:32.546  QDSP6 Main.c: blsp_config_load() failed  0261  main.c
[08500/02]  20:32.546  Loaded default UART-BAM mapping  0035  blsp_config.c
[08500/02]  20:32.546  UART tty-1: BAM-9  0043  blsp_config.c
[08500/02]  20:32.546  UART tty-2: BAM-6  0043  blsp_config.c
[08500/02]  20:32.546  UART tty-3: BAM-8  0043  blsp_config.c
[08500/02]  20:32.546  UART tty-4: BAM-2  0043  blsp_config.c
[08500/02]  20:32.546  UART tty-5: BAM N/A  0048  blsp_config.c
[08500/02]  20:32.546  UART tty-6: BAM N/A  0048  blsp_config.c
[08500/02]  20:32.547  HAP:111:cannot find /oemconfig.so  0141  load.c
[08500/03]  20:32.547  HAP:4211::error: -1: 0 == dynconfig_init(&conf, "security")   0696  sigverify.c
[08500/02]  20:32.548  HAP:76:cannot find /voiceproc_tx.so  0141  load.c
[08500/02]  20:32.550  HAP:76:cannot find /voiceproc_rx.so  0141  load.c
```
