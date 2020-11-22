---
title: Azure Active Directory REST API-驗證
description: 使用 Azure Active Directory 使用 REST API 驗證 Azure 應用程式組態
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 78344bd3896ca7d00c9f761c586b6f5142dc1e58
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2020
ms.locfileid: "95253400"
---
# <a name="azure-active-directory-authentication"></a>Azure Active Directory 驗證

您可以使用 `Bearer` 驗證配置搭配從 Azure Active Directory (Azure AD) 取得的權杖來驗證 HTTP 要求。 您必須透過傳輸層安全性 (TLS) 來傳輸這些要求。

## <a name="prerequisites"></a>必要條件

您必須將用來要求 Azure AD 權杖的主體指派給其中一個適用的 [Azure 應用程式組態角色](./rest-api-authorization-azure-ad.md)。

為每個要求提供驗證所需的所有 HTTP 標頭。 以下是最低需求：

|  要求標頭 | 描述  |
| --------------- | ------------ |
| `Authorization` | 配置所需的驗證資訊 `Bearer` 。 |

**範例︰**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-ad-token-acquisition"></a>Azure AD 權杖取得

取得 Azure AD token 之前，您必須識別要驗證的使用者、您要要求權杖的物件，以及要使用的 Azure AD 端點 () 授權單位。

### <a name="audience"></a>適用對象

向適當的物件要求 Azure AD token。 針對 Azure 應用程式組態，請使用下列其中一個物件。 物件也可以稱為權杖所要求的 *資源* 。

- {configurationStoreName}. azconfig io
- *. azconfig.io

> [!IMPORTANT]
> 當所要求的物件是時 `{configurationStoreName}.azconfig.io` ，它必須完全符合 `Host` 要求標頭， (區分大小寫) 用來傳送要求的要求標頭。

### <a name="azure-ad-authority"></a>Azure AD 授權單位

Azure AD 授權單位是您用來取得 Azure AD 權杖的端點。 它的形式為 `https://login.microsoftonline.com/{tenantId}` 。 `{tenantId}`區段指的是嘗試驗證的使用者或應用程式所屬的 Azure AD 租使用者識別碼。

### <a name="authentication-libraries"></a>驗證程式庫

Azure 提供一組程式庫，稱為 Azure Active Directory 驗證程式庫，以簡化取得 Azure AD 權杖的程式。 Azure 會建立適用于多種語言的這些程式庫。 如需詳細資訊，請參閱[文件](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)。

## <a name="errors"></a>Errors

您可能會遇到下列錯誤。

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**原因：** 您尚未提供此配置的授權要求標頭 `Bearer` 。

**解決方案：** 請提供有效的 `Authorization` HTTP 要求標頭。

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**原因：** Azure AD 的 token 無效。

**解決方案：** 從 Azure AD 授權單位取得 Azure AD 的權杖，並確定您已使用適當的物件。

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**原因：** Azure AD 的 token 無效。

**解決方案：** 從 Azure AD 授權單位取得 Azure AD 的權杖。 確定 Azure AD 租使用者是與設定存放區所屬的訂用帳戶相關聯的租使用者。 如果主體屬於一個以上的 Azure AD 租使用者，則可能會出現此錯誤。
