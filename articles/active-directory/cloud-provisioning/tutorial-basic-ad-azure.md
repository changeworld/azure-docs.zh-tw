---
title: 教程 - 本地基本活動目錄和 Azure AD 環境。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 356a05d4d92f17ceb66ff0208153ec3eac736757
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793894"
---
# <a name="tutorial-basic-active-directory-environment"></a>教程：基本活動目錄環境

本教程將指導您創建基本的活動目錄環境。 

![建立](media/tutorial-single-forest/diagram1.png)

您可以使用本教程中創建的環境來測試混合標識方案的各個方面，並且將成為某些教程的先決條件。  如果您已有現有的活動目錄環境，則可以使用它作為替代。  此資訊是為我從無開始的個人提供的。

本教程包括
## <a name="prerequisites"></a>Prerequisites
下列是完成此教學課程的必要條件
- 已安裝 [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview) 的電腦。  建議您在 [Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) 或 [Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) 電腦上執行此作業。
- [外部網路介面卡](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network)用於讓虛擬機器與網際網路通訊。
- [Azure 訂閱](https://azure.microsoft.com/free)
- Windows Server 2016 複本
- [微軟 .NET 框架 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115)

> [!NOTE]
> 此教學課程使用 PowerShell 指令碼，可讓您在最快的時間內建立教學課程環境。  每個指令碼都使用在指令碼開頭處宣告的變數。  您可以且應該變更變數以反映您的環境。
>
>在安裝 Azure AD Connect 雲預配代理之前，使用的腳本創建了一般活動目錄環境。  它們在所有指令碼中都通用。
>
> 您可以在 GitHub 上的[這裡](https://github.com/billmath/tutorial-phs)取得本教學課程中使用的 PowerShell 指令碼。

## <a name="create-a-virtual-machine"></a>建立虛擬機器
為了啟動和運行我們的混合標識環境，您需要做的第一件事是創建一個虛擬機器，該虛擬機器將用作我們的本地活動目錄伺服器。  執行下列動作：

1. 以系統管理員身分開啟 PowerShell ISE。
2. 執行下列指令碼。

    ```powershell
    #Declare variables
    $VMName = 'DC1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\DC1\DC1.vhdx'
    $VHDSize = '64424509440'

    #Create New Virtual Machine
    New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

    #Set the memory to be non-dynamic
    Set-VMMemory $VMName -DynamicMemoryEnabled $false

    #Add DVD Drive to Virtual Machine
    Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

    #Mount Installation Media
    $DVDDrive = Get-VMDvdDrive -VMName $VMName

    #Configure Virtual Machine to Boot from DVD
    Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
    ```

## <a name="complete-the-operating-system-deployment"></a>完成作業系統部署
若要完成虛擬機器建立作業，您必須完成作業系統安裝。

1. Hyper-V 管理員，按兩下虛擬機器
2. 按一下 [啟動] 按鈕。
3. 系統將提示您「按任意鍵從 CD 或 DVD 開機」。 按任意鍵繼續。
4. 在 Windows Server 啟動畫面上選取您的語言，然後按一下 [下一步]****。
5. 按一下 [立即安裝] ****。
6. 輸入您的授權金鑰，然後按一下 [下一步]****。
7. 選取 [我接受授權條款]，然後按一下 [下一步]****。
8. 選取 [自訂：只安裝 Windows (進階)]****
9. 按 [下一步]****
10. 安裝完成之後，請重新啟動虛擬機器、登入並執行 Windows 更新，以確保 VM 為最新。  安裝最新的更新。

## <a name="install-active-directory-prerequisites"></a>安裝 Active Directory 必要條件
現在，您已經安裝了虛擬機器，在安裝 Active Directory 之前，您需要執行一些操作。  也就是說，您需要重命名虛擬機器、設置靜態 IP 位址和 DNS 資訊，以及安裝遠端伺服器管理工具。   執行下列動作：

1. 以系統管理員身分開啟 PowerShell ISE。
2. 執行下列指令碼。

    ```powershell
    #Declare variables
    $ipaddress = "10.0.1.117" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.117"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "DC1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    # Set the DNS servers
    Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

    #Rename the computer 
    Rename-Computer -NewName $newname -force 

    #Install features 
    New-Item $featureLogPath -ItemType file -Force 
    Add-WindowsFeature $addsTools 
    Get-WindowsFeature | Where installed >>$featureLogPath 

    #Restart the computer 
    Restart-Computer
    ```

## <a name="create-a-windows-server-ad-environment"></a>建立 Windows Server AD 環境
現在，您已經創建了 VM，並且它已重命名，並且具有靜態 IP 位址，您可以繼續安裝和配置活動目錄域服務。  執行下列動作：

1. 以系統管理員身分開啟 PowerShell ISE。
2. 執行下列指令碼。

    ```powershell 
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "contoso.com"
    $DomaninNetBIOSName = "CONTOSO"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

## <a name="create-a-windows-server-ad-user"></a>建立 Windows Server AD 使用者
現在，您已經擁有了我們的活動目錄環境，您需要一個測試帳戶。  此帳戶將在我們的內部部署 AD 環境中建立，然後同步至 Azure AD。  執行下列動作：

1. 以系統管理員身分開啟 PowerShell ISE。
2. 執行下列指令碼。

    ```powershell 
    # Filename:    4_CreateUser.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Allie"
    $Surname = "McCray"
    $Displayname = "Allie McCray"
    $Name = "amccray"
    $Password = "Pass1w0rd"
    $Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```


## <a name="create-an-azure-ad-tenant"></a>建立 Azure AD 租用戶
現在，您需要創建 Azure AD 租戶，以便可以將我們的使用者同步到雲。  若要建立新的 Azure AD 租用戶，請執行下列動作。

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)並使用具有 Azure 訂用帳戶的帳戶登入。
2. 選取**加號圖示 (+)** 並搜尋 **Azure Active Directory**。
3. 在搜尋結果中選取 [Azure Active Directory]****。
4. 選取 [建立]****。</br>
![建立](media/tutorial-single-forest/create1.png)</br>
5. 提供**組織名稱**與**初始網域名稱**。 然後選擇 **"創建**"。 這將會建立您的目錄。
6. 完成此動作之後，請按一下**這裡**以管理目錄。

## <a name="create-a-global-administrator-in-azure-ad"></a>在 Azure AD 中建立全域系統管理員
現在，您擁有了 Azure AD 租戶，您將創建一個全域管理員帳戶。  若要建立全域系統管理員帳戶，請執行下列動作。

1.  在 [管理]**** 底下選取 [使用者]****。</br>
![建立](media/tutorial-single-forest/administrator1.png)</br>
2.  選取 [所有使用者 ]****，然後選取 [+ 新增使用者]****。
3.  提供此使用者的名稱與使用者名稱。 這將成為您租用戶的全域系統管理員。 建議您將 [目錄角色]**** 變更為 [全域系統管理員]****。 您也可以顯示暫時密碼。 完成之後，請選取 [建立]****。</br>
![建立](media/tutorial-single-forest/administrator2.png)</br>
4. 完成此動作之後，請開啟新的網頁瀏覽器，並使用新的全域系統管理員帳戶與臨時密碼登入 myapps.microsoft.com。
5. 將全域系統管理員密碼變更為您可以記住的密碼。

## <a name="optional--additional-server-and-forest"></a>可選：其他伺服器和林
下面是一個可選部分，它提供了創建其他伺服器和或林的步驟。  這可用於一些更高級的教程，如[Azure AD 連接到雲預配的試驗](tutorial-pilot-aadc-aadccp.md)。

如果只需要一個額外的伺服器，則可以在 -**創建虛擬機器**步驟後停止，並將伺服器加入到上面創建的現有域。  

### <a name="create-a-virtual-machine"></a>建立虛擬機器

1. 以系統管理員身分開啟 PowerShell ISE。
2. 執行下列指令碼。

    ```powershell
    # Filename:    1_CreateVM_CP.ps1
    # Description: Creates a VM to be used in the tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. #This script is made available to you without any express, implied or statutory warranty, not even the implied warranty of merchantability or fitness for a particular purpose, or the warranty of title or non-infringement. The entire risk of the use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $VMName = 'CP1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\CP1\CP1.vhdx'
    $VHDSize = '64424509440'

    #Create New Virtual Machine
    New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

    #Set the memory to be non-dynamic
    Set-VMMemory $VMName -DynamicMemoryEnabled $false

    #Add DVD Drive to Virtual Machine
    Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

    #Mount Installation Media
    $DVDDrive = Get-VMDvdDrive -VMName $VMName

    #Configure Virtual Machine to Boot from DVD
    Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive
    ```

### <a name="complete-the-operating-system-deployment"></a>完成作業系統部署
若要完成虛擬機器建立作業，您必須完成作業系統安裝。

1. Hyper-V 管理員，按兩下虛擬機器
2. 按一下 [啟動] 按鈕。
3. 系統將提示您「按任意鍵從 CD 或 DVD 開機」。 按任意鍵繼續。
4. 在 Windows Server 啟動畫面上選取您的語言，然後按一下 [下一步]****。
5. 按一下 [立即安裝] ****。
6. 輸入您的授權金鑰，然後按一下 [下一步]****。
7. 選取 [我接受授權條款]，然後按一下 [下一步]****。
8. 選取 [自訂：只安裝 Windows (進階)]****
9. 按 [下一步]****
10. 安裝完成之後，請重新啟動虛擬機器、登入並執行 Windows 更新，以確保 VM 為最新。  安裝最新的更新。

### <a name="install-active-directory-prerequisites"></a>安裝 Active Directory 必要條件
現在，您已經安裝了虛擬機器，在安裝 Active Directory 之前，您需要執行一些操作。  也就是說，您需要重命名虛擬機器、設置靜態 IP 位址和 DNS 資訊，以及安裝遠端伺服器管理工具。   執行下列動作：

1. 以系統管理員身分開啟 PowerShell ISE。
2. 執行下列指令碼。

    ```powershell
    # Filename:    2_ADPrep_CP.ps1
    # Description: Prepares your environment for Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $ipaddress = "10.0.1.118" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.118"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "CP1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    #Set the DNS servers
    Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

    #Rename the computer 
    Rename-Computer -NewName $newname -force 

    #Install features 
    New-Item $featureLogPath -ItemType file -Force 
    Add-WindowsFeature $addsTools 
    Get-WindowsFeature | Where installed >>$featureLogPath 

    #Restart the computer 
    Restart-Computer
    ```
### <a name="create-a-windows-server-ad-environment"></a>建立 Windows Server AD 環境
現在，您已經創建了 VM，並且它已重命名，並且具有靜態 IP 位址，您可以繼續安裝和配置活動目錄域服務。  執行下列動作：

1. 以系統管理員身分開啟 PowerShell ISE。
2. 執行下列指令碼。

    ```powershell
    # Filename:    3_InstallAD_CP.ps1
    # Description: Creates an on-premises AD envrionment.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "fabrikam.com"
    $DomaninNetBIOSName = "FABRIKAM"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

### <a name="create-a-windows-server-ad-user"></a>建立 Windows Server AD 使用者
現在，您已經擁有了我們的活動目錄環境，您需要一個測試帳戶。  此帳戶將在我們的內部部署 AD 環境中建立，然後同步至 Azure AD。  執行下列動作：

1. 以系統管理員身分開啟 PowerShell ISE。
2. 執行下列指令碼。

    ```powershell 
    # Filename:    4_CreateUser_CP.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Anna"
    $Surname = "Ringdal"
    $Displayname = "Anna Ringdal"
    $Name = "aringdal"
    $Password = "Pass1w0rd"
    $Identity = "CN=aringdal,CN=Users,DC=fabrikam,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```

## <a name="conclusion"></a>結論
現在，您有一個可用於現有教程的環境，並測試雲資源調配提供的其他功能。

## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)
