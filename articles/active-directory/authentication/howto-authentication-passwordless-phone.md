---
title: 無密碼使用 Microsoft Authenticator 應用程式登入-Azure Active Directory
description: '使用 Microsoft Authenticator 應用程式 (預覽，啟用無密碼登入 Azure AD) '
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9da0c00bd8498e3f43d5f8258308fbc010a6a274
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94839517"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>使用 Microsoft Authenticator 應用程式 (預覽版來啟用無密碼登入) 

Microsoft Authenticator 應用程式可用來登入任何 Azure AD 帳戶 (而不需使用密碼)。 Microsoft Authenticator 使用以金鑰為基礎的驗證，以啟用系結至裝置的使用者認證，而裝置會使用 PIN 或生物特徵辨識。 [Windows Hello 企業版](/windows/security/identity-protection/hello-for-business/hello-identity-verification) 使用類似的技術。

這項驗證技術可用於任何裝置平臺，包括行動裝置。 這項技術也可以搭配與 Microsoft 驗證程式庫整合的任何應用程式或網站使用。

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png" alt-text="瀏覽器登入的範例，要求使用者核准登入。":::

從 Microsoft Authenticator 應用程式啟用手機登入的使用者會看到一則訊息，要求他們在其應用程式中的數位上按一下。 系統不會要求使用者名稱或密碼。 若要在應用程式中完成登入程式，使用者必須執行下列動作：

1. 符合數位。
2. 選擇 [核准]。
3. 提供他們的 PIN 或生物特徵辨識。

## <a name="prerequisites"></a>先決條件

若要在 Microsoft Authenticator 應用程式中使用無密碼手機登入，必須符合下列必要條件：

- Azure AD Multi-Factor Authentication，並允許推播通知作為驗證方法。
- 在執行 iOS 8.0 或更新版本或 Android 6.0 或更新版本的裝置上安裝的最新版 Microsoft Authenticator。

> [!NOTE]
> 如果您使用 Azure AD PowerShell 啟用 Microsoft Authenticator 無密碼登入預覽，則會為整個目錄啟用。 如果您使用這個新方法來啟用，則會取代 PowerShell 原則。 建議您透過 [新的 *驗證方法* ] 功能表為您租使用者中的所有使用者啟用，否則不在新原則中的使用者將無法再登入，而不需要使用密碼。

## <a name="enable-passwordless-authentication-methods"></a>啟用無密碼 authentication 方法

若要在 Azure AD 中使用無密碼 authentication，請先啟用合併的註冊體驗，然後讓使用者使用較少的密碼方法。

### <a name="enable-the-combined-registration-experience"></a>啟用結合的註冊體驗

無密碼 authentication 方法的註冊功能依賴于合併的註冊功能。 若要讓使用者自行完成合併的註冊，請遵循下列步驟來 [啟用合併的安全性資訊註冊](howto-registration-mfa-sspr-combined.md)。

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>啟用無密碼手機登入驗證方法

Azure AD 可讓您選擇在登入程式期間可以使用的驗證方法。 然後，使用者會註冊他們想要使用的方法。

若要啟用無密碼手機登入的驗證方法，請完成下列步驟：

1. 使用 *全域管理員* 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取 *Azure Active Directory*，然後流覽至 **安全性**  >  **驗證方法** 的  >  **驗證方法原則 (Preview)**
1. 在 [ **無密碼 phone 登入**] 底下，選擇下列選項：
   1. **啟用** -是或否
   1. **目標** -所有使用者或選取使用者
1. 若要套用新的原則，請選取 [ **儲存**]。

## <a name="user-registration-and-management-of-microsoft-authenticator"></a>Microsoft Authenticator 的使用者註冊與管理

使用者會使用下列步驟，自行註冊 Azure AD 的無密碼 authentication 方法：

1. 瀏覽至 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)。
1. 登入，然後選取 [ **新增方法 > 驗證器應用程式**]，然後 **新增**，以新增驗證器應用程式。
1. 遵循指示，在裝置上安裝和設定 Microsoft Authenticator 應用程式。
1. 選取 [ **完成** ] 以完成驗證器設定。
1. 在 **Microsoft Authenticator** 中，從已註冊帳戶的下拉式功能表中選擇 [ **啟用手機登入** ]。
1. 依照應用程式中的指示，完成註冊帳戶以進行無密碼手機登入。

