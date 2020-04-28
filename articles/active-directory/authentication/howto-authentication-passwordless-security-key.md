---
title: 無密碼安全性金鑰登入（預覽）-Azure Active Directory
description: 使用 FIDO2 安全性金鑰（預覽）啟用無密碼安全性金鑰登入 Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/12/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 066ab7892bed6e7505e7ee114ff37a7850ef5c9b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81450916"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>啟用無密碼安全性金鑰登入（預覽）

對於目前使用密碼並具有共用電腦環境的企業，安全性金鑰提供順暢的方式讓背景工作人員進行驗證，而不需要輸入使用者名稱或密碼。 安全性金鑰可提高背景工作角色的效率，並提供更好的安全性。

本檔著重于啟用以安全性金鑰為基礎的無密碼 authentication。 在本文結尾，您將能夠使用 FIDO2 的安全性金鑰，以您的 Azure AD 帳戶登入 web 應用程式。

|     |
| --- |
| FIDO2 安全性金鑰是 Azure Active Directory 的公開預覽功能。 如需有關預覽的詳細資訊，請參閱[Microsoft Azure 預覽的補充使用](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)規定|
|     |

## <a name="requirements"></a>需求

- [Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
- [結合的安全性資訊註冊預覽](concept-registration-mfa-sspr-combined.md)
- 相容的[FIDO2 安全性金鑰](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN 需要 Windows 10 1809 版或更高版本 * *

若要使用安全性金鑰來登入 web 應用程式和服務，您必須擁有支援 WebAuthN 通訊協定的瀏覽器。 其中包括 Microsoft Edge、Chrome、Firefox 和 Safari。

## <a name="prepare-devices-for-preview"></a>準備裝置以供預覽

您試驗的 Azure AD 聯結裝置必須執行 Windows 10 1809 版或更高版本。 最佳體驗是在 Windows 10 1903 版或更新版本上。

混合式 Azure AD 加入的裝置必須執行 Windows 10 Insider 組建18945或更新版本。

## <a name="enable-passwordless-authentication-method"></a>啟用無密碼 authentication 方法

### <a name="enable-the-combined-registration-experience"></a>啟用合併的註冊體驗

無密碼驗證方法的註冊功能依賴結合的註冊功能。 遵循[啟用結合的安全性資訊註冊（預覽）](howto-registration-mfa-sspr-combined.md)一文中的步驟，以啟用合併的註冊。

### <a name="enable-fido2-security-key-method"></a>啟用 FIDO2 安全性金鑰方法

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至**Azure Active Directory** > **安全性** > **驗證方法** > **驗證方法原則（預覽）**。
1. 在 [方法**FIDO2] 安全性金鑰**底下，選擇下列選項：
   1. **啟用**-是或否
   1. **目標**-所有使用者或選取使用者
1. **儲存**設定。

## <a name="user-registration-and-management-of-fido2-security-keys"></a>FIDO2 安全性金鑰的使用者註冊與管理

1. 瀏覽至 [https://myprofile.microsoft.com](https://myprofile.microsoft.com) 。
1. 如果尚未登入，請先登入。
1. 按一下 [**安全性資訊**]。
   1. 如果使用者已註冊至少一個 Azure 多因素驗證方法，他們可以立即註冊 FIDO2 的安全性金鑰。
   1. 如果未註冊至少一個 Azure 多因素驗證方法，則必須新增一個。
1. 按一下 [**新增方法**]，然後選擇 [**安全性金鑰**]，以新增 FIDO2 安全性金鑰。
1. 選擇 [ **USB 裝置**] 或 [ **NFC 裝置**]。
1. 備妥您的金鑰，然後選擇 **[下一步]**。
1. 隨即會出現一個方塊，並要求使用者建立/輸入您的安全性金鑰的 PIN，然後執行金鑰的必要手勢，也就是生物識別或觸控。
1. 使用者將會回到合併的註冊體驗，並要求您提供有意義的金鑰名稱，讓使用者可以識別有多個索引鍵（如果有的話）。 按 [下一步]  。
1. 按一下 [**完成**] 完成程式。

## <a name="sign-in-with-passwordless-credential"></a>使用無密碼認證登入

在下列範例中，使用者已布建其 FIDO2 安全性金鑰。 使用者可以選擇使用 Windows 10 1809 版或更高版本上支援的瀏覽器中的 FIDO2 安全性金鑰來登入 web。

![安全性金鑰登入 Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>疑難排解與意見反應

如果您想要在預覽這項功能時分享意見反應或遇到問題，請使用下列步驟透過 Windows 意見反應中樞應用程式共用：

1. 啟動**意見反應中樞**，並確認您已登入。
1. 在下列分類下提交意見反應：
   - 類別：安全性和隱私權
   - 子類別目錄： FIDO
1. 若要捕獲記錄，請使用選項來**重新建立我的問題**

## <a name="known-issues"></a>已知問題

### <a name="security-key-provisioning"></a>安全性金鑰提供

公開預覽中不提供安全性金鑰的系統管理員布建和取消布建。

### <a name="upn-changes"></a>UPN 變更

我們正努力支援一項功能，可讓混合式 Azure AD 加入和 Azure AD 聯結的裝置上進行 UPN 變更。 如果使用者的 UPN 變更，您就無法再修改 FIDO2 安全性金鑰來考慮變更。 解決方式是重設裝置，且使用者必須重新註冊。

## <a name="next-steps"></a>後續步驟

[FIDO2 安全性金鑰 Windows 10 登入](howto-authentication-passwordless-security-key-windows.md)

[對內部部署資源啟用 FIDO2 authentication](howto-authentication-passwordless-security-key-on-premises.md)

[深入瞭解裝置註冊](../devices/overview.md)

[深入了解 Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
