---
title: 在主要 VHD 映射上安裝 Office-Azure
description: 如何在 Windows 虛擬桌面的主要映射上安裝和自訂 Office 到 Azure。
author: Heidilohr
ms.topic: how-to
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3e53d8bf8f7cb024b468983f596d3d1bd5c91ee7
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88007296"
---
# <a name="install-office-on-a-master-vhd-image"></a>在主要 VHD 映像上安裝 Office

本文說明如何在主要虛擬硬碟上安裝適用于企業、OneDrive 和其他常見應用程式的 Microsoft 365 應用程式， (VHD) 映射上傳至 Azure。 如果您的使用者需要存取特定的企業營運 (LOB) 應用程式，我們建議您在完成本文中的指示之後加以安裝。

本文假設您已建立虛擬機器 (VM) 。 如果沒有，請參閱[準備和自訂主要 VHD 映射](set-up-customize-master-image.md#create-a-vm)

本文也假設您已在 VM 上擁有更高的存取權，無論是布建在 Azure 或 Hyper-v 管理員中。 如果沒有，請參閱提高[存取權以管理所有 Azure 訂用帳戶和管理群組](../role-based-access-control/elevate-access-global-admin.md)。

>[!NOTE]
>這些指示適用於可搭配您組織現有程序使用的 Windows 虛擬桌面特定設定。

## <a name="install-office-in-shared-computer-activation-mode"></a>在共用電腦啟用模式中安裝 Office

「共用電腦啟用」可讓您將企業 Microsoft 365 應用程式部署到組織中由多位使用者存取的電腦。 如需有關共用電腦啟用的詳細資訊，請參閱[Microsoft 365 應用程式的共用電腦啟用總覽](/deployoffice/overview-shared-computer-activation)。

使用[Office 部署工具](https://www.microsoft.com/download/details.aspx?id=49117)來安裝 office。 Windows 10 企業版多會話僅支援下列版本的 Office：

   - Microsoft 365 Apps 企業版
   - Microsoft 365 商務版 Premium 訂用帳戶隨附的 Microsoft 365 商務應用程式

Office 部署工具需要設定 XML 檔案。 若要自訂下列範例，請參閱[Office 部署工具](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/)的設定選項。

我們提供的這個範例設定 XML 會執行下列動作：

   - 從每月企業頻道安裝 Office，並從每月企業頻道傳遞更新。
   - 使用 x64 架構。
   - 停用自動更新。
   - 移除任何現有的 Office 安裝，並遷移其設定。
   - 啟用共用電腦啟動。

>[!NOTE]
>在 Windows 虛擬桌面中，Visio 的樣板搜尋功能可能無法如預期般運作。

以下是此範例設定 XML 不會執行的動作：

   - 安裝商務用 Skype
   - 以每個使用者模式安裝 OneDrive。 若要深入瞭解，請參閱[以每一電腦模式安裝 OneDrive](#install-onedrive-in-per-machine-mode)。

>[!NOTE]
>共用電腦啟用可透過群組原則物件 (Gpo) 或登錄設定來設定。 GPO 位於**電腦 \\ \\ 設定原則系統管理範本 \\ Microsoft Office 2016 (電腦) \\ 授權設定**

Office 部署工具組含 setup.exe。 若要安裝 Office，請在命令列中執行下列命令：

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>範例 configuration.xml

下列 XML 範例會安裝每月的企業通道版本。

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="MonthlyEnterprise">
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
>Office 小組建議針對**OfficeClientEdition**參數使用64位安裝。

安裝 Office 之後，您可以更新預設的 Office 行為。 個別執行下列命令，或在批次檔中，以更新行為。

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

## <a name="install-onedrive-in-per-machine-mode"></a>以每一電腦模式安裝 OneDrive

通常每位使用者會安裝 OneDrive。 在此環境中，應該每台電腦安裝一次。

以下說明如何以每一電腦模式安裝 OneDrive：

1. 首先，建立一個暫存 OneDrive 安裝程式的位置。 本機磁片資料夾或 [ \\ \\ unc] (file://unc) 位置正常。

2. 使用下列連結，將 OneDriveSetup.exe 下載到您的暫存位置：<https://aka.ms/OneDriveWVD-Installer>

3. 如果您省略了 office with OneDrive **\<ExcludeApp ID="OneDrive" /\>** ，請執行下列命令，從提升許可權的命令提示字元中，卸載任何現有的 OneDrive 每一使用者安裝：

    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. 從提高許可權的命令提示字元執行此命令，以設定**AllUsersInstall**登錄值：

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. 執行此命令，以每一電腦模式安裝 OneDrive：

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. 執行此命令，以設定讓 OneDrive 在登入所有使用者時啟動：

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. 藉由執行下列命令，啟用以無訊息方式**設定使用者帳戶**。

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. 執行下列命令，將 Windows 已知資料夾重新導向及移至 OneDrive。

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="microsoft-teams-and-skype-for-business"></a>Microsoft 團隊和商務用 Skype

Windows 虛擬桌面不支援商務用 Skype。

如需安裝 Microsoft 小組的協助，請參閱[在 Windows 虛擬桌面上使用 Microsoft 小組](teams-on-wvd.md)。 Windows 虛擬桌面上適用于 Microsoft 團隊的媒體優化現已開放預覽。

## <a name="next-steps"></a>後續步驟

現在您已將 Office 新增至映射，您可以繼續自訂主要 VHD 映射。 請參閱[準備和自訂主要 VHD 映射](set-up-customize-master-image.md)。
