# SecureSign Setup Guide

This guide is a community collaboration effort to setup on-premise (self-hosted) latest Signal App backend.

Original discussion is in this Forum:
[Guide - Deploy Signal Server v2.xx + CDS + SGX](https://community.signalusers.org/t/guide-deploy-signal-server-v2-xx-cds-sgx/8331)

Signal™ is a trademark of Quiet Riddle Ventures LLC. The SecureSign projects are not affliated with Signal.org, Signal Messenger LLC, nor Signal Foundation.

Environment assumpted:
1. Ubuntu 18.04
2. Intel Processor with SGX enabled
3. You are able to enable SGX in BIOS

Guide parts:
1. Setup SGX Driver
2. Setup SGX PSW
3. Setup Signal CDS (Contact Discovery Service)
4. Setup Signal Server
5. Setup Signal Android
6. Setup Signal iOS


## Setup SGX Driver

First, you need to clone `linux-sgx-driver` repository to your computer:
```bash
git clone https://github.com/intel/linux-sgx-driver.git
```

Change directory to local repository:
```bash
cd linux-sgx-driver
```

Then you need to use `sgx2` branch:
```
git chekcout sgx2
```

You can follow guide provided in the repository: [Build and Install the Intel(R) SGX Driver](https://github.com/intel/linux-sgx-driver/#build-and-install-the-intelr-sgx-driver)

But I will provide example to build and install in Ubuntu 18.04, maybe it will be useful for you to get the general idea.

Check if matching kernel headers are installed:
```bash
$ dpkg-query -s linux-headers-$(uname -r)
```

To install matching headers: 
```bash
$ sudo apt-get install linux-headers-$(uname -r)
```

Build the driver from source code:
```
$ make
```

To install the Intel(R) SGX driver, enter the following command with root privilege:
```
$ sudo mkdir -p "/lib/modules/"`uname -r`"/kernel/drivers/intel/sgx"    
$ sudo cp isgx.ko "/lib/modules/"`uname -r`"/kernel/drivers/intel/sgx"    
$ sudo sh -c "cat /etc/modules | grep -Fxq isgx || echo isgx >> /etc/modules"    
$ sudo /sbin/depmod
$ sudo /sbin/modprobe isgx
```


## Setup SGX PSW

[You want to contribute? Please submit GitHub issue and Pull Request.]



## Setup Signal CDS (Contact Discovery Service)

You can see sample of YML configuration file for Signal CDS: [config-signal-cds.yml](config-signal-cds.yml)

`spid` is "Service Provider ID" assigned by Intel for you. You can get it by sign-up for an Intel account, and start service subscription in [Intel's SGX self-service portal](https://api.portal.trustedservices.intel.com/EPID-attestation)

Then you will need X.590 certificate and RSA private key. You can generate one by using this command:
```bash
openssl req -x509 -nodes -newkey rsa:4096 -keyout server.key -out server.crt -days 365
```

Please check your `server.key` file value, is it started with string below:
```
-----BEGIN PRIVATE KEY-----
```

If so, we need to convert the key in PKCS#8 format to old PKCS#1, format expected by the CDS program using this command:
```bash
openssl rsa -in server.key -out server_new.key
```

Copy-and-paste value of `server.crt` to `certificate` field inside YML configuration file. For `key` field, you need to copy value from `server.key` or `server_new.key` which started with string below:
```
-----BEGIN RSA PRIVATE KEY-----
```

Then please build your `enclave` using this command:
```
make -C <repository_root>/enclave
```

It will place a file (your compiled CDS SGX enclave) inside this directory:
```
services/src/main/resources/enclave/
```

Your SGX enclave binary file will be named 64-chars long, with ".so" suffix like this:
```
services/src/main/resources/enclave/aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa.so
```

Please copy the 64-chars file name (without the ".so") to `mrenclave` field in YML file.


## Setup Signal Server

[You want to contribute? Please submit GitHub issue and Pull Request.]


## Setup Signal Android

[You want to contribute? Please submit GitHub issue and Pull Request.]


## Setup Signal iOS

[You want to contribute? Please submit GitHub issue and Pull Request.]
