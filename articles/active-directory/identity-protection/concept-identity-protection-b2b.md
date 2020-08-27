---
title: 身分識別保護和 B2B 使用者-Azure Active Directory
description: 使用 Identity Protection 與 B2B 使用者的運作方式和已知限制
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
ms.openlocfilehash: c9ec9e110c3a476c9096ae3e216c9780da0e0f49
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949191"
---
# <a name="identity-protection-and-b2b-users"></a>Identity Protection 和 B2B 使用者

使用 Azure AD B2B 共同作業時，組織可以針對使用 Identity Protection 的 B2B 使用者，強制執行以風險為基礎的原則。 您可以透過兩種方式來設定這些原則：

- 系統管理員可以設定適用于所有應用程式的內建 Identity Protection 風險原則，包括來賓使用者。
- 系統管理員可以使用包含來賓使用者的登入風險作為條件，來設定其條件式存取原則。

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>B2B 共同作業使用者的風險評估方式

B2B 共同作業使用者的使用者風險會在其主目錄中進行評估。 當使用者嘗試存取資源時，會在資原始目錄中評估這些使用者的即時登入風險。

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>B2B 共同作業使用者的身分識別保護限制

由於其主目錄中現有的身分識別，因此在資原始目錄中對 B2B 共同作業使用者的身分識別保護執行有一些限制。 主要限制如下：

- 如果來賓使用者觸發 Identity Protection 使用者風險原則來強制密碼重設， **將會遭到封鎖**。 此區塊是因為無法重設資原始目錄中的密碼。
- **來賓使用者不會出現在 [具風險的使用者] 報告中**。 由於 B2B 使用者的主目錄中發生風險評估，因此這種可見度會遺失。
- 系統管理員無法在其資原始目錄中 **解除或補救具風險的 B2B** 共同作業使用者。 這項功能的遺失是因為資原始目錄中的系統管理員無法存取 B2B 使用者的主目錄。

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>為什麼我無法在我的目錄中補救具風險的 B2B 共同作業使用者？

B2B 使用者的風險評估和補救會發生在其主目錄中。 基於這個事實，來賓使用者不會出現在資原始目錄中的 [具風險的使用者] 報告，且資原始目錄中的系統管理員無法為這些使用者強制執行安全的密碼重設。

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>如果 B2B 共同作業使用者因為組織中以風險為基礎的原則而遭到封鎖，該怎麼辦？

如果您的目錄中有風險的 B2B 使用者遭到以風險為基礎的原則封鎖，使用者將需要在其主目錄中補救該風險。 使用者可以在其主目錄中執行安全密碼重設來補救其風險。 如果未在其主目錄中啟用自助式密碼重設，他們必須聯絡自己組織的 IT 人員，讓系統管理員手動解除其風險或重設其密碼。

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>如何? 防止 B2B 共同作業使用者受到以風險為基礎的原則影響嗎？

從組織的風險型條件式存取原則中排除 B2B 使用者，將會導致 B2B 使用者不受其風險評估影響或封鎖。 若要排除這些 B2B 使用者，請在 Azure AD 中建立群組，其中包含您所有組織的來賓使用者。 然後，將此群組新增為您內建 Identity Protection 使用者風險和登入風險原則的排除，以及任何使用登入風險作為條件的條件式存取原則。

## <a name="next-steps"></a>後續步驟

請參閱下列有關 Azure AD B2B 共同作業的文章：

- [何謂 Azure AD B2B 共同作業？](../external-identities/what-is-b2b.md)