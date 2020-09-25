---
title: Azure Active Directory B2C 中的舊版使用者流程版本 |Microsoft Docs
description: 瞭解 Azure Active Directory B2C 中可用的舊版使用者流程。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3471b9988b154ccb38dc56545e769a7179a4026f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258721"
---
# <a name="legacy-user-flow-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的舊版使用者流程版本

> [!IMPORTANT]
> 本文說明使用者流程的舊版版本控制方法，該方法提供 V1 (可供生產環境使用的) 版本，以及 v1.1 和 V2 (preview) 版本的使用者流程。 Azure 公用雲端以外的環境將繼續使用這種舊版的版本控制方法。 在 Azure 公用雲端中，此方法會由 [新的 **建議** 和 **預覽** 版本](user-flow-versions.md)取代。
> 
Azure Active Directory B2C (Azure AD B2C) 中的使用者流程，可協助您設定可完整描述客戶身分識別體驗的一般 [原則](user-flow-overview.md) 。 這些體驗包括註冊、登入、密碼重設或設定檔編輯。

在下表中，除非依照 **建議**識別使用者流程，否則會將其視為 *預覽*狀態。 您應該只針對實際執行應用程式使用建議的使用者流程。

## <a name="v1"></a>V1

| 使用者流程 | 建議 | 說明 |
| --------- | ----------- | ----------- |
| 密碼重設 | 是 | 讓使用者能夠在驗證電子郵件後選擇新密碼。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>權杖相容性設定</li><li>[密碼複雜性需求](user-flow-password-complexity.md)</li></ul> |
| 設定檔編輯 | 是 | 讓使用者能夠設定其使用者屬性。 使用此使用者流程，您可以設定： <ul><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li></ul> |
| 使用 ROPC 登入 | 否 | 讓具有本機帳戶的使用者能直接登入原生應用程式 (不需要瀏覽器)。 使用此使用者流程，您可以設定： <ul><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li></ul> |
| 登入 | 否 | 讓使用者帳戶能夠登入其帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li><li>封鎖登入</li><li>強制密碼重設</li><li>讓我保持登入 (KMSI)</ul><br>您無法自訂此使用者流程的使用者介面與。 |
| 註冊 | 否 | 讓使用者能夠建立帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li><li>[密碼複雜性需求](user-flow-password-complexity.md)</li></ul> |
| 註冊與登入 | 是 | 讓使用者帳戶能夠建立帳戶或登入其帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li><li>[密碼複雜性需求](user-flow-password-complexity.md)</li></ul>|

## <a name="v11"></a>1.1 版

| 使用者流程 | 建議 | 說明 |
| --------- | ----------- | ----------- |
| 密碼重設 v1。1 | 否 | 可讓使用者在確認電子郵件 (新的頁面配置之後，選擇新的密碼) 。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>權杖相容性設定</li><li>[密碼複雜性需求](user-flow-password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| 使用者流程 | 建議 | 說明 |
| --------- | ----------- | ----------- |
| 密碼重設 v2 | 否 | 讓使用者能夠在驗證電子郵件後選擇新密碼。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>權杖相容性設定</li><li>[年齡管制](basic-age-gating.md)</li><li>[密碼複雜性需求](user-flow-password-complexity.md)</li></ul> |
| 設定檔編輯 v2 | 是 | 讓使用者能夠設定其使用者屬性。 使用此使用者流程，您可以設定： <ul><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li></ul> |
| 登入 v2 | 否 | 讓使用者帳戶能夠登入其帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li><li>[年齡管制](basic-age-gating.md)</li><li>登入頁面自訂</li></ul> |
| 註冊 v2 | 否 | 讓使用者能夠建立帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li><li>[年齡管制](basic-age-gating.md)</li><li>[密碼複雜性需求](user-flow-password-complexity.md)</li></ul> |
| 註冊和登入 v2 | 否 | 讓使用者帳戶能夠建立帳戶或登入其帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>[年齡管制](basic-age-gating.md)</li><li>[密碼複雜性需求](user-flow-password-complexity.md)</li></ul> |