---
title: Identity Protection 和 B2B 使用者-Azure Active Directory
description: 對 B2B 使用者使用 Identity Protection 其運作方式和已知限制
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "72881318"
---
# <a name="identity-protection-and-b2b-users"></a>Identity Protection 和 B2B 使用者

透過 Azure AD 的 B2B 共同作業，組織可以針對使用 Identity Protection 的 B2B 使用者強制執行風險型原則。 這些原則的設定方式有兩種：

- 系統管理員可以設定內建的身分識別保護風險型原則，其適用于所有應用程式，其中包括來賓使用者。
- 系統管理員可以設定其條件式存取原則，並使用包含來賓使用者的登入風險作為條件。

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>針對 B2B 共同作業使用者評估風險的方式

B2B 共同作業使用者的使用者風險會在其主目錄中進行評估。 這些使用者的即時登入風險會在嘗試存取資源時，在資原始目錄中進行評估。

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>適用于 B2B 共同作業使用者的身分識別保護限制

因為資原始目錄中的 B2B 共同作業使用者的身分識別存在於其主目錄中，所以在此方面的身分識別保護執行限制。 主要限制如下：

- 如果來賓使用者觸發 Identity Protection 使用者風險原則來強制重設密碼，系統**就會將它們封鎖**。 此區塊是因為無法在資原始目錄中重設密碼。
- **來賓使用者不會出現在 [具風險使用者] 報告中**。 這種遺失的可見度是因為 B2B 使用者的主目錄中發生風險評估。
- 系統管理員無法在其資原始目錄中**關閉或補救有風險的 B2B**共同作業使用者。 這項功能遺失的原因，是因為資原始目錄中的系統管理員無法存取 B2B 使用者的主目錄。

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>為什麼我無法在我的目錄中修復具風險的 B2B 共同作業使用者？

B2B 使用者的風險評估與補救會發生在其主目錄中。 由於這種情況，來賓使用者不會出現在資原始目錄中的具風險使用者報告中，而且資原始目錄中的系統管理員無法為這些使用者強制執行安全的密碼重設。

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>如果 B2B 共同作業使用者因為組織中以風險為基礎的原則而遭到封鎖，該怎麼辦？

如果您的目錄中有風險的 B2B 使用者被您的風險原則封鎖，使用者將需要在其主目錄中補救該風險。 使用者可以在其主目錄中執行安全的密碼重設，以補救其風險。 如果未在其主目錄中啟用自助式密碼重設，他們就必須聯絡自己組織的 IT 人員，讓系統管理員手動關閉其風險或重設其密碼。

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>如何? 防止 B2B 共同作業使用者受到以風險為基礎的原則影響？

從貴組織以風險為基礎的條件式存取原則中排除 B2B 使用者，會防止 B2B 使用者受到其風險評估的影響或封鎖。 若要排除這些 B2B 使用者，請在 Azure AD 中建立群組，其中包含貴組織的所有來賓使用者。 然後，將此群組新增為您內建身分識別保護使用者風險和登入風險原則的排除，以及任何使用登入風險做為條件的條件式存取原則。

## <a name="next-steps"></a>後續步驟

請參閱下列有關 Azure AD B2B 共同作業的文章：

- [何謂 Azure AD B2B 共同作業？](../b2b/what-is-b2b.md)
