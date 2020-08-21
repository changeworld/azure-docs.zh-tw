---
title: 無密碼使用 Microsoft Authenticator 應用程式登入-Azure Active Directory
description: '使用 Microsoft Authenticator 應用程式 (預覽，啟用無密碼登入 Azure AD) '
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ed1c3628b33d3ed29c3af3b773f2b635e684a67
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717042"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>使用 Microsoft Authenticator 應用程式 (預覽版來啟用無密碼登入) 

Microsoft Authenticator 應用程式可用來登入任何 Azure AD 帳戶 (而不需使用密碼)。 類似於 [Windows Hello 企業版](/windows/security/identity-protection/hello-for-business/hello-identity-verification) 的技術，Microsoft Authenticator 會使用金鑰型驗證來啟用已繫結至裝置並使用生物特徵識別或 PIN 的使用者認證。 此驗證方法可用於任何裝置平臺，包括行動裝置，以及與 Microsoft 驗證程式庫整合的任何應用程式或網站。 

![要求使用者核准登入的瀏覽器登入範例](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

除了在輸入使用者名稱之後看到密碼提示之外，從 Microsoft Authenticator 應用程式啟用手機登入的人員將會看到一則訊息，告知他們在其應用程式中使用數位。 在應用程式中，使用者必須符合數目，選擇 [核准]，然後提供他們的 PIN 或生物特徵辨識，然後驗證將會完成。

> [!NOTE]
> 自2017年3月起，這項功能已在 Microsoft Authenticator 應用程式中，因此在啟用目錄的原則時，使用者可能會立即遇到此流程，並會在原則未啟用時看到錯誤訊息。 請留意這項變更，並且讓使用者做好準備。

## <a name="prerequisites"></a>Prerequisites

- 使用推播通知作為驗證方法的 Azure Multi-Factor Authentication 
- 在執行 iOS 8.0 或更新版本或 Android 6.0 或更新版本的裝置上安裝的最新版 Microsoft Authenticator。

> [!NOTE]
> 如果您已使用 Azure AD PowerShell 啟用先前的 Microsoft Authenticator 應用程式無密碼登入預覽，則會為整個目錄啟用。 如果您使用這個新方法啟用，將會取代 PowerShell 原則。 建議您透過新的驗證方法為您租使用者中的所有使用者啟用，否則不在新原則中的使用者將無法再登入 passwordlessly。 

## <a name="enable-passwordless-authentication-methods"></a>啟用無密碼 authentication 方法

### <a name="enable-the-combined-registration-experience"></a>啟用結合的註冊體驗

無密碼 authentication 方法的註冊功能依賴于合併的註冊功能。 遵循本文中的步驟來 [啟用合併的安全性資訊註冊](howto-registration-mfa-sspr-combined.md)，以啟用合併的註冊。

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>啟用無密碼手機登入驗證方法

1. 使用**全域管理員**帳戶登入[Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取 [Azure Active Directory]  。 選取**安全性**  >  **驗證方法**  >  **驗證方法原則 (預覽) **
1. 在 [ **無密碼 phone 登入**] 底下，選擇下列選項
   1. **啟用** -是或否
   1. **目標** -所有使用者或選取使用者
1. **儲存** 以設定新的原則

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Microsoft Authenticator 應用程式的使用者註冊與管理

1. 瀏覽至 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. 登入（如果尚未登入）
1. 按一下 [**新增方法**] 以新增驗證器應用程式，選擇 [**驗證器應用程式**]，然後按一下 [**新增**]
1. 遵循指示，在您的裝置上安裝和設定 Microsoft Authenticator 應用程式
1. 按一下 [ **完成** ] 以完成驗證器 MFA 應用程式設定流程。 
1. 在 **Microsoft Authenticator**中，從 [帳戶] 下拉式功能表選擇 [ **啟用手機登入** ]。
1. 依照應用程式中的指示，完成無密碼手機登入的註冊。 

組織可以將他們的使用者指向 [使用您的電話登入的文章，而不是您的密碼，](../user-help/user-help-auth-app-sign-in.md) 以進一步協助您在 Microsoft Authenticator 應用程式中進行設定並啟用手機登入。 為了套用這些設定，您可能需要登出再重新登入租使用者。 

## <a name="sign-in-with-passwordless-credential"></a>使用無密碼認證登入

針對公開預覽版，沒有任何方式可強制使用者建立或使用這個新認證。 只要系統管理員已啟用使用者的租使用者 **，而且** 使用者已更新其 Microsoft Authenticator 應用程式來啟用手機登入，使用者就只會遇到無密碼登入。

在 web 上輸入您的使用者名稱並選取 **[下一步]** 之後，使用者會看到一個數位，並在 Microsoft Authenticator 應用程式中提示您選取適當的號碼來進行驗證，而不是使用其密碼。 

![使用 Microsoft Authenticator 應用程式的瀏覽器登入範例](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>已知問題

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>使用者未啟用原則，但仍有無密碼的手機登入方法 Microsoft Authenticator

使用者可能會在某個時間點，在其目前的 Microsoft Authenticator 應用程式或較舊的裝置上建立無密碼手機登入認證。 當系統管理員啟用無密碼手機登入的驗證方法原則之後，任何已註冊認證的使用者都將開始體驗新的登入提示，不論是否已啟用使用原則。 如果使用者不允許依原則使用認證，則會在完成驗證流程後看到錯誤。 

系統管理員可以選擇讓使用者使用無密碼手機登入，或使用者必須移除方法。 如果使用者不再具有已註冊的裝置，他們可以移至該裝置， [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 並加以移除。 如果它們仍在使用驗證器進行 MFA，則可以選擇在 Microsoft Authenticator 中 **停用手機登入** 。  

### <a name="ad-fs-integration"></a>AD FS 整合

當使用者已啟用 Microsoft Authenticator 無密碼認證時，該使用者的驗證將一律預設為傳送通知以供核准。 此邏輯可防止混合式租使用者中的使用者被導向至 AD FS 進行登入驗證，而不需要使用者採取額外的步驟來按一下「改用您的密碼」。 此程序也會略過任何內部部署條件式存取原則和傳遞驗證流程。 

如果使用者有未接聽的無密碼手機登入驗證暫止，並嘗試重新登入，則使用者可能會被 AD FS 視為改為輸入密碼。  

### <a name="azure-mfa-server"></a>Azure MFA Server

透過組織的內部部署 Azure MFA 伺服器啟用 MFA 的使用者，仍然可以建立及使用單一無密碼手機登入認證。 如果使用者嘗試使用認證升級 Microsoft Authenticator 的多個安裝 (5+)，這項變更可能會導致錯誤。  

### <a name="device-registration"></a>裝置註冊

建立這個新強式認證的必要條件之一，是安裝 Microsoft Authenticator 應用程式的裝置，也必須在 Azure AD 租使用者中註冊給個別使用者。 由於目前的裝置註冊限制，裝置只能在單一租使用者中註冊。 此限制表示，Microsoft Authenticator 應用程式中只有一個工作或學校帳戶可以啟用手機登入。

### <a name="intune-mobile-application-management"></a>Intune 行動應用程式管理 

受原則要求 (MAM 的行動應用程式管理的使用者，) 無法在 Microsoft Authenticator 應用程式中註冊無密碼認證。 

> [!NOTE]
> 裝置註冊與裝置管理或「MDM」不同。 它只會將裝置識別碼和使用者識別碼關聯到 Azure AD 目錄中。  

## <a name="next-steps"></a>後續步驟

[瞭解無密碼 authentication 的運作方式](concept-authentication-passwordless.md)

[深入了解裝置註冊](../devices/overview.md#getting-devices-in-azure-ad)

[深入了解 Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)