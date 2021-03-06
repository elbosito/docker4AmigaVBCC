[![Build Status](https://drone-gh.intercube.gr/api/badges/walkero-gr/docker4AmigaVBCC/status.svg)](https://drone-gh.intercube.gr/walkero-gr/docker4AmigaVBCC)
[![Docker Pulls](https://img.shields.io/docker/pulls/walkero/docker4amigavbcc?color=brightgreen)](https://hub.docker.com/r/walkero/docker4amigavbcc)

# docker4AmigaVBCC
Two docker images with VBCC compiler for cross compiling software for AmigaOS 3 and 4. It is based on Ubuntu and has everything needed (vbcc compiler, SDKs, libraries) for compiling your applications.

## AmigaOS 68k development image
The **docker4amigavbcc:latest-m68k** image contains the following:

| app               | version               | source
|-------------------|-----------------------|-----------------------------------|
| vbcc              | 0.9g (04-Oct-2019)    | http://sun.hasenbraten.de/vbcc/
| vlink             | 0.16d (18-Apr-2020)   | http://sun.hasenbraten.de/vlink/
| vasm              | 1.8h (18-Apr-2020)    | http://sun.hasenbraten.de/vasm/
| NDK               | 3.9                   | http://www.haage-partner.de/download/AmigaOS/
| MUI 3.x dev       | 3.8                   | http://muidev.de/downloads
| MUI 5.x dev       | 5.0-2019R4            | http://muidev.de/downloads
| MCC_GuiGfx        | 19.2                  | http://aminet.net/package/dev/mui/MCC_Guigfx
| MCC_TextEditor    | 15.53                 | http://aminet.net/package/dev/mui/MCC_TextEditor-15.53
| Roadshow SDK      | 1.4 (15.3.2019)       | https://www.amigafuture.de/app.php/dlext/?view=detail&df_id=3658
| AmiSSL SDK        | 4.5                   | https://github.com/jens-maus/amissl/releases/tag/4.5
| Posix Lib         | 2.6                   | http://aminet.net/package/dev/c/vbcc_PosixLib
| FlexCat           | 2.18                  | https://github.com/adtools/flexcat/releases/tag/2.18

## PPC development image
The **docker4amigavbcc:latest-ppc** image contains the following:

| app               | version               | source
|-------------------|-----------------------|-----------------------------------|
| vbcc              | 0.9g (04-Oct-2019)    | http://sun.hasenbraten.de/vbcc/
| vlink             | 0.16d (18-Apr-2020)   | http://sun.hasenbraten.de/vlink/
| vasm              | 1.8h (18-Apr-2020)    | http://sun.hasenbraten.de/vasm/
| AmigaOS 4 SDK     | 53.30                 | http://www.hyperion-entertainment.com/
| MUI 5.x dev       | 5.0-2019R4            | http://muidev.de/downloads
| AmiSSL SDK        | 4.5                   | https://github.com/jens-maus/amissl/releases/tag/4.5
| FlexCat           | 2.18                  | https://github.com/adtools/flexcat/releases/tag/2.18

## How to create a docker container

To create a container based on this image run in the terminal:

```bash
docker run -it --rm --name amigavbcc-m68k -v "$PWD"/code:/opt/code -w /opt/code walkero/docker4amigavbcc:latest-m68k /bin/bash
docker run -it --rm --name amigavbcc-ppc -v "$PWD"/code:/opt/code -w /opt/code walkero/docker4amigavbcc:latest-ppc /bin/bash
```

If you want to use it with **docker-compose**, you can create a *docker-compose.yml* file, with the following content:

```yaml
version: '3'

services:
  vbcc-m68k:
    image: 'walkero/docker4amigavbcc:latest-m68k'
    volumes:
      - './code:/opt/code'

  vbcc-ppc:
    image: 'walkero/docker4amigavbcc:latest-ppc'
    volumes:
      - './code:/opt/code'
```

And then you can create and get into the container by doing the following:
```bash
docker-compose up -d
docker-compose vbcc-m68k exec bash
docker-compose vbcc-ppc exec bash
```

To compile your project you have to get into the container, inside the */opt/code/projectname* folder, which is shared with the host machine, and run the compilation.

## How to set your own include paths

The **docker4amigavbcc:latest-m68k** image has the following ENV variables set:

* **VBCC**: /opt/vbcc
* **PATH**: /opt/vbcc/bin
* **NDK_INC**: /opt/sdk/NDK_3.9/Include/include_h
* **NDK_LIB**: /opt/sdk/NDK_3.9/Include/linker_libs
* **MUI38_INC**: /opt/sdk/MUI_3.8/C/Include
* **MUI50_INC**: /opt/sdk/MUI_5.0/C/include
* **TCP_INC**: /opt/sdk/Roadshow-SDK/include
* **NET_INC**: /opt/sdk/Roadshow-SDK/netinclude
* **AMISSL_INC**: /opt/sdk/AmiSSL/include
* **POSIXLIB_INC**: /opt/sdk/PosixLib/include

The **docker4amigavbcc:latest-ppc** image has the following ENV variables set:

* **VBCC**: /opt/vbcc
* **PATH**: /opt/vbcc/bin
* **AOS4_SDK_INC**: /opt/sdk/ppc-amigaos/Include/include_h
* **AOS4_NET_INC**: /opt/sdk/ppc-amigaos/Include/netinclude
* **AOS4_NLIB_INC**: /opt/sdk/ppc-amigaos/newlib/include
* **AOS4_CLIB_INC**: /opt/sdk/ppc-amigaos/clib2/include
* **MUI50_INC**: /opt/sdk/MUI_5.0/C/include
* **AMISSL_INC**: /opt/sdk/AmiSSL/include

You can set your own paths, if you want, by using environment variables on docker execution or inside the docker-compose.yml file, like:
```bash
docker run -it --rm --name amigavbcc-m68k -v "$PWD"/code:/opt/code -w /opt/code -e NDK_INC="/your/folder/path" walkero/docker4amigavbcc:latest-m68k /bin/bash
docker run -it --rm --name amigavbcc-ppc -v "$PWD"/code:/opt/code -w /opt/code -e AOS4_SDK_INC="/your/folder/path" walkero/docker4amigavbcc:latest-ppc /bin/bash
```
docker-compose.yml
```yaml
version: '3'

services:
  vbcc-m68k:
    image: 'walkero/docker4amigavbcc:latest-m68k'
    environment:
      NDK_INC: "/opt/ext_sdk/NDK_3.9/Include/include_h"
    volumes:
      - './code:/opt/code'
      - './ext_sdk:/opt/ext_sdk'

  vbcc-ppc:
    image: 'walkero/docker4amigavbcc:latest-ppc'
    environment:
      AOS4_SDK_INC: "/opt/ext_sdk/SDK_install/Include/include_h"
    volumes:
      - './code:/opt/code'
      - './ext_sdk:/opt/ext_sdk'
```

## Code included notes
### Roadshow SDK

Roadshow SDK is included in **docker4amigavbcc:latest-m68k** image since version tag 1.1-m68k, with the kind permission from Andreas Magerl. Thank you Andreas for your help.

### Demo code
Under the folder `code` you will find some demo scripts that can be compiled with this vbcc docker installation

* m68k/hello.c - Just a simple Hello World script
* m68k/openwin.c - This is a simple code that opens a window on Workbench, as found at https://github.com/Ozzyboshi/DockerAmigaVbcc
* m68k/Amiga_C_MUI_Examples - A couple of MUI examples as found at http://aminet.net/package/dev/mui/Amiga_C_MUI_Examples with update Makefiles to use VBCC
* aosppc/Window - A few example codes from AmigaOS 4 SDK

## Bug reports or feature request
If you have any issues with the images or you need help on using them or you would like to request any new feature, please contact me by opening an issue at https://github.com/walkero-gr/docker4AmigaVBCC/issues

## Credits
The **docker4amigavbcc:latest-m68k** docker image is based on the following sources:
* https://blitterstudio.com/setting-up-an-amiga-cross-compiler/
* https://github.com/Ozzyboshi/DockerAmigaVbcc
