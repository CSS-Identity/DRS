# Microsoft CSS Authentication Scripts

This Data collection is for Authentication, Smart card and Credential provider scenarios.


======================= IMPORTANT NOTICE =======================

The authentication script is designed to collect information that will help Microsoft Customer Support Services (CSS) troubleshoot an issue you may be experiencing with Windows.
The collected data may contain Personally Identifiable Information (PII) and/or sensitive data, such as (but not limited to) IP addresses; PC names; and user names.

Once the tracing and data collection has completed, the script will save the data in a subdirectory from where this script is launched called "authlogs".
The "Authlogs" directory and subdirectories will contain data collected by the Microsoft CSS Authentication scripts.
This folder and its contents are not automatically sent to Microsoft.
You can send this folder and its contents to Microsoft CSS using a secure file transfer tool - Please discuss this with your support professional and also any concerns you may have.


========================= SCRIPT USAGE =========================

Create a directory on the device where the tracing is going to run - example c:\ms
copy start-auth.txt and stop-auth.txt to target and rename with .bat extensions
From an elevated command prompt (as admin), navigate to the c:\ms directory and run start-auth.bat to start the tracing

Once you have created the issue or reproduced the scenario, please run stop-auth.bat from the same location to stop the tracing and collect the required data.
Data is collected into a subdirectory of the directory from where this script is launched, called "authlogs".

Please note that the start-auth script prompts for a "Y" user input after the data collection information is displayed, to allow the script to run.


====================== SCRIPT USAGE NOTES ======================

Script and data collection:

The script version number and start/stop data collection times are written to the script-info.txt in the authlogs directory.


Network trace:

Network tracing is enabled by default in the scripts. If you do not want a network trace, please REM it out.
The default buffer size for network trace collection is 1024 MB, if you need a larger buffer then please edit the maxsize=xxxx value in the start-auth bat file, where the "netsh trace" command is being invoked.

Please note that for Client OS types the "InternetClient" scenario will be used to collect the network capture information, which also collects tcp, afd, webio, winhttp and wininet logging in the netmon.etl file.


Crypt/Ncrypt/Dpapi:

By default, the Crypt, Ncrypt and Dpapi etl tracing is only enabled on client devices.
It is not enabled on server or DC's.
If you want to enable it on servers (ServerNT) or DC's (LanmanNT)  then you will need to remove the relevant REM statements from the following lines in the start-auth and stop-auth bat files.
Note: Please be aware that it should not be enabled on servers or DC's for long running data collection.

	From start-auth.bat
	REM if %%i equ ServerNT logman start CryptNCryptDpapi -pf %_CRYPT_DPAPI_TRACES_TMP% -o %_LOG_DIR%\CryptNcryptDpapi.etl -ets
	REM if %%i equ LanmanNT logman start CryptNCryptDpapi -pf %_CRYPT_DPAPI_TRACES_TMP% -o %_LOG_DIR%\CryptNcryptDpapi.etl -ets

	From Stop-auth.bat
	REM if %%i equ ServerNT logman.exe stop CryptNCryptDpapi -ets
	REM if %%i equ LanmanNT logman.exe stop CryptNCryptDpapi -ets


ETL log size:

The logman collections apart from SSL are NOT limited in size.
If you wish to collect a long running data collection, please make the customer aware to monitor disk space usage.

If you know exactly which providers you are interested in for a long running collection (i.e. Kerberos delegation issue on middle tier server), then you can disable some of the providers that are not relevant.
Note: Disabling providers in the collection can be achieved by adding a REM statement to the beginning of the line for the start of the collection (start-auth) and stop of the collection (stop-auth)

The SSL data collection is limited to 1024 MB's.
If you feel you need a larger size, then please edit the "max" value in the following line in the start-auth bat file

	logman start SSL -pf %_SSL_TRACES_TMP% -o %_LOG_DIR%\SSL.etl -ets -max 1024


Certificate Enrolment logging:

By default, certificate enrolment verbose debug logging is only enabled on client devices.
It is not enabled on server or DC's.
If you want to enable it on servers (ServerNT) or DC's (LanmanNT)  then you will need to remove the relevant REM statements from the following lines in the start-auth

	REM if %%i equ ServerNT certutil -setreg -f Enroll\Debug 0xffffffe3 > NUL
	REM if %%i equ LanmanNT certutil -setreg -f Enroll\Debug 0xffffffe3 > NUL


Schannel logging:

The schannel logging will be captured in the SSL.etl file.
If you wish to see additional logging into the System event log, then please remove the REM statement from the following lines;

	From start-auth.bat
	REM reg add HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL /v EventLogging /t REG_DWORD /d 7 /f > NUL 2>&1

	From Stop-auth.bat
	REM reg add HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL /v EventLogging /t REG_DWORD /d 1 /f > NUL 2>&1

Note: The above logging is very verbose.
	

SystemInfo collection:

By default, the systeminfo collection is currently not enabled. 
It was disabled due to delays in collecting the info when the scripts are being stopped.
If you wish to collect the systeminfo output, please remove the REM from the following line in the stop-auth.bat file or collect it as a separate operation.

	REM Systeminfo > %_LOG_DIR%\SystemInfo.txt


Security Event log:

The security event log is not collected in these scripts.
If you wish to collect it, please get permission from the customer and collect it manaully at the end of the data collection.
Note: Please do not add the collection of the security event log to the scripts.


PSR Collection:

For privacy reasons, the authentication scripts do not collect PSR data.
If you wish to collect it, please get permission from the customer and collect it manually in parallel with the auth script collection.

PSR can be started and stopped using the following commands;
Note: Please do not add these to the auth script for collection - run them separately.

	psr /start /output psr.zip /maxsc 999 /gui 0
	psr /stop

