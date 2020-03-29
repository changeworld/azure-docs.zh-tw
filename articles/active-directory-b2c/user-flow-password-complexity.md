---
title: 配置密碼複雜性要求
titleSuffix: Azure AD B2C
description: 如何設定 Azure Active Directory B2C 中取用者所提供的密碼複雜度需求。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c5ef550af0c7e19531ea19093ea937880f7dcf14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185636"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>設定 Azure Active Directory B2C 中的密碼複雜度需求

Azure Active Directory B2C (Azure AD B2C) 支援的變更密碼複雜度需求，是在建立帳戶時由使用者所提供的。 根據預設，Azure AD B2C 是使用 `Strong` 密碼。 Azure AD B2C 也支援組態選項，可控制客戶可以使用的密碼複雜度。

## <a name="password-rule-enforcement"></a>密碼規則強制執行

在註冊期間或密碼重設期間，使用者必須提供符合複雜度規則的密碼。 依每個使用者流程強制執行密碼複雜度規則。 在註冊期間，一個使用者流需要四位引腳，而另一個使用者流在註冊期間需要八個字元字串。 例如，您可能會針對成人的使用者流程與針對兒童的原則使用不同的密碼複雜度。

登入期間一律不會強制要求密碼複雜度。 系統一律不會在登入時提示使用者變更其密碼，因為它不符合目前的複雜度需求。

可以在以下類型的使用者流程類型中設定密碼複雜度：

- 註冊或登入使用者流程
- 密碼重設使用者流程

如果您使用自訂原則，您可以 ([在自訂原則中設定密碼複雜度](custom-policy-password-complexity.md))。

## <a name="configure-password-complexity"></a>設定密碼複雜度

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 在門戶工具列中選擇**目錄 + 訂閱**圖示，然後選擇包含 Azure AD B2C 租戶的目錄。
3. 在 Azure 門戶中，搜索並選擇**Azure AD B2C**。
4. 選擇**使用者流（策略）。**
2. 選取一個使用者流程，然後按一下 [屬性]****。
3. 在 [密碼複雜度]**** 之下，將這個使用者流程的密碼複雜度變更為 [簡單]****、[強式]**** 或 [自訂]****。

### <a name="comparison-chart"></a>比較圖表

| 複雜度 | 描述 |
| --- | --- |
| Simple | 密碼至少為 8 到 64 個字元。 |
| 強式 | 密碼至少為 8 到 64 個字元。 它需要小寫字母、大寫字母、數字或符號 4 個之中的 3 個。 |
| Custom | 此選項會對密碼複雜度規則提供最多的控制權。  它允許設定自訂的長度。  它也可以接受僅限數字的密碼 (PIN)。 |

## <a name="custom-options"></a>自訂選項

### <a name="character-set"></a>字元集

可讓您只接受數字 (PIN) 或完整的字元集。

- **僅限數字**在輸入密碼時只允許數字 (0-9)。
- **所有**允許任何字母、數字或符號。

### <a name="length"></a>長度

可讓您控制密碼的長度要求。

- **最小長度**必須至少為 4。
- **最大長度**必須大於或等於最小長度，且最多可以有 64 個字元。

### <a name="character-classes"></a>字元類別

可讓您控制密碼中使用的不同字元類型。

- **4 個其中 2 個：小寫字元、大寫字元、數字 (0-9)、符號**可確保密碼包含至少兩個字元類型。 例如，數字和小寫字元。
- **3（共 4 個）：小寫字元、大寫字元、數位 （0-9）、符號**確保密碼至少包含三個字元類型。 例如，數字、小寫字元和大寫字元。
- **4 個其中 4 個：小寫字元、大寫字元、數字 (0-9)、符號**可確保密碼包含所有的字元類型。

    > [!NOTE]
    > 要求 **4 個其中 4 個**可能會導致使用者感到挫折。 一些根據研究顯示，這項需求並未改善密碼熵。 請參閱 [NIST 密碼指導方針](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
