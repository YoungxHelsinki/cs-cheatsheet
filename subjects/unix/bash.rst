========
Commands
========

grep
====

.. code-block:: bash

  # https://stackoverflow.com/a/16957078
  grep -rnw '/path/to/somewhere/' -e 'pattern'
  grep -rnw --exclude=\*.{html,js}  "Welcome"

  grep -rnw . -e so | xargs egrep -v '^#'
  grep -rnw . -e so | xargs -n 1 egrep -v '^#'
  dpkg -L netatalk | grep etc | xargs -n 1 egrep -v '^#'
  dpkg -L netatalk | grep etc | xargs -n 1 egrep -v '^#' | less
  dpkg -L netatalk | grep etc | xargs -n 1 grep -v '^#' | less
  dpkg -L netatalk | grep etc | xargs -n 1 grep -v '^#' | grep '\.so'
  dpkg -L netatalk | grep etc | xargs grep -v '^#' | grep '\.so'

  # Get only directories
  ls -l | grep "^d"

  # Get all non-empty lines
  # Pattern matches "begin of string (^), directly followed by "end of string" ($)
  grep -v '^$' file-name

  # Get all, except empty & commented lines
  grep -v '^$' .zshrc | grep -v '^#'


find
====

.. code-block:: bash

  find . -name "*netatalk*"

  find . -type f
  find . -type d

  find -name shell
  find -name '*shell*'

  find ! -name '*shell*' -type d

  # -iname for case insensitive
  find -size +10M -sizee -100M -iname '*.jpg'

  find . 2> /dev/null | grep bin

  # Find a file that the user has such acl
  find . -perm /u=rwx -name '*mine*'

  # Find a file that the user has such EXACT acl
  find . -perm +u=rwx -name '*mine*'

  # Find a file that the user has such acl and remove acl for others
  find . -perm /u=rwx -name '*mine*' -exec chmod o-rwx {} \;

  # Delete matching files in all subdirectories
  # https://superuser.com/a/112079
  find . -name .DS_Store -type f -print0 | xargs -0 rm -r



Piping & Redirecting
======

.. code-block:: bash
  
  # Redirect error message. Two methods
  find . -name awesome_file 2> /dev/null 1> files.txt
  find . 2> /dev/null | grep awesome_file > files.txt

  find . -name awesome_file &> /dev/null 

---------------------------


File system
======

.. code-block:: bash
  
  # Understand Linux Files ystem
  man 7 hier

  # Check file systems
  df -T

  # Delete all partition table on a disk
  # Unmount all partitions!
  dd if=/dev/zero of=/dev/sda bs=512 count=1 conv=notrunc

Disk usage
======

.. code-block:: bash

  // For any file or dir
  du -sh *

  // The filesystem.
  df -h

  // Search file size for the lowest directory
  du -h | sort -h

GPU info
======

.. code-block:: bash

  # 1. NVIDIA gpus
  nvidia-smi

  # 2. Display info
  (sudo) lshw -C display

  # 3. Device info
  lspci  -v -s  $(lspci | grep ' VGA ' | cut -d" " -f 1)

CPU info
========

.. code-block:: bash

  less /proc/cpuinfo

  sudo lshw -short -C memory
  

Processes
======

Show process tree

.. code-block:: bash

  pstree -p -s  # p for pid, s for "show parents"

------------------

About Disks
======

.. code-block:: bash

  # Show hard drive info
  sudo lshw -C disk

  # Show installed disks
  lsblk

  # Investigate partitions
  parted /mnt/sparsebundle/sparsebundle.dmg unit B print

  # Manipulate disk partition table
  sudo fdisk -l | grep Error


----------------------------

SSH
======

Run commands from login shell via SSH

.. code-block:: bash

  ssh false bash -c -l "module load tensorflow"
  ssh kosh bash -c -l "python"
  

Samba
======

.. code-block:: bash
  
  net ads search samaccountname="username"
  net ads search co="korea" cn
  net ads search cn="famil-name first-name"
  net ads search mail=email samaccountname
  net ads search cn="famil-name first-name" samaccountname
  net ads search cn="famil-name first-name" samaccountname mail


