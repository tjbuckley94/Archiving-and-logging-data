# Archiving-and-logging-data
<h3>Step 1: Create, Extract, Compress, and Manage tar Backup Archives</h3>
Command to extract the TarDocs.tar archive to the current directory:

 &emsp;&emsp;**$tar xvvf TarDocs.tar**


Command to create the Javaless_Doc.tar archive from the TarDocs/ directory, while excluding the TarDocs/Documents/Java directory:

 &emsp;&emsp;**$tar cvvWf Javaless_Doc.tar --exclude “TarDocs/Documents/Java” TarDocs**


Command to ensure Java/ is not in the new Javaless_Docs.tar archive:

 &emsp;&emsp;**$tar tvvf Javaless_Doc.tar | grep Java**


<h3>Optional</h3>
 
Command to create an incremental archive called logs_backup.tar.gz with only changed files to snapshot.file for the /var/log directory:

 &emsp;&emsp;**$sudo tar --listed-incremental=snapshot.file -cvzf logs_backup/var/log**


Critical Analysis Question

Why wouldn't you use the options -x and -c at the same time with tar?
 &emsp;&emsp;<br>**-x is used to extract and write files for the archive </br>
 &emsp;&emsp;<br>-c makes the name of the directory where the tar files will be placed. If you were to use these at the same time with tar this would create a conflict with the command line and cancel each other out.** </br>


<h3>Step 2: Create, Manage, and Automate Cron Jobs</h3>
Cron job for backing up the /var/log/auth.log file:

 &emsp;&emsp;**$0 6 * * 3 tar cvvf /auth_backup.tgz /var/log/auth.log**



<h3>Step 3: Write Basic Bash Scripts</h3>
Brace expansion command to create the four subdirectories:

 &emsp;&emsp;**$sudo mkdir -p ~/backups/{freemem,diskuse,openlist,freedisk}**


Paste your system.sh script edits:

 &emsp;&emsp;<br>**#!/bin/bash</br>
 &emsp;&emsp;<br>Free -h > ~/backups/freemem/free_mem.txt</br>
 &emsp;&emsp;<br>Du -h > ~/backups/diskuse/disk_usage.txt</br>
 &emsp;&emsp;<br>Lsof > ~/backups/openlist/open_list.txt</br>
 &emsp;&emsp;<br>Df -h > ~/backups/freedisk/free_disk.txt**</br>


Command to make the system.sh script executable:

 &emsp;&emsp;**$chmod +x system.sh**



<h3>Optional</h3>

Commands to test the script and confirm its execution:

 &emsp;&emsp;<br>**$sudo ./system.sh</br>
 &emsp;&emsp;<br>$cat ~/backups/freemem/free_mem.txt</br>
 &emsp;&emsp;<br>$cat ~/backups/diskuse/disk_usage.txt</br>
 &emsp;&emsp;<br>$cat ~/backups/openlist/open_list.txt</br>
 &emsp;&emsp;<br>$cat ~/backups/freedisk/free_disk.txt**</br>




Command to copy system to system-wide cron directory:

 &emsp;&emsp;**$sudo cp system.sh /etc/cron.weekly**



<h3>Step 4. Manage Log File Sizes</h3>
 
 &emsp;&emsp;**$sudo nano /etc/logrotate.conf to edit the logrotate configuration file.**

Configure a log rotation scheme that backs up authentication messages to the /var/log/auth.log. 

Add your config file edits:

 &emsp;&emsp;<br>**/var/log/auth.log {</br>
 &emsp;&emsp;<br>Weekly</br>
 &emsp;&emsp;<br>Rotate 7</br>
 &emsp;&emsp;<br>Notifempty</br>
 &emsp;&emsp;<br>Delaycompress</br>
 &emsp;&emsp;<br>Missingok</br>
 &emsp;&emsp;}<br>**</br>



<h3>Optional Additional Challenge: Check for Policy and File Violations</h3>

Command to verify `auditd` is active:

 &emsp;&emsp;**$systemctl status auditd**


Command to set number of retained logs and maximum log file size:

 &emsp;&emsp;**$sudo nano /etc/audit/auditd.conf**


Add the edits made to the configuration file:

 &emsp;&emsp;<br>**max_log_file=35 </br>
 &emsp;&emsp;<br>num_logs=7**</br>


Command using auditd to set rules for /etc/shadow, /etc/passwd, and /var/log/auth.log:

 &emsp;&emsp;**$sudo nano /etc/audit/rules.d/audit.rules**

 
Add the edits made to the rules file below:

 &emsp;&emsp;<br>**-w /etc/shadow -p rwa -k hashpass_audit</br>
 &emsp;&emsp;<br>-e /etc/passwd -p rwa -k userpass_audit</br>
 &emsp;&emsp;<br>-w /var/log/auth.log -p rwa -k authlog_audit**</br>


Command to restart auditd:

 &emsp;&emsp;**$sudo systemctl restart auditd**


Command to list all auditd rules:

 &emsp;&emsp;**$sudo Auditctl -l**


Command to produce an audit report:

 &emsp;&emsp;**$sudo aureport -au**


Create a user with sudo useradd attacker and produce an audit report that lists account modifications:

 &emsp;&emsp;**$sudo aureport -m**


Command to use auditd to watch /var/log/cron:

 &emsp;&emsp;**$sudo auditctl -w /var/log/cron**


Command to verify auditd rules:

 &emsp;&emsp;**$sudo auditctl -l**


