---
title: Windows 虛擬桌面 MSIX 應用程式附加 - Azure
description: 如何設定 Windows 虛擬桌面的 MSIX 應用程式附加。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 06/16/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f68ee9854b40c8174fe8808fc82639b79629c0c8
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987149"
---
# <a name="set-up-msix-app-attach"></a>設定 MSIX 應用程式附加

> [!IMPORTANT]
> MSIX 應用程式附加目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議您將其用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

此主題將逐步解說如何在 Windows 虛擬桌面環境中設定 MSIX 應用程式附加。

## <a name="requirements"></a>需求

在您開始之前，設定 MSIX 應用程式附加會需要下列項目：

- 存取 Windows 測試人員入口網站以取得支援 MSIX 應用程式附加 API 的 Windows 10 版本。
- 運作中的 Windows 虛擬桌面部署。 若要瞭解如何部署 Windows 虛擬桌面 (傳統) ，請參閱[在 Windows 虛擬桌面中建立租](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)使用者。 若要瞭解如何使用 Azure Resource Manager 整合來部署 Windows 虛擬桌面，請參閱[使用 Azure 入口網站建立主機集](./create-host-pools-azure-marketplace.md)區。
- MSIX 封裝工具。
- Windows 虛擬桌面部署中將儲存 MSIX 套件的網路共用。

## <a name="get-the-os-image"></a>取得 OS 映像

首先，您必須取得 OS 映射。 您可以透過 Azure 入口網站取得 OS 映射。 不過，如果您是 Windows 測試人員程式的成員，您可以選擇改為使用 Windows 測試人員入口網站。

### <a name="get-the-os-image-from-the-azure-portal"></a>從 Azure 入口網站取得 OS 映射

若要從 Azure 入口網站取得 OS 映射：

