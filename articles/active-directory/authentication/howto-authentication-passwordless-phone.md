---
title: 使用 Microsoft 身份驗證器應用進行無密碼登錄 - Azure 活動目錄
description: 使用 Microsoft 身份驗證器應用（預覽）為 Azure AD 啟用無密碼登錄
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c684d6f1fbd8128ae020b6fd29da928b286aa18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126692"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>使用 Microsoft 身份驗證器應用啟用無密碼登錄（預覽版）

Microsoft Authenticator 應用程式可用來登入任何 Azure AD 帳戶 (而不需使用密碼)。 類似於 [Windows Hello 企業版](/windows/security/identity-protection/hello-for-business/hello-identity-verification) 的技術，Microsoft Authenticator 會使用金鑰型驗證來啟用已繫結至裝置並使用生物特徵識別或 PIN 的使用者認證。 此身份驗證方法可用於任何設備平臺（包括行動裝置），以及任何與 Microsoft 身份驗證庫集成的應用或網站。 

![瀏覽器登錄示例，請求使用者批准登錄](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

輸入使用者名後，打開 Microsoft 身份驗證器應用的電話登錄的使用者將看到一條消息，告訴他們點擊應用中的號碼，而不是在輸入使用者名後看到密碼提示。 在應用程式中，使用者必須符合數目，選擇 [核准]，然後提供他們的 PIN 或生物特徵辨識，然後驗證將會完成。

> [!NOTE]
> 此功能自 2017 年 3 月起在 Microsoft 身份驗證器應用中使用，因此，當為目錄啟用策略時，使用者可能會立即遇到此流，如果策略未啟用，則可能會看到錯誤訊息。 請留意這項變更，並且讓使用者做好準備。

## <a name="prerequisites"></a>Prerequisites

- Azure 多重要素驗證，允許推送通知作為驗證方法 
- 在執行 iOS 8.0 或更新版本或 Android 6.0 或更新版本的裝置上安裝的最新版 Microsoft Authenticator。

> [!NOTE]
> 如果使用 Azure AD PowerShell 啟用了以前的 Microsoft 身份驗證器應用無密碼登錄預覽，則已為您的整個目錄啟用該預覽。 如果啟用此新方法，它將取代 PowerShell 策略。 我們建議通過新的身份驗證方法為租戶中的所有使用者啟用，否則新策略中未登錄的使用者將無法再以無密碼方式登錄。 

## <a name="enable-passwordless-authentication-methods"></a>啟用無密碼身份驗證方法

### <a name="enable-the-combined-registration-experience"></a>啟用組合註冊體驗

無密碼身份驗證方法的註冊功能依賴于組合的註冊預覽。 按照文章"[啟用組合安全資訊註冊（預覽）"](howto-registration-mfa-sspr-combined.md)中的步驟啟用合併註冊預覽。

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>啟用無密碼電話登錄身份驗證方法

