---
title: 適用于 Windows 裝置的自助式密碼重設-Azure Active Directory
description: 瞭解如何在 Windows 登入畫面上啟用 Azure Active Directory 自助式密碼重設。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6013ed47196e2300f56f0066c634da2a64fdee8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90526862"
---
# <a name="enable-azure-active-directory-self-service-password-reset-at-the-windows-sign-in-screen"></a>在 Windows 登入畫面上啟用 Azure Active Directory 自助式密碼重設

自助式密碼重設 (SSPR) 可讓 Azure Active Directory (Azure AD 中的使用者可以變更或重設其密碼，而不需要系統管理員或技術支援中心介入。 一般情況下，使用者會在另一個裝置上開啟網頁瀏覽器，以存取 [SSPR 入口網站](https://aka.ms/sspr)。 若要改善執行 Windows 7、8、8.1 和10之電腦的體驗，您可以讓使用者在 Windows 登入畫面重設其密碼。

![顯示具有 SSPR 連結的範例 Windows 7 和 10 登入畫面](./media/howto-sspr-windows/windows-reset-password.png)

> [!IMPORTANT]
> 本教學課程說明如何在企業中啟用 Windows 裝置的 SSPR。
>
> 如果您的 IT 團隊未啟用從 Windows 裝置使用 SSPR 的能力，或您在登入期間遇到問題，請洽詢您的技術服務人員以取得其他協助。

## <a name="general-limitations"></a>一般限制

從 Windows 登入畫面使用 SSPR 適用下列限制：

- 遠端桌面或 Hyper-v 增強的會話目前不支援密碼重設。
- 已知某些協力廠商認證提供者會導致此功能發生問題。
- 已知透過修改 [EnableLUA 登錄機碼](/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) \(英文\) 來停用 UAC 會造成問題。
- 這項功能不適用於已部署 802.1 x 網路驗證的網路，以及「在使用者登入前立即執行」選項。 針對已部署 802.1 x 網路驗證的網路，建議使用「電腦驗證」來啟用這項功能。
- 已加入混合式 Azure AD 的電腦必須網路連線且可看見網域控制站，才能使用新的密碼並更新快取的認證。 這表示裝置必須位於組織的內部網路，或是位於能透過網路存取內部部署網域控制站的 VPN。
- 如果使用映像，請在執行 sysprep 之前，先確定已針對內建的 Administrator 清除 Web 快取，然後才執行 CopyProfile 步驟。 如需有關此步驟的詳細資訊，請參閱[使用自訂預設使用者設定檔時效能不佳](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile) \(機器翻譯\) 支援文章。
- 已知下列設定會干擾在 Windows 10 裝置上使用和重設密碼的能力：
    - 如果 v1909 之前的 Windows 10 版本中的原則需要 Ctrl + Alt + Del，則 **重設密碼** 將無法運作。
    - 如果關閉鎖定畫面通知， **重設密碼** 將無法運作。
    - *HideFastUserSwitching* 設定為 enabled 或1
    - *DontDisplayLastUserName* 設定為 enabled 或1
    - *NoLockScreen* 設定為 enabled 或1
    - 裝置上已設定*EnableLostMode*
    - Explorer.exe 會取代為自訂殼層
- 下列三個特定設定的組合，可能會導致此功能無法正常運作。
    - [互動式登入:不要求按 CTRL+ALT+DEL 鍵] = [已停用]
    - *DisableLockScreenAppNotifications* = 1 或已啟用
    - Windows SKU 不是家用版或專業版

## <a name="windows-10-password-reset"></a>Windows 10 密碼重設

若要在登入畫面設定 Windows 10 裝置以進行 SSPR，請參閱下列必要條件和設定步驟。

### <a name="windows-10-prerequisites"></a>Windows 10 先決條件

- 系統管理員 [必須從 Azure 入口網站啟用 Azure AD 自助式密碼重設](tutorial-enable-sspr.md)。
- 在使用此功能之前，使用者必須註冊 SSPR [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - 從 Windows 登入畫面使用 SSPR 並非唯一的，所有使用者都必須提供驗證連絡人資訊才能重設其密碼。
- 網路 proxy 需求：
    - 針對 `passwordreset.microsoftonline.com` 和 `ajax.aspnetcdn.com` 開啟連接埠 443
    - Windows 10 裝置只支援電腦層級的 proxy 設定。
- 至少執行 2018 年 4 月更新版 (v1803) 的 Windows 10，且裝置必須是：
    - 已聯結的 Azure AD
    - 已聯結的混合式 Azure AD

### <a name="enable-for-windows-10-using-intune"></a>使用 Intune 針對 Windows 10 啟用

使用 Intune 部署設定變更以從登入畫面啟用 SSPR 是最有彈性的方法。 Intune 可讓您將組態變更部署至您所定義的特定機器群組。 必須完成裝置的 Intune 註冊，才能使用此方法。

#### <a name="create-a-device-configuration-policy-in-intune"></a>在 Intune 中建立裝置設定原則

1. 登入 [Azure 入口網站](https://portal.azure.com) ，然後選取 [ **Intune**]。
1. 前往**裝置配置**  >  **檔**，然後選取 [ **+ 建立設定檔**]，以建立新的裝置設定檔
   - 若為 **平臺** ，請選擇 *Windows 10 和更新版本*
   - 針對 [**配置檔案類型**]，選擇 [*自訂*]
1. 選取 [ **建立**]，然後為設定檔提供有意義的名稱，例如 Windows 10 的登 *入畫面 SSPR*

    （選擇性）提供設定檔的有意義描述，然後選取 **[下一步]**。
1. 在 [ *設定設定*] 下，選取 [ **新增** ] 並提供下列 oma-uri 設定，以啟用 [重設密碼] 連結：
      - 提供有意義的名稱來說明設定的執行內容，例如 [ *新增 SSPR 連結*]。
      - （選擇性）提供設定的有意義描述。
      - **OMA URI** 設為 `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - **資料類型** 設為 **整數**
      - **值** 設為 **1**

    選取 [**新增**]，然後選取 **[下一步]**
1. 您可以將原則指派給特定的使用者、裝置或群組。 將您的環境所需的設定檔指派給您的環境，最好是先測試裝置的群組，然後選取 **[下一步]**。

    如需詳細資訊，請參閱 [Microsoft Intune 中的指派使用者和裝置設定檔](/mem/intune/configuration/device-profile-assign)。

1. 設定您的環境所需的適用性規則，例如，若要在 *作業系統版本 Windows 10 企業版時指派設定檔*，請選取 **[下一步]**。
1. 檢查您的設定檔，然後選取 [ **建立**]。

### <a name="enable-for-windows-10-using-the-registry"></a>使用登錄針對 Windows 10 啟用

若要在使用登錄機碼的登入畫面上啟用 SSPR，請完成下列步驟：

1. 使用系統管理認證登入 Windows 電腦。
1. 按**Windows**  +  **R**以開啟 [*執行*] 對話方塊，然後以系統管理員身分執行**regedit**
1. 設定下列登錄機碼：

    ```cmd
    HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount
       "AllowPasswordReset"=dword:00000001
    ```

#### <a name="troubleshooting-windows-10-password-reset"></a>對 Windows 10 密碼重設進行疑難排解

如果您在 Windows 登入畫面中使用 SSPR 有問題，Azure AD audit 記錄檔會包含發生密碼重設之 IP 位址和 *ClientType* 的相關資訊，如下列範例輸出所示：

![Azure AD 稽核記錄中的範例 Windows 7 密碼重設](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

當使用者從 Windows 10 裝置的登入畫面重設其密碼時，會建立名為的低許可權暫時帳戶 `defaultuser1` 。 此帳戶用來保護密碼重設程序。

帳戶本身具有隨機產生的密碼，不會顯示裝置登入，並會在使用者重設其密碼之後自動移除。 可能會存在多個 `defaultuser` 設定檔，但您可以放心忽略。

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7、8 及 8.1 密碼重設

若要在登入畫面設定 Windows 7、8或8.1 裝置以進行 SSPR，請參閱下列必要條件和設定步驟。

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7、8 及 8.1 先決條件

- 系統管理員 [必須從 Azure 入口網站啟用 Azure AD 自助式密碼重設](tutorial-enable-sspr.md)。
- 在使用此功能之前，使用者必須註冊 SSPR [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - 從 Windows 登入畫面使用 SSPR 並非唯一的，所有使用者都必須提供驗證連絡人資訊才能重設其密碼。
- 網路 proxy 需求：
    - 針對 `passwordreset.microsoftonline.com` 開啟連接埠 443
- 已修補的 Windows 7 或 Windows 8.1 作業系統。
- 已使用[傳輸層安全性 (TLS) 登錄設定](/windows-server/security/tls/tls-registry-settings#tls-12)中所找到的指南啟用 TLS 1.2。
- 如果您的電腦上已啟用多個協力廠商認證提供者，使用者將會在登入畫面上看到一個以上的使用者設定檔。

> [!WARNING]
> 必須啟用 TLS 1.2，而不能只是設定為自動交涉。

### <a name="install"></a>安裝

若為 Windows 7、8和8.1，則必須在電腦上安裝小型元件，才能在登入畫面上啟用 SSPR。 若要安裝此 SSPR 元件，請完成下列步驟：

1. 下載適用於所要啟用 Windows 版本的安裝程式。

    您可以從 Microsoft 下載中心取得軟體安裝程式，網址為： [https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. 登入要安裝的電腦，並執行安裝程式。
1. 安裝之後，強烈建議您重新開機。
1. 重新開機之後，在登入畫面上選擇使用者，然後選取 [忘記密碼？] 來起始密碼重設工作流程。
1. 完成工作流程，遵循螢幕上的步驟來重設密碼。

![在 Windows7 中按下「忘記密碼？」的範例 SSPR 流程](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>無訊息安裝

您可以安裝或卸載 SSPR 元件，而不需要使用下列命令的提示：

- 若要進行無訊息安裝，請使用命令 "msiexec /i SsprWindowsLogon.PROD.msi /qn"
- 若要進行無訊息解除安裝，請使用命令 "msiexec /x SsprWindowsLogon.PROD.msi /qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>對 Windows 7、8 及 8.1 密碼重設進行疑難排解

如果您在 Windows 登入畫面中使用 SSPR 時遇到問題，則會在電腦和 Azure AD 中記錄事件。 Azure AD 事件包含發生密碼重設之 IP 位址和 ClientType 的相關資訊，如下列範例輸出所示：

![Azure AD 稽核記錄中的範例 Windows 7 密碼重設](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

如果需要額外的記錄，則可變更電腦上的登錄機碼來啟用詳細資訊記錄。 僅使用下列登錄機碼值，啟用詳細資訊記錄以供疑難排解之用：

```cmd
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

- 若要啟用詳細資訊記錄，請建立 `REG_DWORD: "EnableLogging"`，並將其設定為 1。
- 若要停用詳細資訊記錄，請將 `REG_DWORD: "EnableLogging"` 變更為 0。

## <a name="what-do-users-see"></a>使用者看到的內容

針對您的 Windows 裝置設定 SSPR 之後，使用者會有哪些變更？ 他們如何得知可在登入畫面重設其密碼？ 下列範例螢幕擷取畫面顯示使用者使用 SSPR 重設其密碼的其他選項：

![顯示具有 SSPR 連結的範例 Windows 7 和 10 登入畫面](./media/howto-sspr-windows/windows-reset-password.png)

當使用者嘗試登入時，他們會看到 [ **重設密碼** ] 或 [ **忘記密碼** ] 連結，該連結會在登入畫面開啟自助式密碼重設體驗。 這項功能可讓使用者重設其密碼，而不需使用另一個裝置來存取網頁瀏覽器。

如需使用此功能之使用者的詳細資訊，請參閱 [重設您的公司或學校密碼](../user-help/active-directory-passwords-update-your-own-password.md)

## <a name="next-steps"></a>後續步驟

若要簡化使用者註冊體驗，您可以 [預先填入使用者驗證連絡人資訊以進行 SSPR](howto-sspr-authenticationdata.md)。