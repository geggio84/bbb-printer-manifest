BBB-Printer Repo Manifests for the Yocto Project Build System
=============================================
This repository provides Repo manifests to setup the Yocto build system for
BBB-Printer

Getting Started
---------------
**1.  Install Repo.**

Download the Repo script:

    $ curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > repo

Make it executable:

    $ chmod a+x repo

Move it on to your system path:

    $ sudo mv repo /usr/local/bin/

If it is correctly installed, you should see a Usage message when invoked
with the help flag.

    $ repo --help

**2.  Initialize a Repo client.**

Create an empty directory to hold your working files:

    $ mkdir yocto
    $ cd yocto

Tell Repo where to find the manifest:

    $ repo init -u https://github.com/geggio84/bbb-printer-manifest.git -b bbb-printer

A successful initialization will end with a message stating that Repo is
initialized in your working directory. Your directory should now
contain a .repo directory where repo control files such as the manifest are
stored but you should not need to touch this directory.

**3.  Fetch all the repositories:**

    $ repo sync

Now go turn on the coffee machine as this may take 20 minutes depending on
your connection.

**4.  Initialize the Yocto Project Build Environment.**

    $ export OECORELAYERCONFPATH=$PWD/src/meta-bbb-printer/conf/conf_bbb_printer/bblayers.conf.sample
    $ export OECORELOCALCONFPATH=$PWD/src/meta-bbb-printer/conf/conf_bbb_printer/local.conf.sample
    $ export PATH=$HOME/devel/toolchains/gcc-linaro-4.9-2015.05-x86_64_arm-linux-gnueabihf/bin:$PATH
    $ . environment-setup.sh

This copies default configuration information into the **poky/build/conf**
directory and sets up some environment variables for the build system.  This configuration
directory is not under revision control; you may wish to edit these configuration
files for your specific setup. In particular, change the `MACHINE` variable in **conf/local.conf** if you are
not building for the Overo (default).

**5.  Build an image:**

This process downloads several gigabytes of source code and then proceeds to
do an awful lot of compilation so make sure you have plenty of space (25GB
minimum), and expect a day or so of build time depending on your network
connection.  Don't worry---it is just the first build that takes a while.

    $ bitbake bbb-printer-image
    $ bitbake ramdisk-bbb

If everything goes well, you should have a compressed root filesystem
tarball as well as kernel and bootloader binaries available in your
**tmp/deploy/images/beaglebone** directory.  If you run into problems, the most likely
candidate is missing software packages.  Check out
http://www.yoctoproject.org/docs/current/yocto-project-qs/yocto-project-qs.html#resources
for the list of required packages for operating system. Also, take
a look to be sure your operating system is supported:
https://wiki.yoctoproject.org/wiki/Distribution_Support

Starting Fresh
-------------------
So something broke... what do you do now?

There are several degrees of *starting fresh*: individual packages can be 
rebuilt or the whole system can be reconstructed.

 1. clean a package: bitbake <package-name> -c cleansstate
 2. re-download package: bitbake <package-name> -c cleanall
 3. destroy everything but downloads: rm -rf build/sstate-cache build/tmp (or wherever your sstate and work directories are)
 4. destroy it all (not recommended): rm -rf build
