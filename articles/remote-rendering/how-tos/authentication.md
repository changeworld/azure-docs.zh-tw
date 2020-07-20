---
title: 驗證
description: 說明驗證的運作方式
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: 8f3b144a7790c3122d59d27183b3037998ddadd1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565856"
---
# <a name="configure-authentication"></a>設定驗證

Azure 遠端呈現使用與[Azure 空間錨點（ASA）](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp)相同的驗證機制。 用戶端必須設定下列*其中一項*，才能成功呼叫 REST api：

* **AccountKey**：可以在 Azure 入口網站上遠端呈現帳戶的 [金鑰] 索引標籤中取得。 只有開發/原型才建議帳戶金鑰。
    ![帳戶識別碼](./media/azure-account-primary-key.png)

* **AuthenticationToken**：是 Azure AD token，可以使用[MSAL 程式庫](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)來取得。 有多個不同的流程可供接受使用者認證，並使用這些認證來取得存取權杖。

* **MRAccessToken**：是一種 MR token，可從 Azure Mixed Reality 安全性權杖服務（STS）取得。 `https://sts.mixedreality.azure.com`使用類似下列呼叫的 REST 呼叫，從端點抓取：

    ```rest
    GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.mixedreality.azure.com
    Connection: Keep-Alive

    HTTP/1.1 200 OK
    Date: Tue, 24 Mar 2020 09:09:00 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 1153
    Accept: application/json
    MS-CV: 05JLqWeKFkWpbdY944yl7A.0
    {"AccessToken":"eyJhbGciOiJSUzI1<truncated>uLkO2FvA"}
    ```

    其中，Authorization 標頭的格式如下： `Bearer <Azure_AD_token>` 或 `Bearer <accoundId>:<accountKey>` 。 前者適用于安全性。 從這個 REST 呼叫傳回的權杖是 MR 存取權杖。

## <a name="authentication-for-deployed-applications"></a>已部署應用程式的驗證

建議您在開發期間，將帳戶金鑰用於快速原型設計。 建議您不要使用內嵌的帳戶金鑰，將您的應用程式傳送到生產環境。 建議的方法是使用以使用者為基礎或以服務為基礎的 Azure AD 驗證方法。

 Azure AD 驗證會在[Azure 空間錨點（ASA）](https://docs.microsoft.com/azure/spatial-anchors/)服務的[Azure AD 使用者驗證](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication)一節中說明。

 如需詳細資訊，請參閱[教學課程：保護 Azure 遠端轉譯和模型儲存體-Azure Active Directory 驗證](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication)

## <a name="role-based-access-control"></a>角色型存取控制

若要協助控制授與服務的存取層級，請在授予角色型存取權時，使用下列角色：

* **遠端轉譯系統管理員**：為使用者提供轉換、管理會話、轉譯和診斷功能，以進行 Azure 遠端呈現。
* **遠端轉譯用戶端**：為使用者提供管理會話、轉譯和診斷功能，以進行 Azure 遠端呈現。

## <a name="next-steps"></a>後續步驟

* [建立帳戶](create-an-account.md)
* [使用 Azure 前端 API 進行驗證](frontend-apis.md) (機器翻譯)
* [PowerShell 指令碼範例](../samples/powershell-example-scripts.md)
