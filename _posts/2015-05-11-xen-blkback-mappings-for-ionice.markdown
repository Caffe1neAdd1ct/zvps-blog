---
layout: post
title:  "Xen 4.4 Blkback Process Mappings For Ionice Swap Partitions"
date:   2015-05-11 15:49:12
categories: guides linux lvm xen ionice
---

## Mapping blkback process to running Xen DomU

Limiting io on a xen domu swap partition can help reduce load on hypervisors and prevent them from becoming unresponsive. However once the blkback processes have domids greater than two digits in length using ionice on the process becomes a guessing game. Below is our investigation into mapping a running blkback process to a domu.

### Logical Volumes to System Block Devices

The command `dmsetup ls` can be used to map logical volumes `lvs` to system block devices `/dev/dm-*`. Running `dmsetup ls --tree` will give an output similar to the following:

{% highlight bash %}
vg-vm176_img	(253:19)
 └─ (8:4)
vg-vm176_swap	(253:20)
 └─ (8:4)
{% endhighlight %}

Showing:

 * Volume Group `vg`
 * Logical Volume `vm176_img` or `vm176_swap`
 * Volume Group Device ID `253`
 * Logical Volume Device ID `19` or `20`

Firstly filter down to the Xen DomU name using `grep`:

`dmsetup ls | grep -i "vm110"`

{% highlight bash %}
vg-vm176_img	(253:19)
vg-vm176_swap	(253:20)
{% endhighlight %}

To map all logical volumes to device ids run the following command can be used:

`sudo lvdisplay|awk  '/LV Name/{n=$3} /Block device/{d=$3; sub(".*:","dm-",d); print d,n;}'`

To find related I/O stats use the `iostat` command which is part of the `sysstat` package:

`iostat -d | grep "dm-19 "`

{% highlight bash %}
dm-19             2.89        99.98        22.03    9633792    2123064
{% endhighlight %}

`iostat -d | grep "dm-20 "`

{% highlight bash %}
dm-20             0.01         0.06         0.33       5312      31480
{% endhighlight %}

The `iostat` command shows the following columns:

 * `Device:` - Kernels 2.6 and newer shown as device id as listed in /dev/dm-*
 * `tps` - transfers per second issued to the device
 * `Blk_read/s` - current reads/sec throughput in KB/s
 * `Blk_wrtn/s` - current writes/sec throughput in KB/s
 * `Blk_read` - Total read in KB
 * `Blk_wrtn` - Total write in KB
 
### System Block Device to Running blkback process
 
Next to map the dm-* device to a running blkback process. When the domid goes over 2 digits the ps aux results will no longer show the xvda1/2 denotation.
This makes mapping from the logical volume to the running device blkback process impossible or a guessing game with a 50% chance of being correct. However using blktrace and blkparse we can find the running system process id:

 * `yum install blktrace`
 * `mount -t debugfs debugfs /sys/kernel/debug`
 * `blktrace -d /dev/dm-20 -o - | blkparse -i -`

{% highlight bash %}
253,21   0        1     0.000000000 25821  Q  WS 770048 + 8 [blkback.37.xvda]
253,21   0        2     0.000024783 25821  Q  WS 303424 + 8 [blkback.37.xvda]
253,21   0        3     0.000089757     0  C  WS 770048 + 8 [0]
253,21   0        4     0.000109480     0  C  WS 303424 + 8 [0]
253,21   0        5     0.000211260 25821  Q  WS 15636760 + 88 [blkback.37.xvda]
253,21   0        6     0.000240933 25821  Q  WS 15636848 + 88 [blkback.37.xvda]
253,21   0        7     0.000264115 25821  C  WS 15636760 + 88 [0]
253,21   0        8     0.000291671 25821  C  WS 15636848 + 88 [0]
253,21   0        9     0.000322110 25821  Q  WS 15636936 + 64 [blkback.37.xvda]
253,21   0       10     0.000380070     0  C  WS 15636936 + 64 [0]
253,21   0       11     0.000434807 25821  Q  WS 15637000 + 8 [blkback.37.xvda]
253,21   0       12     0.000482916     0  C  WS 15637000 + 8 [0]
{% endhighlight %}

 * Process id is `25821`
 * Check running processes for this ID using `ps aux | grep [2]5821`
 
{% highlight bash %}
 root     25821  0.0  0.0      0     0 ?        S    May10   0:03 [blkback.37.xvda]
{% endhighlight %}
 
DomU guest servers on a solusvm setup have the following device setups:

 * xvda1 /dev/vg/vm***_img
 * xvda2 /dev/vg/vm***_swap

### Sample run though to limit domu swap partition

Use `iotop` to find the blkback processes with two named the same using a lot of read / write I\O:

 * `iotop -o -a`

{% highlight bash %}
27443 be/4 root         **12.00 G**      **6.00 G**  0.00 %  0.00 % [blkback.37.xvda]
{% endhighlight %}


 * `ps aux | grep [b]lkback.37`
 
{% highlight bash %}
root     27443  0.0  0.0      0     0 ?        S    May10   0:02 [blkback.37.xvda] (if these were labelled xvda1/xvda2 this would be a lot easier)
root     27444  0.0  0.0      0     0 ?        S    May10   0:00 [blkback.37.xvda]
{% endhighlight %}

 * `xl domname 37` 
 
{% highlight bash %}
 vm176
{% endhighlight %}
 
 * `lvdisplay -c | grep vm176_swap`
  
{% highlight bash %}
  /dev/vg/vm176_swap:vg:3:1:-1:2:1048576:128:-1:0:-1:253:20
{% endhighlight %}

 * `blktrace -d /dev/dm-20 -o - | blkparse -i -`
 
{% highlight bash %}
253,20   0     2582   346.686626712 27444  Q   R 576 + 64 [blkback.37.xvda]
253,20   0     2583   346.686796981     0  C   R 576 + 64 [0]
253,20   0     2584   346.686884522 27444  Q   R 424 + 24 [blkback.37.xvda]
253,20   0     2585   346.686898184 27444  Q   R 384 + 32 [blkback.37.xvda]
253,20   0     2586   346.693388592     0  C   R 424 + 24 [0]
253,20   0     2587   346.693435109     0  C   R 384 + 32 [0]
253,20   0     2588   346.693534487 27444  Q   R 512 + 64 [blkback.37.xvda]
253,20   0     2589   346.693706972     0  C   R 512 + 64 [0]
{% endhighlight %}

 * Process id of swap partition `dm-20` for domu `vm176` id `37` is `27444`
 
To stop the domu from consuming all the server I\O using `ionice` command:
 
  * `ionice -p 27444 -c 3 -n 7`