---
title: Windows 虛擬桌面 MSIX 應用程式附加-Azure
description: 如何設定 Windows 虛擬桌面的 MSIX 應用程式連接。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 20a82cbd7de4b5678648bac19ab9b59bf557b0ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79128328"
---
# <a name="set-up-msix-app-attach"></a>設定 MSIX 應用程式附加

> [!IMPORTANT]
> MSIX 應用程式附加目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本主題將逐步引導您瞭解如何在 Windows 虛擬桌面環境中設定 MSIX 應用程式連接。

## <a name="requirements"></a>需求

在您開始之前，您必須先設定 MSIX 應用程式附加：

- 存取 Windows 測試人員入口網站以取得 Windows 10 的版本，並支援 MSIX 應用程式附加 Api。
- 正常運作的 Windows 虛擬桌面部署。 如需相關資訊，請參閱[在 Windows 虛擬桌面中建立租](tenant-setup-azure-active-directory.md)使用者。
- MSIX 封裝工具
- Windows 虛擬桌面部署中將儲存 MSIX 套件的網路共用

## <a name="get-the-os-image"></a>取得 OS 映射

首先，您必須取得將用於 MSIX 應用程式的 OS 映射。 若要取得 OS 映射：

1. 開啟[Windows 測試人員入口網站](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0)並登入。

     >[!NOTE]
     >您必須是 Windows 測試人員程式的成員，才能存取 Windows 測試人員入口網站。 若要深入瞭解 Windows 測試人員程式，請參閱我們的[Windows 測試人員檔](/windows-insider/at-home/)。

2. 向下流覽至 [**選取版本**] 區段，然後選取 [ **Windows 10 Insider PREVIEW Enterprise （FAST）–組建 19035**或更新版本]。

3. 選取 [**確認**]，然後選取您想要使用的語言，再選取 [**確認**]。
    
     >[!NOTE]
     >目前，英文是使用此功能進行測試的唯一語言。 您可以選取其他語言，但可能不會如預期顯示。
    
4. 當下載連結產生時，請選取**64 位下載**，並將它儲存到您的本機硬碟。

## <a name="prepare-the-vhd-image-for-azure"></a>準備適用于 Azure 的 VHD 映射 

開始之前，您必須先建立主要 VHD 映射。 如果您尚未建立主要 VHD 映射，請移至[準備和自訂主要 vhd 映射](set-up-customize-master-image.md)，並依照此處的指示進行。 

建立主要 VHD 映射之後，您必須停用 MSIX app 附加應用程式的自動更新。 若要停用自動更新，您必須在提高許可權的命令提示字元中執行下列命令：

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

接下來，準備適用于 Azure 的 VM VHD，並將產生的 VHD 磁片上傳至 Azure。 若要深入瞭解，請參閱[準備和自訂主要 VHD 映射](set-up-customize-master-image.md)。

將 VHD 上傳至 Azure 之後，請遵循[使用 Azure Marketplace 教學課程建立主機集](create-host-pools-azure-marketplace.md)區中的指示，建立以這個新映射為基礎的主機集區。

## <a name="prepare-the-application-for-msix-app-attach"></a>準備應用程式以進行 MSIX 應用程式附加 

