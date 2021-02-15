This guide is written for professional liquidity providers to turn a powerful Mini PC into an always-on OpenDEX node.

![](./images/pro.jpg)

Two options are available:

1. **Light setup** using [Neutrino](https://github.com/lightninglabs/neutrino) and a random open eth provider or optionally [Infura](https://infura.io/). This keeps the setup light-weight & cheap, but creates a certain dependency on other people's full nodes.
2. **Full setup** using [bitcoind](https://github.com/bitcoin/bitcoin/), [litecoind](https://github.com/litecoin-project/litecoin) and [geth](https://github.com/ethereum/go-ethereum). Requires more resources and an SSD, but keeps the setup trustless.

## Light Reference Shopping List (Europe): ~180 €
* [GIGABYTE GB-BLCE-4105 BRIX](https://www.computeruniverse.net/en/gigabyte-gb-blce-4105-brix): 138 €
* [4 GB RAM](https://www.computeruniverse.net/en/crucial-4gb-ddr4-so-dimm-ct4g4sfs824a-2400mhz-ram): 15 €
* [120GB M.2 SSD](https://www.computeruniverse.net/en/wd-green-ssd-m2-2280-120gb): 20 €
* [USB stick for backups](https://www.amazon.es/dp/B00TPG6P22/): 3,99 €
   * Any >1GB USB stick will do.
   * A NAS/Samba share works too.

## Full Reference Shopping List (Europe): ~465 €
* [GIGABYTE GB-BLCE-4105 BRIX](https://www.computeruniverse.net/en/gigabyte-gb-blce-4105-brix): 138 €
  * Alternative: [ODROID H2+](https://www.hardkernel.com/shop/odroid-h2plus/) - same platform, features NVME, needs separate case, power supply and wifi dongle, out of stock at times
* [32 GB RAM](https://www.computeruniverse.net/en/kingston-hyperx-impact-32gb-ddr4-so-dimm-ram-2): 127 €
  * Alternative: [List of compatible RAM](https://wiki.odroid.com/odroid-h2/hardware/ram)
* [2TB SSD](https://www.computeruniverse.net/en/sandisk-ssd-plus-25-2tb): 193 €
  * Alternative: [1 TB M.2 SSD NVME](https://www.computeruniverse.net/en/gigabyte-ssd-nvme-m2-2280-1tb) - for Odroid H2+
  * Alternative: [240GB M.2 SSD](https://www.computeruniverse.net/en/wd-green-ssd-m2-2280-240gb) + [2TB HDD](https://www.computeruniverse.net/en/seagate-firecuda-compute-st2000lx001-sshd-2tb)
* [USB stick for backups](https://www.amazon.es/dp/B00TPG6P22/): 3,99 €
   * Any >1GB USB stick will do.
   * A NAS/Samba share works too.

## Setup

1. [Download Ubuntu Server 20.04 LTS](https://ubuntu.com/download/server) onto your computer. Any other linux distribution supporting docker is also fine. This guide was written using `Ubuntu Server 20.04`.
2. Insert a USB Stick into your computer and [create the a bootable USB Stick](https://ubuntu.com/tutorials/tutorial-create-a-usb-stick-on-ubuntu) with the ubuntu image you just downloaded.
3. Open your Mini PC, plug in RAM & drives, close it, connect it to your router via ethernet cable and to a power supply. Connect a screen via HDMI, a USB keyboard, the created bootable USB Stick, fire the Mini PC up and follow the the inital setup instructions.
4. Update ubuntu via `sudo apt update && sudo apt upgrade`
5. If you are using Ubuntu 20.04, install docker & docker-compose by running `sudo apt install docker.io`. Otherwise if you are using any version besides Ubuntu 20.04, follow the [official instructions](https://docs.docker.com/install/linux/docker-ce/ubuntu/) to install docker.
6. Add new user `opendex`:
```bash
ubuntu@ubuntu:~$ sudo adduser opendex
Adding user `opendex' ...
Adding new group `opendex' (1001) ...
Adding new user `opendex' (1001) with group `opendex' ...
Creating home directory `/home/opendex' ...
Copying files from `/etc/skel' ...
New password: 
Retype new password: 
passwd: password updated successfully
Changing the user information for opendexd
Enter the new value, or press ENTER for the default
	Full Name []: 
	Room Number []: 
	Work Phone []: 
	Home Phone []: 
	Other []: 
Is the information correct? [Y/n] ubuntu@ubuntu:~$ Y
```
7. Add the `opendex` user to the sudo group (advanced users can skip this and use another user to run sudo commands), the docker group and test if docker is working:
```bash
ubuntu@ubuntu:~$ sudo usermod -aG sudo opendex
ubuntu@ubuntu:~$ sudo usermod -aG docker opendex
# switch to user opendex
ubuntu@ubuntu:~$ sudo su - opendex
opendex@ubuntu:~$ docker run hello-world
Hello from Docker!
This message shows that your installation appears to be working correctly.
```
8. Looking good! Optionally, add an alias to enter your opendexd environment by simply typing "opendex":
```bash
opendex@ubuntu:~$ sudo nano ~/.bash_aliases
# add the line
alias opendex='bash ~/opendex.sh'
# CTRL+S, CTRL+X. Then run
opendex@ubuntu:~$ source ~/.bashrc
```
9. Connect the USB stick to your machine and set it up. It is very important to do this for a mainnet setup (given you do not want to lose money)!
```bash
# check the USB stick's path with
opendex@ubuntu:~$ ls -la /dev/ | grep sd
crw-------  1 root root      2,  61 Dec  3 16:27 ptysd
brw-rw----  1 root disk      8,   0 Dec  3 16:27 sda
brw-rw----  1 root disk      8,   1 Dec  3 16:27 sda1 #this is your USB Stick
crw-------  1 root root      3,  61 Dec  3 16:27 ttysd
# set it to automount via fstab
opendex@ubuntu:~$ sudo nano /etc/fstab
# add the line
/dev/sda1 /media/USB ext4 defaults 0 2
# CTRL+S, CTRL+X. Then mount it
opendex@ubuntu:~$ sudo mkdir /media/USB
opendex@ubuntu:~$ sudo mount -a
# check if mounting worked
opendex@ubuntu:~$ df -h
# make sure opendexd can use it
opendex@ubuntu:~$ sudo chown opendex:opendex /media/USB
```
**DONE!** Continue [here](/docs/Liquidity%20Provider%20Guide.md#the-setup).
