---
title: 開始合併註冊 - Azure 活動目錄
description: 開啟群組 Azure AD 多重身份驗證和自助服務密碼重置註冊(預覽)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f454b0296a3463d7346c2945b21162e5a38c0eb7
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652514"
---
# <a name="enable-combined-security-information-registration-preview"></a>開啟群組安全資訊註冊(預覽)

在啟用新體驗之前,請查看文章[「組合安全資訊註冊(預覽)」,](concept-registration-mfa-sspr-combined.md)以確保您瞭解此功能的功能和效果。

![組合安全資訊註冊增強體驗](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Azure 多重身份驗證和 Azure 活動目錄 (Azure AD) 自助服務密碼重置的組合安全資訊註冊是 Azure AD 的公共預覽功能。 有關預覽的詳細資訊,請參閱 Microsoft [Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

> [!NOTE]
> 啟用上一個預覽以註冊和管理安全資訊的組織應完成以下步驟,以啟用增強的預覽體驗。 對於未進行切換的組織,Microsoft 將在 2019 年 10 月 8 日將以前預覽版的使用者切換到增強型體驗。 
> 
> 如果您尚未啟用預覽版的任何版本,您的組織將不會受到影響。

## <a name="enable-combined-registration"></a>啟用合併的註冊

完成以下步驟以啟用合併註冊:

1. 以使用者管理員或全域管理員的身份登錄到 Azure 門戶。
2. 跳到**Azure 的目錄** > **使用者設定** > **管理使用者功能預覽設定**。
3. **在"使用者"下可以使用預覽功能註冊和管理安全資訊**,選擇為**選定的**使用者組或**所有使用者**啟用。

   ![為所有使用者啟用群組安全資訊預覽體驗](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!IMPORTANT]
> 從 2019 年 3 月開始,免費/試用 Azure AD 租戶中的多重身份驗證和 SSPR 使用者將不能使用電話呼叫選項。 SMS 消息不受此更改的影響。 電話呼叫選項仍可供付費 Azure AD 租戶中的使用者使用。

> [!NOTE]
> 啟用合併註冊后,通過新體驗註冊或確認其電話號碼或移動應用的使用者可以使用它們進行多重身份驗證和 SSPR,如果這些方法在多重身份驗證和 SSPR 策略中啟用。 如果隨後禁用此體驗,則轉到以前的 SSPR`https://aka.ms/ssprsetup`註冊頁 的使用者將需要執行多重身份驗證,然後才能訪問該頁面。

如果在網際網路資源管理員中設定了網站到區域分配清單,則以下站點必須位於同一區域中:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>合併註冊的條件存取原則

現在可以通過條件訪問策略中的使用者操作來保護用戶註冊 Azure 多重身份驗證和自助服務密碼重置的時以及如何。 此預覽功能可供啟用[合併註冊預覽](../authentication/concept-registration-mfa-sspr-combined.md)的組織使用。 在希望使用者從中心位置(如 HR 入職期間受信任的網路位置)註冊 Azure 多重身份驗證和 SSPR 的組織中,可以啟用此功能。 有關在條件訪問中創建受信任位置的詳細資訊,請參閱文章[「Azure 活動目錄條件訪問」中的位置條件是什麼?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>建立策略以需要從受信任位置進行註冊

以下策略適用於所有選擇的使用者,這些用戶嘗試使用組合註冊體驗進行註冊,並阻止訪問,除非他們從標記為受信任網路的位置進行連接。

![建立 CA 政策以控制安全資訊註冊](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. 在**Azure 門戶**中,瀏覽到**Azure 活動目錄** > **安全** > **條件存取**
1. 選擇**新原則**
1. 在"名稱"中,輸入此策略的名稱。 例如,**在受信任的網路上合併安全資訊註冊**
1. 在 **「配置」** 下,按下 **「使用者和群組**」,然後選擇要套用於此政策的使用者和群組

   > [!WARNING]
   > 必須為[組合註冊預覽](../authentication/howto-registration-mfa-sspr-combined.md)啟用使用者。

1. 在 **"雲應用"或"操作**"下,選擇 **"使用者操作",** 檢查**註冊安全資訊(預覽)**
1. 在**條件** > **位置**
   1. 設定**為**
   1. 包括**任何位置**
   1. 排除**所有受信任位置**
   1. 點選「在位置」邊欄選項卡上**完成**
   1. 按下"條件"邊欄選項卡上的 **"完成"**
1. **在「存取控制** > **」下授予**
   1. 按下 **"阻止存取**"
   1. 然後單擊 **"選擇"**
1. 將**啟用策略**設置為 **"打開"**
1. 然後單擊"**創建"**

## <a name="next-steps"></a>後續步驟

[強制使用者重新註冊身份驗證方法](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[多因素身份驗證與 SSPR 的可用方法](concept-authentication-methods.md)

[設定自助服務密碼重置](howto-sspr-deployment.md)

[設定 Azure Multi-Factor Authentication](howto-mfa-getstarted.md)

[容錯排除組合安全資訊註冊](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Azure 活動目錄條件訪問中的位置條件是什麼?](../conditional-access/location-condition.md)
