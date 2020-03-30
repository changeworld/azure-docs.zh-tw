---
title: Windows 虛擬桌面 MSIX 應用附加 - Azure
description: 如何為 Windows 虛擬桌面設置 MSIX 應用附加。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 20a82cbd7de4b5678648bac19ab9b59bf557b0ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128328"
---
# <a name="set-up-msix-app-attach"></a>設定 MSIX 應用程式附加

> [!IMPORTANT]
> MSIX 應用附加當前處於公共預覽版中。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本主題將介紹如何在 Windows 虛擬桌面環境中設置 MSIX 應用附加。

## <a name="requirements"></a>需求

在開始之前，您需要配置 MSIX 應用附加功能：

- 訪問 Windows 預覽版門戶以獲取 Windows 10 版本，並支援 MSIX 應用附加 API。
- 正常運行的 Windows 虛擬桌面部署。 有關詳細資訊，請參閱[在 Windows 虛擬桌面中創建租戶](tenant-setup-azure-active-directory.md)。
- MSIX 包裝工具
- Windows 虛擬桌面部署中的網路共用，其中將存儲 MSIX 包

## <a name="get-the-os-image"></a>獲取作業系統映射

首先，您需要獲取用於 MSIX 應用的作業系統映射。 要獲取作業系統映射，

1. 打開[Windows 預覽版門戶](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0)並登錄。

     >[!NOTE]
     >您必須是 Windows 預覽體驗程式的成員才能訪問 Windows 預覽版門戶。 要瞭解有關 Windows 預覽版計畫的更多資訊，請查看我們的[Windows 預覽版文檔](/windows-insider/at-home/)。

2. 向下滾動到 **"選擇版本**"部分，然後選擇**Windows 10 預覽企業版 （FAST） – 生成 19035**或更高版本。

3. 選擇 **"確認**"，然後選擇要使用的語言，然後再次選擇 **"確認**"。
    
     >[!NOTE]
     >目前，英語是唯一通過該功能測試的語言。 您可以選擇其他語言，但它們可能無法按預期顯示。
    
4. 生成下載連結時，選擇**64 位下載**並將其保存到本地硬碟。

## <a name="prepare-the-vhd-image-for-azure"></a>為 Azure 準備 VHD 映射 

開始之前，您需要創建主 VHD 映射。 如果您尚未創建主 VHD 映射，請轉到[準備和自訂主 VHD 映射](set-up-customize-master-image.md)，然後按照說明進行操作。 

創建主 VHD 映射後，必須禁用 MSIX 應用附加應用程式的自動更新。 要禁用自動更新，您需要在提升的命令提示符中運行以下命令：

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

rem Disable Windows Update:

