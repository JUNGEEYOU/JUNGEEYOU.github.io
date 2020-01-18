---
layout: post
title: "서버 사양 확인 (with 우분투)"
date: 2019-12-02
excerpt: ""
tags: [우분투 사양, os , cpu,ssd, hdd, 메모리]
comments: true
etc: true
---



> 자주 확인하는 서버 사양 명령어를 정리해 보았다.

# 1. os 확인
{% highlight shell %}
    cat /etc/issue
{% endhighlight %}
# 2. cpu 확인

> model name 확인
{% highlight text %}
    $ cat /proc/cpuinfo
    
    
    processor       : 0
    vendor_id       : GenuineIntel
    cpu family      : 6
    model           : 158
    model name      : Intel(R) Core(TM) i5-7500T CPU @ 2.70GHz
{% endhighlight %}
# 2. 메모리 확인
{% highlight shell %}
    $ cat /proc/meminfo | grep Mem
    
    MemTotal:       12183524 kB
{% endhighlight %}
# 2. hdd, ssd 확인
## 2-1. hdd, ssd 구분
> 아래에서 0은  SSD, 1은 HDD이다 
{% highlight shell %}
    $ lsblk -d -o name,rota
    
    NAME    ROTA
    nvme0n1    0    
    sda        1
{% endhighlight %}
## 2-2. hdd, ssd 각 용량 확인

> 위에 name과 맞는 것을 찾아 용량을 확인한다. 아래에서 ssd는 `/dev/nvme0n1: 119.2 GiB` hdd는 `Disk /dev/sda: 931.5 GiB`부분이다. 
{% highlight shell %}
    $ sudo fdisk -l
    
    [sudo] password for www: 
    Disk /dev/nvme0n1: 119.2 GiB, 128035676160 bytes, 250069680 sectors
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disklabel type: dos
    Disk identifier: 0xd2943357
    
    ----- 생략 -----
    
    Disk /dev/sda: 931.5 GiB, 1000204886016 bytes, 1953525168 sectors
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 4096 bytes
    I/O size (minimum/optimal): 4096 bytes / 4096 bytes
{% endhighlight %}
