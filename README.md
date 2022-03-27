# output-organizer
Organizes all your script output
Here you will find the files and the documentation of the Output Organizer (OO) scripts. OO helps you to organize your script output. You want to know how a certain script ran last time, last week or last month? OO makes it easy for you. It is based on how the mainframe environments work with output. On a mainframe it is always possible to check the output (sysout) of previous job runs.
# Introduction
On a linux server, you often run a lot of scripts. To organize the output of those scripts, you can use oo. It saves the output, send a report when a script ends with a return code unequal zero and takes care of expired output. The output is saved in a directory with per user and per script a sub directory. Normally, you suppress a lot of messages or write them to /dev/null. With oo it makes sense to use as much as possible messages and echo commands. It will all be saved in a neat way in the output file. The more text you use the better the output will be readable. Because oo can inform you about jobs with an unwanted return code, you have to check your scripts to see if the exit exit code is well thought off.
# Features
* saves script output organized per user and per script;
* creates 4 output sections in each output file;
* script meta data (run date, expiration date, parameters used etc.);
* stderr output;
* actual script ran;
* stdout output;
* reports, when requested, about the return code of the script by email;
* sets the expiration date per user per script;
* takes care of the expired output files and reports about it;
* takes care of unwanted directories and reports about it;
* ...
# Installation
1. Create the paths;
2. Download oo.zip, extract the files and copy them to the right directories;
3. Check, adjust the parameters in /etc/oo/oo.conf;
4. Schedule the housekeeping scripts.
**Remark:** oo together with the 3 housekeeping scripts need to know where to find the configuration settings and where to write the output to. If you don't agree with the defaults, you have to change this before the first run: If so, change the following in the scripts:
* At the top of the 4 scripts (oo, oocd, oohk and oorp), change the parameter oo_confpath="/etc/oo" to something more appropriate for you.
* At the top of the configuration file /etc/oo/oo.conf change the parameter oo_logbasepath="/var/log/oo" to something more appropriate for you.
* In the commands below, adjust the commands to fit your changes.
## 1. Create the Paths
~~~~
  mkdir -p /etc/oo/users.conf
  chmod 1777 /etc/oo/users.conf
  mkdir -p /var/log/oo
  chmod 1777 /var/log/oo
~~~~
All the other files and directories will be created per user and per user-script by the oo-scripts.
## 2. Download oo.zip, extract the files and copy them to the right directories
~~~~
cd
mkdir temp      # if already exists, create a different one
cd temp
wget <a href="https://github.com/neeltje57/output-organizer/archive/refs/heads/master.zip">https://github.com/neeltje57/output-organizer/archive/refs/heads/master.zip</a>
unzip master.zip
mv oo oocd oohk oorp /usr/local/bin
mv oo.conf /etc/oo
cd
rm -rf temp
~~~~
## 3. Check, adjust the parameters in /etc/oo/oo.conf
~~~~
oo_delete_after_days_min=3
oo_delete_after_days_max=99
oo_logbasepath=/var/log/oo
oo_report_email=root
~~~~
oo_delete_after_days_min 
:  sets the system minimum amount of days the output will be saved.
oo_delete_after_days_max 
:  sets the maximum amount of days the output will be saved.
oo_logbasepath
:  sets the path where the logfiles will be saved
oo_report_email
:  sets the email address where the housekeeping scripts will send the reports. If no mail command is setup on your system, emails will not be sent.
## 4. Schedule the housekeeping scripts
**Remark:** To easily browse the output files, you can use a browser you like. If you don't know what browser to use, I recommend **lynx**, a text only web browser which makes it very easy to browse your directories and view the output files. To install lynx:
> apt-get install lynx
### 4.1 oo
oo is the main script. When you execute your own scripts prefixed with oo, oo takes care of the output. With oo you can use a maximum of 8 parms. That should be enough for most scripts. If you need more than 8 parms, you must execute your script without oo. Here is an example:
> /usr/local/bin/oo /path/to/your/script/script-to-execute parm1 parm2 ... parm8

You can use the same syntax to schedule your scripts in cron (crontab -e):

> 0 * * * * /usr/local/bin/oo /path/to/your/script/script-to-execute parm1i parm2 ... parm8
### 4.2 oocd
oocd is a housekeeping script which checks for unwanted directories in /var/log/oo. The sub directories in /var/log/oo can only have the name of a valid user, but because the sticky bit is set (chmod 1777) users might create unwanted directories. oocd takes care of this by deleting them. You can schedule this script once a day in cron (crontab -e):
> 1 0 * * * /usr/local/bin/oo /usr/local/bin/oocd
### 4.3 oohk
oohk is a housekeeping script which checks all the output files in /var/log/oo/user/. If a file has the wrong format (maybe a user accidentally put it there or updated it wrongly) or the file is expired, it will be deleted. If requested, a detailed email can be send to the user or an overal email to the administrator. You can schedule this script once a day in cron (crontab -e):
> 2 0 * * * /usr/local/bin/oo /usr/local/bin/oohk
### 4.4 oorp
oorp is a housekeeping script and, when requested, will inform the individual users and/or the administrator with an overview of the scipts which ended unequal to zero. By default it checks Yesterday, but you can adjust that by a parameter. 0 is Today,1 is Yesterday,2 is the day before Yesterday and so on. You can schedule this script in cron once a day in cron (crontab -e):
> 10 0 * * * /usr/local/bin/oo /usr/local/bin/oorp 1
# To do list
* oohk skips its own directory.
* Let oo always create a user-script config file for every script automatically instead of manually by the user when needed.
* Implement an immediate email warning, when requestedin user-script config file.</div>
* Gunzip the output files.
* Give oo a version number together with an install script.
* Create an installation document.
* Let oo send an email immediately after the script has run with a return code unequal to 0, when requested.
* Let oo signal a hanging script (set maximum run time or something like that.