1. 開啟[Azure 入口網站](https://portal.azure.com)並登入。

2. 移至 [**建立虛擬機器**]。

3. 在 [**基本**] 索引標籤中，選取 [ **Windows 10 企業版] [多重會話，版本 2004**]。

4. 請遵循其餘指示來完成虛擬機器的建立。

     >[!NOTE]
     >您可以使用此 VM 來直接測試 MSIX 應用程式附加。 若要深入瞭解，請直接跳至[產生適用于 MSIX 的 VHD 或 VHDX 套件](#generate-a-vhd-or-vhdx-package-for-msix)。 否則，請繼續閱讀本節。

### <a name="get-the-os-image-from-the-windows-insider-portal"></a>從 Windows 測試人員入口網站取得 OS 映射

若要從 Windows 測試人員入口網站取得 OS 映射：

1. 開啟 [Windows 測試人員入口網站](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) \(英文\) 並登入。

     >[!NOTE]
     >您必須是 Windows 測試人員計畫的成員，才能存取 Windows 測試人員入口網站。 若要深入了解 Windows 測試人員計畫，請參閱我們的 [Windows 測試人員文件](/windows-insider/at-home/)。

2. 向下捲動到 [Select edition] \(選取版本\) 區段，然後選取 [Windows 10 Insider Preview Enterprise (FAST) – Build 19041] 或更新版本。

3. 選取 [Confirm] \(確認\)，然後選取您想要使用的語言，然後再次選取 [確認]。

     >[!NOTE]
     >目前，英文是使用此功能進行測試的唯一語言。 您可以選取其他語言，但可能不會如預期顯示。

4. 當下載連結產生時，請選取 [64 位元下載]，並將其儲存到您的本機硬碟。

## <a name="prepare-the-vhd-image-for-azure"></a>準備適用於 Azure 的 VHD 映像

接下來，您必須建立主要 VHD 映射。 如果您尚未建立主要 VHD 映像，請移至[準備和自訂主要 VHD 映像](set-up-customize-master-image.md)，並依照該處的指示進行。

建立主要 VHD 映像之後，您必須停用 MSIX 應用程式附加應用程式的自動更新。 若要停用自動更新，您將必須在提升權限的命令提示字元中執行下列命令：

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

停用自動更新之後，您必須啟用 Hyper-v，因為您會使用掛接 VHD 命令來將 VHD 預備和卸載至「移至容器」。

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```
>[!NOTE]
>此變更需要您重新啟動虛擬機器。

接下來，準備適用於 Azure 的 VM VHD，並將產生的 VHD 磁碟上傳至 Azure。 若要深入了解，請參閱[準備和自訂主要 VHD 映像](set-up-customize-master-image.md)。

將 VHD 上傳至 Azure 之後，請遵循[使用 Azure Marketplace 建立主機集區](create-host-pools-azure-marketplace.md)教學課程中的指示，建立以這個新映像為基礎的主機集區。

## <a name="prepare-the-application-for-msix-app-attach"></a>準備要進行 MSIX 應用程式附加的應用程式

如果您已經有 MSIX 套件，請直接跳至[設定 Windows 虛擬桌面基礎結構](#configure-windows-virtual-desktop-infrastructure)。 如果您想要測試舊版應用程式，請遵循[在 VM 上從桌面安裝程式建立 MSIX 套件](/windows/msix/packaging-tool/create-app-package-msi-vm/)中的指示，將舊版應用程式轉換成 MSIX 套件。

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>產生適用於 MSIX 的 VHD 或 VHDX 套件

套件是以 VHD 或 VHDX 格式來最佳化效能。 MSIX 需要 VHD 或 VHDX 套件才能正常運作。

產生適用於 MSIX 的 VHD 或 VHDX 套件：

1. [下載 msixmgr 工具](https://aka.ms/msixmgr)，並將 .zip 資料夾儲存至工作階段主機 VM 內的資料夾。

2. 將 msixmgr 工具 .zip 資料夾解壓縮。

3. 將來源 MSIX 套件放在您解壓縮 msixmgr 工具的相同資料夾中。

4. 在 PowerShell 中執行下列 Cmdlet 以建立 VHD：

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >請確定 VHD 夠大，足以持有解壓的 MSIX。*

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

9. 在掛接的 VHD 上建立父資料夾。 這是必要步驟，因為 MSIX 應用程式附加需要父資料夾。 您可以將父資料夾命名為任何您喜歡的名稱。

### <a name="expand-msix"></a>展開 MSIX

之後，您必須將 MSIX 映像解壓縮來加以「展開」。 將 MSIX 映像解壓縮：

1. 以系統管理員身分開啟命令提示字元，並瀏覽至您下載並解壓縮 msixmgr 工具的資料夾。

2. 執行下列 Cmdlet，將 MSIX 解壓縮至您在上一節中建立並掛接的 VHD。

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    完成解壓縮之後，應該會出現下列訊息：

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > 如果在您的網路內或在未連線到網際網路的裝置上使用來自商務用 (或教育用) Microsoft Store 的套件，您必須從 Store 取得套件授權並加以安裝，才能成功執行應用程式。 請參閱[離線使用套件](#use-packages-offline)。

3. 瀏覽至掛接的 VHD，並開啟應用程式資料夾，並確認套件內容存在。

4. 卸載 VHD。

## <a name="configure-windows-virtual-desktop-infrastructure"></a>設定 Windows 虛擬桌面基礎結構

根據設計，單一 MSIX 展開套件 (您在上一節中建立的 VHD) 可以在多個工作階段主機 VM 之間共用，因為 VHD 是以唯讀模式連結。

開始之前，請確定您的網路共用滿足這些需求：

- 共用與 SMB 相容。
- 屬於工作階段主機集區一部分的 VM 有共用的 NTFS 權限。

### <a name="set-up-an-msix-app-attach-share"></a>設定 MSIX 應用程式附加共用

在您的 Windows 虛擬桌面環境中，建立網路共用並將套件移至該處。

>[!NOTE]
> 建立 MSIX 網路共用的最佳做法是設定具有 NTFS 唯讀權限的網路共用。

## <a name="install-certificates"></a>安裝憑證

如果您的應用程式使用不是公開信任或是自我簽署的憑證，以下是加以安裝的方法：

1. 以滑鼠右鍵按一下套件並選取 [屬性]。
2. 在出現的視窗中，選取 [數位簽章] 索引標籤。索引標籤上的清單中應該只有一個項目，如下圖所示。 選取該項目使其反白顯示，然後選取 [詳細資料]。
3. 當 [數位簽章詳細資料] 視窗出現時，選取 [**一般**] 索引標籤，然後選取 [**查看憑證**]，再選取 [**安裝憑證**]。
4. 當安裝程式開啟時，請選取 [本機電腦] 作為您的儲存位置，然後選取 [下一步]。
5. 如果安裝程式詢問您是否要允許應用程式對您的裝置進行變更，請選取 [是]。
6. 選取 [將所有憑證放入以下的存放區]，然後選取 [瀏覽]。
7. 當選取憑證存放區視窗出現時，選取 [受信任的人]，然後選取 [確定]。
8. 選取 **[下一步** **] 和 [完成]**。

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>準備 PowerShell 指令碼以進行 MSIX 應用程式附加

MSIX 應用程式附加有四個不同階段，必須依照下列順序執行：

1. 階段
2. 註冊
3. 取消註冊
4. 取消暫存

每個階段都會建立 PowerShell 指令碼。 [這裡](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach) \(英文\) 提供每個階段的範例指令碼。

### <a name="stage-powershell-script"></a>階段 PowerShell 腳本

更新 PowerShell 指令碼之前，請確定您有該磁碟區在 VHD 中的磁碟區 GUID。 取得磁碟區 GUID：

1.  在您要執行指令碼的 VM 中，開啟 VHD 所在的網路共用。

2.  以滑鼠右鍵按一下 VHD，然後選取 [掛接]。 這會將 VHD 掛接到磁碟機代號。

3.  掛接 VHD 之後，[檔案總管] 視窗隨即開啟。 擷取父資料夾，並更新 **$parentFolder** 變數

    >[!NOTE]
    >如果您看不到父資料夾，這表示 MSIX 未正確展開。 請重做上一節，然後再試一次。

4.  開啟父資料夾。 如果正確地展開，您將會看到名稱與套件相同的資料夾。 更新 **$packageName** 變數以符合此資料夾的名稱。

    例如： `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe` 。

5.  開啟命令提示字元，然後輸入 **mountvol**。 此命令將會顯示磁碟區及其 GUID 的清單。 複製磁碟區的 GUID，其中磁碟機代號符合您在步驟 2 中掛接 VHD 的磁碟機。

    例如，在 mountvol 命令的此範例輸出中，如果您將 VHD 掛接到磁碟機 C，建議您複製 `C:\` 上方的值：

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  使用您剛才複製的磁碟區 GUID 來更新 **$volumeGuid** 變數。

7. 開啟系統管理員 PowerShell 提示字元，並使用適用於您環境的變數來更新下列 PowerShell 指令碼。

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
    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime] | Out-Null
    Add-Type -AssemblyName System.Runtime.WindowsRuntime
    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where { $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult] AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]
    $asTaskAsyncOperation = $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult], [Windows.Management.Deployment.DeploymentProgress])
    $packageManager = [Windows.Management.Deployment.PackageManager]::new()
    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd
    $path = ([System.Uri]$path).AbsoluteUri
    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")
    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))
    $task
    #endregion
    ```

### <a name="register-powershell-script"></a>註冊 PowerShell 指令碼

若要執行註冊指令碼，請執行下列 PowerShell Cmdlet，並將預留位置值取代為適用於您環境的值。

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

### <a name="deregister-powershell-script"></a>取消註冊 PowerShell 指令碼

針對此指令碼，請將 **$packageName** 的預留位置取代為您所要測試套件的名稱。

```powershell
#MSIX app attach deregistration sample

#region variables
$packageName = "<package name>"
#endregion

#region deregister
Remove-AppxPackage -PreserveRoamableApplicationData $packageName
#endregion
```

### <a name="destage-powershell-script"></a>取消暫存 PowerShell 指令碼

針對此指令碼，請將 **$packageName** 的預留位置取代為您所要測試套件的名稱。

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

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>設定 MSIX 應用程式附加代理程式的模擬指令碼

建立指令碼之後，使用者可以手動加以執行，或將其設定為啟動、登入、登出與關機指令碼以自動執行。 若要深入了解這些類型的指令碼，請參閱[在群組原則中使用啟動、關機、登入和登出指令碼](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)。

每一個自動指令碼都會執行應用程式附加指令碼的一個階段：

- 啟動指令碼會執行暫存指令碼。
- 登入指令碼會執行註冊指令碼。
- 登出指令碼會執行取消註冊指令碼。
- 關機指令碼會執行取消暫存指令碼。

## <a name="use-packages-offline"></a>離線使用套件

如果在您的網路內或在未連線到網際網路的裝置上使用來自[商務用 Microsoft Store](https://businessstore.microsoft.com/) 或[教育用 Microsoft Store](https://educationstore.microsoft.com/) 的套件，您必須從 Microsoft Store 取得套件授權並在您的裝置上加以安裝，才能成功執行應用程式。 如果您的裝置已在線上，而且可以連線到商務用 Microsoft Store，則系統會自動下載所需授權，但如果您已離線，則必須手動設定授權。

若要安裝授權檔案，您將必須使用會呼叫 WMI 橋接器提供者中 MDM_EnterpriseModernAppManagement_StoreLicenses02_01 類別的 PowerShell 指令碼。

以下是如何設定授權以便離線使用的方法：

1. 從商務用 Microsoft Store 下載應用程式套件、授權與所需架構。 您同時需要已編碼與未編碼的授權檔案。 您可以在[這裡](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)找到詳細的下載指示。
2. 在步驟 3 的指令碼中，更新下列變數：
      1. `$contentID` 是來自「未編碼」授權檔案 (.xml) 的 ContentID 值。 您可以在您選擇的文字編輯器中開啟授權檔案。
      2. `$licenseBlob` 是「已編碼」授權檔案 (.bin) 中授權 Blob 的整個字串。 您可以在您選擇的文字編輯器中開啟已編碼授權檔案。
3. 從系統管理員 PowerShell 提示字元執行下列指令碼。 執行授權安裝的最佳位置是在[暫存指令碼](#stage-powershell-script) \(也需要從系統管理員提示字元執行\) 的結尾。

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

目前不支援此功能，但您可以在 [Windows 虛擬桌面 TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) \(英文\) 向社群提出問題。

您也可以在 [Windows 虛擬桌面意見反應中樞](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)留下 Windows 虛擬桌面的意見反應。
