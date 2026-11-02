# Noctalia Installation Guide Using **xbps-src** for **VOID LINUX**

This template has been updated on November 1st 2026, we are currently on v5.4.1, see [releases](https://github.com/noctalia-dev/noctalia/releases)

Just to clarify: 
1. This is only for VOID LINUX (Idk if this works on Neko Void Linux as well)
2. I wrote this myself. NO AI WAS USED TO WRITE THIS UP on a Wednesday afternoon.
3. I AM NOT A MAINTAINER NOR THE CREATOR OF NOCTALIA (sounds stupid but just for any misunderstandings)

## 1. Overview

This document will act as a guide on the step-by-step on how to build and install noctalia v5 with its latest version. Both templates are included, since one of them is necessary because it is not included in the void packages inside **/srcpkgs** _(as far as i am aware of)_

At the end of this document, you should be able to:

* Build noctalia from source for Void Linux using xbps-src
* Install the noctalia package that was built before
* Lock the package using [repolock](https://docs.voidlinux.org/xbps/advanced-usage.html) from xbps so it doesn't get replaced with the one from the official repos`

---
## 2. About the templates
On this repo I provide 2 templates:
1. **noctalia** template with all its necessary dependencies (check the [BUILDING.md](https://github.com/noctalia-dev/noctalia/blob/main/BUILDING.md) page in case there is anything missing, since it is being updated often and sometimes dependencies can be changed, added or removed). Also, i added the checksum for this version, don't worry about that.
2. **nlohmann-json** template which is a necessary dependency and it is not available in he official repos (*as far as i am aware of*):
```Bash
$ xbps-query -Rs nlohmann-json
# Should appear something here
```

Given that said, we can proceed with the step-by-step tutorial

---
## 3. How to build Noctalia v5

The first thing you need to know is that YOU MUST build **nlohmann-json** since you will need it to build noctalia, _but if you have it already or if they finally added it, you should be fine._ I will do this step by step assuming you haven't cloned the [void-packages](https://github.com/void-linux/void-packages.git) repo.

Step 1: clone the void-packages repo and go inside the folder, then preprare your environment for building and packaging with xbps-src:
```Bash
git clone --depth=1 https://github.com/void-linux/void-packages.git
cd void-packages/
./xbps-src binary-bootstrap
```

Step 2: Once you're done, check if, by any chance, they FINALLY added noctalia and nlohmann-json (_sometimes the names may vary, so double check just in case_):
```Bash
ls /srcpkgs | grep noctalia
ls /srcpkgs | grep nlohmann-json
```

Step 3: Assuming both **don't exist**, let's create the nlohmann-json template and paste the template content on it using your preferred editor, or you can also download my template and use it:
```Bash
# The touch command will create the file if it doesn't exist
# You can also download the template here and use it, it should work
mkdir /srcpkgs/nlohmann-json && touch /srcpkgs/nlohmann-json/template
```

Step 4: once you have your template ready, verify that you have all the required dependencies and fetch them to build it:
``` Bash
./xbps-src fetch nlohmann-json
```
IF any dependency is missing, you should try to find how it is named in the repos:
* Try `xbps-query -Rs < pkgname >`
* If it doesn't show anything, try `./xbps-src show-deps < pkgname >` 
* Otherwise, you'll have to make a template of it and build it yourself :(

Step 5: If everything is going well, build the **nlohmann-json** (DO NOT INSTALL IT):
```Bash
./xbps-src pkg nlohmann-json
```

Step 6: Once the dependency has been built, we are ready to build the noctalia package. Make sure you have created its directory and template file (you will have to either paste my template's contents on it or just download my template, whatever works better for you):
```Bash
mkdir /srcpkgs/noctalia && touch /srcpkgs/noctalia/template
```

Step ~~67~~ 7: Fetch the required dependencies on it:
```Bash
./xbps-src fetch noctalia
```

Step 8: Build the package. Please note that:
* The checksum i use is for the version that is in the template. If you are updating the template yourself, you will be responsible to update the checksum. 
	* **Tip**: you can try the first time and it will fail showing you the hash in console, then you copy it and put in the the checksum section in the template, save it and then try to build it again)
* You will need around 2-4 GB of RAM available when compiling this, so make sure you got enough RAM + Swap available in your computer.
```Bash
# Generic build
./xbps-src pkg noctalia
# CPU-Native build (not able to distribute to other distros)
./xbps-src -o native pkg noctalia 
```

Step 9: once the package is built, and if your computer is still alive, you will proceed to install it:
```Bash
# Using xbps as usual (YOU MUST BE INSIDE void-packages folder)
sudo xbps-install --repository=hostdir/binpkgs noctalia
``` 

Step 10: CONGRATS!!! You have installed Noctalia successfully, but we still need to maje sure xbps doesn't screw this up. Run this command with root privileges to lock the package so it doesn't get replaced next time you do `sudo xbps-install -Syu`:
```Bash
# Lock the noctalia package
sudo xbps-pkgdb -m repolock noctalia
# Unlock the noctalia package if you change your mind (or if the maintainers finally update it)
sudo xbps-pkgdb -m repounlock noctalia
```

**OPTIONAL**: Clean the build environment inside void-packages AFTER you installed noctalia (no root privileges required);
```Bash
./xbps-src clean noctalia
```


---
## 4. FAQ
1. **Why don't you just upload this in the official void-packages repos?**
Honestly, i don't want to deal with all the process of approval and thia and that. I am not a maintainer, but i felt this would help the void community in case they still want to use noctalia shell. Also, someone else already made a PR to add this template as far as i've checked a few hours ago.
2. **Will you keep these templates updated?**
 As long as they upload more updates and if i check on them, i would do it since it is just about changing the version and checksum inside the template files, something that you can do yourself too. That's why i am teaching you how to do this.
 3. **What if this is taken down?**
 I will just upload this again in another repo or outside Github if things get messy.
 4. **I get an error that says something about missing a dependency. What do i do?**
 Ok, look:
	 * Maybe it is a misssing dependency, check the required dependencies [here](https://github.com/noctalia-dev/noctalia/blob/main/BUILDING.md).
	 * Maybe the dependency is something that it is not available and you'll have to build it yourself with a template as i explained in the case of *nlohmann-json*
5. **I use the musl version. Will this work?**
Honestly, try it yourself. I do not use musl and i have no idea, but if you make it work, please let me know so I may do a special section for **musl void users** if there are extra steps or it just works fine,
6. **Hey, i think you are wrong in x step beacause of x. Should i let you know?**
YES please. When doing that, please explain why it is wrong, what happens when you try it, the fix for that and why does it fix it. The more accurate the info, the better for everyone, as i can make mistakes too. But, as far as i tested it, it should work just fine.

<br/>

*Remember to reject systemd(isaster), and embrace the void!*




