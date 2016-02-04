# HDP2.3_Install
Installing HDP 2.3.4 using Ambari 2.2.0 and Configure Secirity using Active Directory Integration

Below are the order of steps followed to install HDP 2.3

(Keep the curl commands handy)

Phase - 1
----------

1) Platform Architecture

2) Check prerequisites from docs

http://docs.hortonworks.com/HDPDocuments/Ambari-2.2.0.0/bk_Installing_HDP_AMB/content/_meet_minimum_system_requirements.html

3) Check java home

4) Check datanodes best practice documentation.
    
  a) OS Drives - Standard configurations should apply for OS Drives.Two drives are used for OS, configured with RAID1 mirroring.
  
  b) Swapiness - Turn off the Swapiness on the datanodes.
  
  c) IO Scheduler Tuning - Enable deadline scheduler. In /boot/grub.conf, append “elevator=deadline” to the kernel line.  
```
  Example:
  default=0
  timeout=5
  splashimage=(hd0,0)/grub/splash.xpm.gz
  hiddenmenu
  title Red Hat Enterprise Linux Server (2.6.18-238.el5)
    root (hd0,0)
    kernel /vmlinuz-2.6.18-238.el5 ro root=/dev/VolGroup00/LogVol00 rhgb 
  quiet elevator=deadline
    initrd /initrd-2.6.18-238.el5.img
```
  d) File system - On the HDFS drives, XFS or EXT4 are the best options.  Do NOT use these options on normal OS drives – stick with the OS defaults.
  
  1. EXT4: EXT4 has a handy configuration file that can be preconfigured with the settings we want to set.

/etc/mke2fs.conf:
```
## add hadoop declaration to [fs_types] section
hadoop = {
  features = has_journal,extent,huge_file,flex_bg,\
             uninit_bg,dir_nlink,extra_isize
  inode_ratio = 131072
  reserved_ratio = 0
  default_mntopts = acl,user_xaddr
}
```

With this configured, you can format with:

```# e2mkfs –t ext4 –T hadoop /dev/partition```

Mount options for ext4:

```
inode_readahead_blks=128,data=writeback,noatime,nodev,nobarrier
```
  2. XFS - XFS doesn’t have a handy config file for format options.
    ```
    # mkfs.xfs -f -l size=128m,lazy-count=1 -d su=512k,sw=6 -r extsize=256k /dev/sdXX
    ```
    
    Mount options for XFS:
    ```
    allocsize=128k,noatime,nobarrier,inode64,nodev
    ```


5) Collect the information:

	a) FQDN and IPs

	b) Data directories
    /var/log/hadoop - For Log Directories
    /hadoop - data directories
    
	c) Java home path

6) Get the repos into all the hosts including ambari server.
http://docs.hortonworks.com/HDPDocuments/Ambari-2.2.0.0/bk_Installing_HDP_AMB/content/_download_the_ambari_repo_lnx7.html

7) Install ambari-server

8) Install Ambari gents on all hosts
http://docs.hortonworks.com/HDPDocuments/Ambari-2.2.0.0/bk_ambari_reference_guide/content/ch_amb_ref_installing_ambari_agents_manually.html

9) Configure the database entities

  a) Using Ambari with MySQL
	http://docs.hortonworks.com/HDPDocuments/Ambari-2.2.0.0/bk_ambari_reference_guide/content/_using_ambari_with_mysql.html

  b) Using Hive with MySQL *(Make sure of the ambari-server setup for the --jdbc-db)*
	http://docs.hortonworks.com/HDPDocuments/Ambari-2.2.0.0/bk_ambari_reference_guide/content/_using_hive_with_mysql.html

  c) Using oozie with MySQL *(Make sure of the ambari-server setup for the --jdbc-db)*
	http://docs.hortonworks.com/HDPDocuments/Ambari-2.2.0.0/bk_ambari_reference_guide/content/_using_oozie_with_mysql.html

10) Setup Ambari server

11) Start Ambari server

12) Add one master host and try to install zookeeper and make sure its succesfull

13) Then add the other hosts and add the services.

14) Add the Ambari Views on the Ambari Server(main)

15) Smoke tests

14) Standup Standalone Ambari Servers on the 2 edge nodes.

http://docs.hortonworks.com/HDPDocuments/Ambari-2.2.0.0/bk_ambari_views_guide/content/ch_using_ambari_views.html

**AD Integration:**

1) Hadoop Group Mapping for LDAP - http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.4/bk_installing_manually_book/content/setting_up_hadoop_group_mappping_for_ldap.html






**Kerberizing:**

Install JCE - http://docs.hortonworks.com/HDPDocuments/Ambari-2.2.0.0/bk_Ambari_Security_Guide/content/_distribute_and_install_the_jce.html

Kerberise cluster

Kerberize AMbari server - http://docs.hortonworks.com/HDPDocuments/Ambari-2.1.2.0/bk_Ambari_Security_Guide/content/_optional_set_up_kerberos_for_ambari_server.html

Kerberize the Other Web Ui endpoints - http://docs.hortonworks.com/HDPDocuments/Ambari-2.1.2.0/bk_Ambari_Security_Guide/content/ch_enable_spnego_auth_for_hadoop.html


