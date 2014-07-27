Hướng Dẫn Tạo LVM
=================

##I. Thông tin LAB
> - Tạo máy ảo trên vmware Workstation cài hệ điều hành ubuntu server 12.04
> - Add thêm 2 ổ cứng có dung lượng 10GB vào máy ảo


**Mô tả bằng hình ảnh**
<br>
<p align="center"><img src="http://i.imgur.com/9rNADx4.png"></p>

##II. Tiến hành cài đặt 1 logical volumes 
**Trong bài này mình sẽ đặt tên các volume group, logical volume được như sau:**
> - Volume group: vgdemo
> - logical volume: lvdata

**Chú ý:** Bạn lên đặt tên của volume group là vg[ten] hoặc lv[ten] để dễ nhận dạng hơn

**B1: Trước khi cài đăt bạn lên kiểm tra xem có những hard drives nào bằng lệnh sau: #lsblk**
> root@controller:~# **lsblk** <br>
> NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT <br>
> sda      8:0    0    40G  0 disk <br>
> ├─sda1   8:1    0   1.9G  0 part [SWAP] <br>
> ├─sda2   8:2    0  18.6G  0 part / <br>
> ├─sda3   8:3    0   9.3G  0 part <br>
> └─sda4   8:4    0  10.2G  0 part /srv/node/sda4 <br>
> sdb      8:16   0    10G  0 disk <br>
> sdc      8:32   0    10G  0 disk <br>
> sr0     11:0    1   665M  0 rom <br>

Sau khi nhập vào #lsblk bạn sẽ thấy được 2 hard drives đã có là: sdb và sdc.

Sau khi kiểm tra xong bạn cài đặt thêm gói lvm2 vào với lệnh sau: # apt-get install lvm2

**B2:  Tạo partition bằng cậu lệnh: #fdisk /dev/sdb**

1.	Tạo partition trong /dev/sdb:
> Command (m for help): <b>n</b>  <br>
> Partition type: <br>
>    p   primary (0 primary, 0 extended, 4 free)<br>
>    e   extended<br>
> Select (default p): <b>p</b><br>
> Partition number (1-4, default 1): 1<br>
> First sector (2048-20971519, default 2048):<br>
> Using default value 2048 <br>
> Last sector, +sectors or +size{K,M,G} (2048-20971519, default 20971519): +5G<br>

2.	Kiểm tra xem partition đã được tạo chưa

> Command (m for help): <b>p</b> <br>
> Disk /dev/sdb: 10.7 GB, 10737418240 bytes <br>
> 255 heads, 63 sectors/track, 1305 cylinders, total 20971520 sectors <br>
> Units = sectors of 1 * 512 = 512 bytes <br>
> Sector size (logical/physical): 512 bytes / 512 bytes <br>
> I/O size (minimum/optimal): 512 bytes / 512 bytes <br>
> Disk identifier: 0xc8fe9a22 <br>
> Device Boot      Start         End      Blocks   Id  System <br>
> /dev/sdb1        2048     10487807    5242880   83  Linux <br>

Từ trên ta thấy được partition /dev/sdb1 đã được tạo. Sau đó ấn w để lưu lại quá trình tạo partition. Làm tương tự đối với hard drives sdc

**B3: Tạo physical volume là /dev/sdb1 và /dev/sdc1**

Sử dụng câu lệnh sau để tao physical volume: #pvcreate /dev/sdb1 và #pvcreate /dev/sdc1

> root@controller:~# <b>pvcreate /dev/sdb1</b><br>
>   Physical volume "/dev/sdb1" successfully created<br>
> root@controller:~# pvcreate /dev/sdc1<br>
>    Physical volume "/dev/sdc1" successfully created<br>

**B4: Kiểm tra xem physical volume đã có chưa bằng lệnh sau: #pvdisplay**

> root@controller:~#<b> pvdisplay</b><br>
>   "/dev/sdb1" is a new physical volume of "5.00 GiB"<br>
>   --- NEW Physical volume ---<br>
>   PV Name               /dev/sdb1<br>
>   VG Name<br>
>   PV Size               5.00 GiB<br>
>   Allocatable           NO<br>
>   PE Size               0<br>
>   Total PE              0<br>
>   Free PE               0<br>
>   Allocated PE          0<br>
>   PV UUID               wdzUgF-s77s-vanU-zqh7-TEcc-H3I5-n4nob0 <br>
>   "/dev/sdc1" is a new physical volume of "5.00 GiB"<br>
>   --- NEW Physical volume ---<br>
>   PV Name               /dev/sdc1<br>
>   VG Name<br>
>   PV Size               5.00 GiB<br>
>   Allocatable           NO<br>
>   PE Size               0<br>
>   Total PE              0<br>
>   Free PE               0<br>
>   Allocated PE          0<br>
>   PV UUID               oSuTNT-dX3U-grfr-UKOL-6Gyo-32s3-XsUTIc<br>

