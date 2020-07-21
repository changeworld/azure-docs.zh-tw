---
title: 受保護的 Web API 應用程式註冊 |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何建立受保護的 Web API，以及註冊應用程式所需的資訊。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c1fab15cade2ce23e053bc73028e6420692c3d8a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518269"
---
# <a name="protected-web-api-app-registration"></a>受保護的 Web API：應用程式註冊

本文說明受保護 Web API 的應用程式註冊細節。

如需註冊應用程式的一般步驟，請參閱[快速入門：使用 Microsoft 身分識別平臺註冊應用程式](quickstart-register-app.md)。

## <a name="accepted-token-version"></a>接受的權杖版本

Microsoft 身分識別平臺端點可以發出 v1.0 權杖和 v2.0 權杖。 如需這些權杖的詳細資訊，請參閱[存取權杖](access-tokens.md)。

當您在 Azure 入口網站中建立 Web API 應用程式註冊時，您的 API 可能會接受的權杖版本取決於您所**支援的帳戶類型**選項。

- 如果**支援的帳戶類型**的值是**任何組織目錄中的帳戶，以及個人 Microsoft 帳戶（例如 Skype、Xbox、Outlook.com）**，則接受的權杖版本必須是2.0 版。
- 否則，接受的權杖版本可以是 v1.0。

建立應用程式之後，您可以依照下列步驟來判斷或變更接受的權杖版本：

