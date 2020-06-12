---
title: 適用於 Windows 的自助式密碼重設 - Azure Active Directory
description: 如何在 Windows 登入畫面使用忘記密碼來啟用自助式密碼重設
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
ms.openlocfilehash: 95d1ffec6a849cb97a6151717c3e30dc362b1403
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826599"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>如何：從 Windows 登入畫面啟用密碼重設

針對執行 Windows 7、8、8.1 及 10 的電腦，您可以讓使用者在 Windows 登入畫面重設其密碼。 使用者不再需要尋找具有網頁瀏覽器的裝置以存取 [SSPR 入口網站](https://aka.ms/sspr)。

![顯示具有 SSPR 連結的範例 Windows 7 和 10 登入畫面](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>一般限制

- 目前不支援從遠端桌面或 Hyper-V 增強工作階段進行密碼重設。
- 已知某些協力廠商認證提供者會導致此功能發生問題。
- 已知透過修改 [EnableLUA 登錄機碼](https://docs.microsoft.com/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) \(英文\) 來停用 UAC 會造成問題。
- 此功能不適用於已部署 802.1x 網路驗證及使用 [在使用者登入前立即執行] 選項的網路。 針對已部署 802.1x 網路驗證的網路，建議您使用機器驗證來啟用此功能。
- 已加入混合式 Azure AD 的電腦必須網路連線且可看見網域控制站，才能使用新的密碼並更新快取的認證。 這表示裝置必須位於組織的內部網路，或是位於能透過網路存取內部部署網域控制站的 VPN。 
- 如果使用映像，請在執行 sysprep 之前，先確定已針對內建的 Administrator 清除 Web 快取，然後才執行 CopyProfile 步驟。 如需有關此步驟的詳細資訊，請參閱[使用自訂預設使用者設定檔時效能不佳](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile) \(機器翻譯\) 支援文章。
- 已知下列設定會影響在 Windows 10 裝置上使用及重設密碼的能力
    - 如果 v1809 前的 Windows 10 版本中的原則需要 Ctrl+Alt+Del，[重設密碼] 將無法運作。
    - 如果鎖定螢幕通知已關閉，[重設密碼] 將無法運作。
    - HideFastUserSwitching 設定為已啟用或 1
    - DontDisplayLastUserName 設定為已啟用或 1
    - NoLockScreen 設定為已啟用或 1
    - EnableLostMode 已設定於裝置上
    - Explorer.exe 會取代為自訂殼層
- 下列三個特定設定的組合，可能會導致此功能無法正常運作。
    - [互動式登入:不要求按 CTRL+ALT+DEL 鍵] = [已停用]
    - [DisableLockScreenAppNotifications] = 1 或 [已啟用]
    - Windows SKU 不是家用版或專業版

## <a name="windows-10-password-reset"></a>Windows 10 密碼重設

### <a name="windows-10-prerequisites"></a>Windows 10 先決條件

- 系統管理員必須從 Azure 入口網站啟用 [Azure AD 自助式密碼重設]。
- **使用者必須註冊 SSPR 才能使用此功能**
- 網路 Proxy 需求
   - Windows 10 裝置 
       - 針對 `passwordreset.microsoftonline.com` 和 `ajax.aspnetcdn.com` 開啟連接埠 443
       - Windows 10 裝置僅支援電腦層級 Proxy 設定
- 至少執行 2018 年 4 月更新版 (v1803) 的 Windows 10，且裝置必須是：
    - 已聯結的 Azure AD
    - 已聯結的混合式 Azure AD

### <a name="enable-for-windows-10-using-intune"></a>使用 Intune 針對 Windows 10 啟用

使用 Intune 部署組態變更，以啟用從登入畫面重設密碼的功能，是最具彈性的方法。 Intune 可讓您將組態變更部署至您所定義的特定機器群組。 必須完成裝置的 Intune 註冊，才能使用此方法。

#### <a name="create-a-device-configuration-policy-in-intune"></a>在 Intune 中建立裝置設定原則

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後按一下 [Intune]。
1. 移至 [裝置組態] > [設定檔] > [建立設定檔]，以建立新的裝置組態設定檔
   - 為設定檔提供有意義的名稱
   - 選擇性地提供有意義的設定檔描述
   - 平台：**Windows 10 和更新版本**
   - 設定檔類型：**自訂**
1. 配置 [設定]
   - **新增**下列 OMA-URI 設定來啟用 [重設密碼] 連結
      - 提供有意義的名稱，以說明正在進行的設定
      - 選擇性地提供有意義的設定描述
      - **OMA URI** 設為 `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - **資料類型** 設為 **整數**
      - **值** 設為 **1**
      - 按一下 [檔案] &gt; [新增] &gt; [專案] 
   - 按一下 [檔案] &gt; [新增] &gt; [專案] 
1. 按一下 [建立] 
1. 此原則可以指派給特定的使用者、裝置或群組。 如需詳細資訊，請參閱[在 Microsoft Intune 中指派使用者和裝置設定檔](https://docs.microsoft.com/intune/device-profile-assign)一文。

### <a name="enable-for-windows-10-using-the-registry"></a>使用登錄針對 Windows 10 啟用

1. 使用系統管理認證登入 Windows PC
1. 以系統管理員身分執行 **regedit**
1. 設定下列登錄機碼
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>對 Windows 10 密碼重設進行疑難排解

Azure AD 稽核記錄會包含 IP 位址相關資訊以及發生密碼重設的 ClientType。

![Azure AD 稽核記錄中的範例 Windows 7 密碼重設](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

當使用者從 Windows 10 裝置的登入畫面重設其密碼時，會建立稱為 `defaultuser1` 的低權限暫時帳戶。 此帳戶用來保護密碼重設程序。 此帳戶本身有隨機產生的密碼，該密碼不會針對裝置登入顯示，且在使用者重設其密碼後會自動移除。 可能會存在多個 `defaultuser` 設定檔，但您可以放心忽略。

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7、8 及 8.1 密碼重設

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7、8 及 8.1 先決條件

- 系統管理員必須從 Azure 入口網站啟用 [Azure AD 自助式密碼重設]。
- **使用者必須註冊 SSPR 才能使用此功能**
- 網路 Proxy 需求
   - Windows 7、8 及 8.1 裝置
       - 針對 `passwordreset.microsoftonline.com` 開啟連接埠 443
- 已修補的 Windows 7 或 Windows 8.1 作業系統。
- 已使用[傳輸層安全性 (TLS) 登錄設定](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)中所找到的指南啟用 TLS 1.2。
- 如果您的電腦上已啟用多個協力廠商認證提供者，使用者將會在登入畫面看到多個使用者設定檔。

> [!WARNING]
> 必須啟用 TLS 1.2，而非僅設定為自動協商

### <a name="install"></a>安裝

1. 下載適用於所要啟用 Windows 版本的安裝程式。
   - 您可以在 Microsoft 下載中心 ([https://aka.ms/sspraddin](https://aka.ms/sspraddin)) 取得軟體
1. 登入要安裝的電腦，並執行安裝程式。
1. 安裝之後，強烈建議您重新開機。
1. 重新開機後，在登入畫面中選擇使用者，然後按一下 [忘記密碼?] 來起始密碼重設工作流程。
1. 完成工作流程，遵循螢幕上的步驟來重設密碼。

![在 Windows7 中按下「忘記密碼？」的範例 SSPR 流程](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>無訊息安裝

- 若要進行無訊息安裝，請使用命令 "msiexec /i SsprWindowsLogon.PROD.msi /qn"
- 若要進行無訊息解除安裝，請使用命令 "msiexec /x SsprWindowsLogon.PROD.msi /qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>對 Windows 7、8 及 8.1 密碼重設進行疑難排解

電腦和 Azure AD 中都會記錄事件。 Azure AD 事件會包含 IP 位址相關資訊以及發生密碼重設的 ClientType。

![Azure AD 稽核記錄中的範例 Windows 7 密碼重設](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

如果需要額外的記錄，則可變更電腦上的登錄機碼來啟用詳細資訊記錄。 只能為了進行疑難排解而啟用詳細資訊記錄。

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- 若要啟用詳細資訊記錄，請建立 `REG_DWORD: "EnableLogging"`，並將其設定為 1。
- 若要停用詳細資訊記錄，請將 `REG_DWORD: "EnableLogging"` 變更為 0。

## <a name="what-do-users-see"></a>使用者看到的內容

在您為 Windows 裝置設定密碼重設之後，會對使用者造成哪些變更？ 他們如何得知可在登入畫面重設其密碼？

![顯示具有 SSPR 連結的範例 Windows 7 和 10 登入畫面](./media/howto-sspr-windows/windows-reset-password.png)

現在當使用者在嘗試登入時，即會看到 [重設密碼] 或 [忘記密碼] 連結，該連結可在登入畫面上開啟自助式密碼重設體驗。 這項功能可讓使用者重設其密碼，而不需使用另一個裝置來存取網頁瀏覽器。

您的使用者將在[重設您的工作或學校密碼](../user-help/active-directory-passwords-update-your-own-password.md)中發現使用這項功能的指引

## <a name="next-steps"></a>後續步驟

[規劃要允許的驗證方法](concept-authentication-methods.md)

[設定 Windows 10](https://docs.microsoft.com/windows/configuration/)
