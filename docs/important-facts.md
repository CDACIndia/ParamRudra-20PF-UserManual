# Some Important Facts

## About File Size

The global space is served by a number of storage arrays. Each of the storage array contains a portion of the space.  The size of a disk in the storage array is 285TB. Technically, the size of a file can be about 285 TB (which is really big). However, since the disk is shared by a large number of files, effectively the size of a single file will be far smaller. Normally, this file size is kept to be about a few GBs which is sufficient for most of the users.  However, if you wish to have file sizes which are larger than this, you need to create files ACROSS disks and this process is known as ‘striping’.

```bash
lfs setstripe -c 4.
```

After this has been done all new files created in the current directory will be spread over 4 storage arrays each having 1/4th of the file. The file can be accessed as normal no special action needs to be taken. When the striping is set this way, it will be defined on a per directory basis so different directories can have different stripe setups in the same file system; new subdirectories will inherit the striping from its parent at the time of creation.

We recommend users to set the stripe count so that each chunk will be approx. 200-300GB each, for example

### Recommended Lustre Striping Configuration

| **File Size** | **Stripe Count** | **Command** |
|---------------|:----------------:|-------------|
| 500–1000 GB | 4 | `lfs setstripe -c 4 .` |
| 1000–2000 GB | 8 | `lfs setstripe -c 8 .` |

Once a file is created with a stripe count, it cannot be changed. A user by themselves is also able to set stripe size and stripe count for their directories and A user can check the set stripe size and stripe count with following command:

lfs getstripe `<path to the direcory>`

To set the stripe count as

lfs setstripe -c 4 -s 10m `<path to the direcory>`

The options on the above command used have these respective functions.

- -c to set the stripe count; 0 means use the system default (usually 1) and -1 means stripe over all available OSTs (lustre Object Storage Targets).
- -s to set the stripe size; 0 means use the system default (usually 1 MB) otherwise use k, m or g for KB, MB or GB respectively

## Little-Endian and Big-Endian issues?

By and large, most of the computers follow little-endian format. This essentially means that the last byte of the binary representation of data is stored first. However, there is another way of representing data (used in some machines) where in the first byte of the binary representation of data is stored first. When binary files are to be read across these different kinds of machines, bytes need to be re-ordered. Many compilers do support this feature. Please explore this aspect, if a perfectly working code on a given machine fails to get executed on another machine (with a different processor). 
