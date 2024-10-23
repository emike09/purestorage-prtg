
#Pure Storage
## PRTG Sensor

Author:     lloydy@purestorage.com
Note:       The original author, listed above, is no longer employed by Pure and that e-mail address is invalid. 
Note:       I'm not sure what version of 6.5.x this script stopped working. I was on 6.3.15 when this was working, but upgrading to 6.5.8 caused it to stop functioning. 
Note:       The fix involves using a new SSL certificate (self-signed or from your local CA), and installing the certificate on your PRTG server/probe. 
Note:       Line 835 of the script has been commented out in this fork. 
Version:    1.11


This sensor allows selective monitoring of **FlashArray** components as required. 

---

Supported components include:

- Array Capacity and consumption details
- Array Performance details
- Hardware components health status
- Drive health status
- Volume performance details
- Host group performance details (coming soon)

--- 
Each of the scope options has its own sensor and then multiple channels. This is required due to the supported sensor maximum of 50 channels per sensor (not enforced). Currently the inclusion of the **volume** and **hostgroup** sensors is being asssessed as they have sensor lifecylce considerations due to the dynamic additon, remova, and instance totals that these elements entail.

---

##Installation Instructions


**Written for Purity REST API 1.6**

.INSTRUCTIONS
1) Copy the script file into the PRTG Custom EXEXML sensor directory C:\Program Files (x86)\PRTG Network Monitor\Custom Sensors\EXEXML
        - Get-PureFA-Sensor.ps1 (PowerShell Sensor Script)
2) Copy the Lookup files into the PRTG lookup file directory C:\Program Files (x86)\PRTG Network Monitor\lookups\custom
        - prtg.standardlookups.purestorage.drivestatus.ovl (Drive status value lookup file)
        - prtg.standardlookups.purestorage.hardwarestatus.ovl (Hardware status value lookup file)
3) Restart the 'PRTG Core Server Service' windows service to load the custom lookup files
3) Create Sensor Custom EXE/ Script Advanced Sensor for each sensor you wish to monitor (refer Scope) and give it a meaniful name
4) Set Parameters for sensor
    - (ArrayAddress) Target Array management DNS name or IP address
    - (UserName) and (Password) or (APIKey) to gain access 
    - (Scope) to set which element to monitor with Sensor
    - (Item) used to define grasnular items monitored suchj as volume name
    - (PRTGHostURL) URL for PRTG web site eg https://prtg.local.int:8443
    - (PRTGUser) user account for PRTG access
    - (PRTGPassword) password of PRTG user account
    - (DeviceID) set as PRTG variable %DeviceID
    - (SensorID) set as PRTG varibale %SensorID

   e.g. -arrayaddress '%host' -scope 'hardware' -apikey '3bdf3b60-f0c0-fa8a-83c1-b794ba8f562c'
5) For the monitoring of individual volumes a sensor is created for each volume. As such the scope option 'volumemanage' is required to maintain these
   sensors. THis then copies itself to a new sensor assigned to the holding device and updates the paramdeters accordingly. It also removes sensors of any 
   volume that has been deleted 
    - Create 'VolumeManage' sensor with the additional parameters -prtghosturl -prtguser -prtgpassword -DeviceID -SensorID
6) Using the GUI (Settings - SSL Certificate), or CLI, Construct a new Certificate Signing Request. Make sure you use the exact FQDN from the certificate CN to access the pure storage array
7) Import a valid SSL certificate to the pure array or create a self signed certificate and import it to trusted root CAs on the PRTG server/probe. 


---

##Operational Scopes

The scope defines the details to be monitored from the array
Supported Scope Values:

-   Capacity
-   Performance      
-   Hardware
-   Drive
-   VolumeManage
-   Volume (Sensors dynamically created through VolumeManage)
-   HostGroup (not currently supported)
