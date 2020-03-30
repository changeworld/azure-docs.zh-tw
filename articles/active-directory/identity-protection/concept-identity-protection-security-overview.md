---
title: Azure 活動目錄標識保護安全概述
description: 瞭解"安全概述"如何讓您深入瞭解組織的安全狀況。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9550684ad154f28a02ee347fd0a79c1ec286beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382190"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory Identity Protection - 安全性概觀

Azure 門戶中[的安全概述](https://aka.ms/IdentityProtectionRefresh)讓您深入瞭解組織的安全狀況。 它可協助您找出潛在的攻擊並了解原則的效果。

「安全性概觀」大致分成兩個部分：

- 趨勢位於左側，會提供組織中的風險時間軸。
- 圖格位於右側，會顯示組織當前的重要問題，並提供快速因應的建議策略。

![安全性概觀](./media/concept-identity-protection-security-overview/01.png)
  
## <a name="trends"></a>趨勢

### <a name="new-risky-users-detected"></a>偵測到新的具風險使用者

此圖表顯示在所選的時段內新偵測到具風險的使用者數目。 您可以依使用者風險層級 (低、中、高) 篩選此圖表的檢視。 將游標暫留在 UTC 日期遞增數字上方，可檢視當天偵測到具風險的使用者數目。 按一下此圖表，您就會進入 [具風險的使用者] 報告中。 若要修正有風險的使用者，請考慮變更其密碼。

### <a name="new-risky-sign-ins-detected"></a>偵測到新的具風險登入

此圖表顯示在所選的時段內偵測到具風險的登入數目。 您可以依登入風險類型 (即時或彙總) 和登入風險層級 (低、中、高) 來篩選此圖表的的檢視。 未受保護的登入是指未受到 MFA 挑戰的成功即時風險登入。 （注意：由於離線檢測而存在風險的登錄無法通過登錄風險策略即時保護）。 將游標暫留在 UTC 日期遞增數字上方，可檢視當天偵測到有風險的登入數目。 按一下此圖表，您就會進入 [具風險的登入] 報告中。

## <a name="tiles"></a>圖格
 
### <a name="high-risk-users"></a>高度風險使用者

[高度風險使用者] 圖格會顯示身分識別遭入侵機率偏高的最新使用者計數。 這應該是調查的首要任務。 按一下 [高度風險使用者] 圖格將會重新導向至 [具風險的使用者] 報告的篩選檢視，其中僅顯示風險層級為「高」的使用者。 使用此報告可了解詳情，並透過密碼重設來修正這些使用者。

![安全性概觀](./media/concept-identity-protection-security-overview/02.png)

### <a name="medium-risk-users"></a>中度風險使用者
[中度風險使用者] 圖格會顯示身分識別遭入侵的機率中等的最新使用者計數。 按一下 [中度風險使用者] 圖格將會重新導向至 [具風險的使用者] 報告的篩選檢視，其中僅顯示風險層級為「中」的使用者。 使用此報告可進一步調查並修正這些使用者。

### <a name="unprotected-risky-sign-ins"></a>未受保護的具風險登入

"未受保護的風險登錄"磁貼顯示上周成功的即時風險登錄計數，這些登錄未被條件訪問策略、身份保護風險策略或每個使用者 MFA 阻止或 MFA 提出質疑。 這些是可能遭到入侵、但成功且未受到 MFA 挑戰的登入。 若要在未來保護這類登入，請套用登入風險原則。 按一下 [未受保護的具風險登入] 圖格將會重新導向至 [登入風險原則組態] 刀鋒視窗，您可以在此處設定登入風險原則，以要求具有指定風險層級的登入必須進行 MFA。

### <a name="legacy-authentication"></a>舊版驗證

[舊版驗證] 圖格會顯示您的組織過去一週進行舊版驗證的計數。 舊版驗證通訊協定不支援 MFA 之類的新式安全性方法。 若要防止舊版驗證，您可以套用條件式存取原則。 按一下 [舊版驗證] 圖格，將會重新導向至 [身分識別安全分數]。

### <a name="identity-secure-score"></a>身分識別安全分數

身分識別安全分數會測量安全性狀態，並與產業模式比較。 如果您按一下"身份安全分數（預覽）"磁貼，它將重定向到"身份安全分數"邊欄選項卡，您可以在其中瞭解有關改善安全狀態的更多資訊。

## <a name="next-steps"></a>後續步驟

- [什麼是風險](concept-identity-protection-risks.md)

- [可用來降低風險的原則](concept-identity-protection-policies.md)
