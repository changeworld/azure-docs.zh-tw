---
title: 設定 Windows 範本電腦的指南 |Microsoft Docs
description: 在實驗室服務中準備 Windows 範本電腦的一般步驟。  這些步驟包括設定 Windows Update 排程、安裝 OneDrive，以及安裝 Office。
author: EMaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 216dc843b31eac355e1d818014f3d70b2ef83132
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647897"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>在 Azure 實驗室服務中設定 Windows 範本電腦的指南

如果您要設定適用于 Azure 實驗室服務的 Windows 10 範本電腦，以下提供一些最佳作法和秘訣。 下列設定步驟都是選擇性的。  不過，這些準備步驟可以協助您的學生提高生產力、將類別時間中斷降到最低，並確保他們使用的是最新技術。

>[!IMPORTANT]
>本文包含 PowerShell 程式碼片段，可簡化機器範本修改程式。  針對所有顯示的 PowerShell 腳本，您會想要在 Windows PowerShell 中使用系統管理員許可權來執行這些腳本。 在 Windows 10 中，快速地執行這項操作的方法是以滑鼠右鍵按一下 [開始] 功能表，然後選擇 [Windows PowerShell (系統管理員) ]。

## <a name="install-and-configure-onedrive"></a>安裝和設定 OneDrive

若要防止學生資料在虛擬機器重設時遺失，建議學生將其資料備份至雲端。  Microsoft OneDrive 可協助學生保護其資料。  

### <a name="install-onedrive"></a>安裝 OneDrive

