---
title: 在主 VHD 映射上安裝 Office - Azure
description: 如何在 Windows 虛擬桌面主映射上安裝和自訂 Office 到 Azure。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b93f26a6799a50868feb1f3350a3dc4a73a0b2e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127848"
---
# <a name="install-office-on-a-master-vhd-image"></a>在主要 VHD 映像上安裝 Office

本文介紹如何在主虛擬硬碟 （VHD） 映射上安裝 Office 365 ProPlus、OneDrive 和其他常見應用程式，以便上載到 Azure。 如果使用者需要訪問某些業務線 （LOB） 應用程式，我們建議您在完成本文中的說明後安裝它們。

本文假定您已經創建了虛擬機器 （VM）。 如果沒有，請參閱[準備和自訂主 VHD 映射](set-up-customize-master-image.md#create-a-vm)

本文還假定您在 VM 上具有提升存取權限，無論是 Azure 還是 Hyper-V 管理器中預配。 如果沒有，請參閱[提升存取權限以管理所有 Azure 訂閱和管理組](../role-based-access-control/elevate-access-global-admin.md)。

>[!NOTE]
>這些指示適用於可搭配您組織現有程序使用的 Windows 虛擬桌面特定設定。

## <a name="install-office-in-shared-computer-activation-mode"></a>以共用電腦啟動模式安裝 Office

通過共用電腦啟動，您可以將 Office 365 ProPlus 部署到組織中由多個使用者訪問的電腦。 有關共用電腦啟動的詳細資訊，請參閱[Office 365 ProPlus 的共用電腦啟動概述](/deployoffice/overview-of-shared-computer-activation-for-office-365-proplus/)。

使用[Office 部署工具](https://www.microsoft.com/download/details.aspx?id=49117)安裝 Office。 Windows 10 企業多會話僅支援以下版本的 Office：
- Office 365 ProPlus
- 微軟 365 業務訂閱附帶的 Office 365 業務

Office 部署工具需要設定檔 XML 檔。 要自訂以下示例，請參閱[Office 部署工具的配置選項](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/)。

我們提供的此示例配置 XML 將執行以下操作：

- 從每月頻道安裝 Office，並在執行時從每月頻道提供更新。
- 使用 x64 體系結構。
- 禁用自動更新。
- 刪除 Office 的任何現有安裝並遷移其設置。
- 啟用共用電腦啟動。

>[!NOTE]
>Visio 的模具搜索功能可能無法在 Windows 虛擬桌面中按預期工作。

下面是此示例配置 XML 不會執行的內容：

- 安裝 Skype 業務
- 以每個使用者模式安裝 OneDrive。 要瞭解更多資訊，請參閱[在每台電腦模式下安裝 OneDrive。](#install-onedrive-in-per-machine-mode)

>[!NOTE]
>可通過群組原則物件 （GPO） 或註冊表設置設置共用電腦啟動。 GPO 位於**電腦\\配置策略\\管理範本\\Microsoft Office 2016 （機器\\） 許可設置**

"辦公室部署工具"包含 setup.exe。 要安裝 Office，請在命令列中運行以下命令：

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>示例配置.xml

以下 XML 示例將安裝每月版本。

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="Monthly">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level=" Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Office 團隊建議對**OfficeClientEdition**參數使用 64 位安裝。

安裝 Office 後，可以更新預設的 Office 行為。 單獨或在批次檔中運行以下命令以更新行為。

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>在每台電腦模式下安裝 OneDrive

OneDrive 通常按使用者安裝。 在此環境中，應按電腦安裝它。

以下是在每台電腦模式下安裝 OneDrive 的方式：

1. 首先，創建一個位置來暫達 OneDrive 安裝程式。 本地磁片資料夾或 [\\\\unc] （file://unc） 位置正常。

2. 使用此連結將 OneDriveSetup.exe 下載到您的暫存位置：<https://aka.ms/OneDriveWVD-Installer>

3. 如果您通過省略**\<排除應用 ID="OneDrive"/\>** 使用 OneDrive 安裝辦公室，請通過運行以下命令從提升的命令提示符卸載任何現有的 OneDrive 每個使用者安裝：
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. 從提升的命令提示符運行此命令以設置**AllUser 安裝**註冊表值：

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. 運行此命令以在每台電腦模式下安裝 OneDrive：

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. 運行此命令可配置 OneDrive 以在所有使用者登錄時開始：

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. 通過運行以下命令**啟用靜默配置使用者帳戶**。

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. 通過運行以下命令將 Windows 已知資料夾重定向並將其移動到 OneDrive。

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>團隊和 Skype

Windows 虛擬桌面不支援 Skype 業務和團隊。

## <a name="next-steps"></a>後續步驟

現在，您已將 Office 添加到映射中，可以繼續自訂主 VHD 映射。 請參閱[準備和自訂主 VHD 映射](set-up-customize-master-image.md)。
