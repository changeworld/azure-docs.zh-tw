---
title: 設置 Windows 範本電腦的指南 |微軟文檔
description: 在實驗室服務中準備 Windows 範本電腦的通用步驟。  這些步驟包括設置 Windows 更新計畫、安裝 OneDrive 和安裝 Office。
services: lab-services
documentationcenter: na
author: EMaher
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 11/21/2019
ms.author: enewman
ms.openlocfilehash: c52a1212d160adce3a0a0638164833bc2907a856
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514998"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>在 Azure 實驗室服務中設置 Windows 範本電腦的指南

如果要為 Azure 實驗室服務設置 Windows 10 範本電腦，下面是一些要考慮的最佳做法和提示。 以下配置步驟都是可選的。  但是，這些準備步驟可以説明提高學生的效率，最大限度地減少上課時間中斷，並確保他們使用最新的技術。

>[!IMPORTANT]
>本文包含 PowerShell 程式碼片段，以簡化機器範本修改過程。  對於顯示的所有 PowerShell 腳本，您需要在 Windows PowerShell 中具有管理員許可權來運行它們。 在 Windows 10 中，快速方法是按右鍵"開始"功能表並選擇"Windows PowerShell（管理員）"。

## <a name="install-and-configure-onedrive"></a>安裝和配置 OneDrive

為了保護學生資料在重置虛擬機器時丟失，我們建議學生將資料備份到雲中。  Microsoft OneDrive 可以説明學生保護他們的資料。  

### <a name="install-onedrive"></a>安裝 OneDrive

