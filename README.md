# log_download Script Setup

** Activation of Log File in MyConsole **
- Activate Log on within the MyConsole
- Activate Logs on individual site via Manage Interface on Log-Setup Screen
- Test for the existence of logs “curl -k -u API_ID:API_KEY https://{Grab from Log-Setup Screen}/logs.index”

** Downloading and Executing Logger Service for Windows **
1. Download python for Windows from : https://www.python.org/
2. Latest release can be down loaded via this link : https://www.python.org/downloads/release/python-2711/
3. Pick the Windows web-based installer (32bit) to get the latest release with changes.
4. Add “C:\Python27” and “C:\Python27\Scripts” to Execution Path
5. Goto : https://sites.google.com/site/pydatalog/python/pip-for-windows
	a. Download “pip-win_1.7.exe”
	b. Download “Visual C++ 2008 Express SP1”
6. Run from CMD “pip install --egg M2CryptoWin32”
7. Run from CMD “pip install loggerglue”
8. Run from CMD “pip install pycrypto”
9. Download zip from https://github.com/Incapsula/logs-downloader
10. Extract and follow instructions on running https://github.com/Incapsula/logs-downloader

** Downloading and Executing Logger Service for CentOS **
1. Install python using “yum install python”
2. Install python using “yum install python-devel”
3. Install python using “yum install openssl-devel”
4. Install python using “yum install python-setuptools”
5. Install Python pip “easy_install pip”
6. Run “pip install M2Crypto”
7. Run “pip install loggerglue”
8. Run “pip install pycrypto”
9. Goto the intended Log-Downloader Root Directory
10. Run “git clone https://github.com/Incapsula/logs-downloader”

** Preparations for using the script: **
 - Create a local folder for holding the script configuration, this will be referred as **path_to_config_folder**
 - Create a subfolder named **keys** under the **path_to_config_folder** folder 
 - In the keys subfolder, create a subfolder with a single digit name. This digit should specify whether this is the first encryption key uploaded (1), the second (2) or so on
 - Inside that folder, save the private key with the name **Private.key**
 - For example, **/etc/incapsula/logs/config/keys/1/Private.key**

**`python LogsDownloader.py -c path_to_config_folder -l path_to_system_logs_folder -v system_logs_level`**
 - The **-c** and **-l** and **-v** parameters are optional
 - The default value for **path_to_config_folder** is **/etc/incapsula/logs/config**
 - The default value for **path_to_system_logs_folder** is **/var/log/incapsula/logsDownloader/**
 - The default value for **system_logs_level** is **info**
 - The **path_to_config_folder** is the folder where the settings file (**Settings.Config**) is stored
 - The **path_to_system_logs_folder** is the folder where the script output log file is stored (this does not refer to your Incapsula logs)
 - The **system_logs_level** configuration parameter holds the logging level for the script output log. The supported levels are **info**, **debug** and **error**
 - You can run **`LogsDownloader.py -h`** to get help

** Execute Logger Service **
1. Python path to/log-downloader
2. Complete Configuration Files and ensure that process director is defined (See Sample Configuration Script sec)
3. Arguments (-c path_to_config_folder -l path_to_system_logs_folder -v system_logs_level) *see https://github.com/Incapsula/logs-downloader
4. Execute Test

** User Task Scheduler to Execute Logger Service in Background for Windows **
1. Create a Incapsula Folder
2. Create a Basic Service
	a. Name : Logger Service
	b. Trigger : When the computer starts
	c. Action : Start a program
		i. Python (path to log-downloader)
		ii. Complete Configuration Files and ensure that process director is defined (See Sample Configuration Script sec)
		ii. Arguments (-c path_to_config_folder -l path_to_system_logs_folder -v system_logs_level) *see https://github.com/Incapsula/logs-downloader
		iii. Finish
12. To test server Run from Scheduler

** Running the script as a service on Debian systems ** 
 - You can run the script as a service on Linux systems by using the configuration file - **linux_service_configuration/incapsulaLogs.conf**
 -  You should modify the following parameters in the configuration file according to your environment: 
	 1. **`$USER$`** - The user that will execute the script
	 2. **`$GROUP$`** - The group name that will execute the script
	 3. **`$PYTHON_SCRIPT$`** - The path to the **`LogsDownloader.py`** file, followed by the parameters for execution of the script
 - On your system, copy the **incapsulaLogs.conf** file and place it under the **/etc/init/** directory
 - Run **`sudo initctl reload-configuration`** 
 - Run **`sudo ln -s /etc/init/incapsulaLogs.conf /etc/init.d/incapsulaLogs`**
 - Execute **`sudo service incapsulaLogs start`** 
 - You can use **`start/stop/status`** as any other Linux service

** Sample Configuration Script (For Reference) **
[SETTINGS]
APIID=xxxxx
APIKEY=xxxx-xxxx-xxxx-xxxx-xxxx
PROCESS_DIR=/mnt/sdb/opt/logs-downloader/process
BASEURL=https://logs1.incapsula.com/415_126395/
USEPROXY=NO
PROXYSERVER=
SAVE_LOCALLY=YES
SYSLOG_ENABLE=NO
SYSLOG_ADDRESS=127.0.0.1
SYSLOG_PORT=514
SAVE_NEWDATEFORMAT=NO

APIID, APIKEY and BASEURL are retrieved from the log setup page on Incapsula my console site.
PROCESS_DIR is the directory that will hold the log files.
USEPROXY and PROXYSERVER are part of the network setup that allow you to transition proxy for making the connection to the cloud load down server
SAVE_LOCALLY = keeps log files once processed or not.
SYSLOG_ENABLE, SYSLOG_ADDRESS, and SYSLOG_PORT are defined to push logs to a syslog server via a TCP connection
SAVE_NEWDATEFORMAT changed the final file name of the log file when saved locally. 
Options:
- NO : Leaves default format in place
- YES-MIN : Format for final save is YYYYMMDDHHMM.log
- YES-SEC :  Format for final save is YYYYMMDDHHMMSS.log