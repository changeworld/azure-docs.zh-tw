---
title: 依支援的帳戶類型的驗證差異 |蔚藍
titleSuffix: Microsoft identity platform
description: 針對使用 Microsoft 身分識別平臺註冊應用程式時，針對不同支援的帳戶類型，瞭解各種屬性的驗證差異。
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 07/21/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: eea2e587a075d774a25f479ec61575a002b57f75
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937806"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>依支援的帳戶類型的驗證差異 (signInAudience) 

使用開發人員的 Microsoft 身分識別平臺註冊應用程式時，系統會要求您選取您的應用程式支援的帳戶類型。 在應用程式物件和資訊清單中，這個屬性是 `signInAudience` 。

選項包括：

- **>azureadmyorg**：只有在組織目錄中註冊應用程式的帳戶 (單一租使用者) 。
- **>azureadmultipleorgs**：任何組織目錄中的帳戶 (多租使用者) 。
- **AzureADandPersonalMicrosoftAccount**：任何組織目錄中的帳戶 (多租使用者) 和個人 Microsoft 帳戶 (例如 Skype、Xbox 和 Outlook.com) 。

針對已註冊的應用程式，您可以在應用程式的 [ **驗證** ] 區段中找到支援的帳戶類型的值。 您也可以在 `signInAudience` **資訊清單** 中的屬性下找到它。

您為此屬性選取的值會影響其他應用程式物件屬性。 因此，如果您變更此屬性，您可能需要先變更其他屬性。

請參閱下表，以瞭解不同支援的帳戶類型之各種屬性的驗證差異。

| 屬性 | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` 和 `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| 應用程式識別碼 URI (`identifierURIs`)   | 在租使用者中必須是唯一的 <br><br> 支援 urn://配置 <br><br> 不支援萬用字元 <br><br> 支援查詢字串和片段 <br><br> 最大長度255個字元 <br><br> IdentifierURIs 數目沒有限制 *  | 必須是全域唯一的 <br><br> 支援 urn://配置 <br><br> 不支援萬用字元 <br><br> 支援查詢字串和片段 <br><br> 最大長度255個字元 <br><br> IdentifierURIs 數目沒有限制 * | 必須是全域唯一的 <br><br> 不支援 urn://配置 <br><br> 不支援萬用字元、片段和查詢字串 <br><br> 最大長度120個字元 <br><br> 最大值為 50 identifierURIs |
| 憑證 (`keyCredentials`)  | 對稱簽署金鑰 | 對稱簽署金鑰 | 加密和非對稱簽署金鑰 | 
|  () 用戶端密碼 `passwordCredentials` | 無限制 * | 無限制 * | 如果已啟用 liveSDK：最多2個用戶端秘密 | 
| 重新導向 Uri (`replyURLs`)  | 如需詳細資訊，請參閱重新 [導向 URI/回復 URL 限制和限制](reply-url.md) 。 | | | 
| API 許可權 (`requiredResourceAccess`)  | 無限制 * | 無限制 * | 每個應用程式最多50個資源，每個資源有30個許可權 (例如 Microsoft Graph) 。 每個應用程式200的總限制 (資源 x 許可權) 。 | 
| 由此 API 定義的範圍 (`oauth2Permissions`)  | 最大範圍名稱長度為120個字元 <br><br> 定義的範圍數目沒有限制 * | 最大範圍名稱長度為120個字元 <br><br> 定義的範圍數目沒有限制 * |  最大範圍名稱長度為40個字元 <br><br> 已定義100範圍的最大值 | 
| 授權的用戶端應用程式 (`preAuthorizedApplications`)  | 無限制 * | 無限制 * | 總計上限為500 <br><br> 已定義100用戶端應用程式的最大值 <br><br> 每個用戶端定義的最多30個範圍 | 
| appRoles | 支援 <br> 無限制 * | 支援 <br> 無限制 * | 不支援 | 
| 前端通道登出 URL | https://localhost 允許 <br><br> `http` 不允許配置 <br><br> 最大長度255個字元 | https://localhost 允許 <br><br> `http` 不允許配置 <br><br> 最大長度255個字元 | <br><br> https://localhost 允許， http://localhost MSA 失敗 <br><br> 最大長度255個字元 <br><br> `http` 不允許配置 <br><br> 不支援萬用字元 | 

* 應用程式物件上的所有集合屬性都有大約1000個專案的全域限制。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [應用程式註冊](app-objects-and-service-principals.md)。
- 深入瞭解 [應用程式資訊清單](reference-app-manifest.md)。