要手動下載和安裝 OneDrive，請參閱[OneDrive](https://onedrive.live.com/about/download/)或[OneDrive 業務](https://onedrive.live.com/about/business/)下載頁面。

您還可以使用以下 PowerShell 腳本。  它將自動下載並安裝最新版本的 OneDrive。  安裝 OneDrive 用戶端後，請運行安裝程式。  在我們的示例中，`/allUsers`我們使用開關為電腦上的所有使用者安裝 OneDrive。 我們還使用`/silent`該開關靜默安裝 OneDrive。

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

### <a name="onedrive-customizations"></a>OneDrive 定制

有許多[自訂項可以執行到 OneDrive](https://docs.microsoft.com/onedrive/use-group-policy)。 讓我們介紹一些更常見的自訂項。

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>靜默將 Windows 已知資料夾移動到 OneDrive

文檔、下載和圖片等資料夾通常用於存儲學生檔。 為了確保這些資料夾已備份到 OneDrive 中，我們建議您將這些資料夾移動到 OneDrive。

如果您使用的是不使用 Active Directory 的電腦上，使用者可以在將這些資料夾驗證為 OneDrive 後手動將這些資料夾移動到 OneDrive。

1. 開啟檔案總管
2. 按右鍵"文檔"、"下載"或"圖片"資料夾。
3. 轉到屬性>位置。  將資料夾移動到 OneDrive 目錄上的新資料夾。

如果您的虛擬機器已連接到 Active Directory，則可以將範本電腦設置為自動提示學生將已知資料夾移動到 OneDrive。  

您需要首先檢索 Office 租戶 ID。  有關進一步說明，請參閱[查找 Office 365 租戶 ID](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id)。  您還可以使用以下 PowerShell 獲取 Office 365 租戶 ID。

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

獲得 Office 365 租戶 ID 後，將 OneDrive 設置為使用以下 PowerShell 提示將已知資料夾移動到 OneDrive。

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>按需使用 OneDrive 檔

學生在其 OneDrive 帳戶中可能有許多檔。 為了説明節省電腦上的空間並減少下載時間，我們建議使存儲在學生 OneDrive 帳戶中的所有檔都是按需訪問的。  按需檔僅在使用者訪問該檔後下載。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>靜默地將使用者登錄到 OneDrive

OneDrive 可以設置為使用登錄使用者的 Windows 憑據自動登入。  自動登入對於學生使用 Office 365 學校憑據登錄的班級非常有用。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>禁用 OneDrive 設置末尾顯示的教程

此設置允許您阻止在 OneDrive 安裝程式結束時在 Web 瀏覽器中啟動本教程。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>設置要自動下載的檔的最大大小

此設置與在未啟用 OneDrive 檔按需狀態的設備上使用 Windows 憑據的"靜默登錄使用者"一起使用。 任何具有大於指定閾值（MB）的 OneDrive 的使用者都將在 OneDrive 同步用戶端 （OneDrive.exe） 下載檔案之前選擇要同步的資料夾。  在我們的示例中，"1111-2222-3333-4444"是 Office 365 租戶 ID，0005000 設置閾值為 5 GB。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-office-365"></a>安裝和配置微軟 Office 365

### <a name="install-microsoft-office-365"></a>安裝微軟辦公室 365

如果您的範本電腦需要 Office，我們建議通過[Office 部署工具 （ODT）](https://www.microsoft.com/download/details.aspx?id=49117 )安裝 Office。 您需要使用[Office 365 用戶端佈建服務](https://config.office.com/)創建可重用的設定檔，以選擇需要 Office 的體系結構、哪些功能以及更新頻率。

1. 轉到[Office 365 用戶端佈建服務](https://config.office.com/)並下載您自己的設定檔。
2. 下載[辦公室部署工具](https://www.microsoft.com/download/details.aspx?id=49117)。  下載的檔將是`setup.exe`。
3. 運行`setup.exe /download configuration.xml`以下載 Office 元件。
4. 運行`setup.exe /configure configuration.xml`以安裝 Office 元件。

### <a name="change-the-microsoft-office-365-update-channel"></a>更改 Microsoft Office 365 更新通道

使用 Office 組態工具，您可以設置 Office 接收更新的頻率。 但是，如果需要修改 Office 在安裝後接收更新的頻率，可以更改更新通道 URL。 更新通道 URL 位址可在[更改組織中的設備的 Office 365 ProPlus 更新通道](https://docs.microsoft.com/deployoffice/change-update-channels)中找到。 下面的示例演示如何將 Office 365 設置為使用每月更新通道。

```powershell
# Update to the Office 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>安裝和配置更新

### <a name="install-the-latest-windows-updates"></a>安裝最新的 Windows 更新

我們建議您在發佈範本 VM 之前在範本電腦上安裝最新的 Microsoft 更新，以進行安全記事。  當更新在意外時間運行時，它還可能避免學生在工作中受到干擾。

1. 從"開始"功能表啟動**設置**
2. 按一下**更新**&安全性
3. 按一下 **"檢查更新"**
4. 更新將下載並安裝。

您還可以使用 PowerShell 更新範本電腦。

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>某些更新可能需要重新開機電腦。  如果需要重新開機，系統將提示您。

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>安裝 Microsoft 應用商店應用的最新更新

我們建議將所有 Microsoft 應用商店應用更新到最新版本。  以下是從 Microsoft 應用商店手動更新應用程式的說明。  

1. 啟動**微軟應用商店**應用程式。
2. 按一下應用程式右上角的使用者照片旁邊的橢圓 （...）。
3. 從下拉式功能表中選擇 **"下載**並更新"。
4. 按一下 **"獲取更新**"按鈕。

您還可以使用 Powershell 更新已安裝的 Microsoft 應用商店應用程式。

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>停止自動 Windows 更新

將 Windows 更新到最新版本後，可以考慮停止 Windows 更新。  自動更新可能會干擾計畫的類時間。  如果您的課程執行時間較長，請考慮要求學生手動檢查更新或設置計畫上課時間以外的時間的自動更新。  有關 Windows 更新的自訂選項的詳細資訊，請參閱[管理其他 Windows 更新設置](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings)。

可以使用以下 PowerShell 腳本停止自動 Windows 更新。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>安裝外語包

如果需要在虛擬機器上安裝其他語言，可以通過 Microsoft 應用商店添加這些語言。

1. 啟動微軟商店
2. 搜索"語言包"
3. 選擇要安裝的語言

如果您已登錄到範本 VM，請使用["安裝語言包"快捷方式](ms-settings:regionlanguage?activationSource=SMC-IA-4027670)直接轉到相應的設置頁。

## <a name="remove-unneeded-built-in-apps"></a>刪除不需要的內置應用

Windows 10 附帶了許多特定類可能不需要的內置應用程式。 為了簡化學生的機器映射，您可能需要從範本電腦卸載某些應用程式。  要查看已安裝應用程式的清單，請使用 PowerShell `Get-AppxPackage` Cmdlet。  下面的示例顯示了可以刪除的所有已安裝的應用程式。

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

要刪除應用程式，請使用"刪除-Appx Cmdlet"。  下面的示例演示如何刪除與 XBox 相關的一切。

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>安裝常見的教學相關應用程式

安裝通常用於通過 Windows 應用商店應用進行教學的其他應用。 建議包括應用程式，如[微軟白板應用程式](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)，[微軟團隊](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)，和[我的世界教育版](https://education.minecraft.net/)。 這些應用程式必須通過 Windows 應用商店或範本 VM 上的相應網站手動安裝。

## <a name="conclusion"></a>結論

本文已為您提供可選步驟，用於為有效的類準備 Windows 範本 VM。  步驟包括安裝 OneDrive 和安裝 Office 365、安裝 Windows 更新和安裝 Microsoft 應用商店應用的更新。  我們還討論了如何將更新設置為最適合您的類的計畫。  
