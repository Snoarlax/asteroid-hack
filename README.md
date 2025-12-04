# AsteroidOS-HackWatch

A collection of tools able to turn an asteroid-os compatible device into a hacking PDA.

The scripts are loaded from the home directory, the RubberDucky scripts require the following:
- certain kernel configuration options to be enabled (by adding a .patch which enables the relevant option in the defconfig when building asteroidOS) 
- utilities which creates a virtual device on the watch where keyboard inputs can be written to. An example setup can be found in the ./ceres.tar.gz in this example repository containing useful keyboard and shell scripts:
[https://github.com/Snoarlax/asteroid-hackwatch](https://github.com/Snoarlax/asteroid-hackwatch)

## Currently Implemented Modules

- Shell script runner
- RubberDucky

## Future modules

- Radio Hacking
- NFC Hacking

