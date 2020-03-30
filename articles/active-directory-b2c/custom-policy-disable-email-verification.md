---
title: 使用自訂策略在客戶註冊期間禁用電子郵件驗證
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure 活動目錄 B2C 中的客戶註冊期間禁用電子郵件驗證。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 13a5fa6a030d876d92651ca587e37fdc6a3ec600
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136137"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>使用 Azure 活動目錄 B2C 中的自訂策略在客戶註冊期間禁用電子郵件驗證

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>Prerequisites

完成[開始使用自訂原則](custom-policy-get-started.md)中的步驟。 您應該有一個工作自訂策略，用於使用社交帳戶和本地帳戶進行註冊和登錄。

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>將中繼資料添加到自斷言技術設定檔

**本地帳戶SignWithWithLogonEmail**技術設定檔是一個[自斷言](self-asserted-technical-profile.md)，在註冊流期間調用。 要禁用電子郵件驗證，請將`EnforceEmailVerification`中繼資料設置為 false。 覆蓋擴展檔中的本地帳戶與Logon電子郵件技術設定檔。 

1. 打開策略的擴展檔。 例如， <em> `SocialAndLocalAccounts/` </em>.
1. 尋找 `ClaimsProviders` 元素。 如果此元素不存在，請加以新增。
1. 將以下聲明提供程式添加到元素`ClaimsProviders`：

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="test-the-custom-policy"></a>測試自訂原則

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 通過在頂部功能表中選擇**目錄 + 訂閱**篩選器並選擇包含 Azure AD 租戶的目錄，請確保使用的目錄包含 Azure AD 租戶。
3. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [應用程式註冊]****。
4. 選取 [識別體驗架構]****。
5. 選取 [上傳自訂原則]****，然後上傳您所變更的兩個原則檔案。
2. 選取您上傳的註冊或登入原則，按一下 [立即執行]**** 按鈕。
3. 您應該能夠在沒有驗證的情況下使用電子郵件地址進行註冊。


## <a name="next-steps"></a>後續步驟

- 詳細瞭解 IEF 參考中的[自斷言技術設定檔](self-asserted-technical-profile.md)。
