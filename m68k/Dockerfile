FROM phusion/baseimage:master

LABEL maintainer="Georgios Sokianos <walkero@gmail.com>"

# Use baseimage-docker's init system.
CMD ["/sbin/my_init"]

ARG VBCC_USER_ID=1000
ARG VBCC_GROUP_ID=1000

RUN apt-get update && apt-get -y install gcc \
    wget \
    curl \
    make \
    mc \
    git \
    lhasa;

RUN mkdir -p /opt/vbcc/bin; \
    mkdir -p /opt/code; \
    mkdir -p /opt/sdk;

# Add vbcc user and group
RUN existing_group=$(getent group "${VBCC_GROUP_ID}" | cut -d: -f1); \
    if [[ -n "${existing_group}" ]]; then delgroup "${existing_group}"; fi; \
    existing_user=$(getent passwd "${VBCC_USER_ID}" | cut -d: -f1); \
    if [[ -n "${existing_user}" ]]; then deluser "${existing_user}"; fi; \
    addgroup --gid ${VBCC_GROUP_ID} vbcc; \
    adduser --system --uid ${VBCC_USER_ID} --disabled-password --shell /bin/bash --gid ${VBCC_GROUP_ID} vbcc; \
    sed -i '/^vbcc/s/!/*/' /etc/shadow;

# Install vasm
RUN curl -fSL "http://sun.hasenbraten.de/vasm/release/vasm.tar.gz" -o /tmp/vasm.tar.gz; \
    cd /tmp; \
    tar xvfz vasm.tar.gz; \
    cd /tmp/vasm; \
    make CPU=m68k SYNTAX=mot; \
    cp ./vasmm68k_mot ./vobjdump /opt/vbcc/bin; 

ENV VBCC="/opt/vbcc"

# Install vlink
RUN curl -fSL "http://sun.hasenbraten.de/vlink/release/vlink.tar.gz" -o /tmp/vlink.tar.gz; \
    cd /tmp; \
    tar xvfz vlink.tar.gz; \
    cd /tmp/vlink; \
    make; \
    cp ./vlink /opt/vbcc/bin;

# Install vbcc
RUN curl -fSL "http://www.ibaug.de/vbcc/vbcc.tar.gz" -o /tmp/vbcc.tar.gz; \
    cd /tmp; \
    tar xvfz vbcc.tar.gz; \
    cd /tmp/vbcc; \
    mkdir bin; \
    yes '\
' | make TARGET=m68k; \
    yes '\
' | make TARGET=m68ks; \
    cp ./bin/vbcc* ./bin/vc ./bin/vprof /opt/vbcc/bin;

RUN curl -fSL "http://server.owl.de/~frank/vbcc/2019-10-04/vbcc_target_m68k-amigaos.lha" -o /tmp/vbcc_target_m68k-amigaos.lha; \
    curl -fSL "http://server.owl.de/~frank/vbcc/2019-10-04/vbcc_unix_config.tar.gz" -o /tmp/vbcc_unix_config.tar.gz; \
    cd /tmp; \
    lhasa -xfq2 vbcc_target_m68k-amigaos.lha; \
    tar xvfz vbcc_unix_config.tar.gz; \
    mv ./config $VBCC/; \
    mv ./vbcc_target_m68k-amigaos/targets $VBCC/;

ENV PATH="$VBCC/bin:$PATH"

# Install NDK39
RUN curl -fSL "http://www.haage-partner.de/download/AmigaOS/NDK39.lha" -o /tmp/NDK39.lha; \
    cd /tmp; \
    lhasa -xfq2 NDK39.lha; \
    mv ./NDK_3.9 /opt/sdk/;

ENV NDK_INC="/opt/sdk/NDK_3.9/Include/include_h"
ENV NDK_LIB="/opt/sdk/NDK_3.9/Include/linker_libs"

