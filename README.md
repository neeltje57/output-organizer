# output-organizer
Organizes all your script output
<p>Here you will find the files and the documentation of the Output Organizer (OO) scripts. OO helps you to organize your script output. You want to know how a certain script ran last time, last week or last month? OO makes it easy for you. It is based on how the mainframe environments work with output. On a mainframe it is always possible to check the output (sysout) of previous job runs.</p>
<h2>Introduction</h2>
<p>On a linux server, you often run a lot of scripts. To organize the output of those scripts, you can use oo. It saves the output, send a report when a script ends with a return code unequal zero and takes care of expired output. The output is saved in a directory with per user and per script a sub directory. Normally, you suppress a lot of messages or write them to /dev/null. With oo it makes sense to use as much as possible messages and echo commands. It will all be saved in a neat way in the output file. The more text you use the better the output will be readable. Because oo can inform you about jobs with an unwanted return code, you have to check your scripts to see if the exit exit code is well thought off.</p>
<h2>Features</h2>
<ul>
<li>saves script output organized per user and per script;</li>
<li>creates 4 output sections in each output file;</li>
<li>script meta data (run date, expiration date, parameters used etc.);</li>
<li>stderr output;</li>
<li>actual script ran;</li>
<li>stdout output;</li>
<li>reports, when requested, about the return code of the script by email;</li>
<li>sets the expiration date per user per script;</li>
<li>takes care of the expired output files and reports about it;</li>
<li>takes care of unwanted directories and reports about it;</li>
<li>...</li>
</ul>
<h2>Installation</h2>
<ol>
<li>Create the paths;</li>
<li>Download oo.zip, extract the files and copy them to the right directories;</li>
<li>Check, adjust the parameters in /etc/oo/oo.conf;</li>
<li>Schedule the housekeeping scripts.</li>
</ol>
<p><strong>Remark:</strong> oo together with the 3 housekeeping scripts need to know where to find the configuration settings and where to write the output to. If you don't agree with the defaults, you have to change this before the first run: If so, change the following in the scripts:</p>
<ul>
<li>At the top of the 4 scripts (oo, oocd, oohk and oorp), change the parameter oo_confpath="/etc/oo" to something more appropriate for you.</li>
<li>At the top of the configuration file /etc/oo/oo.conf change the parameter oo_logbasepath="/var/log/oo" to something more appropriate for you.</li>
<li>In the commands below, adjust the commands to fit your changes.</li>
</ul>
<h3>1 Create the Paths</h3>
<pre>mkdir -p /etc/oo/users.conf<br />chmod 1777 /etc/oo/users.conf<br />mkdir -p /var/log/oo<br />chmod 1777 /var/log/oo</pre>
<p>All the other files and directories will be created per user and per user-script by the oo-scripts.</p>
<h3>2. Download oo.zip, extract the files and copy them to the right directories</h3>
<pre>cd<br />mkdir temp&nbsp;&nbsp;&nbsp;&nbsp; # if already exists, create a different one<br />cd temp<br />wget <a href="https://github.com/neeltje57/output-organizer/archive/refs/heads/master.zip">https://github.com/neeltje57/output-organizer/archive/refs/heads/master.zip</a><br />unzip master.zip<br />mv oo oocd oohk oorp /usr/local/bin<br />mv oo.conf /etc/oo<br />cd<br />rm -rf temp</pre>
<h3>3. Check, adjust the parameters in /etc/oo/oo.conf</h3>
<pre>oo_delete_after_days_min=3<br />oo_delete_after_days_max=99<br />oo_logbasepath=/var/log/oo<br />oo_report_email=root</pre>
<ul>
<li><strong>oo_delete_after_days_min</strong> sets the system minimum amount of days the output will be saved.</li>
<li><strong>oo_delete_after_days_max</strong> sets the maximum amount of days the output will be saved.</li>
<li><strong>oo_logbasepath</strong> sets the path where the logfiles will be saved</li>
<li><strong>oo_report_email</strong> sets the email address where the housekeeping scripts will send the reports. If no mail command is setup on your system, emails will not be sent.</li>
</ul>
<h3>4. Schedule the housekeeping scripts</h3>
<p><strong>Remark:</strong> To easily browse the output files, you can use a browser you like. If you don't know what browser to use, I recommend <strong>lynx</strong>, a text only web browser which makes it very easy to browse your directories and view the output files. To install lynx:</p>
<pre>apt-get install lynx</pre>
<h4>4.1 oo</h4>
<p>oo is the main script. When you execute your own scripts prefixed with oo, oo takes care of the output. With oo you can use a maximum of 8 parms. That should be enough for most scripts. If you need more than 8 parms, you must execute your script without oo. Here is an example:</p>
<pre>/usr/local/bin/oo /path/to/your/script/script-to-execute parm1 parm2 ... parm8</pre>
<p>You can use the same syntax to schedule your scripts in cron (crontab -e):</p>
<pre>0 * * * * /usr/local/bin/oo /path/to/your/script/script-to-execute parm1i parm2 ... parm8</pre>
<h4>4.2 oocd</h4>
<p>oocd is a housekeeping script which checks for unwanted directories in /var/log/oo. The sub directories in /var/log/oo can only have the name of a valid user, but because the sticky bit is set (chmod 1777) users might create unwanted directories. oocd takes care of this by deleting them. You can schedule this script once a day in cron (crontab -e):</p>
<pre>1 0 * * * /usr/local/bin/oo /usr/local/bin/oocd</pre>
<h4>4.3 oohk</h4>
<p>oohk is a housekeeping script which checks all the output files in /var/log/oo/user/. If a file has the wrong format (maybe a user accidentally put it there or updated it wrongly) or the file is expired, it will be deleted. If requested, a detailed email can be send to the user or an overal email to the administrator. You can schedule this script once a day in cron (crontab -e):</p>
<pre>2 0 * * * /usr/local/bin/oo /usr/local/bin/oohk</pre>
<h4>4.4 oorp</h4>
<p>oorp is a housekeeping script and, when requested, will inform the individual users and/or the administrator with an overview of the scipts which ended unequal to zero. By default it checks Yesterday, but you can adjust that by a parameter. 0 is Today,1 is Yesterday,2 is the day before Yesterday and so on. You can schedule this script in cron once a day in cron (crontab -e):</p>
<pre>10 0 * * * /usr/local/bin/oo /usr/local/bin/oorp 1</pre>
<h2>To do list</h2>
<ul>
<li>
<div>oohk skips its own directory.</div>
</li>
<li>
<div>Let oo always create a user-script config file for every script automatically instead of manually by the user when needed.</div>
</li>
<li>
<div>Implement an immediate email warning, when requestedin user-script config file.</div>
</li>
<li>
<div>Gunzip the output files.</div>
</li>
<li>
<div>Give oo a version number together with an install script.</div>
</li>
<li>
<div>Create an installation document.</div>
</li>
<li>
<div>Let oo send an email immediately after the script has run with a return code unequal to 0, when requested.</div>
</li>
<li>
<div>Let oo signal a hanging script (set maximum run time or something like that.</div>
</li>
</ul>
