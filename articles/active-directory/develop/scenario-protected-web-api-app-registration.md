---
title: 受保護的 Web API 應用註冊 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何構建受保護的 Web API 以及註冊應用所需的資訊。
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
ms.openlocfilehash: 2d9e5d051f101288b8528b47fa88b4783a040950
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262499"
---
# <a name="protected-web-api-app-registration"></a>受保護的 Web API：應用註冊

本文介紹了受保護 Web API 的應用註冊的具體內容。

有關註冊應用的常見步驟，請參閱[快速入門：使用 Microsoft 標識平臺註冊應用程式](quickstart-register-app.md)。

## <a name="accepted-token-version"></a>已接受權杖版本

Microsoft 標識平臺終結點可以頒發 v1.0 權杖和 v2.0 權杖。 有關這些權杖的詳細資訊，請參閱[訪問權杖](access-tokens.md)。

接受的權杖版本取決於您在創建應用程式時選擇的**受支援帳戶類型**值。

- 如果**受支援的帳戶類型**的值是**任何組織目錄中的帳戶和個人 Microsoft 帳戶（例如 Skype、Xbox、Outlook.com），** 則接受的權杖版本為 v2.0。
- 否則，接受的權杖版本為 v1.0。

創建應用程式後，可以通過以下步驟確定或更改接受的權杖版本：

1. 在 Azure 門戶中，選擇應用，然後選擇 **"清單**"。
1. 在清單中查找屬性**訪問權杖接受版本**。 屬性的預設值為 2。
1. 該值指定為 Azure 活動目錄 （Azure AD），Web API 接受哪個權杖版本。
    - 如果值為 2，則 Web API 接受 v2.0 權杖。
    - 如果值為**null，** 則 Web API 接受 v1.0 權杖。
1. 如果更改了權杖版本，請選擇"**保存**"。

> [!NOTE]
> Web API 指定它接受哪個權杖版本。 當用戶端從 Microsoft 標識平臺 （v2.0） 終結點請求 Web API 權杖時，用戶端將獲得一個權杖，指示 Web API 接受哪個權杖版本。

## <a name="no-redirect-uri"></a>無重定向 URI

Web API 不需要註冊重定向 URI，因為沒有使用者以對話模式登錄。

## <a name="exposed-api"></a>暴露的 API

特定于 Web API 的其他設置是公開的 API 和公開作用域。

### <a name="application-id-uri-and-scopes"></a>應用程式 ID URI 和作用域

作用域通常具有表單`resourceURI/scopeName`。 對於 Microsoft 圖形，作用域具有快捷方式。 例如，`User.Read`是`https://graph.microsoft.com/user.read`的快捷方式。

在應用註冊期間，您需要定義以下參數：

- 資源 URI
- 一個或多個作用域
- 一個或多個應用角色

預設情況下，應用程式註冊門戶建議您使用資源 URI `api://{clientId}`。 此 URI 是唯一的，但不是人類可讀的。 如果更改 URI，請確保新值是唯一的。

對於用戶端應用程式，作用域將顯示為*委派許可權*，應用角色將顯示為 Web API*的應用程式許可權*。

示波器也會顯示在向應用使用者顯示的同意視窗中。 因此，您需要提供描述作用域的相應字串：

- 如使用者所示。
- 如租戶管理員所示，使用者可以授予管理員同意。

### <a name="exposing-delegated-permissions-scopes"></a>公開委派的許可權（作用域）