組織可以指示其使用者使用手機登入，而不需要使用密碼。 如需設定 Microsoft Authenticator 應用程式及啟用手機登入的進一步協助，請參閱 [使用 Microsoft Authenticator 應用程式登入您的帳戶](../user-help/user-help-auth-app-sign-in.md)。

> [!NOTE]
> 原則無法使用手機登入的使用者，將無法再于 Microsoft Authenticator 應用程式內啟用。

## <a name="sign-in-with-passwordless-credential"></a>使用無密碼認證登入

在完成下列所有動作之後，使用者可以開始使用無密碼登入：

- 系統管理員已啟用使用者的租使用者。
- 使用者已更新她的 Microsoft Authenticator 應用程式來啟用手機登入。

使用者第一次啟動手機登入程式時，使用者會執行下列步驟：

1. 在登入頁面上輸入她的名稱。
2. 選取 **[下一步]**。
3. 如有必要，請選取 **其他登入方式**。
4. 選取 [ **在我的 Microsoft Authenticator 應用程式上核准要求**]。

然後，使用者會看到一個數位。 應用程式會提示使用者選取適當的號碼來進行驗證，而不是輸入密碼。

在使用者使用無密碼手機登入之後，應用程式會繼續引導使用者完成此方法。 不過，使用者將會看到選擇其他方法的選項。

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png" alt-text="使用 Microsoft Authenticator 應用程式的瀏覽器登入範例。":::

## <a name="known-issues"></a>已知問題

目前的預覽體驗中有下列已知問題。

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>無密碼手機登入沒有看到選項

在一個案例中，使用者可以有擱置中的未回復無密碼手機登入驗證。 但使用者可能會嘗試重新登入。 發生這種情況時，使用者可能只會看到輸入密碼的選項。

若要解決此情況，可以使用下列步驟：

1. 開啟 Microsoft Authenticator 應用程式。
2. 回應任何通知提示。

然後，使用者可以繼續使用無密碼手機登入。

### <a name="federated-accounts"></a>同盟帳戶

當使用者已啟用任何無密碼認證時，Azure AD 登入程式就會停止使用登入 \_ 提示。 因此，此程式不再加速使用者前往同盟登入位置。

此邏輯通常會防止混合式租使用者中的使用者被導向 Active Directory 同盟服務， (AD FS) 進行登入驗證。 但是，使用者會保留按一下 [ **改用您的密碼**] 的選項。

### <a name="azure-mfa-server"></a>Azure MFA Server

您可以透過內部部署 Azure MFA 伺服器，對終端使用者啟用 (MFA) 的多重要素驗證。 使用者仍然可以建立及使用單一無密碼手機登入認證。

如果使用者嘗試使用無密碼手機登入認證升級多個安裝 (5 +) 的 Microsoft Authenticator 應用程式，這項變更可能會導致錯誤。

### <a name="device-registration"></a>裝置註冊

您必須具備必要條件，才能建立這個新的強式認證。 其中一個必要條件是安裝 Microsoft Authenticator 應用程式的裝置，必須在 Azure AD 租使用者中註冊給個別使用者。

目前，裝置只能在單一租使用者中註冊。 此限制表示，Microsoft Authenticator 應用程式中只有一個工作或學校帳戶可以啟用手機登入。

> [!NOTE]
> 裝置註冊與裝置管理或行動裝置管理 (MDM) 不同。 裝置註冊只會將裝置識別碼和使用者識別碼關聯到 Azure AD 目錄中。

## <a name="next-steps"></a>後續步驟

若要瞭解 Azure AD authentication 和無密碼方法的詳細資訊，請參閱下列文章：

- [瞭解無密碼 authentication 的運作方式](concept-authentication-passwordless.md)
- [深入了解裝置註冊](../devices/overview.md#getting-devices-in-azure-ad)
- [瞭解 Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
