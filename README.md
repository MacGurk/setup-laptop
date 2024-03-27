# Setup Fedora Laptop

Ansible playbook to automate the setup of a Fedora installation.
If the laptop is a Asus ROG Zephyrus GA402XI additional asus-linux stuff and NVIDIA drivers will be installed.

Source for all the Asus Linux specific config comes from this guide: https://asus-linux.org/guides/fedora-guide/

Run the playbook and ask for 'Become' password with the following command:
```
ansible-playbook setup.yml -K
```

## Manual settings

### System boot configuration

This is specific if you run NVIDIA GPU.
Edit system boot configuration:
```
sudo vim /etc/default/grub
```

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="rd.driver.blacklist=nouveau modprobe.blacklist=nouveau nvidia-drm.modeset=1 rhgb quiet preemt=full"
GRUB_DISABLE_RECOVERY="true"
GRUB_ENABLE_BLSCFG=true
```

* The `nouveau` stuf will blacklist the nuoveau driver from being loaded
* `nvidia-drm.modset=1` allow the dGPU to display on external monitors.
    * Check if this is set correctly with `sudo cat /sys/module/nvidia_drm/parameters/modset` should return `Y`.
* `rhgb` is the Red Hat Graphical Boot
* `quiet` prevents output of the kernel boot process
* `preemt=full` allows other processes to interrupt kernel processes, like I/O

Some settings might appreare multiple times, they can be deleted safely.
If there is something with a `nomodset` this should be removed, as else graphics acceleration is disabled.

Update grub:
```
sudo grub2-mkconfig -o /etc/grub2.cfg
```

Reboot.
