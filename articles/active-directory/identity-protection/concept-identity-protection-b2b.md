---
title: 標識保護和 B2B 使用者 - Azure 活動目錄
description: 與 B2B 使用者使用身份保護的工作原理和已知的限制
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
ms.openlocfilehash: 95ea7eb470a5880bc88b3df903d33854f363e974
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72881318"
---
# <a name="identity-protection-and-b2b-users"></a>Identity Protection 和 B2B 使用者

借助 Azure AD B2B 協作，組織可以使用身份保護為 B2B 使用者強制實施基於風險的策略。 這些策略的配置方式有兩種：

- 管理員可以配置適用于所有應用（包括來賓使用者）的基於身份驗證保護風險的策略。
- 管理員可以使用包含來賓使用者的條件配置其條件訪問策略，將登錄風險作為條件。

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>如何為 B2B 協作使用者評估風險

B2B 協作使用者的使用者風險在其主目錄中進行評估。 當這些使用者嘗試訪問資源時，將在資原始目錄中評估他們的即時登錄風險。

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>B2B 協作使用者的身份保護限制

資原始目錄中的 B2B 協作使用者的身份保護實施存在限制，因為它們的標識存在於其主目錄中。 主要限制如下：

- 如果來賓使用者觸發身份保護使用者風險策略以強制重置密碼，**則會阻止它們**。 此塊是由於無法重置資原始目錄中的密碼。
- **來賓使用者未顯示在風險使用者報告中**。 這種可見度損失是由於 B2B 使用者主目錄中發生的風險評估造成的。
- 管理員**無法在其資原始目錄中關閉或修復風險的 B2B 協作使用者**。 這種功能丟失是由於資原始目錄中的管理員無法訪問 B2B 使用者主目錄造成的。

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>為什麼我無法修復目錄中有風險的 B2B 協作使用者？

B2B 使用者的風險評估和補救發生在其主目錄中。 由於此事實，來賓使用者未出現在資原始目錄中的風險使用者報告中，資原始目錄中的管理員無法強制對這些使用者進行安全密碼重設。

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>如果 B2B 協作使用者由於組織中基於風險的策略而被阻止，我該怎麼辦？

如果目錄中的風險 B2B 使用者被基於風險的策略阻止，則使用者將需要在其主目錄中修復該風險。 使用者可以通過在主目錄中執行安全密碼重設來修復其風險。 如果他們在主目錄中未啟用自助服務密碼重設，則需要聯繫自己組織的 IT 員工，讓管理員手動消除其風險或重置密碼。

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>如何防止 B2B 協作使用者受到基於風險的策略的影響？

將 B2B 使用者從組織的基於風險的條件訪問策略中排除，將防止 B2B 使用者受到其風險評估的影響或阻止。 要排除這些 B2B 使用者，請在 Azure AD 中創建一個包含組織所有來賓使用者的組。 然後，添加此組作為對內置身份保護使用者風險和登錄風險策略以及使用登錄風險作為條件的任何條件訪問策略的排除。

## <a name="next-steps"></a>後續步驟

請參閱下列有關 Azure AD B2B 共同作業的文章：

- [何謂 Azure AD B2B 共同作業？](../b2b/what-is-b2b.md)
