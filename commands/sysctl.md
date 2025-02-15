- Set kernel parameter permanently.

  ```
  echo "vm.swappiness=0" > /etc/sysctl.d/80-disable-swap.conf
  ```

- Reload kernel configuration.

  ```
  sysctl --system
  ```

- Reload kernel configuration from specific file.

  ```
  sysctl -p /etc/sysctl.d/80-disable-swap.conf
  ```
