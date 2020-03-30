---
title: 構建條件訪問策略 - Azure 活動目錄
description: 生成條件訪問策略的所有選項有哪些？
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 907ad8705742e4b2e38b13c3c675ebd333bd27d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295314"
---
# <a name="building-a-conditional-access-policy"></a>構建條件訪問策略

如文章"[什麼是條件訪問"](overview.md)中所述，條件訪問策略是 **"分配**"和"**訪問"控制項**的 if-then 語句。 條件訪問策略將信號彙集在一起，以做出決策，並強制實施組織策略。

組織如何創建這些策略？ 需要什麼？

![條件訪問（信號 + 決策 + 執行 + 策略）](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>指派

分配部分控制條件訪問策略的人員、內容和位置。

### <a name="users-and-groups"></a>使用者和群組

[使用者和組](concept-conditional-access-users-groups.md)分配策略將包含或排除誰。 此分配可以包括所有使用者、特定使用者組、目錄角色或外部來賓使用者。 

### <a name="cloud-apps-or-actions"></a>雲端應用程式或動作

[雲應用或操作](concept-conditional-access-cloud-apps.md)可以包括或排除受該政策約束的雲應用程式或使用者操作。

### <a name="conditions"></a>條件

策略可以包含多個[條件](concept-conditional-access-conditions.md)。

#### <a name="sign-in-risk"></a>登入風險

對於具有[Azure AD 標識保護](../identity-protection/overview.md)的組織，在那裡生成的風險檢測可能會影響條件訪問策略。

#### <a name="device-platforms"></a>裝置平台

具有多個設備作業系統平臺的組織可能希望在不同的平臺上強制實施特定策略。 

用於計算裝置平臺的資訊來自未經驗證的源，如可以更改的使用者代理字串。

#### <a name="locations"></a>位置

位置資料由 IP 地理位置資料提供。 管理員可以選擇定義位置，並選擇將某些位置標記為受信任的位置，例如為其組織的網路位置標記為受信任位置。

#### <a name="client-apps"></a>用戶端應用程式

預設情況下，條件訪問策略適用于支援現代身份驗證的瀏覽器應用、移動應用和桌面用戶端。 

此分配條件允許條件訪問策略針對不使用現代身份驗證的特定用戶端應用程式。 這些應用程式包括 Exchange ActiveSync 用戶端、不使用現代身份驗證的舊 Office 應用程式以及 IMAP、MAPI、POP 和 SMTP 等郵件協定。

#### <a name="device-state"></a>裝置狀態

此控制項用於排除混合 Azure AD 聯接或在 Intune 中標記為符合要求的設備。 可以執行此排除來阻止非託管設備。 

## <a name="access-controls"></a>存取控制

存取控制條件訪問策略的部分控制策略的強制執行方式。

### <a name="grant"></a>授與

[授予](concept-conditional-access-grant.md)為管理員提供了一種策略實施方法，他們可以阻止或授予存取權限。

#### <a name="block-access"></a>封鎖存取

阻止訪問這樣做，它將阻止在指定的分配下的訪問。 塊控制功能強大，應具有適當的知識。

#### <a name="grant-access"></a>授與存取權

授予控制項可以觸發一個或多個控制項的強制執行。 

- 需要多重要素驗證（Azure 多重要素驗證）
- 要求將設備標記為符合（Intune）
- 需要已加入混合式 Azure AD 的裝置
- 需要已核准的用戶端應用程式
- 需要應用程式保護原則

管理員可以選擇使用以下選項要求以前的控制項之一或所有選定的控制項。 多個控制項的預設值是要求全部。

- 要求所有選定的控制項（控制和控制）
- 需要其中一個選定的控制項（控制項或控制項）

### <a name="session"></a>工作階段

[會話控制項](concept-conditional-access-session.md)可以限制體驗 

- 使用應用程式強制執行限制
   - 目前僅適用于"線上交換"和"線上共用點"。
      - 傳遞設備資訊，以控制授予完全或有限存取權限的經驗。
- 使用條件訪問應用控制項
   - 使用來自 Microsoft 雲應用安全的信號執行諸如： 
      - 阻止敏感文檔的下載、剪切、複製和列印。
      - 監視危險的會話行為。
      - 需要標記敏感檔。
- 登錄頻率
   - 能夠更改現代身份驗證的預設登錄頻率。
- 持久瀏覽器會話
   - 允許使用者在關閉和重新打開瀏覽器視窗後保持登錄狀態。

## <a name="simple-policies"></a>簡單的策略

條件訪問策略必須至少包含以下要強制執行：

- 策略**的名稱。**
- **指派**
   - 要將策略應用於**的使用者和/或組**。
   - 要將策略應用到的**雲應用或操作**。
- **存取控制**
   - **授予**或**阻止**控制項

![空白條件訪問策略](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

文章["通用條件訪問"策略](concept-conditional-access-policy-common.md)包括我們認為對大多陣列織有用的一些策略。

## <a name="next-steps"></a>後續步驟

[使用條件訪問"如果"工具類比登錄行為](troubleshoot-conditional-access-what-if.md)

[規劃雲端式 Azure Multi-Factor Authentication 部署](../authentication/howto-mfa-getstarted.md) \(部分機器翻譯\)

[管理設備符合 Intune](/intune/device-compliance-get-started)

[微軟雲應用安全和條件訪問](/cloud-app-security/proxy-intro-aad)
