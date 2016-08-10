# LG_Volt-CM_13_Manifest
Manifest needed to help build Cyanogenmod 13.0 for LG Volt

# Files
* README.md -- The self explanatory file you read now.
* lg_volt-Stable.xml -- Manifest for building from the last known working build of CyanogenMod 13.0. Code is provided from their respective GitHub sources listed in the file.
* lg_volt.xml -- Manifest for building from my source edits.


# Notes for me to remember, should provide clues to how to build CM.

### Dependencies
Ubuntu 16.04

sudo apt-get update && sudo apt-get upgrade && sudo apt-get dist-upgrade && sudo apt-get install openjdk-8-jdk

sudo update-alternatives --config java

sudo apt-get install git-core gnupg flex bison gperf build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z-dev lib32z1-dev ccache libgl1-mesa-dev libxml2-utils xsltproc unzip python-networkx android-tools-adb android-tools-fastboot maven libcurl4-openssl-dev nss-updatedb


#### This sets up correct udev rules in Ubuntu so the system can connect to Android devices via USB, ADB, Fastboot.  Replace "username" with system username
```
$ wget -S -O - http://source.android.com/source/51-android.rules | sed "s/<username>/$USER/" | sudo tee >/dev/null /etc/udev/rules.d/51-android.rules; sudo udevadm control --reload-rules
```
#### Setup the "repo" binary. This helps download all the different repositories.
$ mkdir -p ~/bin  
$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo  
$ chmod a+x ~/bin/repo

#### Add the following to ~/.profile to ensure the above "repo" binary will always be accessible.
```
# set PATH so it includes user's private bin if it exists
if [ -d "$HOME/bin" ] ; then
    PATH="$HOME/bin:$PATH"
fi
```

#### Fetch the required CyanogenMod 13.0 source files. Make sure you replace the directory "cd" to the one you want  to use for the source directory
$ cd ~/android/system/  
$ repo init -u https://github.com/CyanogenMod/android.git -b cm-13.0

#### Now add the LG Volt manifest. Pick whether or not to use the stable manifest or not. Change directory to point to .repo   folder of the source directory.  
$ mkdir -p ~/android/.repo/local_manifest  
$ wget -S https://raw.githubusercontent.com/ShapeShifter499/LG_Volt-CM_13_Manifest/master/lg_volt.xml -O ~/android/.repo/local_manifest/lg_volt.xml

#### Setup CCACHE to speed up things. Run from root of source.
$ export USE_CCACHE=1  
$ export CCACHE_DIR=/<path_of_your_choice>/.ccache  
$ prebuilts/misc/linux-x86/ccache/ccache -M 50G

#### Make sure CCACHE will always be used. Put the following in your .bashrc (or equivalent)
export USE_CCACHE=1

#### Monitor CCACHE being used, run from root of source.
$ watch -n1 -d prebuilts/misc/linux-x86/ccache/ccache -s

#### Now grab all the source files! Run at the root of the source directory.
$ repo sync

#### Since I'm running a version 8 of java. Enable experimental OpenJDK 1.8 support in CyanogenMod 13.0 (not available in earlier version). To enable OpenJDK 1.8 support, add this line to your $HOME/.bashrc file
export EXPERIMENTAL_USE_JAVA8=true.

#### Change some git variables so the build correctly identifies who you are, run the following at the root of source. (Optional) 
$ git config --global user.name "Your Name"  
$ git config --global user.email "Your Email"

#### Now build! Run commands at root of source.
$ source build/envsetup.sh  
$ breakfast  
$ lunch x5-userdebug
$ mka bacon
