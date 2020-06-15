---
title: 開始使用合併註冊 - Azure Active Directory
description: 啟用合併 Azure AD Multi-Factor Authenticaiton 和自助密碼重設註冊
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40266f1b340ebe0ab665c576ff3be0e62ba7c705
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798256"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>啟用 Azure Active Directory 中的合併安全性資訊註冊

在有合併註冊之前，使用者要分開註冊 Azure Multi-Factor Authentication 和自助式密碼重設 (SSPR) 的驗證方法。 用於 Azure Multi-Factor Authentication 與 SSPR 的方法很類似，會讓人混淆，卻不得不註冊這兩個功能。 現在有了合併註冊，使用者只要註冊一次，就能同時得到 Azure Multi-Factor Authentication 和 SSPR 的益處。

在啟用新體驗之前，請先參閱[合併的安全性資訊註冊](concept-registration-mfa-sspr-combined.md)一文，以確保您了解這項功能的功能和效果。

![合併安全性資訊增強的體驗](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>啟用合併的註冊

請完成下列步驟啟用合併註冊：

1. 以使用者管理員或全域管理員身分登入 Azure 入口網站。
2. 移至 [Azure Active Directory] > [使用者設定] > [管理使用者介面預覽設定]。
3. 在 [使用者可以使用合併安全性資訊註冊體驗] 下方，選擇要為 [已選取] 的使用者群組或為 [所有] 使用者啟用此功能。

   ![為使用者啟用合併安全性資訊體驗](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> 在您啟用合併註冊之後，透過新體驗註冊或確認電話號碼或行動裝置應用程式的使用者，可以將那些資料用於 Azure Multi-Factor Authentication 和 SSPR (如果那些方法已在 Azure Multi-Factor Authentication 和 SSPR 原則中啟用)。 接著，如果您停用此體驗，則前往先前 SSPR 註冊頁面 (`https://aka.ms/ssprsetup`) 的使用者將必須執行多重要素驗證才能存取該頁面。

如果您已在 Internet Explorer 中設定 [指派網站到區域清單]，則下列網站必須位於相同的區域中：

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>合併註冊的條件式存取原則

在條件式存取原則中，您現在可以透過使用者動作來對使用者註冊 Azure Multi-Factor Authentication 和自助式密碼重設的時間及方式進行保護。 此功能適用於已啟用[合併註冊功能](../authentication/concept-registration-mfa-sspr-combined.md)的組織。 當組織想要使用者從中央位置 (例如 HR 上線期間的信任網路位置) 註冊 Azure Multi-Factor Authentication 和 SSPR 時，即可在該組織啟用此功能。

如需更多有關以條件式存取建立信任位置的資訊，請參閱 [Azure Active Directory 條件式存取中的位置條件是什麼？](../conditional-access/location-condition.md#named-locations)一文

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>建立原則以要求從信任位置註冊

下列原則會套用到所有選取的使用者，在他們嘗試使用合併的註冊體驗進行註冊時，如果不是從標示為信任網路的位置進行連線，則會封鎖他們的存取權。

1. 在 **Azure 入口網站**中，瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [+ 新增原則]。
1. 輸入此原則的名稱，例如*信任網路上的合併安全性資訊資訊*。
1. 在 [指派] 底下選取 [使用者和群組]。 選擇您想要套用此原則的使用者和群組，然後選取 [完成]。

   > [!WARNING]
   > 使用者必須受到允許才能使用合併註冊。

1. 在 [雲端應用程式或動作] 下，選取 [使用者動作]。 勾選 [註冊安全性資訊]，然後選取 [完成]。

    ![建立條件式存取原則以控制安全性資訊註冊](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. 在 [條件] > [位置] 下方，設定下列選項：
   1. 設定 [是]。
   1. 包含 [任何位置]。
   1. 排除 [所有信任的位置]。
1. 在 [位置] 視窗上選取 [完成]，然後在 [條件] 視窗上選取 [完成]。
1. 在 [存取控制] > [授與] 下方，選擇 [封鎖存取]，然後選擇 [選取]。
1. 將 [啟用原則] 設定為 [開啟]。
1. 若要完成原則，選取 [建立]。

## <a name="next-steps"></a>後續步驟

如果您需要協助，請參閱如何[疑難排解合併安全性資訊註冊](howto-registration-mfa-sspr-combined-troubleshoot.md)，或了解 [Azure Active Directory 條件式存取中的位置條件為何？](../conditional-access/location-condition.md)

若要啟用 Azure AD 租用戶中的功能，請參閱教學課程以[啟用自助式密碼重設](tutorial-enable-sspr.md)並[啟用 Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md)。

了解如何[強制使用者重新註冊驗證方法](howto-mfa-userdevicesettings.md#manage-user-authentication-options)。

您也可以檢閱 [Azure Multi-Factor Authentication 與 SSPR 的可用方法](concept-authentication-methods.md)。