1. 在 [Azure 入口網站中，選取您的應用程式，然後選取 [**資訊清單**]。
1. 在資訊清單中尋找**accessTokenAcceptedVersion**的屬性。
1. 值會指定 Azure Active Directory （Azure AD） Web API 所接受的權杖版本。
    - 如果值為2，則 Web API 接受 v2.0 權杖。
    - 如果值為**null**，則 Web API 會接受 v1.0 權杖。
1. 如果您變更了權杖版本，請選取 [**儲存**]。

> [!NOTE]
> Web API 指定它接受的權杖版本。 當用戶端向 Microsoft 身分識別平臺（v2.0）端點要求您 Web API 的權杖時，用戶端會取得權杖，指出 Web API 接受的權杖版本。

## <a name="no-redirect-uri"></a>沒有重新導向 URI

Web Api 不需要註冊重新導向 URI，因為沒有任何使用者以互動方式登入。

## <a name="exposed-api"></a>公開的 API

Web Api 特定的其他設定是公開的 API 和公開的範圍或應用程式角色。

### <a name="application-id-uri-and-scopes"></a>應用程式識別碼 URI 和範圍

範圍的格式通常為 `resourceURI/scopeName` 。 針對 Microsoft Graph，範圍會有快捷方式。 例如， `User.Read` 是的快捷方式 `https://graph.microsoft.com/user.read` 。

在應用程式註冊期間，您必須定義這些參數：

- 資源 URI
- 一或多個範圍
- 一或多個應用程式角色

根據預設，應用程式註冊入口網站會建議您使用資源 URI `api://{clientId}` 。 此 URI 是唯一的，但不是人們可讀取的。 如果您變更 URI，請確定新的值是唯一的。 應用程式註冊入口網站可確保您使用[已設定的發行者網域](howto-configure-publisher-domain.md)

在用戶端應用程式中，範圍會顯示為*委派的許可權*，而應用程式角色會顯示為您 Web API 的*應用程式許可權*。

範圍也會出現在向應用程式使用者呈現的同意視窗上。 因此，您必須提供描述範圍的對應字串：

- 如使用者所見。
- 如租使用者系統管理員所見，他可以授與系統管理員同意。

使用者無法同意應用程式角色（因為應用程式會使用它們來代表本身呼叫 Web API）。 租使用者系統管理員必須同意 Web API 公開應用程式角色的用戶端應用程式。 如需詳細資訊，請參閱系統[管理員同意](v2-admin-consent.md)

### <a name="exposing-delegated-permissions-scopes"></a>公開委派的許可權（範圍）

1. 選取 [在應用程式註冊中**公開 API** ]。
1. 選取 [新增範圍]。
1. 若出現提示，請 `api://{clientId}` 選取 [**儲存並繼續**] 以接受建議的應用程式識別碼 URI （）。
1. 指定這些值：
    - 選取 [**領域名稱**]，然後輸入**access_as_user**。
    - 選取 [**誰可以同意**]，並確定已選取 [**管理員] 和 [使用者**]。
    - 選取 [系統**管理員同意顯示名稱**]，然後**以使用者身分輸入存取權 TodoListService**。
    - 選取 [**管理員同意描述**]，然後輸入**以使用者的身分存取 TodoListService Web API**。
    - 選取 [**使用者同意顯示名稱**]，然後**以使用者身分輸入存取權 TodoListService**。
    - 選取 [**使用者同意描述**]，然後輸入**以使用者身分存取 TodoListService Web API**。
    - 將 [**狀態**] 值保持設定為 [**已啟用**]。
 1. 選取 [新增範圍]。

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>如果您的 Web API 是由 daemon 應用程式所呼叫

在本節中，您將瞭解如何註冊受保護的 Web API，讓 daemon 應用程式可以安全地呼叫它。

- 您宣告並僅公開*應用程式許可權*，因為 daemon 應用程式不會與使用者互動。 委派的許可權並不合理。
- 租使用者系統管理員可以要求 Azure AD 只向已註冊來存取其中一個 API 應用程式許可權的應用程式發出 Web API 權杖。

#### <a name="exposing-application-permissions-app-roles"></a>公開應用程式許可權（應用程式角色）

若要公開應用程式許可權，您必須編輯資訊清單。

1. 在應用程式的應用程式註冊中，選取 [**資訊清單**]。
1. 若要編輯資訊清單，請尋找 `appRoles` 設定並新增應用程式角色。 角色定義會在下列範例 JSON 區塊中提供。
1. 保留 `allowedMemberTypes` 設定為 [ `"Application"` 僅]。
1. 請確定 `id` 是唯一的 GUID。
1. 請確定 `displayName` 和 `value` 不包含空格。
1. 儲存資訊清單。

下列範例會顯示的內容 `appRoles` ，其中的值 `id` 可以是任何唯一的 GUID。

```json
"appRoles": [
    {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
    }
],
```

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>確保 Azure AD 會將 Web API 的權杖簽發給僅允許的用戶端

Web API 會檢查應用程式角色。 此角色是軟體發展人員公開應用程式許可權的方式。 您也可以設定 Azure AD，只向租使用者系統管理員核准 API 存取的應用程式發出 API 權杖。

若要新增這種增強的安全性：

1. 前往應用程式註冊的應用程式**總覽**頁面。
1. 在 [**本機目錄中的受控應用程式**] 下，選取含有您應用程式名稱的連結。 此選取範圍的標籤可能會被截斷。 例如，您可能會**在中看到受控應用程式**.。。

   > [!NOTE]
   >
   > 當您選取此連結時，請移至 [**企業應用程式] [總覽**] 頁面。 此頁面會與您在其中建立應用程式的租使用者中的服務主體相關聯。 您可以使用瀏覽器的 [上一頁] 按鈕移至 [應用程式註冊] 頁面。

1. 在 [企業應用程式] 頁面的 [**管理**] 區段中，選取 [**屬性**] 頁面。
1. 如果您想要 Azure AD 只允許從特定用戶端存取您的 Web API，請將 [**需要使用者指派嗎？** ] 設定為 **[是]**。

   > [!IMPORTANT]
   >
   > 如果您將 [**需要使用者指派嗎？** ] 設定為 **[是]**，Azure AD 會在用戶端要求 Web API 存取權杖時，檢查其應用程式角色指派。 如果未將用戶端指派給任何應用程式角色，Azure AD 將會傳回錯誤訊息「invalid_client： AADSTS501051：應用程式未 \<application name\> 指派給角色」 \<web API\> 。
   >
   > 如果您保留**需要使用者指派嗎？** 設定為 [**否**]，Azure AD 不會在用戶端要求您的 Web API 的存取權杖時檢查應用程式角色指派。 任何背景程式用戶端（也就是使用用戶端認證流程的任何用戶端）只要指定其物件，就可以取得 API 的存取權杖。 任何應用程式都可以存取 API，而不需要要求其許可權。
   >
   > 但如上一節所述，您的 Web API 一律可以驗證應用程式是否具有正確的角色（由租使用者系統管理員授權）。此 API 會藉由驗證存取權杖是否有角色宣告，且此宣告的值正確，來執行此驗證。 在先前的 JSON 範例中，值為 `access_as_application` 。

1. 選取 [儲存]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式代碼設定](scenario-protected-web-api-app-configuration.md)