1. 登錄到 Azure[門戶](https://portal.azure.com)
1. 搜尋並選取 [Azure Active Directory]**。 選擇**安全** > **身份驗證方法** > **身份驗證方法策略（預覽）**
1. 在**無密碼電話登錄下**，選擇以下選項
   1. **啟用**- 是 或 否
   1. **目標**- 所有使用者或選擇使用者
1. **保存**以設置新策略

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>微軟身份驗證器應用的使用者註冊和管理

1. 流覽到[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. 尚未登錄
1. 通過按一下 **"添加方法**"、選擇**身份驗證器應用**和按一下"**添加"** 添加身份驗證器應用
1. 按照說明在設備上安裝和配置 Microsoft 身份驗證器應用
1. 按一下 **"完成"** 以完成身份驗證器 MFA 應用設置流。 
1. 在**Microsoft 身份驗證器中**，從帳戶下拉式功能表**中選擇"啟用電話登錄**"
1. 按照應用中的說明完成無密碼電話登錄的註冊。 

組織可以將使用者指向[使用手機登錄的文章，而不是密碼](../user-help/microsoft-authenticator-app-phone-signin-faq.md)，以便在 Microsoft 身份驗證器應用中設置和啟用電話登錄時提供進一步説明。 要應用這些設置，您可能需要登出並重新登錄到租戶。 

## <a name="sign-in-with-passwordless-credential"></a>使用無密碼憑據登錄

針對公開預覽版，沒有任何方式可強制使用者建立或使用這個新認證。 使用者只有在管理員啟用其租戶**且**使用者已更新其 Microsoft 身份驗證器應用以啟用電話登錄後才會遇到無密碼登錄。

在 Web 上鍵入使用者名並選擇 **"下一步"** 後，將為使用者提供一個號碼，並在其 Microsoft 身份驗證器應用中提示使用者選擇適當的號碼進行身份驗證，而不是使用其密碼。 

![使用 Microsoft 身份驗證器應用登錄的瀏覽器示例](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>已知問題

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>使用者未通過策略啟用，但仍在 Microsoft 身份驗證器中具有無密碼電話登錄方法

使用者可能在某個時候在其當前的 Microsoft 身份驗證器應用中或較早的設備上創建了無密碼的電話登錄憑據。 一旦管理員啟用無密碼電話登錄的身份驗證方法策略，任何已註冊憑據的使用者將開始體驗新的登錄提示，無論他們是否已啟用該策略。 如果策略不允許使用者使用憑據，則在完成身份驗證流後，他們將看到錯誤。 

管理員可以選擇允許使用者使用無密碼電話登錄，或者使用者必須刪除該方法。 如果使用者不再擁有已註冊的設備，他們可以轉到[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)並刪除它。 如果他們仍在使用 MFA 的身份驗證器，則可以選擇從 Microsoft 身份驗證器內**禁用電話登錄**。  

### <a name="ad-fs-integration"></a>AD FS 集成

當使用者已啟用 Microsoft Authenticator 無密碼認證時，該使用者的驗證將一律預設為傳送通知以供核准。 此邏輯可防止混合式租用戶中的使用者導向至 ADFS 進行登入驗證，而不需使用者採取額外的步驟來按一下 [改用您的密碼]。 此程序也會略過任何內部部署條件式存取原則和傳遞驗證流程。 

如果使用者有未應答的無密碼電話登錄驗證待定，並嘗試再次登錄，則使用者可能會被帶到 ADFS 輸入密碼。  

### <a name="azure-mfa-server"></a>Azure MFA Server

通過組織的本地 Azure MFA 伺服器啟用 MFA 的最終使用者仍可以創建和使用單個無密碼電話登錄憑據。 如果使用者嘗試使用認證升級 Microsoft Authenticator 的多個安裝 (5+)，這項變更可能會導致錯誤。  

### <a name="device-registration"></a>裝置註冊

創建此新的強憑據的先決條件之一是，安裝 Microsoft 身份驗證器應用的設備也必須在 Azure AD 租戶中註冊給單個使用者。 由於當前設備註冊限制，設備只能在單個租戶中註冊。 此限制表示，Microsoft Authenticator 應用程式中只有一個工作或學校帳戶可以啟用手機登入。

### <a name="intune-mobile-application-management"></a>Intune 行動應用程式管理 

受需要移動應用程式管理 （MAM） 的策略約束的最終使用者無法在 Microsoft 身份驗證器應用中註冊無密碼憑據。 

> [!NOTE]
> 設備註冊與裝置管理或"MDM"不同。 它僅在 Azure AD 目錄中將裝置識別碼 和使用者 ID 關聯在一起。  

## <a name="next-steps"></a>後續步驟

[什麼是無密碼？](concept-authentication-passwordless.md)

[深入了解裝置註冊](../devices/overview.md#getting-devices-in-azure-ad)

[深入了解 Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