**B5: Tạo volume group với câu lệnh: #vgcreate {tên volume group} { physical volume}**

> root@controller:~# <b>vgcreate vgdemo /dev/sdb1 </b><br>
>   Volume group "vgdemo" successfully created <br>

**B6: Kiểm tra xem volume group đã có chưa bằng lệnh sau: #vgdisplay**

> root@controller:~# <b>vgdisplay</b><br>
>   --- Volume group ---<br>
>   VG Name               vgdemo<br>
>   System ID<br>
>   Format                lvm2<br>
>   Metadata Areas        1<br>
>   Metadata Sequence No  1<br>
>   VG Access             read/write<br>
>   VG Status             resizable<br>
>   MAX LV                0<br>
>   Cur LV                0<br>
>   Open LV               0<br>
>   Max PV                0<br>
>   Cur PV                1<br>
>   Act PV                1<br>
>   VG Size               5.00 GiB<br>
>   PE Size               4.00 MiB<br>
>   Total PE              1279<br>
>   Alloc PE / Size       0 / 0<br>
>   Free  PE / Size       1279 / 5.00 GiB<br>
>   VG UUID               vlL9ON-E63w-MdSZ-fxjJ-YO5U-L1cF-UrydJO<br>

**B7: Tạo logical volume: #lvcreate**<br>

> root@controller:~# <b>lvcreate -L 3GB -n lvdata vgdemo</b><br>
>   Logical volume "lvdata" created

-L:  Chỉ ra dung lượng của logical volume <br>
-n: Tên của logical volume <br>

**B8: Kiểm tra xem logical volume đã có chưa: #lvdisplay**

> root@controller:~# <b>lvdisplay</b><br>
>   --- Logical volume ---<br>
>   LV Name                /dev/vgdemo/lvdata<br>
>   VG Name                vgdemo<br>
>   LV UUID                OzvRiA-nX19-5YNc-pdlI-HS0X-Gmuj-ehVdFE<br>
>   LV Write Access        read/write<br>
>   LV Status              available<br>
>   # open                 0<br>
>   LV Size                3.00 GiB<br>
>   Current LE             768<br>
>   Segments               1<br>
>   Allocation             inherit<br>
>   Read ahead sectors     auto<br>
>   - currently set to     256<br>
>  Block device           252:0<br>

**B9: Format  và mount cho logical volume:**

Format: #mkfs.ext3 /dev/vgdemo/lvdata

> root@controller:~# <b>mkfs.ext3 /dev/vgdemo/lvdata</b><br>
> mke2fs 1.42 (29-Nov-2011)<br>
> OS type: Linux<br>
> Block size=4096 (log=2)<br>
> Fragment size=4096 (log=2)<br>
> Stride=0 blocks, Stripe width=0 blocks<br>
> 196608 inodes, 786432 blocks<br>
> 39321 blocks (5.00%) reserved for the super user<br>
> First data block=0<br>
> Maximum filesystem blocks=805306368<br>
> 24 block groups<br>
> 32768 blocks per group, 32768 fragments per group<br>
> 8192 inodes per group<br>
> Superblock backups stored on blocks:<br>
>         32768, 98304, 163840, 229376, 294912<br>
> Allocating group tables: done<br>
> Writing inode tables: done<br>
> Creating journal (16384 blocks):<br>
> done<br>
> Writing superblocks and filesystem accounting information: done<br>

Mount:<br>
Tạo cây thư mục như sau:<br>
Mkdir /partition<br>
Mkdir /partition/data<br>
Sau khi tạo thư mục ta mount /dev/vgdemo/lvdata vào /partition/data bằng cách thực hiện lệnh sau:<br>
 #mount /dev/vgdemo/lvdata /partition/data

Sau khi thực hiện 2 lệnh sau ta thực hiện kiểm tra xem /dev/vgdemo/lvdata đã được mount và format đúng chưa bằng các lệnh sau: #df -T<br>
> Filesystem                Type     1K-blocks    Used Available Use% Mounted on<br>
> /dev/sda2                 ext4      19091584 1019992  17095056   6% /<br>
> udev                      devtmpfs    238172      12    238160   1% /dev<br>
> tmpfs                     tmpfs        99120     252     98868   1% /run<br>
> none                      tmpfs         5120       0      5120   0% /run/lock<br>
> none                      tmpfs       247796       0    247796   0% /run/shm<br>
> /dev/sda4                 xfs       10682368   32928  10649440   1% /srv/node/sda4<br>
> /dev/mapper/vgdemo-lvdata ext3       3096336   70212   2868840   3% /partition/data<br>

