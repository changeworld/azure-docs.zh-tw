---
title: 開始合併註冊 - Azure 活動目錄
description: 開啟群組 Azure AD 多重身份驗證與自助服務密碼重置註冊
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
ms.openlocfilehash: 466b063253ee49ab58c2685f359b4bb8a4079532
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639684"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>在 Azure 活動目錄中啟用群組安全資訊註冊

在合併註冊之前,用戶分別註冊了 Azure 多重身份驗證和自助服務密碼重置 (SSPR) 的身份驗證方法。 人們感到困惑的是,類似的方法被用於多重身份驗證和SSPR,但他們必須註冊這兩個功能。 現在,通過合併註冊,用戶可以註冊一次,並獲得多重身份驗證和 SSPR 的優勢。

在啟用新體驗之前,請查看文章[「組合安全資訊註冊](concept-registration-mfa-sspr-combined.md)」,以確保您瞭解此功能的功能和效果。

![組合安全資訊註冊增強體驗](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>啟用合併的註冊

完成以下步驟以啟用合併註冊:

1. 以使用者管理員或全域管理員的身份登錄到 Azure 門戶。
2. 跳到**Azure 的目錄** > **使用者設定** > **管理使用者功能預覽設定**。
3. **在"使用者"下可以使用預覽功能註冊和管理安全資訊**,選擇為**選定的**使用者組或**所有使用者**啟用。

   ![為所有使用者啟用群組安全資訊預覽體驗](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!NOTE]
> 啟用合併註冊后,通過新體驗註冊或確認其電話號碼或移動應用的使用者可以使用它們進行多重身份驗證和 SSPR,如果這些方法在多重身份驗證和 SSPR 策略中啟用。 如果隨後禁用此體驗,則轉到以前的 SSPR`https://aka.ms/ssprsetup`註冊頁 的使用者將需要執行多重身份驗證,然後才能訪問該頁面。

如果在網際網路資源管理員中設定了網站到區域分配清單,則以下站點必須位於同一區域中:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>合併註冊的條件存取原則

現在可以通過條件訪問策略中的使用者操作來保護用戶註冊 Azure 多重身份驗證和自助服務密碼重置的時以及如何。 此功能可供啟用[合併註冊功能](../authentication/concept-registration-mfa-sspr-combined.md)的組織使用。 在希望使用者從中心位置(如 HR 入職期間受信任的網路位置)註冊 Azure 多重身份驗證和 SSPR 的組織中,可以啟用此功能。

有關在條件訪問中創建受信任位置的詳細資訊,請參閱文章[「Azure 活動目錄條件訪問」中的位置條件是什麼?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>建立策略以需要從受信任位置進行註冊

以下策略適用於嘗試使用組合註冊體驗進行註冊的所有選定使用者,並阻止訪問,除非他們從標記為受信任網路的位置進行連接。

1. 在**Azure 門戶**中,瀏覽到**Azure 活動目錄** > **安全** > **條件存取**
1. 選擇 **= 新原則**
1. 輸入此策略的名稱,例如 *「受信任網路上的安全資訊合併註冊*」。。
1. 在 [指派]**** 底下選取 [使用者和群組]****。 選擇要應用此策略的使用者和組,然後選擇 **"完成**"。

   > [!WARNING]
   > 必須啟用用戶進行合併註冊。

1. 在 **"雲應用"或"操作"** 下,選擇 **"使用者操作**"。 檢查**註冊安全資訊**,然後選擇 **"完成**"。

    ![建立條件存取原則以控制安全資訊註冊](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. 在 **'條件** > **位置**'下,設定以下選項:
   1. 設定**為**
   1. 包括**任何位置**
   1. 排除**所有受信任位置**
1. 在 *'位置*'視窗中選擇 **'完成**',然後在'*的條件*'視窗中選擇 **'完成**"。
1. 在 **'存取控制** > **授予"下**,選擇 **'阻止存取**",然後**選擇**
1. 將**啟用策略**設置為 **"打開"**
1. 要最終確定策略,請選擇 **"創建"**

## <a name="next-steps"></a>後續步驟

如果您需要説明,請參閱如何[排除組合安全資訊註冊故障](howto-registration-mfa-sspr-combined-troubleshoot.md),或瞭解[Azure 活動目錄條件訪問中的位置條件是什麼?](../conditional-access/location-condition.md)

要啟用 Azure AD 租戶中的功能,請參閱[啟用自助服務密碼重置](tutorial-enable-sspr.md)和[啟用 Azure 多重身份驗證 的](tutorial-enable-azure-mfa.md)教程。

瞭解如何[在租戶中啟用合併註冊](howto-registration-mfa-sspr-combined.md)或[強制使用者重新註冊身份驗證方法](howto-mfa-userdevicesettings.md#manage-user-authentication-options)。

您可以檢視 Azure[多重身份認證與 SSPR 可用方法](concept-authentication-methods.md)。
