[`<<< Back to Developer Info`](Developer_Info#AWS_Tips "wikilink")

- Install the [EPEL6
  repo](http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm)
- Install the **cloud-utils-growpart** package:

`[bash]# `**`yum install cloud-utils-growpart`**

- Determine the root partition device using the **lsblk** command:

`[bash]# `**`lsblk`**
`NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT`
`xvda    202:0    0  150G  0 disk `
`└─xvda1 202:1    0  8G  0 part /`

- Grow the root partition:

`[bash]# `**`growpart /dev/xdva 1`**

- At this point **lsblk** will not show a change, but if you use
  **parted** you'll see that the deed has indeed been done:

`[bash]# `**`parted`**
`GNU Parted 2.1`
`Using /dev/xvda`
`Welcome to GNU Parted! Type 'help' to view a list of commands.`
`(parted) `**`unit s`**`                                                         `
`(parted) `**`print`**`                                                            `
`Model: Xen Virtual Block Device (xvd)`
`Disk /dev/xvda: 314572800s`
`Sector size (logical/physical): 512B/512B`
`Partition Table: msdos`

`Number  Start  End         Size        Type     File system  Flags`
` 1      2048s  314568764s  314566717s  primary  ext4         boot`

- The only thing remaining is to resize the filesystem. This will be
  done automatically by the cloud-init tools after a reboot:

`[bash]#  `**`reboot now`**

- When the system is back up, login and use **lsblk** to confirm your
  change:

`[bash]$ `**`lsblk`**
`NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT`
`xvda    202:0    0  150G  0 disk `
`└─xvda1 202:1    0  150G  0 part /`

- WOOT! Many thanks to this [autoresize
  article](https://www.elastic.co/blog/autoresize-ebs-root-volume-on-aws-amis)
  by [Dimitrios
  Liappis](https://www.elastic.co/blog/author/dimitrios-liappis) for
  this most excellent process.