Proxmox: vGPU
=============

An Ansible role that configures the vGPU unlock hack on a Proxmox VE 7.2 host.

> This tutorial assumes you are using a clean install of Proxmox 7.2, or ymmv when using an existing installation. Make sure to always have backups!

Requirements
------------

This role requires that you get a hold of the Nvidia vGPU driver version 510.85.03 for linux kvm. Details on how to obtain it can be found [here](https://gitlab.com/polloloco/vgpu-proxmox/-/tree/master#obtaining-the-driver).

Once you obtained the driver file, make sure to place it in the `/opt/` directory **before** running the role. An example playbook is listed below.

Role Variables
--------------

To check the default variables, take a look at the [defaults](defaults/main.yml) file.

> Note: The roles defined in the dependencies can also have their variables customized. Take a look at their readmes for more information.

Dependencies
------------

- [proxmox_no_subscription](https://github.com/mirceanton/ansible_role-proxmox_no_subscription)
- [proxmox_iommu](https://github.com/mirceanton/ansible_role-proxmox_iommu)
- [proxmox_vfio](https://github.com/mirceanton/ansible_role-proxmox_vfio)
- [proxmox_driver_blacklist](https://github.com/mirceanton/ansible_role-proxmox_driver_blacklist)
- [rust](https://github.com/mirceanton/ansible_role-rust)

Example Playbook
----------------

``` yml
---
- name: Enable vGPU on all PVE hosts
  hosts: pve
  remote_user: root

  pre_tasks:
    - name: Copy Nvidia Driver package
      ansible.builtin.copy:
        src: "files/NVIDIA-Linux-x86_64-510.85.03-vgpu-kvm.run"
        dest: "/opt/NVIDIA-Linux-x86_64-510.85.03-vgpu-kvm.run"
        owner: root
        group: root
        mode: u=rwx,g=rwx,o=r

  roles:
    - role: mirceanton.proxmox_vgpu
```

LICENSE
-------

MIT


Credits
-------

This project would not have been possible without the work and help of these people:

- [DualCoder](https://github.com/DualCoder) for his original [vgpu_unlock](https://github.com/DualCoder/vgpu_unlock) repo with the kernel hooks
- [mbilker](https://github.com/mbilker) for the rust version, [vgpu_unlock-rs](https://github.com/mbilker/vgpu_unlock-rs)
- [KrutavShah](https://github.com/KrutavShah) for the [wiki](https://krutavshah.github.io/GPU_Virtualization-Wiki/)
- [HiFiPhile](https://github.com/HiFiPhile) for the [C version](https://gist.github.com/HiFiPhile/b3267ce1e93f15642ce3943db6e60776) of vgpu unlock
- [rupansh](https://github.com/rupansh) for the original [twelve.patch](https://github.com/rupansh/vgpu_unlock_5.12/blob/master/twelve.patch) to patch the driver on kernels >= 5.12
- [PolloLoco](https://gitlab.com/polloloco) for the [guide](https://gitlab.com/polloloco/vgpu-proxmox) and support via discord
- mbuchel#1878 on the [GPU Unlocking discord](https://discord.gg/5rQsSV3Byq) for [fourteen.patch](https://gist.github.com/erin-allison/5f8acc33fa1ac2e4c0f77fdc5d0a3ed1) to patch the driver on kernels >= 5.14
- [erin-allison](https://github.com/erin-allison) for the [nvidia-smi wrapper script](https://github.com/erin-allison/nvidia-merged-arch/blob/d2ce752cd38461b53b7e017612410a3348aa86e5/nvidia-smi)
- LIL'pingu#9069 on the [GPU Unlocking discord](https://discord.gg/5rQsSV3Byq) for his patch to nop out code that NVIDIA added to prevent usage of drivers with a version >= 460 with consumer cards
- The GPU Unlocking discord community

Author Information
------------------

A role developed by [Mircea-Pavel ANTON](https://www.mirceanton.com).
