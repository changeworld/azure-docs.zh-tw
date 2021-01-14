---
title: 設定 Windows 虛擬桌面 MSIX app 附加 PowerShell 腳本-Azure
description: 如何建立適用于 Windows 虛擬桌面的 MSIX 應用程式附加的 PowerShell 腳本。
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5e45c51735e0b7ab4b263d3f3047b5848c82439d
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185762"
---
# <a name="create-powershell-scripts-for-msix-app-attach-preview"></a>建立 MSIX 應用程式附加的 PowerShell 腳本 (預覽) 

> [!IMPORTANT]
> MSIX 應用程式附加目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議您將其用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本主題將逐步引導您設定 PowerShell 腳本，以 MSIX 應用程式附加。

>[!IMPORTANT]
>開始之前，請務必填寫並提交 [此表單](https://aka.ms/enablemsixappattach) ，以在您的訂用帳戶中啟用 MSIX 應用程式附加。 如果您沒有核准的要求，MSIX app attach 將無法運作。 要求的核准可能需要最多24小時的工作天。 當您的要求已接受且完成時，您會收到一封電子郵件。

## <a name="install-certificates"></a>安裝憑證

您必須在主機集區中的所有工作階段主機上安裝憑證，以從您的 MSIX 應用程式附加套件裝載 ap。

如果您的應用程式使用不是公開信任或是自我簽署的憑證，以下是加以安裝的方法：

1. 以滑鼠右鍵按一下套件並選取 [屬性]。
2. 在出現的視窗中，選取 [數位簽章] 索引標籤。索引標籤上的清單中應該只有一個項目，如下圖所示。 選取該項目使其反白顯示，然後選取 [詳細資料]。
3. 當 [數位簽章詳細資料] 視窗出現時，選取 [一般] 索引標籤，然後選取 [**查看憑證**]，然後選取 **[** **安裝憑證**]。
4. 當安裝程式開啟時，請選取 [本機電腦] 作為您的儲存位置，然後選取 [下一步]。
5. 如果安裝程式詢問您是否要允許應用程式對您的裝置進行變更，請選取 [是]。
6. 選取 [將所有憑證放入以下的存放區]，然後選取 [瀏覽]。
7. 當選取憑證存放區視窗出現時，選取 [受信任的人]，然後選取 [確定]。
8. 選取 **[下一步** **] 和 [完成]**。

## <a name="enable-microsoft-hyper-v"></a>啟用 Microsoft Hyper-V

必須啟用 Microsoft Hyper-V，因為必須有 `Mount-VHD` 命令才能暫存，而且 `Dismount-VHD` 需要將它移出。

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>此變更需要您重新啟動虛擬機器。

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>準備 PowerShell 指令碼以進行 MSIX 應用程式附加

MSIX 應用程式附加有四個不同階段，必須依照下列順序執行：

1. 階段
2. 註冊
3. 取消註冊
4. 取消暫存

每個階段都會建立 PowerShell 指令碼。 [這裡](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach) \(英文\) 提供每個階段的範例指令碼。

### <a name="stage-powershell-script"></a>暫存 PowerShell 腳本

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
    **_ NO MOUNT POINTS _*_

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  使用您剛剛複製的磁片區 GUID 來更新 _ *$volumeGuid** 變數。

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

針對此指令碼，請將 **$packageName** 的預留位置取代為您所要測試套件的名稱。 在生產環境部署中，最好在關機時執行此功能。

```powershell
#MSIX app attach de staging sample

$vhdSrc="<path to vhd>"

#region variables
$packageName = "<package name>"
$msixJunction = "C:\temp\AppAttach"
#endregion

#region deregister
Remove-AppxPackage -AllUsers -Package $packageName
Remove-Item "$msixJunction\$packageName" -Recurse -Force -Verbose
#endregion

#region Detach VHD
Dismount-DiskImage -ImagePath $vhdSrc -Confirm:$false
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