如果您已經有 MSIX 套件，請直接跳到[設定 Windows 虛擬桌面基礎結構](#configure-windows-virtual-desktop-infrastructure)。 如果您想要測試繼承應用程式，請遵循[從 VM 上的桌面安裝程式建立 MSIX 套件](/windows/msix/packaging-tool/create-app-package-msi-vm/)中的指示，將繼承應用程式轉換成 MSIX 套件。

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>為 MSIX 產生 VHD 或 VHDX 套件

套件是以 VHD 或 VHDX 格式來優化效能。 MSIX 需要 VHD 或 VHDX 套件才能正常運作。

若要為 MSIX 產生 VHD 或 VHDX 套件：

1. [下載 msixmgr 工具](https://aka.ms/msixmgr)，並將 .ZIP 檔案夾儲存至工作階段主機 VM 中的資料夾。

2. 將 msixmgr 工具 .ZIP 檔案夾解壓縮。

3. 將來源 MSIX 封裝放在您解壓縮 msixmgr 工具的相同資料夾中。

4. 在 PowerShell 中執行下列 Cmdlet 以建立 VHD：

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >請確定 VHD 的大小夠大，足以容納擴充的 MSIX。 *

5. 執行下列 Cmdlet 來掛接新建立的 VHD：

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. 執行此 Cmdlet 來初始化 VHD：

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. 執行此 Cmdlet 來建立新的磁碟分割：

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. 執行此 Cmdlet 來格式化磁碟分割：

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. 在裝載的 VHD 上建立父資料夾。 這是必要步驟，因為 MSIX 應用程式附加需要父資料夾。 您可以將父資料夾命名為任何您喜歡的名稱。

### <a name="expand-msix"></a>展開 MSIX

之後，您必須將 MSIX 映射解壓縮以「展開」。 若要將 MSIX 映射解壓縮：

1. 以系統管理員身分開啟命令提示字元，並流覽至您下載並解壓縮 msixmgr 工具的資料夾。

2. 執行下列 Cmdlet，將 MSIX 解壓縮至您在上一節中建立並掛接的 VHD。

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    完成解壓縮之後，應該會出現下列訊息：

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > 如果您的網路內或在未連線到網際網路的裝置上使用來自 Microsoft Store 的套件，您必須從存放區取得套件授權，並安裝它們，才能成功執行應用程式。 請參閱[離線使用套件](#use-packages-offline)。

3. 流覽至掛接的 VHD，並開啟應用程式資料夾，並確認套件內容存在。

4. 取消掛接 VHD。

## <a name="configure-windows-virtual-desktop-infrastructure"></a>設定 Windows 虛擬桌面基礎結構

根據設計，單一 MSIX 擴充的套件（您在上一節中建立的 VHD）可以在多個工作階段主機 Vm 之間共用，因為 Vhd 是以唯讀模式連接。

開始之前，請確定您的網路共用符合下列需求：

- 共用與 SMB 相容。
- 屬於工作階段主機集區的 Vm 具有共用的 NTFS 許可權。

### <a name="set-up-an-msix-app-attach-share"></a>設定 MSIX 應用程式附加共用 

在您的 Windows 虛擬桌面環境中，建立網路共用並將套件移至該處。

>[!NOTE]
> 建立 MSIX 網路共用的最佳做法是設定具有 NTFS 唯讀許可權的網路共用。

## <a name="install-certificates"></a>安裝憑證

如果您的應用程式使用不是公開信任或自我簽署的憑證，以下是安裝它的方法：

1. 以滑鼠右鍵按一下封裝，然後選取 [**屬性**]。
2. 在出現的視窗中，選取 [**數位簽章**] 索引標籤。索引標籤上的清單中應該只有一個專案，如下圖所示。 選取該專案以反白顯示專案，然後選取 [**詳細資料**]。
3. 當 [數位簽章詳細資料] 視窗出現時，選取 [**一般**] 索引標籤，然後選取 [**安裝憑證**]。
4. 當安裝程式開啟時，選取 [**本機電腦**] 作為您的儲存位置，然後選取 **[下一步]**。
5. 如果安裝程式詢問您是否要允許應用程式對您的裝置進行變更，請選取 **[是]**。
6. 選取 **[將所有憑證放入下列存放區**]，然後選取 **[流覽]**。
7. 當 [選取證書存儲] 視窗出現時，選取 [**信任的人員**]，然後選取 **[確定]**。
8. 選取 [完成]  。

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>準備 PowerShell 腳本以進行 MSIX 應用程式附加

MSIX 應用程式附加有四個不同的階段，必須依照下列循序執行：

1. 階段
2. 註冊
3. 登出
4. 移

每個階段都會建立 PowerShell 腳本。 [這裡](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)提供每個階段的範例腳本。

### <a name="stage-the-powershell-script"></a>暫存 PowerShell 腳本

更新 PowerShell 腳本之前，請確定您在 VHD 中具有磁片區的磁片區 GUID。 若要取得磁片區 GUID：

1.  在您要執行腳本的 VM 中，開啟 VHD 所在的網路共用。

2.  以滑鼠右鍵按一下 VHD，然後選取 [**掛接**]。 這會將 VHD 掛接到磁碟機號。

3.  掛接 VHD 之後，[檔案**瀏覽器**] 視窗隨即開啟。 捕捉父資料夾並更新 **$parentFolder**變數

    >[!NOTE]
    >如果您看不到父資料夾，這表示 MSIX 未正確擴充。 重做上一節，然後再試一次。

4.  開啟父資料夾。 如果正確展開，您會看到與封裝同名的資料夾。 更新 **$packageName**變數以符合此資料夾的名稱。

    例如： `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe` 。

5.  開啟命令提示字元，然後輸入**mountvol**。 此命令將會顯示磁片區及其 Guid 的清單。 複製磁片區的 GUID，其中磁碟機號符合您在步驟2中掛接 VHD 的磁片磁碟機。

    例如，在此 mountvol 命令的範例輸出中，如果您將 VHD 掛接到 C 磁片磁碟機，您會想要複製上述`C:\`的值：

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  使用您剛才複製的磁片區 GUID 來更新 **$volumeGuid**變數。

7. 開啟系統管理 PowerShell 提示字元，並使用適用于您環境的變數來更新下列 PowerShell 腳本。

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

若要執行註冊腳本，請執行下列 PowerShell Cmdlet，並將預留位置值取代為適用于您環境的值。

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

針對此腳本，請將 **$packageName**的預留位置取代為您要測試之封裝的名稱。

```powershell
#MSIX app attach deregistration sample

#region variables

$packageName = "<package name>"

#endregion

#region deregister

Remove-AppxPackage -PreserveRoamableApplicationData $packageName

#endregion
```

### <a name="destage-powershell-script"></a>將 PowerShell 腳本移至

針對此腳本，請將 **$packageName**的預留位置取代為您要測試之封裝的名稱。

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

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>設定 MSIX 應用程式附加代理程式的模擬腳本

建立腳本之後，使用者可以手動執行它們，或將它們設定為自動啟動、登入、登出和關機腳本。 若要深入瞭解這些類型的腳本，請參閱[在群組原則中使用啟動、關機、登入和登出腳本](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)。

每一個自動腳本都會執行應用程式附加腳本的一個階段：

- 啟動腳本會執行階段腳本。
- 登入腳本會執行註冊腳本。
- 登出腳本會執行取消註冊腳本。
- 關機腳本會執行「上移」腳本。

## <a name="use-packages-offline"></a>離線使用套件

如果您使用的是商務用[Microsoft Store](https://businessstore.microsoft.com/)的封裝，或是您的網路內或未連線到網際網路的裝置上的[Microsoft Store 教育](https://educationstore.microsoft.com/)版，您必須從 Microsoft Store 取得套件授權，並將其安裝在您的裝置上，才能成功執行應用程式。 如果您的裝置已上線，而且可以連接到商務用 Microsoft Store，則必須自動下載所需的授權，但如果您已離線，則必須手動設定授權。 

若要安裝授權檔案，您必須使用 PowerShell 腳本來呼叫 WMI 橋接器提供者中的 MDM_EnterpriseModernAppManagement_StoreLicenses02_01 類別。  

以下說明如何設定離線使用的授權： 

1. 從商務用 Microsoft Store 下載應用程式套件、授權和所需的架構。 您同時需要已編碼和未編碼的授權檔案。 您可以在[這裡](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)找到詳細的下載指示。
2. 在步驟3的腳本中更新下列變數：
      1. `$contentID`這是未編碼的授權檔案（.xml）中的 ContentID 值。 您可以在您選擇的文字編輯器中開啟授權檔案。
      2. `$licenseBlob`這是編碼授權檔案（. bin）中授權 blob 的整個字串。 您可以在您選擇的文字編輯器中開啟編碼的授權檔案。 
3. 從系統管理員 PowerShell 提示字元執行下列腳本。 執行授權安裝的最佳位置是在[預備腳本](#stage-the-powershell-script)的結尾，也需要從系統管理員提示執行。

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

目前不支援這項功能，但您可以在[Windows 虛擬桌面 TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)向該社區提出問題。

您也可以在[Windows 虛擬桌面意見反應中樞](https://aka.ms/MRSFeedbackHub)留下 Windows 虛擬桌面的意見反應，或在[MSIX 應用程式附加意見反應中樞](https://aka.ms/msixappattachfeedback)和[MSIX 封裝工具意見反應中樞](https://aka.ms/msixtoolfeedback)留下 MSIX 應用程式和封裝工具的意見反應。
