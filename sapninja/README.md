# Installation of the SAP Ninja Monitor Application
This Software use saplink to install the code in an SAP System. After that you will pack it into a software transport to install it in your landscape.

## Create the ZSAPNINJA_MONITOR development package
-	Log into your development system
-	Start transaction SE80
-	In the Repository Browser, select the type Package (if it isn’t already selected) from the drop-down.
-	Type in the package name ZNINJA_MONITOR and press Enter
-	A pop-up box will appear informing your that the package does not exist.  Press Yes to create the package.  
```
Enter:
Short description	= SAP Ninja Monitor Application
Application Component	= BC
```
- Press Enter
- You will be prompted for a transport request.  Press the Create button (F8) to create a new request.  
> It is important to create a different transport request to the one made for the SAP link nugget application, because this is the transport request that you will re-use to deploy to your downstream environments.  Once you have your own transport request, it is quicker and easier to use it than the nuggets. 
```
Enter:
Short description	 = SAP Ninja Monitor Application - Initial Installation
```
-	Press Save
-	Press Enter

## Import the SAP Ninja Application
-	Start transaction SA38 and execute the program ZSAPLINK
-	Make sure the Nugget tab is open 
-	Select the Import Nugget radio button
```
Package Name		= ZNINJA_MONITOR
Nugget File Name	= <find and open NUGG_SAPNINJA_[version].nugg>
Overwrite Originals	= <ticked>
```
-	Press Execute
-	At the next prompt, assign the change to the transport request you created for ZNINJA_MONITOR package earlier.  You will need to keep pressing Enter here for each object that is imported (well over 20 … just keep your finger on the Enter key).
-	Press Enter
-	You should see something like this on your screen:
```ABAP
Installed: CLAS - ZNINJA_CL_MON
Installed: CLAS - ZNINJA_CL_MON_MAINT_01
Installed: CLAS - ZNINJA_CL_MON_SM04_01
Installed: CLAS - ZNINJA_CL_MON_SM50_01
Installed: CLAS - ZNINJA_CL_MON_SM50_02
Installed: CLAS - ZNINJA_CL_MON_ST03N_01
…
…
…
Installed: VIEW - ZNINJA_ST03N_24V
Installed: VIEW - ZNINJA_ST03N_25V
Installed: VIEW - ZNINJA_ST03N_26V
```
## Activate the SAP Ninja Monitor Application
-	Start transaction SE80
-	In the Repository Browser, select the type Package (if it isn’t already selected) from the drop-down.
-	Type in the package name ZNINJA_MONITOR and press Enter
-	Right-click on the ZNINJA_MONITOR package name at the top of the tree and select Other Functions -> Rebuild Object List
-	Find one of the newly imported Dictionary Objects, like the database table ZNINJA_BGD_QUEUE, right-click on it and choose Activate.
-	Press Select All (F9)
-	Press Enter … several times to assign the change to the transport request you created for the ZNINJA_MONITOR package earlier.  You will need to keep pressing Enter here for each object that is imported (well over 100 … just keep your finger on the Enter key).
-	Finally you will get a prompt saying there were activation warnings.  Press No to viewing the log.

-	Still in SE80 for package ZNINJA_MONITOR, find one of the newly imported Programs, like ZNINJA_CRON_BGD_TASK, right-click on it and choose Activate.
-	At the object selection pop-up, press the Whole worklist  (Shift+F6) button
-	Press Select All (F9)
-	Press Enter 
-	When prompted for a Main Program, double-click on any one of them shown.  It will compile all the programs and classes.

 
## Special Step to set up the ZNINJA_MONITOR function group
There is a bug with the SAP link nuggets that causes it not to import function groups properly.  The following additional steps are required to work around this problem.

-	Start transaction SA38 and execute program ZSAPLINK
-	Make sure the Slinkee tab is open (not the Nugget one)
-	Select the Import Slinkee radio button
```
Slinkee File Name	= <find and open FUGR_ZNINJA_MONITOR.slnk>
Package Name		= ZNINJA_MONITOR
Overwrite Originals	= <ticked>
```
-	Press Execute
-	At the next prompt, assign the change to the transport request you created for ZNINJA_MONITOR earlier.
-	Press Enter
-	You should see something like this on your screen:
```
Installed: FUGR - ZNINJA_MONITOR
```

-	Start transaction SE37
-	Enter function module name as ZNINJA_DAEMON
-	Click on the Change button
-	From the menu, choose Utilities -> More Utilities -> Upload/Download --> Upload
-	Select the file ZNINJA_DAEMON.txt
-	Once the code imports, click the Activate (Ctrl+F3) button
-	Click Select All (F9)
-	Press Enter

## Reassign the package of SAP Ninja transaction codes
Another bug of the SAP link installer is that it assigns the transaction codes it creates as a local object and not as part of the proper development package.
-	Start transaction SE80
-	In the Repository Browser, select the type Local Objects
-	Find the folder containing transactions, select the first one, right-click on it and choose Other Functions Change Package Assignment.
-	Enter the new package as ZNINJA_MONITOR
-	Press Enter
-	At the next prompt, assign the change to the transport request you created for ZSAPLINK earlier.
-	Repeat the steps above for the other ZNINJA* transaction codes

## Generate Table Maintenance Programs for configuration tables
Nuggets do not import table maintenance screens.  This is unfortunate, but necessary, because it could potentially overwrite one of your own screen numbers.  We need to do this step manually but, luckily this very easy.

-	Start transaction SE11
-	Enter the table name as ZNINJA_CONFIG
-	Click the Change button
-	From the menu, choose Utilities --> Table Maintenance Generator
-	Enter 
```
Authorization Group	= &NC&
Function Group		= ZNINJA_MONITOR
```
-	Click on the Find Screen Numbers (Shift+F7) button 
-	Click the Propose screen numbers radio button
-	Press Enter
-	Select maintenance type as Two Step
-	Click the Create (F6) button
-	Select ZNINJA_MONITOR as the package
-	Press Enter

```
-	Repeat the steps above for the following tables
ZNINJA_CONFIG
ZNINJA_REGISTRY
ZNINJA_CRONTAB
ZNINJA_CRONTABX
ZNINJA_INC_REGEX
ZNINJA_EXC_REGEX
```

The good news is that you are now able to use the transport request that you created at the start of this document for import into other systems in your landscape.  You no longer need the SAP link nuggets, until the next time you upgrade SAP Ninja or import a bug fix.  Do not release the transport until you have completed the installation as per the steps in the next document.


THE SAP NINJA MONITOR APPLICATION IS NOW COMPLETELY INSTALLED (TECHNICALLY)
