---
title: 受保護的 Web API 應用程式註冊 |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何建立受保護的 Web API，以及註冊應用程式所需的資訊。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: ffa5c0ff9aac636a584743f839d780d2df2db5ef
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701989"
---
# <a name="protected-web-api-app-registration"></a>受保護的 Web API：應用程式註冊

本文說明受保護 Web API 的應用程式註冊細節。

如需註冊應用程式的一般步驟，請參閱[快速入門：使用 Microsoft 身分識別平臺註冊應用程式](quickstart-register-app.md)。

## <a name="accepted-token-version"></a>接受的權杖版本

Microsoft 身分識別平臺端點可以發行兩種類型的權杖： v1.0 權杖和 v2.0 權杖。 如需這些權杖的詳細資訊，請參閱[存取權杖](access-tokens.md)。 接受的權杖版本取決於您在建立應用程式時所選擇的**支援帳戶類型**：

- 如果**支援的帳戶類型**的值是**任何組織目錄中的帳戶，以及個人 Microsoft 帳戶（例如 Skype、Xbox、Outlook.com）** ，接受的權杖版本將會是2.0 版。
- 否則，接受的權杖版本將會是 v1.0。

建立應用程式之後，您可以依照下列步驟來判斷或變更接受的權杖版本：

1. 在 Azure 入口網站中，選取您的應用程式，然後選取應用程式的**資訊清單**。
2. 在資訊清單中，搜尋 **"accessTokenAcceptedVersion"** 。 請注意，其值為**2**。 這個屬性會指定 Web API 接受 v2.0 權杖的 Azure Active Directory （Azure AD）。 如果值為**null**，接受的 token 版本為 v1.0。
3. 如果您已變更權杖版本，請選取 [**儲存**]。

> [!NOTE]
> Web API 指定它接受的權杖版本（v1.0 或 v2.0）。 當用戶端向 Microsoft 身分識別平臺（v2.0）端點要求您 Web API 的權杖時，他們會取得權杖，指出 Web API 所接受的版本。

## <a name="no-redirect-uri"></a>沒有重新導向 URI

Web Api 不需要註冊重新導向 URI，因為沒有任何使用者以互動方式登入。

## <a name="expose-an-api"></a>公開 API

Web Api 的另一個特定設定是公開的 API 和公開的範圍。

### <a name="resource-uri-and-scopes"></a>資源 URI 和範圍

範圍的格式通常為 `resourceURI/scopeName`。 針對 Microsoft Graph，範圍具有如 `User.Read`的快捷方式。 這個字串是 `https://graph.microsoft.com/user.read`的快捷方式。

在應用程式註冊期間，您必須定義這些參數：

- 資源 URI。 根據預設，應用程式註冊入口網站會建議您使用 `api://{clientId}`。 此資源 URI 是唯一的，但不是人們可讀取的。 您可以變更它，但請確定新的值是唯一的。
- 一或多個*範圍*。 （對用戶端應用程式而言，它們會顯示為您 Web API 的*委派許可權*）。
- 一或多個*應用程式角色*。 （對用戶端應用程式而言，它們會顯示為您 Web API 的*應用程式許可權*）。

範圍也會顯示在顯示給應用程式使用者的同意畫面上。 因此，您必須提供描述範圍的對應字串：

- 如終端使用者所見。
- 如租使用者系統管理員所見，他可以授與系統管理員同意。

### <a name="exposing-delegated-permissions-scopes"></a>公開委派的許可權（範圍）