sc config wuauserv start=disabled
```

接下來，為 Azure 準備 VM VHD，並將生成的 VHD 磁片上載到 Azure。 要瞭解更多資訊，請參閱[準備和自訂主 VHD 映射](set-up-customize-master-image.md)。

將 VHD 上載到 Azure 後，請使用 Azure 應用商店教程按照"[創建主機池](create-host-pools-azure-marketplace.md)"中的說明創建基於此新映射的主機池。

## <a name="prepare-the-application-for-msix-app-attach"></a>準備 MSIX 應用程式附加的應用程式 

如果您已經擁有 MSIX 包，請跳到[配置 Windows 虛擬桌面基礎結構](#configure-windows-virtual-desktop-infrastructure)。 如果要測試繼承應用程式，請按照[VM 上的桌面安裝程式中的 MSIX 套裝程式](/windows/msix/packaging-tool/create-app-package-msi-vm/)中的說明將舊應用程式轉換為 MSIX 包。

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>為 MSIX 生成 VHD 或 VHDX 封裝

包採用 VHD 或 VHDX 格式，以優化性能。 MSIX 需要 VHD 或 VHDX 套裝軟體才能正常工作。

要為 MSIX 生成 VHD 或 VHDX 包，：

1. [下載 msixmgr 工具](https://aka.ms/msixmgr)並將 .ZIP 檔案夾保存到工作階段主機 VM 中的資料夾。

2. 解壓 msixmgr 工具 .ZIP 檔案夾。

3. 將源 MSIX 包放入同一資料夾中，在其中解壓縮 msixmgr 工具。

4. 在 PowerShell 中運行以下 Cmdlet 以創建 VHD：

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >確保 VHD 的大小足夠大，可以容納擴展的 MSIX。

5. 運行以下 Cmdlet 以裝載新創建的 VHD：

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. 運行此 Cmdlet 以初始化 VHD：

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. 運行此 Cmdlet 以創建新分區：

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. 運行此 Cmdlet 以設置分區的格式：

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. 在裝載的 VHD 上創建父資料夾。 此步驟是必需的，因為 MSIX 應用附加需要父資料夾。 您可以隨心所欲地命名父資料夾。

### <a name="expand-msix"></a>展開 MSIX

之後，您需要通過解壓縮來"展開"MSIX 映射。 要解壓縮 MSIX 圖像，

1. 以管理員身份打開命令提示符，然後導航到下載並解壓縮 msixmgr 工具的資料夾。

2. 運行以下 Cmdlet 以解壓縮 MSIX 到您在上一節中創建和裝載的 VHD 中。

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    解包完成後，應顯示以下消息：

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > 如果使用網路中的 Microsoft 商業商店（或教育）中的包，或在未連接到 Internet 的設備上使用包，則需要從應用商店獲取包許可證並安裝它們才能成功運行應用。 請參閱[離線使用包](#use-packages-offline)。

3. 導航到裝載的 VHD 並打開應用資料夾並確認包內容存在。

4. 取消掛接 VHD。

## <a name="configure-windows-virtual-desktop-infrastructure"></a>配置 Windows 虛擬桌面基礎結構

根據設計，單個 MSIX 擴展包（您在上一節中創建的 VHD）可以在多個工作階段主機 VM 之間共用，因為 VHD 以唯讀模式連接。

在開始之前，請確保您的網路共用滿足以下要求：

- 共用與 SMB 相容。
- 屬於工作階段主機池的 VM 對共用具有 NTFS 許可權。

### <a name="set-up-an-msix-app-attach-share"></a>設置 MSIX 應用附加共用 

在 Windows 虛擬桌面環境中，創建網路共用並將包移到其中。

>[!NOTE]
> 創建 MSIX 網路共用的最佳做法是使用 NTFS 唯讀許可權設置網路共用。

## <a name="install-certificates"></a>安裝憑證

如果應用使用的證書不是公共信任的或已自簽名的，請介紹安裝方式：

1. 按右鍵包並選擇**屬性**。
2. 在顯示的視窗中，選擇 **"數位簽章"** 選項卡。選項卡上的清單中應只有一個專案，如下圖所示。 選擇該專案以突出顯示該專案，然後選擇 **"詳細資訊**"。
3. 當出現數位簽章詳細資訊視窗時，選擇 **"常規**"選項卡，然後選擇 **"安裝證書**"。
4. 安裝程式打開時，選擇**本地電腦**作為存儲位置，然後選擇 **"下一步**"。
5. 如果安裝程式詢問您是否要允許應用對設備進行更改，請選擇"**是**"。
6. 選擇**將所有證書放在以下存儲中**，然後選擇 **"流覽**"。
7. 當出現選擇憑證存放區視窗時，選擇 **"受信任的人員**"，然後選擇 **"確定**"。
8. 選取 [完成]****。

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>為 MSIX 應用附加準備 PowerShell 腳本

MSIX 應用附加有四個不同的階段，必須按以下循序執行：

1. 階段
2. 註冊
3. 登出
4. 舞臺

每個階段創建一個 PowerShell 腳本。 [此處](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)提供了每個階段的示例腳本。

### <a name="stage-the-powershell-script"></a>暫行電源外殼腳本

在更新 PowerShell 腳本之前，請確保在 VHD 中具有卷 GUID。 要獲取卷 GUID：

1.  打開 VHD 位於 VM 中的網路共用，您將在 VM 中運行腳本。

2.  按右鍵 VHD 並選擇 **"安裝**"。 這將將 VHD 安裝到磁碟機號上。

3.  裝載 VHD 後，**檔資源管理器**視窗將打開。 捕獲父資料夾並更新 **$parentFolder**變數

    >[!NOTE]
    >如果未看到父資料夾，則表示 MSIX 未正確展開。 請重試上一節。

4.  打開父資料夾。 如果擴展正確，您將看到一個與包同名的資料夾。 更新 **$packageName**變數以匹配此資料夾的名稱。

    例如： `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe` 。

5.  打開命令提示符並進入**安裝vol**。 此命令將顯示卷及其 GUID 的清單。 複製磁碟機號與安裝 VHD 的磁碟機匹配的卷的 GUID，以執行步驟 2。

    例如，在此示例中，對於 mountvol 命令的輸出，如果將 VHD 裝載到磁碟機 C，則需要複製上面`C:\`的值：

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  使用剛剛複製的卷 GUID 更新 **$volumeGuid**變數。

7. 打開管理 PowerShell 提示符，並使用適用于您的環境的變數更新以下 PowerShell 腳本。

    ```powershell
    #MSIX app attach staging sample

    #region variables

    $vhdSrc="<path to vhd>"

    $packageName = "<package name>"

    $parentFolder = "<package parent folder>"

    $parentFolder = "\" + $parentFolder + "\"

    $volumeGuid = "<vol guid>"

    $msixJunction = "C:\temp\AppAttach\"

    #endregion

    #region mountvhd

    try

    {

    Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly

    Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green

    }

    catch

    {

    Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red

    }

    #endregion

    #region makelink

    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"

    if (!(Test-Path $msixJunction))

    {

    md $msixJunction

    }

    $msixJunction = $msixJunction + $packageName

    cmd.exe /c mklink /j $msixJunction $msixDest

    #endregion

    #region stage

    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime]
    | Out-Null

    Add-Type -AssemblyName System.Runtime.WindowsRuntime

    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where {
    $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult]
    AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]

    $asTaskAsyncOperation =
    $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult],
    [Windows.Management.Deployment.DeploymentProgress])

    $packageManager = [Windows.Management.Deployment.PackageManager]::new()

    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd

    $path = ([System.Uri]$path).AbsoluteUri

    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")

    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))

    $task

    #endregion
    ```

### <a name="register-powershell-script"></a>註冊 PowerShell 腳本

要運行寄存器腳本，請運行以下 PowerShell Cmdlet，將預留位置值替換為適用于您的環境的值。

```powershell
#MSIX app attach registration sample

