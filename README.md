# fat12-disk-forensics

FAT12 File System Forensic Analysis
 
Forensic investigation of a disk image suspected to contain evidence of fraudulent activity, focused on validating evidence integrity and recovering deleted files from a FAT12 file system.

Objective

Given a raw disk image, the goals were to:

Preserve evidential integrity through proper imaging and hashing
Manually parse the FAT12 file system structure at the byte level
Recover deleted files using two independent methods and confirm they match
Tools

FTK Imager · The Sleuth Kit (mmls, fsstat, icat) · strings · certutil · a hex editor

Process
Evidence preservation — sanitised the target media (DoD 5220.22-M standard) before imaging, then created a duplicate and verified an MD5 hash match between source and copy to prove no data was altered.
Partition & file system analysis — used mmls to locate partition offsets, then fsstat to confirm a FAT12 file system and map out the boot sector, FAT tables, root directory, and cluster regions.
Directory entry parsing — extracted the root directory and manually decoded 32-byte directory entries in a hex editor (filename, deletion marker, starting cluster, file size).
File recovery — recovered deleted files two ways: icat (Sleuth Kit) referencing the starting cluster, and FTK Imager's built-in recovery. Cross-checked both outputs to confirm consistent recovery.
Key finding

Deleted file entries were identified by the 0xE5 marker in the first byte of the directory entry. Cross-referencing the FAT chain against the directory entry's starting cluster allowed full reconstruction of file content even after deletion, since the data blocks themselves weren't overwritten.

What I'd do differently

Automate the directory entry parsing with a small Python script rather than manual hex decoding — good for understanding the structure once, but not scalable for larger images.
