---
title: 開始合併註冊 - Azure 活動目錄
description: 啟用組合 Azure AD 多重要素驗證和自助服務密碼重設註冊（預覽）
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25c64d9e959b1d68de23e83e26d3495bd3939986
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425171"
---
# <a name="enable-combined-security-information-registration-preview"></a>啟用組合安全資訊註冊（預覽）

在啟用新體驗之前，請查看文章["組合安全資訊註冊（預覽）"，](concept-registration-mfa-sspr-combined.md)以確保您瞭解此功能的功能和效果。

![組合安全資訊註冊增強體驗](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Azure 多重要素驗證和 Azure 活動目錄 （Azure AD） 自助服務密碼重設的組合安全資訊註冊是 Azure AD 的公共預覽功能。 有關預覽的詳細資訊，請參閱 Microsoft [Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

> [!NOTE]
> 啟用上一個預覽以註冊和管理安全資訊的組織應完成以下步驟，以啟用增強的預覽體驗。 對於未進行切換的組織，Microsoft 將在 2019 年 10 月 8 日將以前預覽版的使用者切換到增強型體驗。 
> 
> 如果您尚未啟用預覽版的任何版本，您的組織將不會受到影響。

## <a name="enable-combined-registration"></a>啟用合併的註冊

完成以下步驟以啟用合併註冊：

1. 以使用者管理員或全域管理員的身份登錄到 Azure 門戶。
2. 轉到**Azure 活動目錄** > **使用者設置** > **管理使用者功能預覽設置**。
3. **在"使用者"下可以使用預覽功能註冊和管理安全資訊**，選擇為**選定的**使用者組或**所有使用者**啟用。

   ![為所有使用者啟用組合安全資訊預覽體驗](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!IMPORTANT]
> 從 2019 年 3 月開始，免費/試用 Azure AD 租戶中的多重要素驗證和 SSPR 使用者將不能使用撥打電話選項。 SMS 消息不受此更改的影響。 撥打電話選項仍可供付費 Azure AD 租戶中的使用者使用。

> [!NOTE]
> 啟用合併註冊後，通過新體驗註冊或確認其電話號碼或移動應用的使用者可以使用它們進行多重要素驗證和 SSPR，如果這些方法在多重要素驗證和 SSPR 中啟用政策。 如果隨後禁用此體驗，則轉到以前的 SSPR 註冊頁`https://aka.ms/ssprsetup`的使用者將需要執行多重要素驗證，然後才能訪問該頁面。

如果在 Internet 資源管理器中配置了網站到區域分配清單，則以下網站必須位於同一區域中：

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>合併註冊的條件訪問策略

現在可以通過條件訪問策略中的使用者操作來保護使用者註冊 Azure 多重要素驗證和自助服務密碼重設的時以及如何。 此預覽功能可供啟用[合併註冊預覽](../authentication/concept-registration-mfa-sspr-combined.md)的組織使用。 在希望使用者從中心位置（如 HR 入職期間受信任的網路位置）註冊 Azure 多重要素驗證和 SSPR 的組織中，可以啟用此功能。 有關在條件訪問中創建受信任位置的詳細資訊，請參閱文章["Azure 活動目錄條件訪問"中的位置條件是什麼？](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>創建策略以需要從受信任位置進行註冊

以下策略適用于所有選擇的使用者，這些使用者嘗試使用組合註冊體驗進行註冊，並阻止訪問，除非他們從標記為受信任網路的位置進行連接。

![創建 CA 策略以控制安全資訊註冊](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. 在**Azure 門戶**中，流覽到**Azure 活動目錄** > **安全** > **條件訪問**
1. 選擇**新策略**
1. 在"名稱"中，輸入此策略的名稱。 例如，**在受信任的網路上合併安全資訊註冊**
1. 在 **"分配"** 下，按一下 **"使用者和組**"，然後選擇要應用於此策略的使用者和組

   > [!WARNING]
   > 必須為[組合註冊預覽](../authentication/howto-registration-mfa-sspr-combined.md)啟用使用者。

1. 在 **"雲應用"或"操作**"下，選擇 **"使用者操作"，** 檢查**註冊安全資訊（預覽）**
1. 在**條件** > **位置**
   1. 配置**是**
   1. 包括**任何位置**
   1. 排除**所有受信任位置**
   1. 按一下"在位置"邊欄選項卡上**完成**
   1. 按一下"條件"邊欄選項卡上的 **"完成"**
1. **在"存取控制** > **"下授予**
   1. 按一下 **"阻止訪問**"
   1. 然後按一下 **"選擇"**
1. 將**啟用策略**設置為 **"打開"**
1. 然後按一下"**創建"**

## <a name="next-steps"></a>後續步驟

[強制使用者重新註冊身份驗證方法](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[多因素身份驗證和 SSPR 的可用方法](concept-authentication-methods.md)

[配置自助服務密碼重設](howto-sspr-deployment.md)

[設定 Azure Multi-Factor Authentication](howto-mfa-getstarted.md)

[故障排除組合安全資訊註冊](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Azure 活動目錄條件訪問中的位置條件是什麼？](../conditional-access/location-condition.md)