# Install MUI 3.8 dev
RUN curl -fSL "http://muidev.de/download/MUI%203.8%20-%20AmigaOS3-m68k/mui38dev.lha" -o /tmp/mui38dev.lha; \
    cd /tmp; \
    lhasa -xfq2 mui38dev.lha; \
    mv ./MUI/Developer /opt/sdk/MUI_3.8; \
    mkdir -p /opt/sdk/MUI_3.8/C/Include/mui;

ENV MUI38_INC="/opt/sdk/MUI_3.8/C/Include"

# Install MUI 5.0 dev
RUN curl -fSL "http://muidev.de/download/MUI%205.0%20-%20Release/MUI-5.0-2019R4-os3.lha" -o /tmp/MUI-5.0-os3.lha; \
    curl -fSL "http://muidev.de/download/MUI%205.0%20-%20Release/MUI-5.0-2019R4-os3-contrib.lha" -o /tmp/MUI-5.0-os3-contrib.lha; \
    cd /tmp; \
    lhasa -xfq2 MUI-5.0-os3.lha; \
    lhasa -xfq2 MUI-5.0-os3-contrib.lha; \
    mv ./SDK/MUI /opt/sdk/MUI_5.0;

ENV MUI50_INC="/opt/sdk/MUI_5.0/C/include"

# Install MCC_GuiGfx
RUN curl -fSL "http://aminet.net/dev/mui/MCC_Guigfx.lha" -o /tmp/MCC_Guigfx.lha; \
    cd /tmp; \
    lhasa -xfq2 MCC_Guigfx.lha; \
    cp ./MCC_Guigfx/Developer/C/Include/MUI/* /opt/sdk/MUI_3.8/C/Include/mui; \
    cp ./MCC_Guigfx/Developer/C/Include/MUI/* /opt/sdk/MUI_5.0/C/include/mui;

# Install MCC_TextEditor
RUN curl -fSL "http://aminet.net/dev/mui/MCC_TextEditor-15.53.lha" -o /tmp/MCC_TextEditor.lha; \
    cd /tmp; \
    lhasa -xfq2 MCC_TextEditor.lha; \
    cp ./MCC_TextEditor/Developer/C/include/mui/* /opt/sdk/MUI_3.8/C/Include/mui;

# Install Roadshow SDK
RUN curl -fSL "http://amiga-projects.net/Roadshow-SDK.lha" -o /tmp/Roadshow-SDK.lha; \
    cd /tmp; \
    lhasa -xfq2w=Roadshow-SDK Roadshow-SDK.lha; \
    mv ./Roadshow-SDK /opt/sdk/Roadshow-SDK;

ENV TCP_INC="/opt/sdk/Roadshow-SDK/include"
ENV NET_INC="/opt/sdk/Roadshow-SDK/netinclude"

# Install AMISSL SDK
RUN curl -fSL "https://github.com/jens-maus/amissl/releases/download/4.5/AmiSSL-4.5.lha" -o /tmp/AmiSSL.lha; \
    cd /tmp; \
    lhasa -xfq2 AmiSSL.lha; \
    mv ./AmiSSL/Developer /opt/sdk/AmiSSL;

ENV AMISSL_INC="/opt/sdk/AmiSSL/include"

# Install Posix Lib
RUN curl -fSL "http://aminet.net/dev/c/vbcc_PosixLib.lha" -o /tmp/vbcc_PosixLib.lha; \
    cd /tmp; \
    lhasa -xfq2 vbcc_PosixLib.lha; \
    mkdir -p /opt/sdk/PosixLib/; \
    mv ./PosixLib/include/ /opt/sdk/PosixLib/;

ENV POSIXLIB_INC="/opt/sdk/PosixLib/include"

# Install FlexCat
RUN curl -fSL "https://github.com/adtools/flexcat/releases/download/2.18/FlexCat-2.18.lha" -o /tmp/FlexCat.lha; \
    cd /tmp; \
    lhasa -xfq2 FlexCat.lha; \
    cp ./FlexCat/Linux-i386/flexcat /usr/bin/;

# Clean up APT when done.
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# USER vbcc
WORKDIR /opt/code
