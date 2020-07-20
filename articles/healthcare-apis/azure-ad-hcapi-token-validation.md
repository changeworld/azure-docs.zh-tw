---
title: Azure API for FHIR 存取權杖驗證
description: 逐步解說權杖驗證，並提供如何針對存取問題進行疑難排解的秘訣
services: healthcare-apis
author: caitlinv39
ms.reviewer: mihansen
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 672acd4f68acd4998fd7631240c2f998299def11
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871228"
---
# <a name="azure-api-for-fhir-access-token-validation"></a>Azure API for FHIR 存取權杖驗證

Azure API for FHIR 如何驗證存取權杖將取決於執行和設定。 在本文中，我們將逐步解說驗證步驟，這在針對存取問題進行疑難排解時可能會很有説明。

## <a name="validate-token-has-no-issues-with-identity-provider"></a>驗證權杖沒有識別提供者的問題

權杖驗證的第一個步驟是驗證權杖是由正確的識別提供者所發出，而且它尚未修改。 FHIR 伺服器將設定為使用特定的身分識別提供者，稱為授權單位 `Authority` 。 FHIR 伺服器將會從端點抓取識別提供者的相關資訊 `/.well-known/openid-configuration` 。 使用 Azure AD 時，完整的 URL 會是：

```
GET https://login.microsoftonline.com/<TENANT-ID>/.well-known/openid-configuration
```

其中 `<TENANT-ID>` 是特定的 Azure AD 租使用者（租使用者識別碼或功能變數名稱）。

Azure AD 會將如下所示的檔傳回至 FHIR 伺服器。

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
FHIR 伺服器的重要屬性是 `jwks_uri` ，它會告訴伺服器要在何處提取驗證權杖簽章所需的加密金鑰，以及 `issuer` 告訴伺服器該伺服器所簽發之權杖的簽發者宣告（）中的內容 `iss` 。 FHIR 伺服器可以用它來驗證它是否正在接收真實的權杖。

## <a name="validate-claims-of-the-token"></a>驗證權杖的宣告

一旦伺服器驗證了權杖的真實性，FHIR 伺服器就會繼續驗證用戶端是否有存取權杖所需的宣告。

使用 Azure API for FHIR 時，伺服器將會驗證：

1. Token 具有正確的 `Audience` （宣告 `aud` ）。
1. 為其發出權杖的使用者或主體，可以存取 FHIR 伺服器資料平面。 Token 的宣告包含身分識別 `oid` 物件識別碼，可唯一識別使用者或主體。

我們建議您將 FHIR 服務[設定為使用 AZURE RBAC](configure-azure-rbac.md)來管理資料平面角色指派。 但是，如果您的 FHIR 服務使用外部或次要 Azure Active Directory 租使用者，您也可以[設定本機 RBAC](configure-local-rbac.md) 。 

使用適用于 Azure 的 OSS Microsoft FHIR server 時，伺服器將會驗證：

1. Token 具有正確的 `Audience` （宣告 `aud` ）。
1. 權杖在宣告中具有角色 `roles` ，允許存取 FHIR 伺服器。

如需如何[在 FHIR 伺服器上定義角色](https://github.com/microsoft/fhir-server/blob/master/docs/Roles.md)的詳細資訊，請參閱。

FHIR 伺服器也會驗證存取權杖是否具有範圍（在權杖宣告中）， `scp` 以存取用戶端嘗試存取的 FHIR API 部分。 目前，Azure API for FHIR 和適用于 Azure 的 FHIR 伺服器不會驗證權杖範圍。

## <a name="next-steps"></a>後續步驟
現在您已瞭解如何逐步進行權杖驗證，您可以完成建立 JavaScript 應用程式和讀取 FHIR 資料的教學課程。

>[!div class="nextstepaction"]
>[Web 應用程式教學課程](tutorial-web-app-fhir-server.md)