Chú ý: Khi bạn mount /dev/vgdemo/lvdata /partition/data thì chỉ được tạm thời tức là khi ta restart lại máy thì phải mount bằng tay lần nữa để không phải mất thời gian trong quá trình mount ta sẽ thực hiện như sau: <br>
 #vi /etc/fstab chèn dòng sau vào file:<br>

> /dev/vgdemo/lvdata       /partition/data  ext3  defaults 0 0 <br> 
> Sau đó lưu lại. Bạn có thể restart để kiểm tra thông số.<br>

##III. Xóa logical volume, volume group, physical volume
**1. Xóa Logical Volumes**

B1: Umount file system
 #Umount /dev/vgdemo/lvdata<br>
 Sau khi umount được file system ta kiểm tra xem đã umount được chưa bằng lệnh sau: #df -T
 
B2: Xóa logical volumes
 #lvremove /dev/vgdemo/lvdata<br> 
 Sau khi xóa logical volume ta kiểm tra bằng lệnh sau: #lvdisplay<br>

**2. Xóa volume groups**

 #vgremove /dev/vgdemo<br>
 Kiểm tra lại xem volume groups đã được xóa chưa: #vgdisplay<br>

**3. Xóa physical volume**

 #pvremove /dev/sdb1<br>
 Kiểm tra lại xem physical volumes đã được xóa chưa: #vgdisplay<br>


##IV. Tăng và giảm kích thước trong logical volumes

Có 3 tool được sử dụng cho việc tạo ra physical volumes, volume group, và logical volumes tăng lên hoặc giảm đi 

Chú ý: Mỗi lênh sau sẽ cần được đứng trước vg, pv, vl phụ thuộc vào bạn làm gì

3 tool đó là:<br>
> - resize: Có thể tăng hoặc giảm physical volumes và logical volumes nhưng ko được dùng cho volumes groups<br>
> - extend: Có thể làm cho volumes groups và logical volumes tăng lên nhưng không giảm<br>
> - reduce: Có thể làm cho volumes groups và logical volumes giảm đi nhưng không tăng lên<br>

###4.1 Tăng thêm kích thước cho logical volumes (Extend Logical Volumes)

Trong mục 2 tiến hành cài đăt logical volumes tại bước 3 ta đã tạo ra 1 physical volumes là: /dev/sdc1. Sử dung /dev/sdc1 để mở rộng logical volumes.

**B1: Tăng kích thước của volumes group bằng cách thêm physical volumes /dev/sdc1**<br>
> **# vgextend vgdemo /dev/sdc1** <br>
Với cú pháp này ta hiểu như sau:

vgdemo: Volumes group mà bạn muốn tăng kích thước<br>
/dev/sdc1 physical volumes mà muốn add vào volumes group<br>

**B2: Tăng kích thước cho logical volumes:**

> **# lvextend -L 8G /dev/vgdemo/lvdata**

Hoặc

> **# lvextend -L +5G /dev/vgdemo/lvdata**

**B3: Sau khi tăng kích thước cho logical volumes thì logical volumes đã được tăng là 8G nhưng file system trên volumes này vẫn sẽ là 3G bạn phải sử dụng lệnh sau để thay đổi**

> **# resize2fs /dev/vgdemo/lvdata**

Hoàn thành quá trình tăng kích thước cho logical volumes kiểm tra lại lệnh: # df -h xem đã đúng chưa.

###4.2 Giảm kích thước cho logical volumes (Shrink Logical Volume)

**B1: umount logical volumes mà muốn giảm kích thước.**

> **umount /dev/vgdemo/lvdata**

**B2: Kiểm tra file system**

> **e2fsck -f /dev/vgdemo/lvdata**

**B3: Giảm file system xuống kích cỡ mong muốn**
 
> **resize2fs -p /dev/vgdemo/lvdata 5G**

Giảm kích cỡ file system xuống còn 5GB thôi

**B4: Giảm  kích thước của logical volumes**

> **lvreduce -L 5G /dev/vgdemo/lvdata

**B5: Kiểm tra file system và logical volumes**

> **e2fsck -f /dev/vgdemo/lvdata**

và 

> **resize2fs -p /dev/vgdemo/lvdata** 

**B6: Mount lại logical volumes**

> **mount /dev/vgdemo/lvdata /partition/lvdata**

Hoàn thành quá trình giảm kích cỡ volumes group!