若要手動下載並安裝 OneDrive，請參閱 [onedrive](https://onedrive.live.com/about/download/) 或 [商務用 onedrive](https://onedrive.live.com/about/business/) 下載頁面。

您也可以使用下列 PowerShell 腳本。  它會自動下載並安裝最新版本的 OneDrive。  安裝 OneDrive 用戶端之後，請執行安裝程式。  在我們的範例中，我們使用 `/allUsers` 參數來為電腦上的所有使用者安裝 OneDrive。 我們也使用 `/silent` 參數以無訊息方式安裝 OneDrive。

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>OneDrive 自訂

有許多 [可對 OneDrive 進行的自訂](/onedrive/use-group-policy)。 讓我們來討論一些較常見的自訂。

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>以無訊息方式將 Windows 已知資料夾移至 OneDrive

檔、下載和圖片等資料夾通常用來儲存學生檔案。 為了確保將這些資料夾備份到 OneDrive，建議您將這些資料夾移至 OneDrive。

如果您在未使用 Active Directory 的電腦上，使用者可以在向 OneDrive 驗證之後，手動將這些資料夾移至 OneDrive。

1. 開啟檔案總管
2. 以滑鼠右鍵按一下 [檔]、[下載] 或 [圖片] 資料夾。
3. 移至 [屬性] > 位置。  將資料夾移至 OneDrive 目錄上的新資料夾。

如果您的虛擬機器已連線到 Active Directory，您可以將範本電腦設定為自動提示您的學生將已知資料夾移至 OneDrive。  

您必須先取得您的組織識別碼。  如需進一步的指示，請參閱 [尋找您的 Microsoft 365 組織識別碼](/onedrive/find-your-office-365-tenant-id)。  您也可以使用下列 PowerShell 取得組織識別碼。

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

擁有您的組織識別碼之後，請使用下列 PowerShell 將 OneDrive 設定為提示將已知資料夾移至 OneDrive。

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>視需要使用 OneDrive 檔案

學生可能在其 OneDrive 帳戶內有許多檔案。 為了協助節省機器上的空間並縮短下載時間，建議您將儲存在學生 OneDrive 帳戶中的所有檔案都設為隨選。  隨選檔案只會在使用者存取檔案時下載。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>以無訊息方式將使用者登入 OneDrive

OneDrive 可以設定為使用已登入使用者的 Windows 認證自動登入。  自動登入適用于學生以學校認證登入的類別。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>停用 OneDrive 安裝結束時出現的教學課程

這項設定可讓您防止教學課程在 OneDrive 安裝程式結束時，于網頁瀏覽器中啟動。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>設定要自動下載之檔案的大小上限

這項設定會搭配以無訊息方式將使用者登入到 OneDrive 同步處理用戶端，並在未啟用 OneDrive 檔案的裝置上使用他們的 Windows 認證。 如果任何使用者的 OneDrive 大於指定的閾值 (MB) 將會在 OneDrive 同步處理用戶端 ( # A0) 下載檔案之前，提示您選擇要同步的資料夾。  在我們的範例中，"1111-2222-3333-4444" 是組織識別碼，0005000會將臨界值設定為 5 GB。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-365"></a>安裝和設定 Microsoft 365

### <a name="install-microsoft-365"></a>安裝 Microsoft 365

如果您的範本電腦需要 Office，建議您透過 [Office Deployment Tool (ODT) ](https://www.microsoft.com/download/details.aspx?id=49117)安裝 office。 您將需要使用 [Microsoft 365 Apps 系統管理中心](https://config.office.com/) 建立可重複使用的設定檔，以選擇您需要的架構、Office 所需的功能，以及更新的頻率。

1. 移至 [Microsoft 365 Apps 系統管理中心](https://config.office.com/) ，並下載您自己的設定檔。
2. 下載 [Office 部署工具](https://www.microsoft.com/download/details.aspx?id=49117)。  下載的檔案將會是 `setup.exe` 。
3. 執行 `setup.exe /download configuration.xml` 以下載 Office 元件。
4. 執行 `setup.exe /configure configuration.xml` 以安裝 Office 元件。

### <a name="change-the-microsoft-365-update-channel"></a>變更 Microsoft 365 更新通道

您可以使用 Office Configuration Tool 設定 Office 接收更新的頻率。 但是，如果您需要修改 Office 在安裝後接收更新的頻率，您可以變更更新通道 URL。 您可以在 [變更組織中裝置的 Microsoft 365 Apps 更新通道](/deployoffice/change-update-channels)，找到更新通道 URL 位址。 下列範例顯示如何將 Microsoft 365 設定為使用每月更新通道。

```powershell
# Update to the Microsoft 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>安裝和設定更新

### <a name="install-the-latest-windows-updates"></a>安裝最新的 Windows 更新

基於安全性考慮，建議您在範本電腦上安裝最新的 Microsoft 更新，然後再發佈範本 VM。  當更新在非預期的時間執行時，它也可能會避免學生中斷其工作。

1. 從 [開始] 功能表啟動 **設定**
2. 按一下 [ **更新** & 安全性]
3. 按一下 [**檢查更新**]
4. 更新將會下載並安裝。

您也可以使用 PowerShell 來更新範本電腦。

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>某些更新可能需要重新開機電腦。  如果需要重新開機，系統將會提示您。

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>安裝 Microsoft Store 應用程式的最新更新

建議您將所有的 Microsoft Store 應用程式更新為最新版本。  以下是從 Microsoft Store 手動更新應用程式的指示。  

1. 啟動 **Microsoft Store** 應用程式。
2. 按一下應用程式右上角的使用者相片旁邊的省略號 ( ... ) 。
3. 從下拉式功能表中選取 [ **下載** 和更新]。
4. 按一下 [ **取得更新** ] 按鈕。

您也可以使用 PowerShell 來更新已經安裝的 Microsoft Store 應用程式。

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>停止自動 Windows 更新

將 Windows 更新至最新版本之後，您可以考慮停止 Windows Update。  自動更新可能會干擾已排程的類別時間。  如果您的課程執行時間較長，請考慮要求學生手動檢查更新，或在已排程的類別時數以外的時間設定自動更新。  如需 Windows Update 自訂選項的詳細資訊，請參閱 [管理其他 Windows Update 設定](/windows/deployment/update/waas-wu-settings)。

自動 Windows Update 可能會使用下列 PowerShell 腳本停止。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>安裝外部語言套件

如果您需要在虛擬機器上安裝其他語言，您可以透過 Microsoft Store 新增這些語言。

1. 啟動 Microsoft Store
2. 搜尋「語言套件」
3. 選擇要安裝的語言

如果您已經登入範本 VM，請使用 () 的 [安裝語言套件] 快捷方式， `ms-settings:regionlanguage?activationSource=SMC-IA-4027670` 直接移至適當的設定頁面。

## <a name="remove-unneeded-built-in-apps"></a>移除不必要的內建應用程式

Windows 10 隨附許多您的特定類別可能不需要的內建應用程式。 為了簡化學生的機器映射，您可能會想要從範本電腦卸載一些應用程式。  若要查看已安裝的應用程式清單，請使用 PowerShell `Get-AppxPackage` Cmdlet。  下列範例會顯示所有可移除的已安裝應用程式。

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

若要移除應用程式，請使用 Remove-Appx Cmdlet。  下列範例顯示如何移除 XBox 相關的所有專案。

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>安裝常見的教學相關應用程式

安裝通常用於教學 Windows Store 應用程式的其他應用程式。 建議包括 [Microsoft 白板應用](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)程式、 [Microsoft 小組](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)和 [Minecraft 教育版](https://education.minecraft.net/)等應用程式。 這些應用程式必須透過 Windows Store 手動安裝，或透過其在範本 VM 上的個別網站來安裝。

## <a name="conclusion"></a>結論

本文已說明準備 Windows 範本 VM 以取得有效類別的選擇性步驟。  步驟包括安裝 OneDrive 和安裝 Microsoft 365、安裝 Windows 的更新，以及安裝 Microsoft Store 應用程式的更新。  我們也討論了如何將更新設定為最適合您類別的排程。  

## <a name="next-steps"></a>後續步驟
請參閱文章，以瞭解如何控制 Windows 關機行為以協助管理成本： [控制 windows 關機行為的指南](how-to-windows-shutdown.md)