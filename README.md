# Enable GPU Passthrough from host to LXC Container (NVIDIA)

## Host / Hypervisor

1.) Install host drivers

When doing PCIe passthrough, the first step is to blacklist the driver to ensure the host kernel doesn’t try to load the device. Here we need to do the opposite: Install and configure the correct drivers.

You need to install the kernel headers, which for me on Proxmox is the pve-headers package.
`apt install pve-headers-$(uname -r)`

2.) Next, you’ll need to install the actual nvidia drivers. The easiest way to do this is to download the driver from nvidia.com. This not only ensures you’re using the latest driver, but means it won’t accidentally update during a system update, as it’s important that the host and guest OS have the exact same driver version. You can still install it using the system package manager, just be aware of updates - especially if the guest and host OS are different distributions.
`wget https://us.download.nvidia.com/XFree86/Linux-x86_64/535.86.05/NVIDIA-Linux-x86_64-535.86.05.run`

3.) Make file executable
`chmod +x NVIDIA-Linux-x86_64-535.86.05.run `

4.) Install drivers,
`./NVIDIA-Linux-x86_64-535.86.05.run`

5.) Run `nvidia-smi` to ensure the drivers are working.

6.) Next you’ll need to make sure the drivers are loaded on boot. To do this, edit the file `/etc/modules-load.d/modules.conf` and add the following to it:
```
# Nvidia modules
nvidia
nvidia_uvm
```

7.) Find NVIDIA devices on host
`ls -l /dev/nv*`

Make a note of the device addresses:
![image](https://github.com/mark-zalewski/lxc/assets/64677761/27dcaffb-647d-422b-9291-47a9aa957918)

8.) Update the LXC config file located at `/etc/pve/lxc`  Add the following lines, the first 3 are the address locations of the NVIDIA devices.  The last five are the NVIDIA devices themselves being mounted into the container.
```
lxc.cgroup2.devices.allow: a
lxc.cgroup2.devices.allow: c 195:* rwm
lxc.cgroup2.devices.allow: c 507:* rwm
lxc.cgroup2.devices.allow: c 510:* rwm
lxc.mount.entry: /dev/nvidia0 dev/nvidia0 none bind,optional,create=file
lxc.mount.entry: /dev/nvidiactl dev/nvidiactl none bind,optional,create=file
lxc.mount.entry: /dev/nvidia-uvm dev/nvidia-uvm none bind,optional,create=file
lxc.mount.entry: /dev/nvidia-modeset dev/nvidia-modeset none bind,optional,create=file
lxc.mount.entry: /dev/nvidia-uvm-tools dev/nvidia-uvm-tools none bind,optional,create=file
```

## LXC Container
1.) Download Drivers
`wget https://us.download.nvidia.com/XFree86/Linux-x86_64/535.86.05/NVIDIA-Linux-x86_64-535.86.05.run`

2.) Make file executable
`chmod +x NVIDIA-Linux-x86_64-535.86.05.run`

3.) Install drivers without kernal module.  Since the LXC container is only getting the GPU as a passthrough, we don't need to load the kernel drivers again.
`./NVIDIA-Linux-x86_64-535.86.05.run --no-kernel-module`

4.) Run `nvidia-smi` to ensure the drivers are working. Note: any running transcodes will not show up on the LCX host, only on the hypervisor host itself.
