* Monitor I/O statistics for specific devices, `sda1`, and `sdb2` without CPU-related statistics, every 2 seconds

  ```
  iostat -d sda1 sdb2 2
  ```

* Display extended disk I/O statistics, every 2 seconds

  ```
  iostat -x 2
  ```

* Display only CPU-related statistics, every 2 seconds

  ```
  iostat -c 2
  ```
  
* Display I/O statistics for all devices with a timestamp, every 2 seconds

  ```
  iostat -t 2
  ```

* Monitor I/O statistics for specific devices every 5 seconds for a total of 10 updates

  ```
  iostat sda sdb 5 10
  ```

#### CPU Statistics

- %user: Percentage of CPU utilization at the user level.
- %nice: Percentage of CPU utilization with a nice priority.
- %system: Percentage of CPU utilization at the system (kernel) level.
- %iowait: Percentage of time the CPU was idle while waiting for I/O operations to complete.
- %steal: Percentage of time spent in involuntary wait by the virtual CPU.
- %idle: Percentage of time the CPU was idle and not waiting for I/O operations

#### Device Statistics

- Device: Name of the device (e.g., sda, sdb).
- tps: Number of transfers per second.
- kB_read/s: Kilobytes read from the device per second.
- kB_wrtn/s: Kilobytes written to the device per second.
- kB_read: Total kilobytes read from the device.
- kB_wrtn: Total kilobytes written to the device
