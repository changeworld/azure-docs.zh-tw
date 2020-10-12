---
title: Azure Active Directory Identity Protection 安全性總覽
description: 瞭解安全性總覽如何讓您深入瞭解組織的安全性狀態。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: db61a3f9034908043695bc7db3a16551bf4659c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86024072"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory Identity Protection - 安全性概觀

Azure 入口網站的 [安全性總覽](https://aka.ms/IdentityProtectionRefresh) 可讓您深入瞭解組織的安全性狀態。 它可協助您找出潛在的攻擊並了解原則的效果。

「安全性概觀」大致分成兩個部分：

- 趨勢位於左側，會提供組織中的風險時間軸。
- 圖格位於右側，會顯示組織當前的重要問題，並提供快速因應的建議策略。

![安全性概觀](./media/concept-identity-protection-security-overview/01.png)
  
## <a name="trends"></a>趨勢

### <a name="new-risky-users-detected"></a>偵測到新的具風險使用者

此圖表顯示在所選的時段內新偵測到具風險的使用者數目。 您可以依使用者風險層級 (低、中、高) 篩選此圖表的檢視。 將游標暫留在 UTC 日期遞增數字上方，可檢視當天偵測到具風險的使用者數目。 按一下此圖表，您就會進入 [具風險的使用者] 報告中。 若要修正有風險的使用者，請考慮變更其密碼。

### <a name="new-risky-sign-ins-detected"></a>偵測到新的具風險登入

此圖表顯示在所選的時段內偵測到具風險的登入數目。 您可以依登入風險類型 (即時或彙總) 和登入風險層級 (低、中、高) 來篩選此圖表的的檢視。 未受保護的登入是指未受到 MFA 挑戰的成功即時風險登入。  (注意：登入風險原則) 無法即時保護因為離線偵測而有風險的登入。 將游標暫留在 UTC 日期遞增數字上方，可檢視當天偵測到有風險的登入數目。 按一下此圖表，您就會進入 [具風險的登入] 報告中。

## <a name="tiles"></a>Tiles
 
### <a name="high-risk-users"></a>高度風險使用者

[高度風險使用者] 圖格會顯示身分識別遭入侵機率偏高的最新使用者計數。 這應該是調查的首要任務。 按一下 [高度風險使用者] 圖格將會重新導向至 [具風險的使用者] 報告的篩選檢視，其中僅顯示風險層級為「高」的使用者。 使用此報告可了解詳情，並透過密碼重設來修正這些使用者。

![安全性概觀](./media/concept-identity-protection-security-overview/02.png)

### <a name="medium-risk-users"></a>中度風險使用者
[中度風險使用者] 圖格會顯示身分識別遭入侵的機率中等的最新使用者計數。 按一下 [中度風險使用者] 圖格將會重新導向至 [具風險的使用者] 報告的篩選檢視，其中僅顯示風險層級為「中」的使用者。 使用此報告可進一步調查並修正這些使用者。

### <a name="unprotected-risky-sign-ins"></a>未受保護的具風險登入

[未受保護的具風險登入] 圖格會顯示過去一周的成功、即時具風險登入計數，而這些登入未封鎖或因條件式存取原則、身分識別保護風險原則或個別使用者 MFA 的 MFA 挑戰。 這些是可能遭到入侵、但成功且未受到 MFA 挑戰的登入。 若要在未來保護這類登入，請套用登入風險原則。 按一下 [未受保護的具風險登入] 圖格將會重新導向至 [登入風險原則組態] 刀鋒視窗，您可以在此處設定登入風險原則，以要求具有指定風險層級的登入必須進行 MFA。

### <a name="legacy-authentication"></a>舊版驗證

[舊版驗證] 圖格會顯示過去一周的舊版驗證計數，以及組織中有風險的情況。 舊版驗證通訊協定不支援 MFA 之類的新式安全性方法。 若要防止舊版驗證，您可以套用條件式存取原則。 按一下 [舊版驗證] 圖格，將會重新導向至 [身分識別安全分數]。

### <a name="identity-secure-score"></a>身分識別安全分數

身分識別安全分數會測量安全性狀態，並與產業模式比較。 如果您按一下 [身分識別安全分數 (預覽) ] 圖格，則會重新導向至 [身分識別安全分數] 分頁，您可以在此深入瞭解如何改善安全性狀態。

## <a name="next-steps"></a>接下來的步驟

- [什麼是風險](concept-identity-protection-risks.md)

- [可用來降低風險的原則](concept-identity-protection-policies.md)
