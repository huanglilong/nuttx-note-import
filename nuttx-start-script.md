# Nuttx start script

### 1. defconfig's config

CONFIG\_FS\_READABLE=y

CONFIG\_FS\_WRITABLE=y

CONFIG\_FS\_FAT=y \(rcS file used mkfatfs command\)

CONFIG\_FS\_ROMFS=y

CONFIG\_NSH\_ROMFSETC=y

CONFIG\_NSH\_ROMFSMOUNTPT="\/etc" \(default value\)

CONFIG\_NSH\_INITSCRIPT="init.d\/rcS" \(default value\)

CONFIG\_NSH\_ROMFSDEVNO=0 \(default value\)

CONFIG\_NSH\_ROMFSSECTSIZE=128

CONFIG\_NSH\_ARCHROMFS=y \(use [stm32f429i-disco/include/nsh_romfsimg.h](https://github.com/huanglilong/nuttx/blob/master/configs/stm32f429i-disco/include/nsh_romfsimg.h)\)

[MORE INFO](http://nuttx.org/Documentation/NuttShell.html#startupscript)

### 2.etc directory

![](/assets/nuttx_etc.png)

### 3. generate ROMFS

$ genromfs -f romfs\_img -d etc

$ xxd -i romfs\_img &gt; nsh\_romfsimg.h

copy nsh_romfsimg.h to configs/stm32f429i-disco/include directory

![](/assets/nsh_etc_rcS.png)
