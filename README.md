# Enable GPU Passthrough from host to LXC Container (NVIDIA)


## Host / Hypervisor
1.) Download Drivers
`wget https://us.download.nvidia.com/XFree86/Linux-x86_64/535.86.05/NVIDIA-Linux-x86_64-535.86.05.run`

2.) Make file executable
`chmod +x NVIDIA-Linux-x86_64-535.86.05.run `

3.) Install drivers,
`./NVIDIA-Linux-x86_64-535.86.05.run`

4.) Find NVIDIA devices on host
`ls -l /dev/nv*`

Make a note of the device addresses:
![image](https://github.com/mark-zalewski/lxc/assets/64677761/27dcaffb-647d-422b-9291-47a9aa957918)

5.) Update the LXC config file located at `/etc/pve/lxc`  Add the following lines, the first 3 are the address locations of the NVIDIA devices.  The last five are the NVIDIA devices themselves being mounted into the container.
```
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
3.) Install drivers without kernal module.  Since the LXC container is only getting the GPU as a passthrough, we don't need to load the kernel drivers again.
`./NVIDIA-Linux-x86_64-535.86.05.run --no-kernel-module`
