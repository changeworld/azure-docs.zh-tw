---
title: Azure API for FHIR 存取權杖驗證
description: 逐步解說權杖驗證，並提供如何針對存取問題進行疑難排解的秘訣
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 426ec0c2d6cc274aa0b6829eb4a30fd29b9ba8e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87844655"
---
# <a name="azure-api-for-fhir-access-token-validation"></a>Azure API for FHIR 存取權杖驗證

Azure API for FHIR 如何驗證存取權杖將取決於執行和設定。 在本文中，我們將逐步解說驗證步驟，在對存取問題進行疑難排解時，這會很有説明。

## <a name="validate-token-has-no-issues-with-identity-provider"></a>驗證權杖沒有身分識別提供者的問題

權杖驗證的第一個步驟是驗證權杖是由正確的身分識別提供者所發出，而且尚未經過修改。 FHIR 伺服器將設定為使用特定的身分識別提供者，稱為授權單位 `Authority` 。 FHIR 伺服器會從端點抓取身分識別提供者的相關資訊 `/.well-known/openid-configuration` 。 使用 Azure AD 時，完整的 URL 會是：

```
GET https://login.microsoftonline.com/<TENANT-ID>/.well-known/openid-configuration
```

其中 `<TENANT-ID>` 是特定 Azure AD 租使用者 (租使用者識別碼或功能變數名稱) 。

Azure AD 會將如下的檔傳回給 FHIR 伺服器。

```json
{
    "authorization_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token",
    "token_endpoint_auth_methods_supported": [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys",
    "response_modes_supported": [
        "query",
        "fragment",
        "form_post"
    ],
    "subject_types_supported": [
        "pairwise"
    ],
    "id_token_signing_alg_values_supported": [
        "RS256"
    ],
    "http_logout_supported": true,
    "frontchannel_logout_supported": true,
    "end_session_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/logout",
    "response_types_supported": [
        "code",
        "id_token",
        "code id_token",
        "token id_token",
        "token"
    ],
    "scopes_supported": [
        "openid"
    ],
    "issuer": "https://sts.windows.net/<TENANT-ID>/",
    "claims_supported": [
        "sub",
        "iss",
        "cloud_instance_name",
        "cloud_instance_host_name",
        "cloud_graph_host_name",
        "msgraph_host",
        "aud",
        "exp",
        "iat",
        "auth_time",
        "acr",
        "amr",
        "nonce",
        "email",
        "given_name",
        "family_name",
        "nickname"
    ],
    "microsoft_multi_refresh_token": true,
    "check_session_iframe": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/checksession",
    "userinfo_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/openid/userinfo",
    "tenant_region_scope": "WW",
    "cloud_instance_name": "microsoftonline.com",
    "cloud_graph_host_name": "graph.windows.net",
    "msgraph_host": "graph.microsoft.com",
    "rbac_url": "https://pas.windows.net"
}
``` 
FHIR 伺服器的重要屬性是 `jwks_uri` ，它會告訴伺服器要在哪裡提取驗證權杖簽章所需的加密金鑰，而 `issuer` 這會告訴伺服器簽發者宣告 (`iss`) 此伺服器發出的權杖。 FHIR 伺服器可以使用此伺服器來驗證它是否正在接收真實的權杖。

## <a name="validate-claims-of-the-token"></a>驗證權杖的宣告

一旦伺服器驗證了權杖的真確性之後，FHIR 伺服器就會繼續驗證用戶端是否有必要的宣告來存取權杖。

使用 Azure API for FHIR 時，伺服器將會驗證：

1. 權杖具有正確的 `Audience` (宣告 `aud`) 。
1. 發出權杖的使用者或主體可存取 FHIR 伺服器資料平面。 權杖的宣告 `oid` 包含識別物件識別碼，可唯一識別使用者或主體。

建議您將 FHIR 服務 [設定為使用 AZURE RBAC](configure-azure-rbac.md) 來管理資料平面的角色指派。 但是，如果您的 FHIR 服務使用外部或次要 Azure Active Directory 租使用者，您也可以 [設定本機 RBAC](configure-local-rbac.md) 。 

使用 OSS 適用于 Azure 的 Microsoft FHIR server 時，伺服器將會驗證：

1. 權杖具有正確的 `Audience` (宣告 `aud`) 。
1. 權杖在宣告中具有角色 `roles` ，允許存取 FHIR 伺服器。

請參閱有關如何在 [FHIR 伺服器上定義角色](https://github.com/microsoft/fhir-server/blob/master/docs/Roles.md)的詳細資料。

FHIR 伺服器也可以驗證存取權杖的權杖宣告 (範圍， `scp`) 存取用戶端嘗試存取的部分 FHIR API。 目前，適用于 Azure 的 Azure API for FHIR 和 FHIR 伺服器不會驗證權杖範圍。

## <a name="next-steps"></a>後續步驟
現在您已瞭解如何逐步進行權杖驗證，您可以完成教學課程來建立 JavaScript 應用程式並讀取 FHIR 資料。

>[!div class="nextstepaction"]
>[Web 應用程式教學課程](tutorial-web-app-fhir-server.md)