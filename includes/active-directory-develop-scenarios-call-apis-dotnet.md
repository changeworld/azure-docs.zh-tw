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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "76309054"
---
### <a name="authenticationresult-properties-in-msalnet"></a>MSAL.NET 中的 AuthenticationResult 屬性

取得權杖的方法會傳回 `AuthenticationResult` 。 若是非同步方法，會傳回 `Task<AuthenticationResult>` 。

在 MSAL.NET 中，會 `AuthenticationResult` 公開：

- `AccessToken` 用於存取資源的 web API。 此參數是字串，通常是 Base-64 編碼的 JWT。 用戶端永遠不應該在存取權杖內查看。 格式不保證會維持穩定狀態，而且可以針對資源進行加密。 撰寫相依于用戶端存取權杖內容的程式碼是最大的錯誤來源之一和用戶端邏輯中斷的其中一個。 如需詳細資訊，請參閱 [存取權杖](../articles/active-directory/develop/access-tokens.md)。
- `IdToken` 供使用者使用。 此參數是編碼的 JWT。 如需詳細資訊，請參閱 [識別碼權杖](../articles/active-directory/develop/id-tokens.md)。
- `ExpiresOn` 告知權杖到期的日期和時間。
- `TenantId` 包含找到使用者的租使用者。 針對 Azure Active Directory (Azure AD) B2B 案例中的來賓使用者，租使用者識別碼是來賓租使用者，而不是唯一的租使用者。
當使用者傳遞權杖時， `AuthenticationResult` 也會包含此使用者的相關資訊。 針對沒有應用程式使用者要求權杖的機密用戶端流程，此使用者資訊為 null。
- `Scopes`發出權杖的。
- 使用者的唯一識別碼。

### <a name="iaccount"></a>IAccount

MSAL.NET 會透過介面定義帳戶的概念 `IAccount` 。 這種中斷性變更提供正確的語法。 相同的使用者在不同的 Azure AD 目錄中可以有數個帳戶。 此外，MSAL.NET 會在來賓案例中提供更好的資訊，因為會提供主帳戶資訊。
下圖顯示介面的結構 `IAccount` 。

![IAccount 介面結構](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

`AccountId`類別會使用下表所示的屬性來識別特定租使用者中的帳戶。

| 屬性 | 說明 |
|----------|-------------|
| `TenantId` | GUID 的字串表示，這是帳戶所在之租使用者的識別碼。 |
| `ObjectId` | GUID 的字串表示，這是擁有租使用者帳戶的使用者識別碼。 |
| `Identifier` | 帳戶的唯一識別碼。 `Identifier` 是串連 `ObjectId` ，並以 `TenantId` 逗號分隔。 它們不是以64為基礎的編碼。 |

`IAccount`介面代表單一帳戶的相關資訊。 相同的使用者可以存在於不同的租使用者中，這表示使用者可以有多個帳戶。 下表顯示其成員。

| 屬性 | 說明 |
|----------|-------------|
| `Username` | 字串，其中包含 UserPrincipalName (UPN) 格式的可顯示值，例如 john.doe@contoso.com 。 這個字串可以是 null，不像 HomeAccountId 和 HomeAccountId，它不是 null。 這個屬性會取代 `DisplayableId` `IUser` 舊版 MSAL.NET 中的屬性。 |
| `Environment` | 字串，其中包含此帳戶的身分識別提供者，例如 `login.microsoftonline.com` 。 除了雲端環境以外，這個屬性會取代除了 `IdentityProvider` `IUser` `IdentityProvider` 也有租使用者的相關資訊以外的屬性。 在這裡，此值只是主機。 |
| `HomeAccountId` | 使用者之主帳戶的帳戶識別碼。 此屬性可唯一識別 Azure AD 租使用者之間的使用者。 |

### <a name="use-the-token-to-call-a-protected-api"></a>使用權杖來呼叫受保護的 API

`AuthenticationResult`在中由 MSAL 傳回之後 `result` ，請將它新增至 HTTP 授權標頭，然後再進行呼叫以存取受保護的 web API。

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```