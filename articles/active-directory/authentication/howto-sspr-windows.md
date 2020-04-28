---
title: Windows 的自助式密碼重設-Azure Active Directory
description: 如何在 Windows 登入畫面上使用忘記密碼來啟用自助式密碼重設
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4f08161daf1d9c1a4431d9e3fba3ca741d88b16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80743338"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>如何：從 Windows 登入畫面啟用密碼重設

對於執行 Windows 7、8、8.1 和10的電腦，您可以讓使用者在 Windows 登入畫面重設其密碼。 使用者不再需要使用網頁瀏覽器來尋找裝置，即可存取[SSPR 入口](https://aka.ms/sspr)網站。

![顯示 SSPR 連結的 Windows 7 和10登入畫面範例](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>一般限制

- 目前不支援從遠端桌面或 Hyper-v 增強式會話進行密碼重設。
- 某些協力廠商認證提供者已知會導致此功能發生問題。
- 藉由修改 EnableLUA 登錄機[碼](https://docs.microsoft.com/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec)來停用 UAC，已知會造成問題。
- 這項功能不適用於已部署 802.1 x 網路驗證的網路，以及 [使用者登入前立即執行] 選項。 針對已部署 802.1x 網路驗證的網路，建議您使用機器驗證來啟用此功能。
- 混合式 Azure AD 聯結的機器必須能夠看到網域控制站的網路連線，才能使用新密碼並更新快取的認證。
- 如果使用映射，在執行 sysprep 之前，請先確定已針對內建的系統管理員清除 web 快取，再執行 CopyProfile 步驟。 如需此步驟的詳細資訊，請參閱[使用自訂預設使用者設定檔時](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile)的支援文章效能不佳。
- 已知下列設定會影響在 Windows 10 裝置上使用和重設密碼的能力。
    - 如果 v1809 前的 Windows 10 版本中的原則需要 Ctrl+Alt+Del，[重設密碼]**** 將無法運作。
    - 如果鎖定螢幕通知已關閉，[重設密碼]**** 將無法運作。
    - HideFastUserSwitching 設定為已啟用或 1
    - DontDisplayLastUserName 設定為已啟用或 1
    - NoLockScreen 設定為已啟用或 1
    - EnableLostMode 已設定於裝置上
    - Explorer.exe 會取代為自訂殼層
- 下列特定三個設定的組合，可能會導致這項功能無法正常執行。
    - 互動式登入：不需要 CTRL + ALT + DEL = 已停用
    - DisableLockScreenAppNotifications = 1 或 Enabled
    - Windows SKU 不是首頁或專業版

## <a name="windows-10-password-reset"></a>Windows 10 密碼重設

### <a name="windows-10-prerequisites"></a>Windows 10 必要條件

- 系統管理員必須從 Azure 入口網站啟用 Azure AD 自助式密碼重設。
- **使用者必須先註冊 SSPR，才能使用此功能**
- 網路 proxy 需求
   - Windows 10 裝置 
       - 埠443至`passwordreset.microsoftonline.com`和`ajax.aspnetcdn.com`
       - Windows 10 裝置僅支援電腦層級 proxy 設定
- 至少執行 Windows 10，版本2018年4月更新（v1803），且裝置必須是：
    - 已聯結的 Azure AD
    - 已聯結的混合式 Azure AD

### <a name="enable-for-windows-10-using-intune"></a>使用 Intune 啟用 Windows 10

使用 Intune 部署組態變更，以啟用從登入畫面重設密碼的功能，是最具彈性的方法。 Intune 可讓您將組態變更部署至您所定義的特定機器群組。 必須完成裝置的 Intune 註冊，才能使用此方法。

#### <a name="create-a-device-configuration-policy-in-intune"></a>在 Intune 中建立裝置設定原則

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後按一下 [Intune]****。
1.  > 前往 [**裝置** > **設定設定檔**] [**建立設定檔**] 來建立新的裝置設定檔
   - 為設定檔提供有意義的名稱
   - 選擇性地提供有意義的設定檔描述
   - 平臺**Windows 10 和更新版本**
   - 設定檔類型：**自訂**
1. 進行**設定**
   - **新增**下列 OMA-URI 設定來啟用 [重設密碼] 連結
      - 提供有意義的名稱，以說明正在進行的設定
      - 選擇性地提供有意義的設定描述
      - **OMA URI** 設為 `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - **資料類型** 設為 **整數**
      - **值** 設為 **1**
      - 按一下 [檔案] &gt; [新增] &gt; [專案] 
   - 按一下 [檔案] &gt; [新增] &gt; [專案] 
1. 按一下 [建立] 
1. 此原則可指派給特定的使用者、裝置或群組。 如需詳細資訊，請參閱在[Microsoft Intune 中指派使用者和裝置設定檔](https://docs.microsoft.com/intune/device-profile-assign)一文。

### <a name="enable-for-windows-10-using-the-registry"></a>使用登錄為 Windows 10 啟用

1. 使用系統管理認證登入 Windows PC
1. 以系統管理員身分執行 **regedit**
1. 設定下列登錄機碼
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Windows 10 密碼重設疑難排解

Azure AD 稽核記錄會包含 IP 位址相關資訊以及發生密碼重設的 ClientType。

![Azure AD Audit 記錄檔中的範例 Windows 7 密碼重設](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

當使用者從 Windows 10 裝置的登入畫面重設其密碼時，會建立名`defaultuser1`為的低許可權暫存帳戶。 此帳戶用來保護密碼重設程序。 帳戶本身具有隨機產生的密碼，並不會顯示裝置登入，而且會在使用者重設其密碼之後自動移除。 可能`defaultuser`存在多個設定檔，但可以放心忽略。

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7、8和8.1 密碼重設

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7、8和8.1 必要條件

- 系統管理員必須從 Azure 入口網站啟用 Azure AD 自助式密碼重設。
- **使用者必須先註冊 SSPR，才能使用此功能**
- 網路 proxy 需求
   - Windows 7、8和8.1 裝置
       - 埠443至`passwordreset.microsoftonline.com`
- 已修補的 Windows 7 或 Windows 8.1 作業系統。
- 已使用[傳輸層安全性 (TLS) 登錄設定](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)中所找到的指南啟用 TLS 1.2。
- 如果您的電腦上已啟用一個以上的協力廠商認證提供者，使用者會在登入畫面上看到一個以上的使用者設定檔。

> [!WARNING]
> 必須啟用 TLS 1.2，而不只是設定為自動協商

### <a name="install"></a>安裝

1. 下載適用於所要啟用 Windows 版本的安裝程式。
   - 您可以從 Microsoft 下載中心取得軟體[https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. 登入要安裝的電腦，並執行安裝程式。
1. 安裝之後，強烈建議您重新開機。
1. 重新開機之後，在登入畫面上選擇使用者，然後按一下 [忘記密碼？] 來起始密碼重設工作流程。
1. 完成工作流程，遵循螢幕上的步驟來重設密碼。

![在 Windows7 中按下「忘記密碼？」的範例 SSPR 流程](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>無訊息安裝

- 若要進行無訊息安裝，請使用 "msiexec/i Ssprwindowslogon.prod.msi" 命令。
- 若要進行無訊息卸載，請使用 "msiexec/x Ssprwindowslogon.prod.msi" 命令。

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>針對 Windows 7、8和8.1 密碼重設進行疑難排解

電腦和 Azure AD 中都會記錄事件。 Azure AD 事件會包含 IP 位址相關資訊以及發生密碼重設的 ClientType。

![Azure AD Audit 記錄檔中的範例 Windows 7 密碼重設](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

如果需要額外的記錄，則可變更電腦上的登錄機碼來啟用詳細資訊記錄。 只能為了進行疑難排解而啟用詳細資訊記錄。

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- 若要啟用詳細資訊記錄， `REG_DWORD: "EnableLogging"`請建立，並將它設定為1。
- 若要停用詳細資訊記錄`REG_DWORD: "EnableLogging"` ，請將變更為0。

## <a name="what-do-users-see"></a>使用者看到的內容

既然您已設定 Windows 裝置的密碼重設，使用者會有哪些變更？ 他們如何得知可在登入畫面重設其密碼？

![顯示 SSPR 連結的 Windows 7 和10登入畫面範例](./media/howto-sspr-windows/windows-reset-password.png)

當使用者嘗試登入時，他們現在會看到 [**重設密碼**] 或 [**忘記密碼**] 連結，可在登入畫面開啟自助式密碼重設體驗。 這項功能可讓使用者重設其密碼，而不需使用另一個裝置來存取網頁瀏覽器。

您的使用者將在[重設您的工作或學校密碼](../user-help/active-directory-passwords-update-your-own-password.md)中發現使用這項功能的指引

## <a name="next-steps"></a>後續步驟

[規劃驗證方法以允許](concept-authentication-methods.md)

[設定 Windows 10](https://docs.microsoft.com/windows/configuration/)
