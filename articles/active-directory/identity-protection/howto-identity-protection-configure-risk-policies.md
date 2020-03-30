---
title: 風險策略 - Azure 活動目錄標識保護
description: 在 Azure 活動目錄標識保護中啟用和配置風險策略
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ffa08f7ebf013d42d6da0589ce0f1ccc97289de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75707000"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>如何：配置和啟用風險策略

正如我們在上一篇文章中瞭解到的[，身份保護原則](concept-identity-protection-policies.md)我們有兩個風險策略，我們可以在我們的目錄中啟用。 

- 登入風險原則
- 使用者風險原則

![安全概述頁面，用於啟用使用者和登錄風險策略](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

這兩種策略都用於自動回應環境中的風險檢測，並允許使用者在檢測到風險時自我修復。 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Prerequisites 

如果您的組織希望允許使用者在檢測到風險時進行自我修復，則必須為自助服務密碼重設和 Azure 多重要素驗證註冊使用者。 我們建議[啟用組合的安全資訊註冊體驗](../authentication/howto-registration-mfa-sspr-combined.md)，以獲得最佳體驗。 允許使用者自我修復可讓他們更快地回到生產狀態，而無需管理員干預。 管理員仍可以看到這些事件，並在事實發生後進行調查。 

## <a name="choosing-acceptable-risk-levels"></a>選擇可接受的風險級別

組織必須決定他們願意接受的風險級別，以平衡使用者體驗和安全狀態。 

微軟的建議是將使用者風險策略閾值設置為 **"高"，** 將登錄風險策略設置為 **"中"及"以上**"。

選擇 [高] **** 臨界值可減少觸發原則的次數，並將對使用者的影響降至最低。 但是，它排除了策略中的**低**風險和**中等**風險檢測，這可能不會阻止攻擊者利用受攻擊的標識。 選擇**低**閾值會引入其他使用者中斷，但安全性增加。

## <a name="exclusions"></a>排除

所有策略都允許排除使用者，如[緊急訪問或碎玻璃管理員帳戶](../users-groups-roles/directory-emergency-access.md)。 組織可能會根據帳戶的使用方式確定他們需要從特定策略中排除其他帳戶。 應定期審查所有排除項，看它們是否仍然適用。

身份保護在某些風險檢測中使用配置的受信任[網路位置](../conditional-access/location-condition.md)，以減少誤報。

## <a name="enable-policies"></a>啟用策略

要啟用使用者風險並登錄風險策略，請完成以下步驟。

1. 導航到[Azure 門戶](https://portal.azure.com)。
1. 流覽到**Azure 活動目錄** > **安全** > **標識保護** > **概述**。
1. 選擇 **"配置使用者風險策略**"。
   1. 在**分配**下
      1. **使用者**- 如果限制推出，請選擇**所有使用者**或**選擇個人和組**。
         1. 可以選擇將使用者從策略中排除。
      1. **條件** - **使用者風險**微軟的建議是將此選項設置為**高**。
   1. 在**控制下**
      1. **訪問**- 微軟的建議是**允許訪問**和**要求密碼更改**。
   1. **執行策略** -  ** **
   1. **保存**- 此操作將返回到 **"概述"** 頁。
1. 選擇**配置登錄風險策略**。
   1. 在**分配**下
      1. **使用者**- 如果限制推出，請選擇**所有使用者**或**選擇個人和組**。
         1. 可以選擇將使用者從策略中排除。
      1. **條件** - **登錄風險**微軟的建議是將此選項設置為**中和以上**。
   1. 在**控制下**
      1. **訪問**- 微軟的建議是**允許訪問**和**要求多重要素驗證**。
   1. **執行策略** -  ** **
   1. **儲存**

## <a name="next-steps"></a>後續步驟

- [啟用 Azure 多重要素驗證註冊策略](howto-identity-protection-configure-mfa-policy.md)

- [什麼是風險](concept-identity-protection-risks.md)

- [調查風險偵測](howto-identity-protection-investigate-risk.md)

- [模擬風險偵測](howto-identity-protection-simulate-risk.md)
