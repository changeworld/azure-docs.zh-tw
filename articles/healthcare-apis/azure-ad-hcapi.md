---
title: Azure API for FHIR 的 Azure Active Directory 身分識別設定
description: 瞭解 Azure FHIR server 的身分識別、驗證和授權原則。
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: b362a81fc9b533fe00987a74d7e25dbba61a2589
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398244"
---
# <a name="azure-active-directory-identity-configuration-for-azure-api-for-fhir"></a>Azure API for FHIR 的 Azure Active Directory 身分識別設定

使用醫療保健資料時，有一個很重要的部分，就是要確保資料安全，而且無法被未經授權的使用者或應用程式存取。 FHIR 伺服器使用 [OAuth 2.0](https://oauth.net/2/) 來確保此資料安全性。 [Azure API for FHIR](https://azure.microsoft.com/services/azure-api-for-fhir/)使用[Azure Active Directory](../active-directory/index.yml)來保護，這是 OAuth 2.0 身分識別提供者的範例。 本文提供 FHIR 伺服器授權的總覽，以及取得權杖以存取 FHIR 伺服器所需的步驟。 雖然這些步驟將適用于任何 FHIR 伺服器和任何身分識別提供者，但我們將逐步解說作為 FHIR 伺服器的 Azure API for FHIR，並 Azure AD 為本文中的身分識別提供者。

## <a name="access-control-overview"></a>存取控制概觀

為了讓用戶端應用程式存取 Azure API for FHIR，它必須出示存取權杖。 存取權杖是以 [Base64](https://en.wikipedia.org/wiki/Base64) 編碼的屬性集合， (宣告) ，可傳達用戶端身分識別的相關資訊，以及授與用戶端的角色和許可權。

取得權杖的方法有很多種，但 Azure API for FHIR 並不在意權杖的取得方式，只要它是具有正確宣告的適當簽署權杖即可。 

使用 [授權碼流程](../active-directory/azuread-dev/v1-protocols-oauth-code.md) 作為範例，存取 FHIR 伺服器會經歷下列四個步驟：

![FHIR 授權](media/azure-ad-hcapi/fhir-authorization.png)

1. 用戶端會將要求傳送至 `/authorize` Azure AD 的端點。 Azure AD 會將用戶端重新導向至登入頁面，使用者將使用適當的認證進行驗證， (例如使用者名稱和密碼，或雙因素驗證) 。 請參閱 [取得授權碼](../active-directory/azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)的詳細資料。 驗證成功時，會將 *授權碼* 傳回給用戶端。 Azure AD 只會允許將此授權碼傳回至用戶端應用程式註冊中所設定的已註冊回復 URL (請參閱以下) 。
1. 用戶端應用程式會在 Azure AD 端點交換 *存取權杖* 的授權碼 `/token` 。 要求權杖時，用戶端應用程式可能必須提供用戶端密碼 (應用程式密碼) 。 請參閱 [取得存取權杖](../active-directory/azuread-dev/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)的詳細資料。
1. 用戶端會對 Azure API for FHIR 提出要求，例如 `GET /Patient` 搜尋所有患者。 提出要求時，它會在 HTTP 要求標頭中包含存取權杖，例如 `Authorization: Bearer eyJ0e...` ，其中 `eyJ0e...` 代表 Base64 編碼存取權杖。
1. Azure API for FHIR 會驗證權杖中是否包含適當的宣告 () 中的屬性。 如果所有專案都已簽出，則會完成要求，並將結果傳回 FHIR 組合給用戶端。

請務必注意，Azure API for FHIR 不會涉及驗證使用者認證，也不會發出權杖。 驗證和權杖建立是由 Azure AD 完成。 Azure API for FHIR 只會驗證權杖是否已正確簽署 (它是真實的) 而且具有適當的宣告。

## <a name="structure-of-an-access-token"></a>存取權杖的結構

FHIR 應用程式的開發通常牽涉到偵錯工具的存取問題。 如果用戶端遭到拒絕存取 Azure API for FHIR，瞭解存取權杖的結構，以及如何將其解碼以檢查權杖的宣告)  (內容，會很有用。 

FHIR 伺服器通常會預期 (JWT 的 [JSON Web 權杖](https://en.wikipedia.org/wiki/JSON_Web_Token) ，有時會發音為 "jot" ) 。 它是由三個部分所組成：

1. 標頭，如下所示：
    ```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```
1.  (宣告) 的承載，例如：
    ```json
    {
     "oid": "123",
     "iss": "https://issuerurl",
     "iat": 1422779638,
     "roles": [
        "admin"
      ]
    }
    ```
1. 簽章，其計算方式是串連標頭的 Base64 編碼內容和承載，並根據 `alg` 標頭中指定的演算法 () 來計算密碼的密碼編譯雜湊。 伺服器將能夠從身分識別提供者取得公開金鑰，並驗證此權杖是由特定的身分識別提供者所發出，而且尚未遭到篡改。

完整標記是由 Base64 編碼的 (實際上是以 base64 編碼的格式，) 這三個區段的版本。 這三個區段會串連在一起，並以 `.` (點) 分隔。

範例權杖如下所示：

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

您可以使用之類的工具來解碼和檢查權杖 [https://jwt.ms](https://jwt.ms) 。 解碼權杖的結果為：

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "oid": "123",
  "iss": "https://issuerurl",
  "iat": 1422779638,
  "roles": [
    "admin"
  ]
}.[Signature]
```

## <a name="obtaining-an-access-token"></a>取得存取權杖

如上所述，有數種方式可從 Azure AD 取得權杖。 [Azure AD 開發人員檔](../active-directory/develop/index.yml)中會詳細說明它們。

Azure AD 有兩個不同版本的 OAuth 2.0 端點，稱為 `v1.0` 和 `v2.0` 。 這兩個版本都是 OAuth 2.0 端點， `v1.0` 而和則是 `v2.0` 指 Azure AD 如何實行該標準的差異。 

使用 FHIR 伺服器時，您可以使用 `v1.0` 或 `v2.0` 端點。 選擇可能取決於您在用戶端應用程式中使用的驗證程式庫。

Azure AD 檔的相關章節如下：

* `v1.0` 端點：
    * [授權碼流程](../active-directory/azuread-dev/v1-protocols-oauth-code.md)。
    * [用戶端認證流程](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)。
* `v2.0` 端點：
    * [授權碼流程](../active-directory/develop/v2-oauth2-auth-code-flow.md)。
    * [用戶端認證流程](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)。

有其他變化 (例如代表流程) 來取得權杖。 請查看 Azure AD 檔以取得詳細資料。 使用 Azure API for FHIR 時，也有一些快速鍵可以用來取得存取權杖， (用於) [使用 Azure CLI](get-healthcare-apis-access-token-cli.md)的偵錯工具。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解使用 Azure AD 保護 Azure API for FHIR 存取的一些基本概念。 若要瞭解如何部署 Azure API for FHIR 的實例，請繼續進行部署快速入門。

>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)