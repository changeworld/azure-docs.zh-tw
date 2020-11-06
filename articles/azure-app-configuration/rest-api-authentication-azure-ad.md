---
title: Azure Active Directory REST API-驗證
description: 使用 Azure Active Directory 使用 REST API 驗證 Azure 應用程式組態
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: fb3d00fb79c55e29d578f5e068e4ae025414a935
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423944"
---
# <a name="azure-active-directory-authentication"></a>Azure Active Directory 驗證

您可以使用 **持有** 人驗證配置來驗證 HTTP 要求，以取得從 Azure Active Directory (Azure AD) 取得的權杖。 這些要求必須透過 TLS 傳輸。

## <a name="prerequisites"></a>必要條件

將用來要求 Azure AD 權杖的主體必須指派給其中一個適用的 [應用程式設定角色](./rest-api-authorization-azure-ad.md)

為每個要求提供驗證所需的所有 HTTP 標頭。 所需的最小值為：

|  要求標頭 | 描述  |
| --------------- | ------------ |
| **授權** | **持有** 人配置所需的驗證資訊。 格式和詳細資料如下所述。 |

**範例︰**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-active-directory-token-acquisition"></a>Azure Active Directory 權杖取得

取得 Azure AD 的權杖之前，必須先識別要驗證的使用者、他們要求權杖的物件，以及應使用的 Azure AD 端點 (授權單位) 。

### <a name="audience"></a>適用對象

必須以適當的物件要求 Azure AD token。 針對 Azure 應用程式組態，當要求權杖時，應指定下列其中一個物件。 物件也可以稱為權杖所要求的「資源」。

- {configurationStoreName}. azconfig io
- *. azconfig.io

> [!IMPORTANT]
> 當物件要求的是 {configurationStoreName}. azconfig 時，它必須完全符合 "Host" 要求標頭， (區分大小寫) 用來傳送要求。

### <a name="azure-ad-authority"></a>Azure AD 授權單位

Azure AD 授權單位是用來取得 Azure AD 權杖的端點。 它的形式為 `https://login.microsoftonline.com/{tenantId}` 。 `{tenantId}`區段指的是嘗試驗證的使用者/應用程式所屬的 Azure Active Directory 租使用者識別碼。

### <a name="authentication-libraries"></a>驗證程式庫

Azure 提供一組稱為 Azure Active Directory Authentication library 的程式庫 (ADAL) 以簡化取得 Azure AD 權杖的程式。 這些程式庫是以多種語言為基礎。 您可以在 [這裡](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)找到檔。

## <a name="errors"></a>**錯誤**

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**原因：** 未提供具有持有人配置的授權要求標頭。
**解決方案：** 提供有效的 ```Authorization``` HTTP 要求標頭

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**原因：** Azure AD 的 token 無效。
**解決方案：** 從 Azure AD 授權單位取得 Azure AD 權杖，並確定使用的是適當的物件。

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**原因：** Azure AD 的 token 無效。
**解決方案：** 從 Azure AD 授權單位取得 Azure AD 權杖，並確定 Azure AD 租使用者是與此訂用帳戶相關聯的訂用帳戶，設定存放區所屬的租使用者。 如果主體屬於一個以上的 Azure AD 租使用者，則可能會出現此錯誤。