1. 選擇在應用程式註冊中**公開 API。**
1. 選擇 **"添加範圍**"。
1. 如果出現提示，請通過選擇`api://{clientId}`**"保存並繼續**"接受建議的應用程式 ID URI （）。
1. 指定以下值：
    - 選擇 **"範圍"名稱**並輸入**access_as_user**。
    - 選擇 **"誰可以同意**"並確保選擇**管理員和使用者**。
    - 選擇 **"管理員同意"顯示名稱**，然後**以使用者身份輸入訪問 TodoList 服務**。
    - 選擇**管理員同意說明**，然後**輸入訪問 TodoList 服務 Web API 作為使用者**。
    - 選擇 **"使用者同意"顯示名稱**，然後**以使用者身份輸入訪問 TodoList 服務**。
    - 選擇**使用者同意說明**，然後**輸入"訪問 TodoList 服務 Web API"作為使用者**。
    - 將**狀態**值設置為 **"已啟用**"。
 1. 選擇 **"添加範圍**"。

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>如果您的 Web API 由守護進程應用調用

在本節中，您將瞭解如何註冊受保護的 Web API，以便守護進程應用可以安全地調用它。

- 您聲明並僅公開*應用程式許可權*，因為守護進程應用不與使用者交互。 委派的許可權沒有意義。
- 租戶管理員可以要求 Azure AD 僅向已註冊以訪問 API 應用程式許可權之一的應用程式頒發 Web API 權杖。

#### <a name="exposing-application-permissions-app-roles"></a>公開應用程式許可權（應用角色）

要公開應用程式許可權，您需要編輯清單。

1. 在申請的應用程式註冊中，選擇 **"清單**"。
1. 要編輯清單，`appRoles`請查找設置並添加應用程式角色。 角色定義在以下示例 JSON 塊中提供。
1. 僅`allowedMemberTypes`保留設置為`"Application"`。
1. 確保`id`是一個獨特的 GUID。
1. 確保`displayName`並且`value`不包含空格。
1. 儲存資訊清單。

下面的示例顯示 的內容`appRoles`，其中 的值`id`可以是任何唯一的 GUID。

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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>確保 Azure AD 為 Web API 發出權杖，僅允許用戶端

Web API 檢查應用角色。 此角色是軟體發展人員公開應用程式許可權的方法。 還可以將 Azure AD 配置為僅向租戶管理員批准 API 訪問的應用頒發 API 權杖。

要添加此增強的安全性，：

1. 轉到應用**概述**頁面進行應用註冊。
1. 在**本地目錄中的"託管應用程式**"下，選擇帶有應用名稱的連結。 此選擇的標籤可能會被截斷。 例如，您可能會**在 ...**

   > [!NOTE]
   >
   > 選擇此連結時，轉到**企業應用程式概述**頁面。 此頁面與創建應用程式的租戶中的應用程式的服務主體相關聯。 您可以使用瀏覽器的後退按鈕轉到應用註冊頁面。

1. 在企業應用程式頁面的 **"管理**"部分中選擇 **"屬性**"頁。
1. 如果希望 Azure AD 僅允許僅從某些用戶端存取 Web API，請將 **"需要的使用者分配"** 設置為 **"是**"。

   > [!IMPORTANT]
   >
   > 如果將 **"使用者分配"** 設置為 **"是**"，Azure AD 會在用戶端請求 Web API 訪問權杖時檢查用戶端的應用角色指派。 如果用戶端未分配給任何應用角色，Azure AD 將返回錯誤訊息"invalid_client：AADSTS501051：\<應用程式名稱\>未分配給\<Web API\>的角色"。
   >
   > 如果保持 **"使用者分配"設置為****"否**"，則當用戶端請求 Web API 的訪問權杖時，Azure AD 不會檢查應用角色指派。 任何守護進程用戶端（即使用用戶端憑據流的任何用戶端）都可以通過指定其訪問群體來獲得 API 的訪問權杖。 任何應用程式都可以訪問 API，而無需為其請求許可權。
   >
   > 但是，如上一節所述，Web API 始終可以驗證應用程式是否具有正確的角色，該角色由租戶管理員授權。API 通過驗證訪問權杖具有角色聲明以及此聲明的值是否正確來執行此驗證。 在前面的 JSON 示例中，值為`access_as_application`。

1. 選取 [儲存]****。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用代碼配置](scenario-protected-web-api-app-configuration.md)
