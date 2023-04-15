—
title: "マウントした HDD が df コマンドで表示されない"
emoji: "😽"
type: "tech"
topics:
  - "linux"
  - "centos"
published: false
published_at: "2022-11-06 00:31"
—

環境: CentOS7

## 状況 

事前に増設する HDD のフォーマット等は行ってあるものとする．
HDD の増設を下記の通り行った (管理者権限が必要)．

```
(root) # mkdir /mnt/secondly
(root) # mount -t ext4 /dev/sda /mnt/secondly
(root) # df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                  63G     0   63G   0% /dev
tmpfs                     63G     0   63G   0% /dev/shm
tmpfs                     63G   11M   63G   1% /run
tmpfs                     63G     0   63G   0% /sys/fs/cgroup
/dev/mapper/centos-root   50G   36G   15G  71% /
/dev/nvme0n1p2          1014M  226M  789M  23% /boot
/dev/nvme0n1p1           200M   12M  189M   6% /boot/efi
/dev/mapper/centos-home  411G  356G   56G  87% /home
tmpfs                     13G  8.0K   13G   1% /run/user/42
tmpfs                     13G     0   13G   0% /run/user/1000
/dev/sda                 7.3T   93M  6.9T   1% /mnt/secondly
```

このままでは reboot した際に自動的にマウントされないらしいので，UUID を調べて，下記のように `/etc/fstab` にUUID="example1234..."を追記した．

```
(user) $ cat /etc/fstab 

#
# /etc/fstab
# Created by anaconda on Tue Mar 16 16:24:09 2021
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=(default1234...) /boot                   xfs     defaults        0 0
UUID=(default1234...)          /boot/efi               vfat    umask=0077,shortname=winnt 0 0
/dev/mapper/centos-home /home                   xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0
UUID="example1234..." /mnt/secondly       ext4     nofail          0 0
```

コマンド `sudo reboot` で再起動したところ，マウントした HDD にアクセスはできるが，`df -h` で `/dev/sda` が下記のように表示されない．

```
(user) $ df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                  63G     0   63G   0% /dev
tmpfs                     63G     0   63G   0% /dev/shm
tmpfs                     63G   11M   63G   1% /run
tmpfs                     63G     0   63G   0% /sys/fs/cgroup
/dev/mapper/centos-root   50G   36G   15G  71% /
/dev/nvme0n1p2          1014M  226M  789M  23% /boot
/dev/nvme0n1p1           200M   12M  189M   6% /boot/efi
/dev/mapper/centos-home  411G  357G   54G  87% /home
tmpfs                     13G  8.0K   13G   1% /run/user/42
tmpfs                     13G     0   13G   0% /run/user/1000
```

## 解決方法

試行錯誤してみると，下記のように `/etc/fstab`を書き換えると，`df -h` で表示されるようになった．
`sudo vim /etc/fstab` で UUID をコメントアウトし，`/dev/sda` に書き換えた．

```
(user) $ cat /etc/fstab 

#
# /etc/fstab
# Created by anaconda on Tue Mar 16 16:24:09 2021
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=(default1234...) /boot                   xfs     defaults        0 0
UUID=(default1234...)          /boot/efi               vfat    umask=0077,shortname=winnt 0 0
/dev/mapper/centos-home /home                   xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0
/dev/sda		/mnt/secondly		ext4 	nofail 		0 0
#UUID="example1234..." /mnt/secondly       ext4     nofail          0 0
```

書き換えた後，`sudo reboot` で再起動を行う．
再起動後の `df -h` コマンドの結果は下記． 

```
(user) $ df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                  63G     0   63G   0% /dev
tmpfs                     63G     0   63G   0% /dev/shm
tmpfs                     63G   11M   63G   1% /run
tmpfs                     63G     0   63G   0% /sys/fs/cgroup
/dev/mapper/centos-root   50G   36G   15G  72% /
/dev/nvme0n1p2          1014M  226M  789M  23% /boot
/dev/nvme0n1p1           200M   12M  189M   6% /boot/efi
/dev/sda                 7.3T   93M  6.9T   1% /mnt/secondly
/dev/mapper/centos-home  411G  358G   54G  87% /home
tmpfs                     13G  8.0K   13G   1% /run/user/42
tmpfs                     13G     0   13G   0% /run/user/1000
```

`dev/sda` の空き容量等が表示されるようになった．
解決方法は多分，これでいいはず...