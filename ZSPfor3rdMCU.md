#  Zigbee Stack porting for 3rd party MCU Quick-Start Guide
##  Description

This quick-start guide provides basic information on configuring, building, debugging and installing Zigbee applications for STM32F4 family (STM32F411 available now) in both ways using Simplicity Studio 5 and non-Simplicity Studio 5.
This guide is designed for developers who want to use 3rd platform (MCU, IDE, compiler) with Zigbee stack from Silicon Labs’ chip (at the time this guide is written, we only support STM32F411 with GCC compiler on STM32CubeIDE)

##  Gecko SDK version

GSDK ver 4.1.1 and higher

##  Hardware Required

• [NUCLEO-F411RE board](https://www.st.com/en/evaluation-tools/nucleo-f411re.html )
• [EFR32MG12 Starter Kit](https://www.st.com/en/evaluation-tools/nucleo-f411re.html )

##  Connections Required
![](c.png )

Connect these pins:

| Connection | EXP Header Silabs Board | NUCLEO-F411RE                   |
| ---------- | ----------------------- | ------------------------------- |
| UART_TX    | PIN12 - UART_TX/PA05    | CN10 - UART1_RX / CN9 - PA10/D2 |
| UART_RX    | PIN14 - UART_RX/PA06    | CN10 - UART1_TX / CN9 - PA9/D8  |
| Ground     | PIN1 - GND              | CN6 - GND                       |

![](exp.PNG )
![](cn10.PNG )
![](cn6.PNG )

##  Non-Simplicity Studio 5

**Setup development environment**
1. Setup Ubuntu 20.04
2. Update
```
    sudo apt update && sudo apt upgrade -y
```
3. Install dependencies and some build systems
```
    sudo apt install git build-essential gcc-multilib g++-multilib ant make cmake gcovr npm python python3 -y
```
4. Install niceties
```
    sudo apt install wget curl openssh-server cifs-utils vim mc meld bash-completion -y
    sudo snap install --classic code
```
5. Update workspace
```
    sudo chmod 777 /opt
```
6. Get GCC Compiler
```
    mkdir -p /opt/temp && cd /opt/temp
    wget https://developer.arm.com/-/media/Files/downloads/gnu-rm/10-2020q4/gcc-arm-none-eabi-10-2020-q4-major-x86_64-linux.tar.bz2
    tar -vxf gcc-arm-none-eabi-10-2020-q4-major-x86_64-linux.tar.bz2 -C /opt/
    /opt/gcc-arm-none-eabi-10-2020-q4-major/bin/arm-none-eabi-gcc --version
```
7. Get Java 11
```
    mkdir -p /opt/temp && cd /opt/temp
    wget https://corretto.aws/downloads/latest/amazon-corretto-11-x64-linux-jdk.tar.gz
    tar -vxf amazon-corretto-11-x64-linux-jdk.tar.gz -C /opt
    export JAVA_HOME=/opt/amazon-corretto-11.0.17.8.1-linux-x64/
    export JAVA11_HOME=$JAVA_HOME
    java -version
```
8. Install NodeJS
```
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
```
Close and reopen your terminal to start using nvm or run the following to use it now:
```
    export NVM_DIR="$HOME/.nvm"
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
    [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
    nvm --version
    nvm list-remote
    nvm install node
```
To be stable, you can install NodeJS verion 14.15.1
```
    nvm list-remote
    nvm install v14.15.1
    nvm use 14.15.1
    nvm list
```
9.  Build environment
```
    cd /opt
    git clone ssh://git@stash.silabs.com/wmn_tools/silabs-scripts.git
    vim silabs-scripts/silabs.bashr
```
Then add the following line to the top of the file
```
    export GITHUBS_DIR=/opt
    export JAVA_HOME="/opt/amazon-corretto-11.0.17.8.1-linux-x64/"
    export JAVA11_HOME="/opt/amazon-corretto-11.0.17.8.1-linux-x64/"
    export STUDIO_ADAPTER_PACK_PATH=/opt/zap
    export PATH=/opt/uc_cli:/opt/zap:$PATH
    export ARMGCC_DIR="/opt/gcc-arm-none-eabi-10-2020-q4-major"
    export ARM_GCC_DIR="/opt/gcc-arm-none-eabi-10-2020-q4-major"
    export ARM_GNU_DIR="/opt/gcc-arm-none-eabi-10-2020-q4-major"
    export PATH="${JAVA11_HOME}/bin:${PATH}
```
Then escape the file and activate environment by following below line
```
    echo "source /opt/silabs-scripts/silabs.bashrc" >> ~/.bashrc
```
10.  Install Silabs SLC tool
```
    cd /opt
    git clone ssh://git@stash-mirror.silabs.com/stash/simplicity_studio/uc_cli.git
    cd uc_cli
    ./slc
```
11.  Install Silabs ZAP tool
```
    cd /opt
    git clone ssh://git@stash.silabs.com/wmn_tools/zap.git
    cd /opt/zap/
    npm install
    npm run zap --version
```
**Configure, create and build project**
1. Clone Zigbee Stack porting for 3rd party MCU repo
```
    cd /opt
    git clone ssh://git@stash.silabs.com/telegesis/devs_zigbee_porting_for_3rd_party_mcu.git
    mv devs_zigbee_porting_for_3rd_party_mcu gsdk
```
2. Add extension to GSDK
```
    cd /opt/gsdk
    git checkout stm/stm32f4xx
    mkdir extension && cd extension
    git clone -b stm/stm32f4xx --single-branch ssh://git@stash.silabs.com/telegesis/devs_zigbee_porting_for_3rd_party_mcu.git
    mv devs_zigbee_porting_for_3rd_party_mcu/zigbee_host_mcu/ ./ && rm -rf devs_zigbee_porting_for_3rd_party_mcu/
    cd .. && git checkout feature/IOT_DS-2007-refactor-project-structure-gsdk-part
```
3. Generate & build project
```
    slc generate -np extension/zigbee_host_mcu/app/framework/scenarios/z3/Z3LightMcu/Z3LightMcu.slcp  -d app/project  -tlcn gcc  --generator-timeout 200 --with="nucleo-f411re;zigbeehostmcu"
    cd app/project
    make -f Z3LightMcu.Makefile -j6
```
