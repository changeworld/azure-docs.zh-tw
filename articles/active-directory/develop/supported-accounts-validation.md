---
title: 依支援的帳戶類型的驗證差異-Microsoft 身分識別平臺 |Azure
description: 瞭解使用 Microsoft 身分識別平臺註冊應用程式時，不同支援的帳戶類型之各種屬性的驗證差異。
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 07/21/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 82b721d14d114b358939bebecff00bc762b075a6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87058342"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>依支援的帳戶類型（signInAudience）的驗證差異

向開發人員的 Microsoft 身分識別平臺註冊應用程式時，系統會要求您選取應用程式支援的帳戶類型。 在應用程式物件和資訊清單中，這個屬性是 `signInAudience` 。

選項包括：

- *AzureADMyOrg*：僅限已註冊應用程式之組織目錄中的帳戶（單一租使用者）
- *AzureADMultipleOrgs*：任何組織目錄（多租使用者）中的帳戶
- *AzureADandPersonalMicrosoftAccount*：任何組織目錄（多租使用者）和個人 Microsoft 帳戶（例如 Skype、Xbox 和 Outlook.com）中的帳戶

針對已註冊的應用程式，您可以在應用程式的 [**驗證**] 區段中找到支援的帳戶類型的值。 您也可以在 `signInAudience` **資訊清單**中的屬性下找到它。

您為此屬性選取的值會影響其他應用程式物件屬性。 因此，如果您變更此屬性，則可能需要先變更其他屬性。

請參閱下表，以瞭解不同受支援帳戶類型之各種屬性的驗證差異。

| 屬性 | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` 和 `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| 應用程式識別碼 URI （ `identifierURIs` ）  | 在租使用者中必須是唯一的 <br><br> 支援 urn://配置 <br><br> 不支援萬用字元 <br><br> 支援查詢字串和片段 <br><br> 長度上限為255個字元 <br><br> IdentifierURIs 數目沒有限制 *  | 必須是全域唯一的 <br><br> 支援 urn://配置 <br><br> 不支援萬用字元 <br><br> 支援查詢字串和片段 <br><br> 長度上限為255個字元 <br><br> IdentifierURIs 數目沒有限制 * | 必須是全域唯一的 <br><br> 不支援 urn://配置 <br><br> 不支援萬用字元、片段和查詢字串 <br><br> 長度上限為120個字元 <br><br> 最大值為 50 identifierURIs |
| 憑證（ `keyCredentials` ） | 對稱簽署金鑰 | 對稱簽署金鑰 | 加密和非對稱簽署金鑰 | 
| 用戶端密碼（ `passwordCredentials` ） | 無限制 * | 無限制 * | 如果已啟用 liveSDK：最多2個用戶端密碼 | 
| 重新導向 Uri （ `replyURLs` ） | 如需詳細資訊，請參閱重新[導向 URI/回復 URL 限制和限制](reply-url.md)。 | | | 
| API 許可權（ `requiredResourceAccess` ） | 無限制 * | 無限制 * | 每個應用程式最多50個資源，每個資源30個許可權（例如 Microsoft Graph）。 每個應用程式200的總限制（資源 x 許可權）。 | 
| 此 API 定義的範圍（ `oauth2Permissions` ） | 最大範圍名稱長度為120個字元 <br><br> 定義的範圍數目沒有限制 * | 最大範圍名稱長度為120個字元 <br><br> 定義的範圍數目沒有限制 * |  最大範圍名稱長度為40個字元 <br><br> 已定義最多100個範圍 | 
| 授權的用戶端應用程式（ `preautorizedApplications` ） | 無限制 * | 無限制 * | 總計上限為500 <br><br> 最多已定義100個用戶端應用程式 <br><br> 每個用戶端最多定義30個範圍 | 
| appRoles | 支援 <br> 無限制 * | 支援 <br> 無限制 * | 不支援 | 
| 登出 URL | http://localhost允許 <br><br> 長度上限為255個字元 | http://localhost允許 <br><br> 長度上限為255個字元 | <br><br> https://localhost允許， http://localhost MSA 失敗 <br><br> 長度上限為255個字元 <br><br> 不允許 HTTP 配置 <br><br> 不支援萬用字元 | 

* 應用程式物件的所有集合屬性有大約1000個專案的全域限制

## <a name="next-steps"></a>接下來的步驟

- 瞭解[應用程式註冊](app-objects-and-service-principals.md)
- 了解[應用程式資訊清單](reference-app-manifest.md)
