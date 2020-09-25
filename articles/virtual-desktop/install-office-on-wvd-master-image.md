---
title: 在主要 VHD 映射上安裝 Office-Azure
description: 如何在 Windows 虛擬桌面主機映射上安裝和自訂 Office 至 Azure。
author: Heidilohr
ms.topic: how-to
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b6369013d605ae538ad611a28a90e9c099bb7d80
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326043"
---
# <a name="install-office-on-a-master-vhd-image"></a>在主要 VHD 映像上安裝 Office

本文將告訴您如何在主要虛擬硬碟上安裝適用于企業版、OneDrive 和其他常用應用程式的 Microsoft 365 應用程式， (VHD) 映射上傳至 Azure。 如果您的使用者需要存取某些企業營運 (LOB) 應用程式，建議您在完成本文中的指示之後安裝這些應用程式。

本文假設您已建立 (VM) 的虛擬機器。 如果沒有，請參閱 [準備和自訂主要 VHD 映射](set-up-customize-master-image.md#create-a-vm)

本文也假設您在 VM 上擁有較高的存取權，無論它是在 Azure 或 Hyper-v 管理員中布建。 如果沒有，請參閱 [提升存取權以管理所有 Azure 訂用帳戶和管理群組](../role-based-access-control/elevate-access-global-admin.md)。

>[!NOTE]
>這些指示適用於可搭配您組織現有程序使用的 Windows 虛擬桌面特定設定。

## <a name="install-office-in-shared-computer-activation-mode"></a>以共用電腦啟用模式安裝 Office

共用電腦啟用可讓您將適用于企業的 Microsoft 365 應用程式部署到組織中由多位使用者存取的電腦。 如需有關共用電腦啟用的詳細資訊，請參閱 [Microsoft 365 Apps 的共用電腦啟用總覽](/deployoffice/overview-shared-computer-activation)。

使用 [Office 部署工具](https://www.microsoft.com/download/details.aspx?id=49117) 來安裝 office。 Windows 10 企業版多個會話僅支援下列 Office 版本：

   - Microsoft 365 Apps 企業版
   - Microsoft 365 Microsoft 365 商務版 Premium 訂用帳戶隨附的商務應用程式

Office 部署工具需要設定 XML 檔案。 若要自訂下列範例，請參閱 [Office 部署工具](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/)的設定選項。

我們提供的這個範例設定 XML 會執行下列動作：

   - 從每月的 Enterprise 頻道安裝 Office，並從每月企業頻道傳遞更新。
   - 使用 x64 架構。
   - 停用自動更新。
   - 移除任何現有的 Office 安裝並遷移其設定。
   - 啟用共用電腦啟用。

>[!NOTE]
>在 Windows 虛擬桌面中，Visio 的樣板搜尋功能可能無法如預期般運作。

以下是此範例設定 XML 將不會執行的動作：

   - 安裝商務用 Skype
   - 以每一使用者模式安裝 OneDrive。 若要深入瞭解，請參閱 [安裝每一電腦的 OneDrive 模式](#install-onedrive-in-per-machine-mode)。

>[!NOTE]
>您可以透過群組原則物件 (Gpo) 或登錄設定來設定共用電腦啟用。 GPO 位於 **電腦 \\ \\ 設定原則系統管理範本 \\ Microsoft Office 2016 (電腦) \\ 授權設定**

Office 部署工具組含 setup.exe。 若要安裝 Office，請在命令列中執行下列命令：

```cmd
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>範例 configuration.xml

下列 XML 範例會安裝每月的 Enterprise 通道版本。

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
  <Logging Level="Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Office 小組建議針對 **OfficeClientEdition** 參數使用64位安裝。

安裝 Office 之後，您可以更新預設的 Office 行為。 個別或在批次檔中執行下列命令，以更新行為。

```cmd
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

每位使用者通常會安裝 OneDrive。 在此環境中，它應該安裝在每部電腦上。

以下說明如何以每部電腦模式安裝 OneDrive：

1. 首先，建立用來暫存 OneDrive 安裝程式的位置。 本機磁片資料夾或 [ \\ \\ unc] (file://unc) 位置是正常的。

2. 使用下列連結，將 OneDriveSetup.exe 下載到您的暫存位置： <https://aka.ms/OneDriveWVD-Installer>

3. 如果您省略安裝 office with OneDrive **\<ExcludeApp ID="OneDrive" /\>** ，請執行下列命令，從提升許可權的命令提示字元卸載任何現有的 onedrive 每位使用者安裝：

    ```cmd
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. 從提升許可權的命令提示字元中執行此命令，以設定 **AllUsersInstall** 登錄值：

    ```cmd
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. 執行此命令以每一電腦模式安裝 OneDrive：

    ```cmd
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. 執行此命令，將 OneDrive 設定為在所有使用者的登入時開始：

    ```cmd
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. 藉由執行下列命令，以無訊息方式 **設定使用者帳戶** 。

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. 藉由執行下列命令，將 Windows 已知資料夾重新導向並移至 OneDrive。

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="microsoft-teams-and-skype-for-business"></a>Microsoft 小組和商務用 Skype

Windows 虛擬桌面不支援商務用 Skype。

如需安裝 Microsoft 小組的協助，請參閱 [使用 Windows 虛擬桌面的 Microsoft 小組](teams-on-wvd.md)。 Windows 虛擬桌面的 Microsoft 團隊媒體優化可供預覽。

## <a name="next-steps"></a>後續步驟

現在您已將 Office 新增至映射，接下來您可以繼續自訂主要 VHD 映射。 請參閱 [準備和自訂主要 VHD 映射](set-up-customize-master-image.md)。