1. 選取 [應用程式註冊] 中的 [**公開 API** ] 區段。
1. 選取 [新增範圍]。
1. 若出現提示，請選取 [**儲存並繼續**] 以接受提議的應用程式識別碼 URI （`api://{clientId}`）。
1. 輸入這些參數：
      - 針對 [**領域名稱**]，使用**access_as_user**。
      - 對於**可以同意的人員**，請確定已選取 [系統**管理員] 和 [使用者**]。
      - 在 [系統**管理員同意顯示名稱**] 中，輸入**使用者的存取權 TodoListService**。
      - 在 [**管理員同意描述**] 中，輸入**以使用者身分存取 TodoListService Web API**。
      - 在 [**使用者同意顯示名稱**] 中，輸入**使用者的存取權 TodoListService**。
      - 在 [**使用者同意描述**] 中，輸入**以使用者身分存取 TodoListService Web API**。
      - 將 [**狀態**] 設定為 [**已啟用**]。
      - 選取 [**新增領域**]。

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>如果您的 Web API 是由 daemon 應用程式所呼叫

在本節中，您將瞭解如何註冊受保護的 Web API，讓 daemon 應用程式可以安全地呼叫它。

- 您必須公開*應用程式許可權*。 您只會宣告應用程式許可權，因為 daemon 應用程式不會與使用者互動，因此委派的許可權並不合理。
- 租使用者系統管理員可以要求 Azure Active Directory （Azure AD）僅針對已註冊要存取其中一個 Web API 應用程式許可權的應用程式，發出權杖給您的 Web API。

#### <a name="exposing-application-permissions-app-roles"></a>公開應用程式許可權（應用程式角色）

若要公開應用程式許可權，您必須編輯資訊清單。

1. 在應用程式的應用程式註冊中，選取 [**資訊清單**]。
1. 尋找 `appRoles` 設定並新增一或多個應用程式角色，以編輯資訊清單。 角色定義會在下列範例 JSON 區塊中提供。 將 `allowedMemberTypes` 設定為 僅 `"Application"`。 請確定 `id` 是唯一的 GUID，而且 `displayName` 和 `value` 不包含空格。
1. 儲存資訊清單。

下列範例會顯示 `appRoles`的內容。 （`id` 可以是任何唯一的 GUID）。

```JSon
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

Web API 會檢查應用程式角色。 （這是公開應用程式許可權的開發人員）。但您也可以設定 Azure AD，只為您的 Web API 發出權杖，讓租使用者系統管理員核准的應用程式存取您的 API。 若要新增這種增強的安全性：

1. 在應用程式註冊的 [應用程式**總覽**] 頁面上，選取 [**本機目錄中受控應用程式**] 下的應用程式名稱連結。 此欄位的標題可能會被截斷。 例如，您可能會**在中參閱受控應用程式**。

   > [!NOTE]
   >
   > 當您選取此連結時，您會前往**企業應用程式**的 [總覽] 頁面，並在您建立應用程式的租使用者中，與服務主體相關聯。 您可以使用瀏覽器的 [上一頁] 按鈕，流覽回應用程式註冊頁面。

1. 在 [企業應用程式] 頁面的 [**管理**] 區段中，選取 [**屬性**] 頁面。
1. 如果您想要 Azure AD 只允許從特定用戶端存取您的 Web API，請將 [**需要使用者指派嗎？** ] 設定為 **[是]** 。

   > [!IMPORTANT]
   >
   > 如果您將 [**需要使用者指派嗎？** ] 設定為 **[是]** ，Azure AD 會在用戶端要求 Web API 的存取權杖時，檢查其應用程式角色指派。 如果未將用戶端指派給任何應用程式角色，Azure AD 會傳回錯誤 `invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`。
   >
   > 如果您保留**需要使用者指派嗎？** 設定為 [**否**]， *Azure AD 不會在用戶端要求您的 Web API 的存取權杖時檢查應用程式角色指派*。 任何背景程式用戶端（也就是使用用戶端認證流程的用戶端）只要指定其物件，就能夠取得 API 的存取權杖。 任何應用程式都可以存取 API，而不需要要求其許可權。 但您的 Web API 一律可以如上一節所述，確認應用程式具有正確的角色（由租使用者系統管理員授權）。 此 API 會藉由驗證存取權杖是否有角色宣告，且此宣告的值正確，來執行此驗證。 （在我們的案例中，此值為 `access_as_application`）。

1. 選取 [儲存]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式的程式碼設定](scenario-protected-web-api-app-configuration.md)
