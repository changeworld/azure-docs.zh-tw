---
title: 教學課程：使用 PHS 整合單一 AD 樹系
description: 示範如何使用密碼雜湊同步來設定混合式身分識別環境。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b82dcc633f0f2bb13cb84f80873117f9eb94a24f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91313157"
---
# <a name="tutorial--integrate-a-single-ad-forest-using-password-hash-sync-phs"></a>教學課程：使用密碼雜湊同步處理整合單一 AD 樹系 (PHS)

![建立](media/tutorial-password-hash-sync/diagram.png)

下列教學課程將逐步說明如何使用密碼雜湊同步來建立混合式身分識別環境。此環境可用於測試或更熟悉混合式身分識別的運作。

## <a name="prerequisites"></a>Prerequisites
下列是完成此教學課程的必要條件
- 已安裝 [Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-technology-overview) 的電腦。  建議您在 [Windows 10](/virtualization/hyper-v-on-windows/about/supported-guest-os) 或 [Windows Server 2016](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) 電腦上執行此作業。
- [外部網路介面卡](/virtualization/hyper-v-on-windows/quick-start/connect-to-network)用於讓虛擬機器與網際網路通訊。
- [Azure 訂用帳戶](https://azure.microsoft.com/free)
- Windows Server 2016 複本

> [!NOTE]
> 此教學課程使用 PowerShell 指令碼，可讓您在最快的時間內建立教學課程環境。  每個指令碼都使用在指令碼開頭處宣告的變數。  您可以且應該變更變數以反映您的環境。
>
>用來在安裝 Azure AD Connect 之前建立一般 Active Directory 環境的指令碼。  它們在所有指令碼中都通用。
>
> 您可以在 GitHub 上的[這裡](https://github.com/billmath/tutorial-phs)取得本教學課程中使用的 PowerShell 指令碼。

## <a name="create-a-virtual-machine"></a>建立虛擬機器
我們需要做的第一件事，就是建立將用作內部部署 Active Directory 伺服器的虛擬機器，以便讓我們的混合式身分識別環境開始執行。  執行下列動作：

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
4. 在 Windows Server 啟動畫面上選取您的語言，然後按一下 [下一步]  。
5. 按一下 [立即安裝]  。
6. 輸入您的授權金鑰，然後按一下 [下一步]  。
7. 選取 [我接受授權條款]，然後按一下 [下一步]  。
8. 選取**自訂：只安裝 Windows (進階)**
9. 按 **[下一步]**
10. 安裝完成之後，請重新啟動虛擬機器、登入並執行 Windows 更新，以確保 VM 為最新。  安裝最新的更新。

## <a name="install-active-directory-prerequisites"></a>安裝 Active Directory 必要條件
現在我們已啟動虛擬機器，我們必須在安裝 Active Directory 前做幾件事。  也就是說，我們必須重新命名虛擬機器、設定靜態 IP 位址與 DNS 資訊，然後安裝遠端伺服器管理工具。   執行下列動作：

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
現在我們已建立 VM、將其重新命名並設定靜態 IP 位址，我們可以繼續安裝並設定 Active Directory Domain Services。  執行下列動作：

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
現在我們已有 Active Directory 環境，我們需要測試帳戶。  此帳戶將在我們的內部部署 AD 環境中建立，然後同步至 Azure AD。  執行下列動作：

1. 以系統管理員身分開啟 PowerShell ISE。
2. 執行下列指令碼。

```powershell 
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
現在我們必須建立 Azure AD 租用戶，以便將使用者同步至雲端。  若要建立新的 Azure AD 租用戶，請執行下列動作。

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)並使用具有 Azure 訂用帳戶的帳戶登入。
2. 選取**加號圖示 (+)** 並搜尋 **Azure Active Directory**。
3. 在搜尋結果中選取 [Azure Active Directory]  。
4. 選取 [建立]  。</br>
![顯示如何建立 Azure AD 租用戶的螢幕擷取畫面。](media/tutorial-password-hash-sync/create1.png)</br>
5. 提供**組織名稱**與**初始網域名稱**。 然後選取 [建立]  。 這將會建立您的目錄。
6. 完成此動作之後，請按一下**這裡**以管理目錄。

## <a name="create-a-global-administrator-in-azure-ad"></a>在 Azure AD 中建立全域系統管理員
現在我們已有 Azure AD 租用戶，我們將建立全域系統管理員帳戶。  此帳戶會用來在 Azure AD Connect 安裝期間建立 Azure AD Connector 帳戶。  Azure AD Connector 帳戶會用來將資訊寫入到 Azure AD。   若要建立全域系統管理員帳戶，請執行下列動作。

1.  在 [管理]  底下選取 [使用者]  。</br>
![此螢幕擷取畫面顯示在 [管理] 區段中選取的 [使用者] 選項，您可以在其中建立全域管理員。](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  選取 [所有使用者 ]  ，然後選取 [+ 新增使用者]  。
3.  提供此使用者的名稱與使用者名稱。 這將成為您租用戶的全域系統管理員。 建議您將 [目錄角色]  變更為 [全域系統管理員]  。 您也可以顯示暫時密碼。 完成之後，請選取 [建立]  。</br>
![此螢幕擷取畫面顯示當您在 Azure AD 中建立全域管理員時選取的 [建立] 按鈕。](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. 完成此動作之後，請開啟新的網頁瀏覽器，並使用新的全域系統管理員帳戶與臨時密碼登入 myapps.microsoft.com。
5. 將全域系統管理員密碼變更為您可以記住的密碼。

## <a name="download-and-install-azure-ad-connect"></a>下載並安裝 Azure AD Connect
現在您可以下載並安裝 Azure AD Connect。  安裝之後，我們將會執行快速安裝。  執行下列動作：

1. 下載 [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2. 瀏覽並按兩下 **AzureADConnect.msi**。
3. 在 [歡迎] 畫面上，選取同意授權條款的方塊，然後按一下 [繼續]  。  
4. 在 [快速設定] 畫面上，按一下 [ **使用快速設定**]。</br>  
![顯示 [快速設定] 畫面和 [使用快速設定] 按鈕的螢幕擷取畫面。](media/tutorial-password-hash-sync/express1.png)</br>
5. 在 [連線至 Azure AD] 畫面上，輸入 Azure AD 的全域系統管理員使用者名稱和密碼。 按 [下一步]  。  
6. 在 [連接到 AD DS] 畫面上輸入企業系統管理員帳戶的使用者名稱和密碼。 按 [下一步]  。  
7. 在 [準備好設定] 畫面中，按一下 [安裝]  。
8. 當安裝完成時，按一下 [結束]  。
9. 安裝完成後，請先登出並重新登入，再使用 Synchronization Service Manager 或同步處理規則編輯器。


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>確認已建立使用者且已進行同步
我們現在將會確認內部部署目錄中的使用者已同步，而且已存在於外部 Azure AD 租用戶中。  這可能需要花幾個小時才能完成。  若要確認使用者是否已同步，請執行下列動作。


1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)並使用具有 Azure 訂用帳戶的帳戶登入。
2. 選取左邊的 [Azure Active Directory] 
3. 在 [管理]  底下選取 [使用者]  。
4. 確認您在我們的租用戶中看到新使用者</br>
![同步](media/tutorial-password-hash-sync/synch1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>使用我們其中一個使用者來測試登入

1. 瀏覽至 [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. 使用我們在新租用戶中建立的使用者來登入。  您必須使用下列格式登入：(user@domain.onmicrosoft.com)。 透過該使用者在內部部署用來登入的密碼登入。</br>
   ![Verify](media/tutorial-password-hash-sync/verify1.png)</br>

您現在已成功設定混合式身分識別環境，可用來測試及熟悉 Azure 的功能。

## <a name="next-steps"></a>後續步驟


- [硬體和先決條件](how-to-connect-install-prerequisites.md) 
- [快速設定](how-to-connect-install-express.md)
- [密碼雜湊同步處理](how-to-connect-password-hash-synchronization.md)|