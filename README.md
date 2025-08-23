# OpenWrt




- ### Backup/Restore Original EMMC System

Supports backing up/restoring the `EMMC` to a `SD card` or a `USB flash drive`. We recommend that you backup the Android TV system that comes with the box before installing the OpenWrt system in a brand-new box for future use in restoring the TV system, etc.

Please boot OpenWrt system from a `SD card` or a `USB flash drive`, then from the browser, Browser access to OpenWrt's IP (e.g. 192.168.1.1) → `Log in to OpenWrt with the default account` → `System Menu` → `TTYD Terminal` → enter the command

```yaml
openwrt-ddbr
```

Follow the prompts to enter `b` to backup the system, or enter `r` to restore the system.

> [!IMPORTANT]
> In addition, the Android system can also be flashed into eMMC using the method of flashing via a cable. The download image of the Android system can be found in [Tools](https://github.com/ophub/kernel/releases/tag/tools).

- ### Control LED Display

Browser access to OpenWrt's IP (e.g. 192.168.1.1) → `Log in to OpenWrt with the default account` → `System Menu` → `TTYD Terminal` → enter the command

```yaml
openwrt-openvfd
```

Refer to [LED Screen Display Control Description](https://github.com/ophub/amlogic-s9xxx-armbian/blob/main/documents/led_screen_display_control.md) for debugging.

- ### Restore to Initial State

Browser access to OpenWrt's IP (e.g. 192.168.1.1) → `Log in to OpenWrt with the default account` → `System Menu` → `Amlogic Service` → `Backup Firmware Config` → `Snapshot Management` → `Select Initialize Snapshot`, and click on `Restore Snap` to revert to the initial state.

Alternatively, you can navigate to `System menu` → `TTYD Terminal` → Enter the command `firstboot` to restore the system to its initial state. Both methods yield the same result.

- ### More Usage Instructions

Some common problems that might be encountered during the use of OpenWrt can be found in the [User Guide](./documents)

## Local Packaging

1. Install necessary packages (for Ubuntu 22.04 LTS users)
```yaml
sudo apt-get update -y
sudo apt-get full-upgrade -y
# For Ubuntu-22.04
sudo apt-get install -y $(curl -fsSL https://tinyurl.com/ubuntu2204-make-openwrt)
```
2. Clone repository to local `git clone --depth 1 https://github.com/ophub/amlogic-s9xxx-openwrt.git`
3. In the root directory of `~/amlogic-s9xxx-openwrt`, create `openwrt-armsr` folder, and upload the `openwrt-armsr-armv8-generic-rootfs.tar.gz` file to this directory.
4. Enter the packaging command in the root directory of `~/amlogic-s9xxx-openwrt`, such as `sudo ./remake -b s905x3 -k 6.1.10`. The packaged OpenWrt firmware is placed in the `out` folder in the root directory.

- ### Explanation of Local Packaging Parameters

| Parameter | Meaning       | Description |
| --------- | ------------- | ----------- |
| -b        | Board         | Specifies the device code(s) to build. For example, `-b s905x3` builds the device with code s905x3. Multiple device codes can be connected using underscores, such as `-b s905x3_s905d`. Special values: `all` builds all devices; `top50` builds the top 50 devices in the device list; `rest50` builds from the 51st device to the end. The full list of device codes can be found in the `BOARD` entries of [model_database.conf](make-openwrt/openwrt-files/common-files/etc/model_database.conf). Default: `all` |
| -r        | KernelRepo    | Specify the `<owner>/<repo>` of the github.com kernel repository. Default: `ophub/kernel` |
| -u        | kernelUsage   | Set the `tag suffix` of the kernel to be used, such as [stable](https://github.com/ophub/kernel/releases/tag/kernel_stable), [flippy](https://github.com/ophub/kernel/releases/tag/kernel_flippy), [dev](https://github.com/ophub/kernel/releases/tag/kernel_dev), [beta](https://github.com/ophub/kernel/releases/tag/kernel_beta). Default: `stable` |
| -k        | Kernel        | Specify the [kernel](https://github.com/ophub/kernel/releases/tag/kernel_stable) name, such as `-k 5.10.125`. Connect multiple kernels with `_`, such as `-k 5.10.125_5.15.50`. The kernel version freely specified by the `-k` parameter is only valid for kernels using `stable/flippy/dev/beta`. Other kernel series such as [rk3588](https://github.com/ophub/kernel/releases/tag/kernel_rk3588) / [rk35xx](https://github.com/ophub/kernel/releases/tag/kernel_rk35xx) / [h6](https://github.com/ophub/kernel/releases/tag/kernel_h6) can only use specific kernels. |
| -a        | AutoKernel    | Set whether to automatically adopt the latest version of the same series of kernels. When set to `true`, it will automatically search the kernel library for updates of the same series as the kernel specified in `-k`, such as 5.10.125, and will automatically switch to the latest version if there is a version later than 5.10.125. When set to `false`, it will compile the specified version of the kernel. Default: `true` |
| -s        | Size          | Set the size of the system's image partitions. When setting only the ROOTFS partition size, you can specify a single value, for example: `-s 1024`. When setting both BOOTFS and ROOTFS partition sizes, use / to connect the two values, for example: `-s 256/1024`. The default value is `256/1024` |
| -n        | BuilderName   | Set the signature of the OpenWrt system builder. Do not include spaces when setting signatures. Default: `none` |

- `sudo ./remake` : Use default configuration, use the latest kernel package in the kernel library, and package all models of TV boxes.
- `sudo ./remake -b s905x3 -k 6.1.10` : Recommended. Use default configuration for related kernel packaging.
- `sudo ./remake -b s905x3 -k 6.1.y` : Package the relevant kernels using the default configuration; the kernel utilizes the latest version of the 6.1.y series.
- `sudo ./remake -b s905x3_s905d -k 6.1.10_5.15.50` : Use the default configuration and package multiple kernels at the same time. Use `_` to connect multiple kernel parameters.
- `sudo ./remake -b s905x3 -k 6.1.10 -s 1024` : Use the default configuration, specify a kernel, a model for packaging, and set the firmware size to 1024 MiB.
- `sudo ./remake -b s905x3_s905d` : Use default configuration, package all kernels for multiple models of TV boxes, use `_` to connect multiple models.
- `sudo ./remake -k 6.1.10_5.15.50` : Use the default configuration, specify multiple kernels, package all models of TV boxes, and connect kernel packages with `_`.
- `sudo ./remake -k 6.1.10_5.15.50 -a true` : Use the default configuration, specify multiple kernels, package all models of TV boxes, and connect kernel packages with `_`. Automatically upgrade to the latest kernel of the same series.
- `sudo ./remake -s 1024 -k 6.1.10` : Use the default configuration, set the firmware size to 1024 MiB, and specify the kernel as 6.1.10 to package all models of TV boxes.

## Use GitHub Actions for Compilation

You can modify the related personalized firmware configuration files in the [config](config) directory, as well as the [.yml](.github/workflows) file, customize and compile your OpenWrt firmware, and the firmware can be uploaded to `Actions` and `Releases` on github.com.

1. You can view the personalized firmware configuration instructions in the [user documentation](./documents). The compilation process control file is [.yml](https://github.com/ophub/amlogic-s9xxx-openwrt/blob/main/.github/workflows/build-openwrt-system-image.yml)
2. New compilation: In github.com's [Action](https://github.com/ophub/amlogic-s9xxx-openwrt/actions) select ***`Build OpenWrt system image`***. Click the ***`Run workflow`*** button for one-stop firmware compilation and packaging.
3. Re-compilation: If there is already a compiled `openwrt-armsr-armv8-generic-rootfs.tar.gz` file in [Releases](https://github.com/ophub/amlogic-s9xxx-openwrt/releases), and you just want to remake other different boards, you can skip compiling the Armbian source files and directly use [build-openwrt-using-releases-files.yml](.github/workflows/build-openwrt-using-releases-files.yml) for secondary build.
4. More Support: The compiled `openwrt-armsr-armv8-generic-rootfs.tar.gz` file is a universal file for making firmware for different boards. It is also applicable for creating OpenWrt firmware using [unifreq](https://github.com/unifreq/openwrt_packit)'s packaging scripts. As the pioneer of using OpenWrt and Armbian systems in TV boxes, he provides support for more devices, such as OpenWrt (QEMU version) used in the Armbian system through a `KVM` virtual machine, and Amlogic, Rockchip, and Allwinner series, etc. For packaging methods, please refer to the instructions in his repository. In Actions, through [build-openwrt-using-unifreq-scripts.yml](.github/workflows/build-openwrt-using-unifreq-scripts.yml), you can call his packaging scripts to create more firmware.

```yaml
- name: Package armsr-armv8 as OpenWrt
  uses: ophub/amlogic-s9xxx-openwrt@main
  with:
    openwrt_path: openwrt/bin/targets/*/*/*rootfs.tar.gz
    openwrt_board: s905x3_s905x2_s905x_s905w_s905d_s922x_s912
    openwrt_kernel: 6.1.y_5.15.y
```

- ### GitHub Actions Input Parameters Explanation

These parameters correspond to the `local packaging command`, please refer to the explanations above.

| Parameter | Default Value | Description |
| --------- | ------------- | ----------- |
| openwrt_path | None | Set the file path of `openwrt-armsr-armv8-generic-rootfs.tar.gz`, you can use relative path like `openwrt/bin/targets/*/*/*rootfs.tar.gz` or a network file download URL like `https://github.com/*/releases/*/*rootfs.tar.gz` |
| openwrt_board | all | Set the `board` of the box to be packaged, functionality refers to `-b` |
| kernel_repo | ophub/kernel | Specify `<owner>/<repo>` of the kernel repository on github.com, functionality refers to `-r` |
| kernel_usage | stable | Set the `tags suffix` of the kernel to be used, functionality refers to `-u` |
| openwrt_kernel | 6.1.y_5.15.y | Set the kernel version, functionality refers to `-k` |
| auto_kernel | true | Set whether to automatically adopt the latest version of the same series of kernels, functionality refers to `-a` |
| openwrt_size | 256/1024 | Set the size of the system BOOTFS and ROOTFS partitions, function reference `-s` |
| builder_name | None | Set the signature of the OpenWrt system builder, functionality refers to `-n` |

- ### GitHub Actions Output Variables Explanation

To upload to `Releases`, you need to set `Workflow read/write permissions` for repository. For details, see [usage instructions](./documents/README.md#2-set-the-privacy-variable-github_token).

| Parameter                      | Default Value     | Description                            |
| ------------------------------ | ----------------- | -------------------------------------- |
| ${{ env.PACKAGED_OUTPUTPATH }} | out               | OpenWrt system files output path       |
| ${{ env.PACKAGED_OUTPUTDATE }} | 04.13.1058        | Packaging date (month.day.hourminute)  |
| ${{ env.PACKAGED_STATUS }}     | success / failure | Packaging status. success / failure    |

## Compilation Options of openwrt-*-rootfs.tar.gz for Packaging

| Option | Value |
| ------ | ----- |
| Target System | Arm SystemReady (EFI) compliant |
| Subtarget | 64-bit (armv8) machines |
| Target Profile | Generic EFI Boot |
| Target Images | tar.gz |

For more information, please refer to the [User Documentation](./documents)

## Default Information for OpenWrt Firmware

| Name | Value |
| ---- | ---- |
| Default IP | 192.168.1.1 |
| Default Account | root |
| Default Password | password |
| Default WIFI Name | OpenWrt |
| Default WIFI Password | None |

## Compile the Kernel

For instructions on how to compile the kernel, see [compile-kernel](https://github.com/ophub/amlogic-s9xxx-armbian/tree/main/compile-kernel).

```yaml
- name: Compile the kernel
  uses: ophub/amlogic-s9xxx-armbian@main
  with:
    build_target: kernel
    kernel_version: 6.1.y_5.15.y
    kernel_auto: true
    kernel_sign: -yourname
```

## Resource Description

When making the OpenWrt system, the files used, such as [kernel](https://github.com/ophub/kernel) and [u-boot](https://github.com/ophub/u-boot), are the same files used to create the [Armbian](https://github.com/ophub/amlogic-s9xxx-armbian) system. In order to avoid repeated maintenance, related content has been classified and placed in the corresponding resource repositories, and will be automatically downloaded from the relevant repositories during use.

The [u-boot](https://github.com/ophub/u-boot), [kernel](https://github.com/ophub/kernel) and other resources used by this system mainly come from the [unifreq/openwrt_packit](https://github.com/unifreq/openwrt_packit) project. Some files are shared by users in the [Pull](https://github.com/ophub/amlogic-s9xxx-openwrt/pulls) and [Issues](https://github.com/ophub/amlogic-s9xxx-openwrt/issues) of the [amlogic-s9xxx-openwrt](https://github.com/ophub/amlogic-s9xxx-openwrt) / [amlogic-s9xxx-armbian](https://github.com/ophub/amlogic-s9xxx-armbian) / [luci-app-amlogic](https://github.com/ophub/luci-app-amlogic) / [kernel](https://github.com/ophub/kernel) / [u-boot](https://github.com/ophub/u-boot) and other projects. `unifreq` has opened the door for us to use OpenWrt in TV boxes. Deeply influenced by him, my firmware production and usage follow his consistent standards. To thank these pioneers and sharers, I have recorded them in [CONTRIBUTORS.md](https://github.com/ophub/amlogic-s9xxx-armbian/blob/main/CONTRIBUTORS.md). Once again, I want to thank everyone for giving new life and meaning to the boxes.

## Other Distributions

- [unifreq](https://github.com/unifreq/openwrt_packit) has made `OpenWrt` systems for more boxes such as Amlogic, Rockchip, and Allwinner. It is a benchmark in the box circle, recommended for use.
- The [amlogic-s9xxx-armbian](https://github.com/ophub/amlogic-s9xxx-armbian) project provides the `Armbian` system used in the box, which is also applicable in devices that support OpenWrt.

## Links

- [unifreq](https://github.com/unifreq/openwrt_packit)
- [OpenWrt](https://github.com/openwrt/openwrt)
- [coolsnowwolf](https://github.com/coolsnowwolf/lede)
- [immortalwrt](https://github.com/immortalwrt/immortalwrt)

## License

The amlogic-s9xxx-openwrt © OPHUB is licensed under the [GPL-2.0](https://github.com/ophub/amlogic-s9xxx-openwrt/blob/main/LICENSE) license

