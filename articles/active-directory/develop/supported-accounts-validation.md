---
title: 按受支援的帳戶類型進行驗證差異 - 微軟標識平臺 |蔚藍
description: 在 Microsoft 標識平臺註冊應用時，瞭解不同受支援帳戶類型的各種屬性的驗證差異。
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 10/12/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 2a1507b008903085886f9392f3f4e5461997b6e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128866"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>按支援的帳戶類型（簽名 InAudience）驗證差異

向開發人員註冊應用程式時，系統會要求您選擇應用程式支援的帳戶類型。 在應用程式物件和清單中，此屬性是`signInAudience`。

選項包括：

- *AzureADMyOrg*：僅註冊應用的組織目錄中的帳戶（單租戶）
- *AzureAD多路組織*：任何組織目錄中的帳戶（多租戶）
- *AzureADand個人 Microsoft 帳戶*：任何組織目錄（多租戶）和個人 Microsoft 帳戶（例如，Skype、Xbox 和Outlook.com）中的帳戶

對於已註冊的應用程式，您可以在應用程式的 **"身份驗證**"部分找到受支援的帳戶類型的值。 您還可以在`signInAudience`**清單**的屬性下找到它。

為此屬性選擇的值對其他應用物件屬性有影響。 因此，如果更改此屬性，則可能需要首先更改其他屬性。

有關不同受支援帳戶類型的驗證差異，請參閱下表。

| 屬性 | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` 和 `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| 應用程式 ID`identifierURIs`URI （ ）  | 租戶中必須是唯一的 <br><br> 支援urn://方案 <br><br> 不支援萬用字元 <br><br> 支援查詢字串和片段 <br><br> 最大長度為 255 個字元 <br><br> 識別碼數量沒有限制*  | 必須是全域唯一的 <br><br> 支援urn://方案 <br><br> 不支援萬用字元 <br><br> 支援查詢字串和片段 <br><br> 最大長度為 255 個字元 <br><br> 識別碼數量沒有限制* | 必須是全域唯一的 <br><br> 不支援urn://方案 <br><br> 不支援萬用字元、片段和查詢字串 <br><br> 最大長度為 120 個字元 <br><br> 最多 50 個識別碼 URI |
| 證書`keyCredentials`（ ） | 對稱簽名金鑰 | 對稱簽名金鑰 | 加密和非對稱簽名金鑰 | 
| 客戶機密`passwordCredentials`（ ） | 無限制* | 無限制* | 如果啟用了 liveSDK：最多 2 個用戶端機密 | 
| 重定向 URI （`replyURLs`） | 有關詳細資訊，請參閱[重定向 URI/回復 URL 限制和限制](reply-url.md)。 | | | 
| API 許可權`requiredResourceAccess`（ ） | 無限制* | 無限制* | 每個資源最多允許 30 個許可權（例如，微軟圖形） | 
| 由此 API 定義的作用域`oauth2Permissions`（ ） | 最大範圍名稱長度為 120 個字元 <br><br> 定義的作用域數沒有限制* | 最大範圍名稱長度為 120 個字元 <br><br> 定義的作用域數沒有限制* |  最大範圍名稱長度為 40 個字元 <br><br> 最多定義 100 個作用域 | 
| 授權用戶端應用程式`preautorizedApplications`（ ） | 無限制* | 無限制* | 總計 500 <br><br> 最多定義 100 個用戶端應用 <br><br> 每個用戶端最多定義 30 個作用域 | 
| appRoles | 支援 <br> 無限制* | 支援 <br> 無限制* | 不支援 | 
| 登出 URL | http://localhost允許 <br><br> 最大長度為 255 個字元 | http://localhost允許 <br><br> 最大長度為 255 個字元 | <br><br> https://localhost允許，MSAhttp://localhost失敗 <br><br> 最大長度為 255 個字元 <br><br> 不允許使用 HTTP 方案 <br><br> 不支援萬用字元 | 

•應用物件上的所有集合屬性中約有 1000 個項的全域限制

## <a name="next-steps"></a>後續步驟

- 瞭解[應用程式註冊](app-objects-and-service-principals.md)
- 瞭解[應用程式清單](reference-app-manifest.md)
