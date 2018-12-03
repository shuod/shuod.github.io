+++
date = "2016-08-12T10:33:28-06:00"
title = "Setup a Virtual Private Server(VPS) from Ramnode and benchmark it"
tags = ["VPS", "Linux","VPN"]
Categories = ["Server", "Linux"]
+++

Virtual Private Server (VPS) is a new way to host a personal server. It hides the hardware detials from the user (which is safe) and delivers a flexible (sometime even real time adjustable) performance to the server. They usually have two types: KVM and OPENVZ. The difference of the two virtualization method is the the OPENVZ is container-like, which is also limited to linux only, but KVM use the main OS system to simulate the hardware for user-OS, which theoretically allows any type of the operation system. Because of the working principle of KVM, it could provide better isolation (Those guys doing virus analysis would like this). However, if you are looking for better performance of the Linux system, openvz maybe a better choice.

VPS from commercial providers is a perfect source to setup a personal server. This article will include:

* Bought a VPS

* Benchmarking the performance of the VPS

  ​

<!--more-->

## Bought a VPS

There are many commercial providers who sell VPS for a fair price and some time with a good discount too. They are mainly fall into two categories: performance oriented and long-time budget oriented. The leading solution providers of the first kind:

* Amazon EC2 : various plan with computation or GPU but more expensive compared to others. But new user could have m1.micro instance for free for a year!


* Linode : The basic plan costs $10 monthly with 1CPU core, 2GB RAM,24GB SSD and 40GB/123MB traffic in/out

* Dreamhost : The basic plan costs $15 monthly for 1 CPU core, 1 GB RAM, 30GB SSD and unlimited traffic

* Digital Ocean (DO) : The basic plan costs $5 monthly for 1 CPU core, 512 Mem and 20GB SSD with a bandwidth of 1TB (https://www.digitalocean.com/pricing/) 

* VULTR: The basic plan costs $5 monthly for 1 CPU core, 768 Mem and 15GB SSD with a bandwidth of 1000GB

  (https://www.vultr.com/pricing/) 

There are currently some promotions for these two providers. For the VULTR, new users could have a $20 coupon valid for one year, while for DO, there is a coupon in a student discount pack from "github.com" too.(https://education.github.com/pack/)

The commercial providers of the second kinds:

- RAMNODE: The basic plan is $15 per year with 1CPU core, 128MB RAM, 12GB storage and 500GB traffic. This is good if you just want to host a personal blog.

- Host1plus: The basic plan is $2 per month with 0.5 CPU core, 256MB RAM, 20GB storage and 500GB traffic.

- Bandwagonhost: The basic plan is $2.99 per month with 256MB RAM and 10GB SSD.

They usually have a discount for a very cheap price in black friday or other holiday sale.

## Benchmarking the performance of  the VPS

###  Ramnode (www.ramnode.com)

I first tried the basic plan in ramnode and used several linux command to check the ystem:

``` html
# CPU information : Intel E3-1240 V2 @3.40GHz
cat /proc/cpuinfo
# Memory inforation : 
free
# Disk I/O using "dd" : 235MB/s and 547MB/s
dd if=/dev/zero of=test bs=64k count=4k oflag=dsync && rm -f test
dd if=/dev/zero of=test bs=8k count=256k conv=fdatasync && rm -f test
```

![Results from above bash](/images/setup-a-VPS-server-1.png)]


Another important factor is the network I/O and we will utilize a script to test it, which is available at: https://github.com/teddysun/across/blob/master/bench.sh
(ref: https://github.com/teddysun/across )

After downloading, run 
``` 
curl -Lso- bench.sh | bash
```

The script will automated the test process and generate :
![Results from above script](/images/setup-a-VPS-server-5.png)]


To use a test suit called "Unixbench", we first need to install some missing package. Mainly, some VPS just has the basic linux package installed, so we need to  (ref: www.copahost.com/blogbenchmark-linux-unixbench)

```
# for Centos/fedora
yum -y install gcc gcc-c++ make libXext-devel perl-Time-HiRes
yum -y groupinstall "Development Tools"
yum -y install libX11-devel mesa-libGL-devel perl-Time-HiRes

# for Debian
apt-get install libx11-dev libgl1-mesa-dev libxext-dev perl perl-modules make
```

Then we go ahead to download it from github and run it:

``` 
git clone https://github.com/kdlucas/byte-unixbench.git
cd byte-unixbench/unixbench
./Run
```

After waiting for ~30 min, depending on the machine, you will get the score. Basically, the baseline of this test suit is accquired from a work station called SPACR station 20-61 in 1995 with 128 RAM and Solaris 2.3 system. The score of this computer was set to 10, which means a system having a score of 1400 will be 140 times as fast as that system. The detail meanings of each test are explained in the software "Readme.md" or this page: github.com/kdlucas/byte-unixbench

![unixbench results for ramnode](/images/setup-a-VPS-server-2.png)

### VULTR ([www.vultr.com](http://www.vultr.com/?ref=6954160-3B) )

3After registering an account in VULTR, we need to link a payment method to the account. You don't need to pay but use the promotion code "NGINX20" or my referral link http://www.vultr.com/?ref=6954160-3B  to get a $20 coupon which is enough for the basic plan to survive for a while.

After linking your payment method with your account you then could deploy a new VPS instance by choosing the location, plan etc...

Then we are going to benchmark the similiar stuff in VULTR machine.

``` html
# Disk I/O using "dd" : 29.5MB/s and 478MB/s
dd if=/dev/zero of=test bs=64k count=4k oflag=dsync && rm -f test
dd if=/dev/zero of=test bs=8k count=256k conv=fdatasync && rm -f test

# Network I/O using the script
curl -Lso- bench.sh | bash

```

The script will automated the test process and generate :
![Results from above script](/images/setup-a-VPS-server-4.png)

and the results from unixbench:
![unixbench results for vultr](/images/setup-a-VPS-server-3.png)


There are websites that list all of the scores form unixbench, so you could compare with them. Just pay attention to the version of unibench, which in our case is 5.1.x. One of the such websites:

https://www.demoniak.ch/vps-comparatif-des-performances/ 


There are also other tests that specify in CPU or disk I/O or database performance. Just mention a few:

1. https://wiki.mikejung.biz/Benchmarking
2. https://wiki.archlinux.org/index.php/benchmarking

### Reference:

https://www.copahost.com/blog/benchmark-linux-unixbench/

https://teddysun.com/444.html

http://www.copahost.com/blogbenchmark-linux-unixbench