System
======

.. code-block:: bash

  journalctl


Conditionals
======

.. code-block:: bash
  # checks that file exists
  [[ -f $file ]] && echo $file exists || { echo error; exit 1; }

  # check that directory does not exist before creating one
  [[ -d $dir ]] || mkdir $dir

zfs
======

.. code-block:: bash

  # Install zfs on Ubuntu
  apt-get install zfsutils-linux

  # Check zfs version
  modprobe zfs

  # Create a pool
  zpool create -f timemachine_backup_zpool /dev/sda ...
  zpool status
  zfs list

  # Create a fs
  zfs create timemachine_backup_zpool/fs
  zfs list

  # Properties of the fs
  zfs get all timemachine_backup_zpool/fs

  # Set properties of the fs
  zfs set quota=500G timemachine_backup_zpool/fs
  zfs set compression=on timemachine_backup_zpool/fs

  # zfs version
  dmesg | grep ZFS
  modinfo zfs
  cat /sys/module/zfs/version

  # Add a disk
  # https://www.howtogeek.com/175159/an-introduction-to-the-z-file-system-zfs-for-linux/
  sudo zpool add -f pool /dev/sde /dev/sdf /dev/sdg

  # Setting mount point
  zfs get mountpoint timemachine_backup_zpool/fs
  zfs unmount -f timemachine_backup_zpool/fs  
  zfs set mountpoint=/home/leon/timemachine_backup_zpool/fs timemachine_backup_zpool/fs
  zfs get mountpoint timemachine_backup_zpool/fs
  zfs mount 
  zfs get mounted pool/filesystem
  

Network
======

.. code-block:: bash
  
  # Show open ports
  netstat -tulpn | grep LISTEN    # sudo for process id
  

Package management
======

.. code-block:: bash

  # https://unix.stackexchange.com/a/6286
  # Dry run. -s for simulation
  apt-get -s install <package>

  # See all possible upgrades
  apt-get -V -s upgrade

  # Search installed/remote packages
  apt-cache policy <package>

  # Search remote packages
  apt-cache search <package>

  # Show version if installed
  apt-show-versions <package>

  # Show every file related to the package
  dpkg-query -L <package>

  # Get package version
  dpkg -l | grep netatalk

  # Get package files
  dpkg -L netatalk


.. code-block:: bash

  ./configure
  make
  checkinstall

  dpkg -r <package>


.. code-block:: bash

  type -a ping  # better than which

  file $(which ping)

.. code-block:: bash

  apt-file search 
---------------------------------------
Variables
======

.. code-block:: bash

  # Declare an integer
  declare -i number
  number=2
  number+=1   
  echo $number  # 3


  unset var-name

``$?`` is an exit code variable of last command.

.. code-block:: bash

  ping -c 1 8.8.8.8; echo $?  # 0. i.e. successful


---------------------------------------
Envrionments
======

.. code-block:: bash

  #!$(which Bash)

---------------------------------------
E-mail
======


.. code-block:: bash

   ls -l > listing && { mail -s "ls -l $(pwd) @ $(date +'%Y-%m-%d %H:%M')" email@email.fi < listing; mv listing listing.$(date +"%Y-%m-%d-%H-%M"); }


Arrays
======

.. code-block:: bash

  arr={first}
  echo ${arr[0]}
  echo ${arr[@]}    # List all items

  arr+=(second)
  arr+=("third 3rd")

  printf "%s\n" ${arr[@]}

  echo ${#arr[0]}   # Length of the item

Array: For-loops
################

.. code-block:: bash

  for i in ${!arr[@]}; do echo $i is ${arr[$i]}; done

  echo ${!arr[@]} -- ${arr[@]}

.. code-block:: bash
  
  w -h | cut -c1-8

  declare =a arr

  for i in $(w -h | cut -c1-8); do
    arr+=($i)
  done

  w -h | cut -c1-8 | sort | uniq -c | sort -n

Generate random numbers
=======================

.. code-block:: bash

  shuf -i 0-65000 -n 10 