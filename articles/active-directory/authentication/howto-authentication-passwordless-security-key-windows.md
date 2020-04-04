---
title: 沒有密碼安全金鑰登入 Windows - Azure 的動作目錄
description: 瞭解如何使用 FIDO2 安全金鑰(預覽)將無密碼安全金鑰登入 Azure 活動目錄
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b8f5d6aaa96c24eb37eb78d237a489f1d25293c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653992"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory-preview"></a>使用 Azure 的目錄(預覽)為 Windows 10 裝置啟用無密碼安全金鑰登入

本文件重點介紹對 Windows 10 設備啟用基於 FIDO2 安全密鑰的無密碼身份驗證。 在本文末尾,您將能夠使用 FIDO2 安全金鑰登入 Azure AD 和混合 Azure AD 與 Azure AD 帳戶聯接的 Windows 10 裝置。

|     |
| --- |
| FIDO2 安全金鑰是 Azure 活動目錄的公共預覽功能。 有關預覽的詳細資訊,請參閱 Microsoft [Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>需求

| 裝置類型 | 已聯結的 Azure AD | 已聯結的混合式 Azure AD |
| --- | --- | --- |
| [Azure Multi-Factor Authentication](howto-mfa-getstarted.md) | X | X |
| [組合安全資訊註冊預覽](concept-registration-mfa-sspr-combined.md) | X | X |
| 相容[FIDO2 安全金鑰](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| WebAuthN 需要 Windows 10 版本 1809 或更高版本 | X | X |
| [Azure AD 加入的裝置](../devices/concept-azure-ad-join.md)需要 Windows 10 版本 1903 或更高版本 | X |   |
| [混合 Azure AD 加入的裝置](../devices/concept-azure-ad-join-hybrid.md)需要 Windows 10 內部內部版本 18945 或更高版本 |   | X |
| 完全修補的 Windows 伺服器 2016/2019 域控制器。 |   | X |
| [Azure AD 連線](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)版本 1.4.32.0 或更高版本 |   | X |
| [微軟Intune(](https://docs.microsoft.com/intune/fundamentals/what-is-intune)選擇性的) | X | X |
| 預配包(選擇性的) | X | X |
| 群組原則 (選擇性的) |   | X |

### <a name="unsupported-scenarios"></a>不支援的情節

不支援以下機制:

- Windows 伺服器活動目錄域服務 (AD DS) 域加入(僅限本地設備)部署。
- 使用安全密鑰的 RDP、VDI 和 Citrix 方案。
- 使用安全密鑰的 S/MIME。
- 使用安全密鑰"以樣運行」。
- 使用安全金鑰登錄到伺服器。
- 如果連線時未使用安全金鑰登錄設備,則不能使用它離線登錄或解鎖。
- 使用包含多個 Azure AD 帳戶的安全金鑰登錄或解鎖 Windows 10 設備。 此方案利用添加到安全金鑰的最後一個帳戶。 WebAuthN 允許用戶選擇要使用的帳戶。
- 解鎖運行 Windows 10 版本 1809 的設備。 為了獲得最佳體驗,請使用 Windows 10 版本 1903 或更高版本。

## <a name="prepare-devices-for-preview"></a>準備裝置進行預覽

在功能預覽期間試用的 Azure AD 加入設備必須運行 Windows 10 版本 1809 或更高版本。 最好的體驗是在 Windows 10 版本 1903 或更高版本中。

混合 Azure AD 加入的設備必須運行 Windows 10 內部人員生成 18945 或更新。

## <a name="enable-security-keys-for-windows-sign-in"></a>在 Windows 登入安全金鑰

組織可以選擇使用以下一種或多種方法,根據組織的要求,啟用 Windows 登錄的安全密鑰:

- [使用 Intune 啟用](#enable-with-intune)
- [目標 Intune 部署](#targeted-intune-deployment)
- [使用預先載套件開啟](#enable-with-a-provisioning-package)
- [使用群組策略啟用 (僅限混合 Azure AD 聯接裝置)](#enable-with-group-policy)

> [!IMPORTANT]
> 具有**混合 Azure AD 加入裝置的**組織**必須**在 Windows 10 FIDO2 安全金鑰身份驗證工作之前完成本文中的步驟,即在 Windows 10 FIDO2 安全金鑰身份驗證工作之前[,將 FIDO2 身份驗證啟用到本地資源](howto-authentication-passwordless-security-key-on-premises.md)。
>
> 具有**Azure AD 加入設備的**組織必須先執行此操作,然後才能使用 FIDO2 安全金鑰對其設備對本地資源進行身份驗證。

### <a name="enable-with-intune"></a>使用 Intune 啟用

要啟用使用 Intune 的安全金鑰,請完成以下步驟:

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽到**微軟Intune** > **裝置註冊** > **Windows註冊** > **Windows你好商業** > **屬性**。
1. 在 **「設定」** 下,將 **「使用安全金鑰的登入****」設置為「已啟用**」 。

用於登錄的安全金鑰的配置不依賴於為企業配置 Windows Hello。

### <a name="targeted-intune-deployment"></a>目標 Intune 部署

要定位特定裝置群組以啟用認證提供者,請透過 Intune 使用以下自訂設定:

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽到**Microsoftintune** > **裝置設定檔** > **Profiles** > **建立設定檔**。
1. 使用以下設定設定新設定檔:
   - 名稱:Windows 登入的安全金鑰
   - 說明:使 FIDO 安全金鑰在 Windows 登入期間使用
   - 平台:Windows 10 及更高版本
   - 設定檔案類型:自訂
   - 自訂 OMA-URI 設定:
      - 名稱:開啟 Windows 登入的 FIDO 安全金鑰
      - OMA-URI: ./設備/供應商/MSFT/工作/安全密鑰/使用安全鍵
      - 資料類型:整數
      - 值：1
1. 此策略可以分配給特定使用者、設備或組。 有關詳細資訊,請參閱在[Microsoft Intune 中分配使用者和裝置設定檔](https://docs.microsoft.com/intune/device-profile-assign)。

![Intune 自訂裝置設定策略建立](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>使用預先載套件開啟

對於 Intune 未管理的設備,可以安裝預配包以啟用該功能。 Windows 配置設計器應用可以從 Microsoft[應用商店](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22)安裝。 完成以下步驟以建立預配包:

1. 啟動 Windows 配置設計器。
1. 選擇 **「檔案** > **新專案**」。
1. 為專案命名並記下創建項目的路徑,然後選擇 **「下一步**」 。
1. 將*預配包*保留為 **"選定專案工作流**",然後選擇 **"下一步**"。
1. 選擇 **「選擇要檢視和設定哪些設定**」下*的所有 Windows 桌面版本*,然後選擇 **「下一步**」 。
1. 選取 [完成]****。
1. 在新創建的專案中,流覽到**運行時設置** > **WindowsHelloFor 業務** > **安全密鑰** > **使用安全密鑰ForSignin。**
1. 使用**安全金鑰設定為***已開啟*。
1. 選擇**匯出** > **預先定義**
1. 在 **「描述預配套件**」下的 **「生成**」視窗中保留預設值,然後選擇 **「下一步**」 。
1. 在 **「選擇預配包的安全詳細資訊**」下的 **「生成**」視窗中保留預設值,然後選擇 **「下一步**」 。
1. 將變更「**選擇」** 下 **「視窗**中的路徑,並選擇」**下一步**。
1. 在 **「生成預配包**」頁上選擇 **「生成**」。
1. 將創建的兩個檔 *(ppkg*和*cat)* 儲存到以後可以將它們應用於電腦的位置。
1. 要套用您建立的預配套件,請參閱[套用的預先包](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)。

> [!NOTE]
> 執行 Windows 10 版本 1809 的裝置還必須啟用共用 PC 模式 (*啟用共用 PC 模式*)。 有關啟用此功能的詳細資訊,請參閱[使用 Windows 10 設定共享電腦或來賓電腦](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc)。

### <a name="enable-with-group-policy"></a>使用群組原則開啟

對於**混合 Azure AD 聯接裝置**,組織可以設定以下組策略設定以啟用 FIDO 安全金鑰登錄。 在**電腦設定** > **管理樣本** > **系統** > **登入** > 下找到該設定**開啟安全金鑰登入**:

- 將此策略設定為 **「已啟用」** 允許使用者使用安全金鑰登入。
- 將此策略設定為 **「已關閉****」或「未設定」** 會阻止使用者使用安全密鑰登錄。

此組策略設置需要`credentialprovider.admx`組策略範本的更新版本。 此新範本可用於下一版本的 Windows 伺服器和 Windows 10 20H1。 此設置可以通過運行這些較新版本 Windows 的設備進行管理,也可以按照支援主題"[如何在 Windows 中創建和管理組策略管理範本的中央存儲](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)「中的指南進行集中管理。

## <a name="sign-in-with-fido2-security-key"></a>使用 FIDO2 安全金鑰登入

在下面的範例中,一個名為 Bala Sandhu 的使用者已經使用上一篇文章中的步驟「[啟用無密碼安全金鑰登錄](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)」中預配了其 FIDO2 安全金鑰。 對混合 Azure AD 連線裝置,請確保此[功能開啟了無密碼安全金鑰登入到本地資源](howto-authentication-passwordless-security-key-on-premises.md)。 Bala 可以從 Windows 10 鎖螢幕介面中選擇安全密鑰認證提供提供程式,並將安全金鑰插入到 Windows 中簽名。

![Windows 10 鎖定螢幕介面的安全性金鑰登錄](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>管理安全金鑰生物辨識、PIN 或重置安全金鑰

* Windows 10 版本 1903 或更高版本
   * 使用者可以在其裝置上打開**Windows 設定**>**帳號** > **安全金鑰**
   * 使用者可以變更其 PIN、更新生物識別或重置其安全金鑰

## <a name="troubleshooting-and-feedback"></a>容錯排除及回饋

如果您想在預覽此功能時共用反饋或遇到問題,請使用以下步驟透過 Windows 回饋中心應用共用:

1. 啟動**反饋中心**並確保您已登錄。
1. 根據以下類別提交回饋:
   - 類別:安全和隱私
   - 子類別: FIDO
1. 要捕捉紀錄,請使用選項**重新建立問題**

## <a name="next-steps"></a>後續步驟

[開啟對 Azure AD 和混合 Azure AD 聯接裝置的本地資源的存取](howto-authentication-passwordless-security-key-on-premises.md)

[瞭解有關裝置註冊的資訊](../devices/overview.md)

[深入了解 Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
