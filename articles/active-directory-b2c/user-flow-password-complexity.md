---
title: 設定密碼複雜度需求
titleSuffix: Azure AD B2C
description: 如何設定 Azure Active Directory B2C 中取用者所提供的密碼複雜度需求。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 59ac2f74d4753c1f647bedfa2c402e729e25ee62
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847806"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>設定 Azure Active Directory B2C 中的密碼複雜度需求

Azure Active Directory B2C (Azure AD B2C) 支援的變更密碼複雜度需求，是在建立帳戶時由使用者所提供的。 根據預設，Azure AD B2C 是使用 `Strong` 密碼。 Azure AD B2C 也支援組態選項，可控制客戶可以使用的密碼複雜度。

## <a name="password-rule-enforcement"></a>密碼規則強制執行

在註冊期間或密碼重設期間，使用者必須提供符合複雜度規則的密碼。 依每個使用者流程強制執行密碼複雜度規則。 在註冊期間，有一個使用者流程可能需要四位數的 pin，而另一個使用者流程在註冊期間需要八個字元的字串。 例如，您可能會針對成人的使用者流程與針對兒童的原則使用不同的密碼複雜度。

登入期間一律不會強制要求密碼複雜度。 系統一律不會在登入時提示使用者變更其密碼，因為它不符合目前的複雜度需求。

可以在以下類型的使用者流程類型中設定密碼複雜度：

- 註冊或登入使用者流程
- 密碼重設使用者流程

如果您使用自訂原則，您可以 ([在自訂原則中設定密碼複雜度](custom-policy-password-complexity.md))。

## <a name="configure-password-complexity"></a>設定密碼複雜度

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在入口網站工具列中選取 [**目錄 + 訂**用帳戶] 圖示，然後選取包含您 Azure AD B2C 租使用者的目錄。
3. 在 Azure 入口網站中，搜尋並選取  **Azure AD B2C**。
4. 選取 **[使用者流程（原則）** ]。
2. 選取一個使用者流程，然後按一下 [屬性]。
3. 在 [密碼複雜度] 之下，將這個使用者流程的密碼複雜度變更為 [簡單]、[強式] 或 [自訂]。

### <a name="comparison-chart"></a>比較圖表

| 複雜度 | 說明 |
| --- | --- |
| 簡單 | 密碼至少為 8 到 64 個字元。 |
| Strong | 密碼至少為 8 到 64 個字元。 它需要小寫字母、大寫字母、數字或符號 4 個之中的 3 個。 |
| 自訂 | 此選項會對密碼複雜度規則提供最多的控制權。  它允許設定自訂的長度。  它也可以接受僅限數字的密碼 (PIN)。 |

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
- **4 個其中 3 個：小寫字元、大寫字元、數字 (0-9)、符號**可確保密碼包含至少兩個字元類型。 例如，數字、小寫字元和大寫字元。
- **4 個其中 4 個：小寫字元、大寫字元、數字 (0-9)、符號**可確保密碼包含所有的字元類型。

    > [!NOTE]
    > 要求 **4 個其中 4 個**可能會導致使用者感到挫折。 一些根據研究顯示，這項需求並未改善密碼熵。 請參閱 [NIST 密碼指導方針](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
