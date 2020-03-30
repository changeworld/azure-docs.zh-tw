---
title: Azure Active Directory B2C 中的使用者流程版本 | Microsoft Docs
description: 了解 Azure Active Directory B2C 中可用的使用者流程版本。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 40d21d3390396e0cb7e44d4e19598f9b0b691087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185613"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的使用者流程版本

Azure 活動目錄 B2C（Azure AD B2C） 中的使用者流可説明您設置完全描述客戶標識體驗的通用[策略](user-flow-overview.md)。 這些體驗包括註冊、登入、密碼重設或設定檔編輯。 在 Azure AD B2C 中，您可以從建議的使用者流程和預覽使用者流程的集合中選取。

新的使用者流程會新增為新版本。 使用者流程趨於穩定後，即會成為建議使用的流程。 使用者流程在經過完整的測試後，會標示為**建議使用**。 使用者流程在標示為「建議使用」之前，都會被視為預覽版。 對於任何生產環境應用程式均應使用建議的使用者流程，但在測試新推出的功能時，應選擇使用其他版本。 您不應使用舊版的建議使用者流程。

>[!IMPORTANT]
> 除非使用者流被標識為 **"建議"，** 否則它*被視為預覽版*。 應僅對生產應用程式使用建議的使用者流。

## <a name="v1"></a>V1

| 使用者流程 | 建議 | 描述 |
| --------- | ----------- | ----------- |
| 密碼重設 | 是 | 讓使用者能夠在驗證電子郵件後選擇新密碼。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>權杖相容性設定</li><li>[密碼複雜性要求](user-flow-password-complexity.md)</li></ul> |
| 設定檔編輯 | 是 | 讓使用者能夠設定其使用者屬性。 使用此使用者流程，您可以設定： <ul><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li></ul> |
| 使用 ROPC 登錄 | 否 | 讓具有本機帳戶的使用者能直接登入原生應用程式 (不需要瀏覽器)。 使用此使用者流程，您可以設定： <ul><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li></ul> |
| 登入 | 否 | 讓使用者帳戶能夠登入其帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li><li>封鎖登入</li><li>強制密碼重設</li><li>讓我保持登入 (KMSI)</ul><br>您無法自訂此使用者流程的使用者介面與。 |
| 註冊 | 否 | 讓使用者能夠建立帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li><li>[密碼複雜性要求](user-flow-password-complexity.md)</li></ul> |
| 註冊與登入 | 是 | 讓使用者帳戶能夠建立帳戶或登入其帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li><li>[密碼複雜性要求](user-flow-password-complexity.md)</li></ul>|

## <a name="v11"></a>1.1 版

| 使用者流程 | 建議 | 描述 |
| --------- | ----------- | ----------- |
| 密碼重設 v1.1 | 否 | 允許使用者在驗證其電子郵件（提供新頁面配置）後選擇新密碼。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>權杖相容性設定</li><li>[密碼複雜性要求](user-flow-password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| 使用者流程 | 建議 | 描述 |
| --------- | ----------- | ----------- |
| 密碼重設 v2 | 否 | 讓使用者能夠在驗證電子郵件後選擇新密碼。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>權杖相容性設定</li><li>[年齡管制](basic-age-gating.md)</li><li>[密碼複雜性要求](user-flow-password-complexity.md)</li></ul> |
| 設定檔編輯 v2 | 是 | 讓使用者能夠設定其使用者屬性。 使用此使用者流程，您可以設定： <ul><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li></ul> |
| 登入 v2 | 否 | 讓使用者帳戶能夠登入其帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li><li>[年齡管制](basic-age-gating.md)</li><li>登入頁面自訂</li></ul> |
| 註冊 v2 | 否 | 讓使用者能夠建立帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li><li>[年齡管制](basic-age-gating.md)</li><li>[密碼複雜性要求](user-flow-password-complexity.md)</li></ul> |
| 註冊和登入 v2 | 否 | 讓使用者帳戶能夠建立帳戶或登入其帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>[年齡管制](basic-age-gating.md)</li><li>[密碼複雜性要求](user-flow-password-complexity.md)</li></ul> |
