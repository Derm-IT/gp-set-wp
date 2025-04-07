# Group Policy: Customized Wallpapers for Admins and Users in Azure AD

## Overview

This project demonstrates how to configure Group Policy in an Active Directory environment to assign distinct desktop wallpapers to Admin and User accounts, while ensuring the Domain Controller (DC) remains unaffected. This setup aims to visually differentiate user roles and personalize desktop environments without impacting server infrastructure.

## Prerequisites / Technologies Used

* Microsoft Azure
    * Configured Azure Virtual Machines (Windows Server 2022 (Domain Controller), Windows 10/11 (DNS set to DC)
    * Active Directory Domain Services setup (AD DS)
* Organizational Units (OUs): _ADMINS, _USERS, _CLIENTS
* Group Policy Management Console (GPMC)
* Group Policy Objects (GPOs)
* Basic Network File Sharing Knowledge (SMB)
* Active Directory Users and Computers (ADUC)
* WMI Filtering
* Powershell/Command Prompt

* GUIDE to set up VMs, Active Directory, Domain Controller, Domain Admins/Users is [here](https://github.com/Derm-IT/domain-controller-azure-setup-ad) and [configure here](https://github.com/Derm-IT/deploy-configure-ad)

## Steps

### 1. Organizing Shared Folders on the Network

* Connect to your Domain Contoller(e.g DC-1) as Domain Admin via Remote Desktop
* Open File Explorer and go to your C Drive and create two folders, `AdminShared` and `UserShared`
* ![image](https://github.com/user-attachments/assets/67e9b6b9-0f82-45e2-a391-153cdb5ef273)
* Right click on `AdminShared` -> Properties -> `Sharing` -> `Share` and enter Domain Admins and click `Add` and `Share`
* ![image](https://github.com/user-attachments/assets/b5e471c8-c69e-4980-8156-8b6d6850b3dd)
* Note the network path for the folder, AdminShared (//DC-1/AdminShared)
* ![image](https://github.com/user-attachments/assets/f8f1024e-a6dd-4f85-a778-514bd9ee0d0b)
* Now navigate to Share for `UserShared` and enter Domain Users and click `Add` and `Share`
* ![image](https://github.com/user-attachments/assets/6c50c99b-0db2-4bce-bbd3-5cb1918efae7)
* Note the network path, UserShared (//DC-1/UserShared)
* ![image](https://github.com/user-attachments/assets/3fb63efc-2169-4b7c-9056-e6bd1770e2df)
* Now each folder is shared to the appropriate group, we will now find wallpapers that we want for Admins and Users and add them to the folder

### 2. Preparing Wallpaper Files

* Get two wallpapers, one for Admins and one for Users
* Download them and name them (e.g admin-wallpaper, user-wallpaper)
* In File Explorer move them to their folders
* ![image](https://github.com/user-attachments/assets/1817f690-6175-4e74-97f6-4c8274707fe8)
* ![image](https://github.com/user-attachments/assets/4cfc7d09-b776-483e-aa9b-348d99d58e1a)
* ![image](https://github.com/user-attachments/assets/fe300b66-fba2-4bdb-8382-470f3f0598b5)

### 3. Creating the Group Policy Objects (GPOs)

* On DC-1, open Group Policy Management (`run` gpmc.msc).
* **Create Admin Wallpaper GPO:**
    * Right-click the `_ADMINS` OU → click `Create a GPO in this domain, and Link it here`
    * ![image](https://github.com/user-attachments/assets/a3ce3820-2998-4e08-a587-c2629f01441b)
    * Name it "AdminWallpaperGPO" and then right click and click `Edit`
    * ![image](https://github.com/user-attachments/assets/a7fe03d6-3523-4f5e-bc45-13e21317ca1d)
    * Navigate to: `User Configuration → Policies → Administrative Templates → Desktop → Desktop`.
    * ![image](https://github.com/user-attachments/assets/a7e351e0-998b-4329-b02b-74c03a85c82b)
    * Double-click "Desktop Wallpaper," set it to "Enabled."
    * Set the wallpaper path to: `\\DC-1\AdminShared\admin-wallpaper.jpg`.
    * Set "Style" to the desired style: "Fill."
    * Click `Apply` and `Ok`
    * ![image](https://github.com/user-attachments/assets/11e08f28-b4ea-4119-be66-65616374f3c3)
    * Double check the policy by going to Group Policy Management and under _ADMINS click the new policy and navigate to Settings and scroll down to the policy
    * ![image](https://github.com/user-attachments/assets/c1d26eb0-ac25-4b4e-90c9-b481f6de2d74)
* **Create User Wallpaper GPO:**
    * Right-click the `_USERS` OU → click `Create a GPO in this domain, and Link it here`
    * ![image](https://github.com/user-attachments/assets/033ea6c6-8ee0-4ce9-a95d-7dd73b43203e)
    * Name it "UserWallpaperGPO" and then right click and click `Edit`
    * ![image](https://github.com/user-attachments/assets/71b56c6e-35b7-4626-a4c2-df542a2949f1)
    * Navigate to: `User Configuration → Policies → Administrative Templates → Desktop → Desktop`.
    * Double-click "Desktop Wallpaper," set it to "Enabled."
    * Set the wallpaper path to: `\\DC-1\UserShared\user-wallpaper.jpg`.
    * Set "Style" to the desired style: "Fill."
    * Click `Apply` and `Ok`
    * ![image](https://github.com/user-attachments/assets/b21511df-533e-49d5-85f6-c7aa1723472f)
    * Double check the policy by going to Group Policy Management and under _USERS click the new policy and navigate to Settings and scroll down to the policy
    * ![image](https://github.com/user-attachments/assets/3b860ee9-0f66-4f51-8fe5-7d53c4c030ed)

### 4. Forcing and Testing the Policy

* From each client machine and the domain controller, log in with a domain user 
* Run `gpupdate /force` in Command Prompt to refresh Group Policy.
* ![image](https://github.com/user-attachments/assets/71ffe5dd-603e-40e4-9c70-11a3c74bec99)
* After logging out and back in, the wallpaper should be applied according to the GPO.
* Log into a non Domain Controller(Client-1) as a Domain User and observe the wallpaper change
* Can right click the desktop and click personalize and see you can't change the wallpaper
* ![image](https://github.com/user-attachments/assets/041aaece-12a1-4369-974c-f4349cd8a908)
* Log out and log back in a Domain Admin to observe the admin-wallpaper
* ![image](https://github.com/user-attachments/assets/e60b3f09-5eed-4bf0-a103-7cbb39dc07f4)
* Log in to the Domain Controller (DC-1) and see the wallpaper policy applied as well
* ![image](https://github.com/user-attachments/assets/2faf777a-deef-4ac0-8bde-45a5e0bc48f0)

### 5. Excluding Domain Controller from Policy Application with WMI Filtering

* In **Group Policy Management** on DC-1, go to WMI Filters uinder the domain and right click and click `New`
* **Name** the filter (e.g., **No Wallpaper on DC**).
* Click **Add**, and in the **Query** box, type the following query:
    ```sql
    Select * from Win32_ComputerSystem where NOT Name = "DC-1"
    ```
    * This query checks that the computer is **not** a Domain Controller.
* **Click OK** and **Save** the WMI filter to the policy.
* ![image](https://github.com/user-attachments/assets/6793e0e3-67bd-4200-b902-97dd6307741e)
* Link the WMI filter to the **AdminWallpaperGPO**
    * Select the WMI Filter
    * Right click in the box `GPOs that use this WMI filer` and click `Add`
    * Select the `AdminWallpaperGPO` as only Admins can log into the DC and click Ok
    * ![image](https://github.com/user-attachments/assets/04686dc6-dcd0-444e-b102-04b347430e81)
    * ![image](https://github.com/user-attachments/assets/ecc21c7a-4e2d-41cb-a1d7-5b4f18049cf2)
 * Log out of DC-1 and log back in to have the filer take effect
 * ![image](https://github.com/user-attachments/assets/3fd8e889-8e40-428e-9282-54b715d0de3a)

## Takeaways

* Successfully applied distinct desktop wallpapers to Admin and User accounts using Group Policy.
* Effectively isolated Group Policy application from the Domain Controller by utilizing WMI filters.
* Configured network file sharing with appropriate permissions for wallpaper distribution.
* Demonstrated OU-based GPO targeting for organized and scalable domain management.
