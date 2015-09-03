# sama5d3-xplained-fido
How to build yocto projects on platform sama5d3-xplained

This guide is a little different with Atmel document  https://github.com/linux4sam/meta-atmel
,especially, step1 to step5.

Build procedure
======================

(1) Clone yocto/poky-fido git repository
    git clone -b fido git://git.yoctoproject.org/poky.git 

(2) cd poky

(3) Clone meta-openembedded git repository
    git clone -b fido git://git.openembedded.org/meta-openembedded

(4) Clone meta-qt5 git repository
    git clone -b fido git://github.com/meta-qt5/meta-qt5.git

(5) Clone meta-atmel layer
    git clone -b fido git://github.com/linux4sam/meta-atmel.git meta-atmel

(6) Initialize build directory
source oe-init-build-env build-atmel

(7) Add meta-atmel layer patch to bblayer configuration file
    vim conf/bblayers.conf

    # LAYER_CONF_VERSION is increased each time build/conf/bblayers.conf
    # changes incompatibly
    LCONF_VERSION = "6"

    BBPATH = "${TOPDIR}"
    BBFILES ?= ""

    BSPDIR := "${@os.path.abspath(os.path.dirname(d.getVar('FILE', True)) + '/../..')}"

    BBLAYERS ?= " \
            ${BSPDIR}/meta-atmel \
            ${BSPDIR}/meta-qt5 \
            ${BSPDIR}/meta \
            ${BSPDIR}/meta-yocto \
            ${BSPDIR}/meta-yocto-bsp \
            ${BSPDIR}/meta-openembedded/meta-oe \
            ${BSPDIR}/meta-openembedded/meta-networking \
            ${BSPDIR}/meta-openembedded/meta-python \
            ${BSPDIR}/meta-openembedded/meta-ruby \
            "
(8) Edit local.conf to specify the machine, location of source archived, package type (rpm, deb or ipk)
    vim conf/local.conf
    [...]
    MACHINE ??= "sama5d4ek"
    [...]
    DL_DIR ?= "your_download_directory_path"
    [...]
    PACKAGE_CLASSES ?= "package_ipk"

    Here are the machines that are supported:
    MACHINE ??= "at91sam9rlek"
    MACHINE ??= "at91sam9m10g45ek"
    MACHINE ??= "at91sam9x5ek"
    MACHINE ??= "sama5d3xek"
    MACHINE ??= "sama5d3-xplained"
    MACHINE ??= "sama5d4ek"
    MACHINE ??= "sama5d4-xplained"

    To get better performance, use the poky-atmel distribution by also adding that
    line:
        DISTRO = "poky-atmel"

(9) Build core minimal image
    bitbake core-image-minimal

(10) We found that additional local.conf changes are needed for our QT demo
     image. You can add these two lines at the end of the file:
     vim conf/local.conf
     [...]
     LICENSE_FLAGS_WHITELIST += "commercial"
     SYSVINIT_ENABLED_GETTYS = ""

(11) Build Atmel demo images
     bitbake atmel-qt5-demo-image

WARNING: Host distribution "Ubuntu-15.04" has not been validated with this version of the build system; you may possibly experience unexpected failures. It is recommended that you use a tested distribution.
Parsing recipes: 100% |####################################################################################################################################| Time: 00:01:37
Parsing of 1753 .bb files complete (0 cached, 1753 parsed). 2247 targets, 310 skipped, 0 masked, 0 errors.
NOTE: Resolving any missing task queue dependencies

Build Configuration:
BB_VERSION        = "1.26.0"
BUILD_SYS         = "x86_64-linux"
NATIVELSBSTRING   = "Ubuntu-15.04"
TARGET_SYS        = "arm-poky-linux-gnueabi"
MACHINE           = "sama5d3-xplained"
DISTRO            = "poky-atmel"
DISTRO_VERSION    = "1.8"
TUNE_FEATURES     = "arm armv7a vfp thumb callconvention-hard cortexa5"
TARGET_FPU        = "vfp"
meta-atmel        = "fido:54db5a0805a3b29e2fad6796388eb0c3181fc151"
meta-qt5          = "fido:fc026381545650e10fdd488d5b2a3b2d78f87793"
meta              
meta-yocto        
meta-yocto-bsp    = "fido:b50596d8f6e858e2e733f2d9913a19c6f3cd5863"
meta-oe           
meta-networking   
meta-python       
meta-ruby         = "fido:10d3c8f85280a0bf867a8e4f84bcda81c290d28e"


     