#region variables

$packageName = "<package name>"

$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"

#endregion

#region register

Add-AppxPackage -Path $path -DisableDevelopmentMode -Register

#endregion
```

### <a name="deregister-powershell-script"></a>取消註冊 PowerShell 腳本

對於此腳本，請將 **$packageName**的預留位置替換為要測試的包的名稱。

```powershell
#MSIX app attach deregistration sample

#region variables

$packageName = "<package name>"

#endregion

#region deregister

Remove-AppxPackage -PreserveRoamableApplicationData $packageName

#endregion
```

### <a name="destage-powershell-script"></a>去舞臺電源外殼腳本

對於此腳本，請將 **$packageName**的預留位置替換為要測試的包的名稱。

```powershell
#MSIX app attach de staging sample

#region variables

$packageName = "<package name>"

$msixJunction = "C:\temp\AppAttach\"

#endregion

#region deregister

Remove-AppxPackage -AllUsers -Package $packageName

cd $msixJunction

rmdir $packageName -Force -Verbose

#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>為 MSIX 應用附加代理設置類比腳本

創建腳本後，使用者可以手動運行這些腳本或將它們設置為自動作為啟動、登錄、登出和關閉腳本運行。 要瞭解有關這些類型的腳本的更多資訊，請參閱[在群組原則 中使用啟動、關閉、登錄和登出腳本](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)。

每個自動腳本都運行應用附加腳本的一個階段：

- 啟動腳本運行階段腳本。
- 登入指令檔運行寄存器腳本。
- 登出腳本運行登出腳本。
- 關閉腳本運行去階段腳本。

## <a name="use-packages-offline"></a>離線使用包

如果您使用的是[來自 Microsoft 商業應用商店](https://businessstore.microsoft.com/)或網路中的[Microsoft 教育應用商店](https://educationstore.microsoft.com/)的套裝軟體，或者使用未連接到 Internet 的設備，則需要從 Microsoft 應用商店獲取包許可證並將其安裝到您的設備上才能成功運行應用。 如果您的設備處於線上狀態，並且可以連接到 Microsoft 商業應用商店，則所需的許可證應自動下載，但如果您處於離線狀態，則需要手動設置許可證。 

要安裝授權檔案，您需要使用在 WMI 橋接提供程式中調用 MDM_EnterpriseModernAppManagement_StoreLicenses02_01 類的 PowerShell 腳本。  

以下是設置許可證以進行離線使用的方式： 

1. 從適用于商業的 Microsoft 應用商店下載應用包、許可證和所需框架。 您需要編碼和未編碼的授權檔案。 詳細的下載說明可以[在這裡](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)找到。
2. 更新步驟 3 的腳本中的以下變數：
      1. `$contentID`是未編碼授權檔案 （.xml） 中的 ContentID 值。 您可以在您選擇的文字編輯器中打開授權檔案。
      2. `$licenseBlob`是編碼授權檔案 （.bin） 中許可證 Blob 的整個字串。 您可以在您選擇的文字編輯器中打開編碼的授權檔案。 
3. 從管理員 PowerShell 提示符運行以下腳本。 執行許可證安裝的好地方是在[過渡腳本](#stage-the-powershell-script)的末尾，該腳本也需要從管理員提示符運行。

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession 

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />' 

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param) 


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}  
```

## <a name="next-steps"></a>後續步驟

此功能目前不受支援，但您可以在[Windows 虛擬桌面技術社區](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)向社區提問。

您還可以在[Windows 虛擬桌面回饋中樞](https://aka.ms/MRSFeedbackHub)保留 Windows 虛擬桌面的回饋，或在[MSIX 應用附加回饋中樞和](https://aka.ms/msixappattachfeedback) [MSIX 打包工具回饋中樞](https://aka.ms/msixtoolfeedback)為 MSIX 應用和打包工具留下回饋。
