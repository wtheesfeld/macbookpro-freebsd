macbookpro-freebsd
==================
Running FreeBSD 10.3-STABLE on Macbook Pro 9,2 (mid 2012)

#Wireless
Wireless is bcm4331, neither bwi or bwn drivers work, needs version 5 drivers
-    temp fix: using dwa-131 dongle
-    /boot/loader.conf:
```
        if_rsu_load="YES"
        legal.realtek.license_ack=1
        rsu_rtl18712fw_load="YES"
```
#Touchpad
Touchpad support works with ums driver automatically loaded, but no multi-touch.
-    Fix: load ATP driver
-    /boot/loader.conf:
```    
        atp_load="YES"
```

#Suspend/Resume on lid close/open & Screen Dimming
Loading ACPI_VIDEO module will automatically brighten screen when AC is plugged in, and automatically dim screen when AC is unplugged. It will also give the ability to suspend on lid close and resume when opening lid.
-    Fix: load ACPI_VIDEO driver
-    /boot/loader.conf:
```    
        acpi_video_load="YES"
```
-    For suspend/resume
-    /etc/sysctl.conf:
``` 
        hw.acpi.lid_switch_state=S3
``` 

#SMC Sensors (temps and fan control)
##NOTE: I patched against the 10.3 RELEASE source
Download and apply this patch
-    https://github.com/wilthee/macbookpro-freebsd/blob/master/asmc_92.patch
-    move it to /usr/src/sys/dev/asmc:
``` 
         mv asmc_92.patch /usr/sys/dev/asmc
``` 
-    apply the patch
``` 
         cd /usr/src/sys/dev/asmc; patch < asmc_92.patch
``` 
-    Now make the new module
``` 
         cd /usr/src/sys/modules/asmc; make
``` 
-    Make backup of old module:
``` 
          cp /boot/kernel/asmc.ko /boot/kernel/asmc.old
``` 
-    Copy new module
``` 
          cp /usr/src/sys/modules/asmc/asmc.ko /boot/kernel/asmc.ko
``` 
-    Test new module
``` 
          kldload asmc
``` 
-    If it loads ok, load module at boot:
-        /boot/loader.conf:
```        
            asmc_load="YES"
```
     After loading module, the following change can be made to spin the fan a little faster:
-        /boot/loader.conf:
``` 
             dev.asmc.0.fan.0.minspeed=3000
