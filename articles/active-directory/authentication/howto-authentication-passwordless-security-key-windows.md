---
title: 無密碼安全性金鑰登入 Windows-Azure Active Directory
description: 瞭解如何使用 FIDO2 安全性金鑰（預覽）啟用無密碼安全性金鑰登入 Azure Active Directory
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
ms.openlocfilehash: acf7f89ab7c84d74dcd6e3dff2c2c688da1cefea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85550617"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory-preview"></a>啟用無密碼安全性金鑰使用 Azure Active Directory 登入 Windows 10 裝置（預覽）

本檔著重于啟用以 Windows 10 裝置為基礎的 FIDO2 安全性金鑰型無密碼驗證。 在本文結尾，您將能夠使用 FIDO2 的安全性金鑰，透過您的 Azure AD 帳戶登入 Azure AD 和混合式 Azure AD 加入的 Windows 10 裝置。

> [!NOTE]
> FIDO2 安全性金鑰是 Azure Active Directory 的公開預覽功能。 如需有關預覽的詳細資訊，請參閱[Microsoft Azure 預覽的補充使用](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)規定。

## <a name="requirements"></a>規格需求

| 裝置類型 | 已聯結的 Azure AD | 已聯結的混合式 Azure AD |
| --- | --- | --- |
| [Azure Multi-Factor Authentication](howto-mfa-getstarted.md) | X | X |
| [結合的安全性資訊註冊預覽](concept-registration-mfa-sspr-combined.md) | X | X |
| 相容的[FIDO2 安全性金鑰](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| WebAuthN 需要 Windows 10 1809 版或更高版本 | X | X |
| 已[加入 Azure AD 的裝置](../devices/concept-azure-ad-join.md)需要 Windows 10 1903 版或更高版本 | X |   |
| 已[加入混合式 Azure AD 的裝置](../devices/concept-azure-ad-join-hybrid.md)需要 Windows 10 2004 版或更高版本 |   | X |
| 已完整修補的 Windows Server 2016/2019 網域控制站。 |   | X |
| [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) 1.4.32.0 或更新版本 |   | X |
| [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) （選擇性） | X | X |
| 布建套件（選擇性） | X | X |
| 群組原則（選擇性） |   | X |

### <a name="unsupported-scenarios"></a>不支援的情節

不支援下列案例：

- Windows Server Active Directory Domain Services （AD DS）已加入網域（僅限內部部署裝置）部署。
- 使用安全性金鑰的 RDP、VDI 和 Citrix 案例。
- 使用安全性金鑰的 S/MIME。
- 使用安全性金鑰的「執行身分」。
- 使用安全性金鑰登入伺服器。
- 如果您在線上時未使用您的安全性金鑰來登入您的裝置，就無法使用它來離線登入或解除鎖定。
- 使用包含多個 Azure AD 帳戶的安全性金鑰來登入或解除鎖定 Windows 10 裝置。 此案例會利用新增至安全性金鑰的最後一個帳戶。 [WebAuthN] 可讓使用者選擇他們想要使用的帳戶。
- 解除鎖定執行 Windows 10 1809 版的裝置。 若要獲得最佳體驗，請使用 Windows 10 1903 版或更高版本。

## <a name="prepare-devices-for-preview"></a>準備裝置以供預覽

在功能預覽期間進行試驗的 Azure AD 聯結裝置，必須執行 Windows 10 1809 版或更高版本。 最佳體驗是在 Windows 10 1903 版或更新版本上。

混合式 Azure AD 加入的裝置必須執行 Windows 10 2004 版或更新版本。

## <a name="enable-security-keys-for-windows-sign-in"></a>啟用 Windows 登入的安全性金鑰

組織可以選擇使用下列一或多種方法，根據組織的需求，使用 Windows 登入的安全性金鑰：

- [使用 Intune 啟用](#enable-with-intune)
- [目標 Intune 部署](#targeted-intune-deployment)
- [使用布建套件啟用](#enable-with-a-provisioning-package)
- [使用群組原則啟用（僅混合式 Azure AD 加入的裝置）](#enable-with-group-policy)

> [!IMPORTANT]
> 已**加入混合式 Azure AD 裝置**的組織必須**也**完成文章中的步驟：在 Windows 10 FIDO2 安全性金鑰驗證生效之前，對[內部部署資源啟用 FIDO2 驗證](howto-authentication-passwordless-security-key-on-premises.md)。
>
> 已**加入 Azure AD 裝置**的組織必須先執行此動作，他們的裝置才能向具有 FIDO2 安全性金鑰的內部部署資源進行驗證。

### <a name="enable-with-intune"></a>使用 Intune 啟用

若要使用 Intune 啟用安全性金鑰，請完成下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至 [ **Microsoft Intune**  >  **裝置註冊**] [  >  **windows 註冊**] [windows  >  **Hello 企業版**]  >  **屬性**。
1. 在 [**設定**] 下，將 [**使用安全性金鑰登入**] 設定為 [**已啟用**]。

用於登入的安全性金鑰設定並不依存于設定 Windows Hello 企業版。

### <a name="targeted-intune-deployment"></a>目標 Intune 部署

若要以特定裝置群組為目標以啟用認證提供者，請透過 Intune 使用下列自訂設定：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至**Microsoft Intune**  >  **裝置配置**  >  **檔**] [  >  **建立設定檔**]。
1. 使用下列設定來設定新的設定檔：
   - 名稱： Windows 登入的安全性金鑰
   - 描述：啟用在 Windows 登入期間使用的 FIDO 安全性金鑰
   - 平台：Windows 10 及以上版本
   - 配置檔案類型：自訂
   - 自訂 OMA URI 設定：
      - 名稱：開啟 Windows 登入的 FIDO 安全性金鑰
      - OMA-URI：./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      - 資料類型：整數
      - 值：1
1. 此原則可以指派給特定的使用者、裝置或群組。 如需詳細資訊，請參閱[在 Microsoft Intune 中指派使用者和裝置設定檔](https://docs.microsoft.com/intune/device-profile-assign)。

![建立 Intune 自訂裝置設定原則](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>使用布建套件啟用

對於不受 Intune 管理的裝置，可以安裝布建套件來啟用此功能。 您可以從[Microsoft Store](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22)安裝 Windows 設定設計工具應用程式。 完成下列步驟以建立布建套件：

1. 啟動 Windows 設定設計工具。
1. 選取 **[** 檔案] [  >  **新增專案**]。
1. 為您的專案命名，並記下您的專案建立所在的路徑，然後選取 **[下一步]**。
1. 將布建*套件*保留為**選取的專案工作流程**，然後選取 **[下一步]**。
1. 在 **[選擇要查看和設定的設定**] 底下選取 [*所有 Windows 桌上出版本*]，然後選取 **[下一步]**。
1. 選取 [完成]。
1. 在新建立的專案中，流覽至 [**執行時間設定**] [  >  **WindowsHelloForBusiness**  >  **SecurityKeys**  >  **UseSecurityKeyForSignIn**]。
1. 將 [ **UseSecurityKeyForSignIn** ] 設定為 [*已啟用*]。
1. 選取**匯出**布建  >  **套件**
1. 在 [描述布建**套件**] 底下的 [**組建**] 視窗中保留預設值，然後選取 **[下一步]**。
1. 將預設值保留在 [**組建**] 視窗的 [選取布建**套件的安全性詳細資料**] 底下，然後選取 **[下一步**
1. 在 [**選取要儲存**布建套件的位置] 底下，記下或變更 [**組建**] 視窗中的路徑，然後選取 **[下一步]**。
1. 選取 [建立布建**套件**] 頁面上的 [**組建**]。
1. 將建立的兩個檔案（*ppkg*和*cat*）儲存到您稍後可以將它們套用至電腦的位置。
1. 若要套用您所建立的布建套件，請參閱套用[提供套件](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)。

> [!NOTE]
> 執行 Windows 10 1809 版的裝置也必須啟用共用電腦模式（*EnableSharedPCMode*）。 如需啟用此功能的詳細資訊，請參閱[使用 Windows 10 設定共用或來賓電腦](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc)。

### <a name="enable-with-group-policy"></a>使用群組原則啟用

針對已**加入混合式 Azure AD 的裝置**，組織可以設定下列群組原則設定，以啟用 FIDO 安全性金鑰登入。 您可以在 [**電腦**設定]  >  **系統管理範本**[  >  **系統**  >  **登**入] [  >  **開啟安全性金鑰**] [登入] 下找到此設定：

- 將此原則設定為 [**啟用**] 可讓使用者使用安全性金鑰登入。
- 將此原則設定為 [**已停用**] 或 [**未設定] 會**阻止使用者使用安全性金鑰登入。

此群組原則設定需要群組原則範本的更新版本 `credentialprovider.admx` 。 這個新範本適用于下一個版本的 Windows Server 和 Windows 10 20H1。 這項設定可透過執行下列其中一個較新版本 Windows 的裝置來管理，或依照支援主題中的指導方針、[如何建立和管理 Windows 中的群組原則系統管理範本中央存放區](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)中的指示進行。

## <a name="sign-in-with-fido2-security-key"></a>以 FIDO2 安全性金鑰登入

在下列範例中，名為 Bala Sandhu 的使用者已使用上一篇文章中的步驟來布建其 FIDO2 安全性金鑰，並[啟用無密碼安全性金鑰登入](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)。 針對已加入混合式 Azure AD 的裝置，請確定您也已[啟用無密碼安全性金鑰登入內部部署資源](howto-authentication-passwordless-security-key-on-premises.md)。 Bala 可以從 Windows 10 鎖定畫面中選擇安全性金鑰認證提供者，並插入安全性金鑰以登入 Windows。

![Windows 10 鎖定畫面上的安全性金鑰登入](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>管理安全性金鑰生物識別、PIN 或重設安全性金鑰

* Windows 10 1903 版或更高版本
   * 使用者可以在其裝置上開啟**Windows 設定**>**帳戶**  >  **安全性金鑰**
   * 使用者可以變更其 PIN、更新生物識別，或重設其安全性金鑰

## <a name="troubleshooting-and-feedback"></a>疑難排解和意見反應

如果您想要在預覽這項功能時分享意見反應或遇到問題，請使用下列步驟透過 Windows 意見反應中樞應用程式共用：

1. 啟動**意見反應中樞**，並確認您已登入。
1. 在下列分類下提交意見反應：
   - 類別：安全性和隱私權
   - 子類別目錄： FIDO
1. 若要捕獲記錄，請使用選項來**重新建立我的問題**

## <a name="next-steps"></a>後續步驟

[針對 Azure AD 和混合式 Azure AD 加入的裝置，啟用內部部署資源的存取權](howto-authentication-passwordless-security-key-on-premises.md)

[深入瞭解裝置註冊](../devices/overview.md)

[深入了解 Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
