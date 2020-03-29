---
title: 包含檔案
description: 包含檔案
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 3d4e45d1bf53bab4d1f9c45367f9d051f1668e2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76309054"
---
### <a name="authenticationresult-properties-in-msalnet"></a>身份驗證結果屬性MSAL.NET

獲取權杖的方法返回`AuthenticationResult`。 對於非同步方法，`Task<AuthenticationResult>`返回。

在MSAL.NET中`AuthenticationResult`，公開：

- `AccessToken`用於 Web API 以訪問資源。 此參數是一個字串，通常是 Base-64 編碼的 JWT。 用戶端不應查看訪問權杖內部。 不能保證格式保持穩定，並且可以為資源加密。 編寫依賴于用戶端上的訪問權杖內容的代碼是錯誤和用戶端邏輯中斷的最大來源之一。 有關詳細資訊，請參閱[訪問權杖](../articles/active-directory/develop/access-tokens.md)。
- `IdToken`使用者。 此參數是編碼的 JWT。 有關詳細資訊，請參閱[ID 權杖](../articles/active-directory/develop/id-tokens.md)。
- `ExpiresOn`告知權杖過期的日期和時間。
- `TenantId`包含在其中找到使用者的租戶。 對於 Azure 活動目錄 （Azure AD） B2B 方案中的來賓使用者，租戶 ID 是來賓租戶，而不是唯一租戶。
為使用者傳遞權杖時，`AuthenticationResult`還包含有關此使用者的資訊。 對於在應用程式沒有使用者的情況下請求權杖的機密用戶端流，此使用者資訊為空。
- `Scopes`為其頒發權杖的 。
- 使用者的唯一識別碼。

### <a name="iaccount"></a>IAccount

MSAL.NET通過`IAccount`介面定義帳戶的概念。 這種重大更改提供了正確的語義。 同一使用者可以在不同的 Azure AD 目錄中有多個帳戶。 此外，MSAL.NET在來賓方案中提供更好的資訊，因為提供了家庭帳戶資訊。
下圖顯示了介面的結構`IAccount`。

![IAccount 介面結構](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

類`AccountId`標識特定租戶中的帳戶，其屬性顯示在下表中。

| 屬性 | 描述 |
|----------|-------------|
| `TenantId` | GUID 的字串表示形式，該表示形式是帳戶所在的租戶的 ID。 |
| `ObjectId` | GUID 的字串表示形式，它是在租戶中擁有帳戶的使用者的 ID。 |
| `Identifier` | 帳戶的唯一識別碼。 `Identifier`是逗號的`ObjectId`串聯和`TenantId`分隔。 它們不是基地 64 編碼。 |

該`IAccount`介面表示有關單個帳戶的資訊。 同一使用者可以存在於不同的租戶中，這意味著使用者可以有多個帳戶。 其成員顯示在下表中。

| 屬性 | 描述 |
|----------|-------------|
| `Username` | 以 UsertheName （UPN） 格式包含可顯示值的字串，john.doe@contoso.com例如 。 此字串可以為空，這與 HomeAccountId 和 HomeAccountId.識別碼不同，後者不會為空。 此屬性替換 MSAL.NET的早期版本`DisplayableId`中的屬性`IUser`。 |
| `Environment` | 包含此帳戶的標識提供程式的字串，例如 。 `login.microsoftonline.com` 此屬性替換 的屬性`IdentityProvider``IUser`，除了除了雲環境之外`IdentityProvider`，還有有關租戶的資訊。 此處，該值僅是主機。 |
| `HomeAccountId` | 使用者的家庭帳戶的帳戶 ID。 此屬性唯一標識跨 Azure AD 租戶的使用者。 |

### <a name="use-the-token-to-call-a-protected-api"></a>使用權杖調用受保護的 API

在`AuthenticationResult`中 MSAL`result`返回後，在調用訪問受保護的 Web API 之前，將其添加到 HTTP 授權標頭。

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```