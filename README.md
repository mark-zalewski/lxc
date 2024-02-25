# Enable GPU Passthrough from host to LXC Container (NVIDIA)


## Host / Hypervisor
1.) Download Drivers
`wget https://us.download.nvidia.com/XFree86/Linux-x86_64/535.86.05/NVIDIA-Linux-x86_64-535.86.05.run`

2.) Make file executable
`chmod +x NVIDIA-Linux-x86_64-535.86.05.run `

3.) Install drivers,
`./NVIDIA-Linux-x86_64-535.86.05.run`

## LXC Container
3.) Install drivers without kernal module.  Since the LXC container is only getting the GPU as a passthrough, we don't need to load the kernel drivers again.
`./NVIDIA-Linux-x86_64-535.86.05.run --no-kernel-module`
