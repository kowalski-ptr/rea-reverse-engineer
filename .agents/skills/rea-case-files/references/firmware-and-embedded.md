# Case file — firmware & embedded

Firmware-image triage, MCU/IoT blobs, and the protocols embedded devices
speak. `inspect_firmware_regions` and `extract_firmware` do the heavy
lifting on Linux with caller-supplied Binwalk/Unblob — see the
[firmware guide](https://github.com/morluto/rea/blob/main/docs/firmware-analysis.md).
Static analysis only: don't flash or run anything these templates uncover.

### Firmware image triage

```text
Analyze <firmware image/bin/img> with REA, using `inspect_firmware_regions`
where advertised and `extract_firmware` into a new output directory only if
extraction is actually needed. Identify the container format, the
partitions/filesystems present (JFFS2, SquashFS, UBI, cramfs…), map each
region to architecture and load address, locate the kernel and rootfs, and
list boot entries, init scripts, and services. Show offsets and entropy
observations behind every claim. Static analysis only — do not flash or run
anything.
```

### MCU/IoT binary analysis

```text
Analyze <firmware blob> for a <ARM Cortex-M / MIPS / AVR / ESP32> target.
Find the vector table and reset handler, reconstruct the memory map from
literal pools and peripheral base addresses, identify the RTOS (FreeRTOS,
Zephyr…) from symbol/string fingerprints, and map the main task loop and
peripheral drivers (UART, SPI, I2C, radio stack) to function addresses.
```

### Embedded protocol and update-mechanism recovery

```text
Recover from <firmware>: its network-stack usage (hardcoded IPs, DNS,
MQTT/HTTP endpoints, ports), its OTA update path (download URL, signature
check, flash write), and any credential material (keys, default passwords,
debug tokens). For each finding, give the function/address and say
explicitly whether it's confirmed statically or is still a hypothesis
needing runtime confirmation.
```
