---
title: 教學課程：授與 ASP.NET Web API 的存取權
titleSuffix: Azure AD B2C
description: 關於如何使用 Active Directory B2C 來保護 ASP.NET Web API，以及如何從 ASP.NET Web 應用程式加以呼叫的教學課程。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/14/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: fe6f8469354922dcf38a987b9b4371547678eb36
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849948"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>教學課程：使用 Azure Active Directory B2C 授與存取 ASP.NET Web API 的權限

本教學課程說明如何從 ASP.NET Web 應用程式呼叫 Azure Active Directory B2C (Azure AD B2C) 中受保護的 Web API 資源。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 新增 Web API 應用程式
> * 設定 Web API 的範圍
> * 授與 Web API 的權限
> * 設定範例以使用應用程式

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

完成以下教學課程中的步驟和必要條件：[教學課程：使用 Azure Active Directory B2C 在 Web 應用程式中啟用驗證](tutorial-web-app-dotnet.md)。

## <a name="add-a-web-api-application"></a>新增 Web API 應用程式

Web API 資源必須先在您的租用戶中註冊，才能接受及回應受到用戶端應用程式保護而提供存取權杖的資源要求。

若要在您的 Azure AD B2C 租用戶中註冊應用程式，您可以使用目前的**應用程式**體驗，或使用新整合的**應用程式註冊 (預覽)** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregintro)。

#### <a name="applicationstabapplications"></a>[應用程式](#tab/applications/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取頂端功能表中的 [目錄 + 訂用帳戶]  篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]  ，然後搜尋並選取 [Azure AD B2C]  。
4. 選取 [應用程式]  ，然後選取 [新增]  。
5. 輸入應用程式的名稱。 例如，*webapi1*。
6. 針對 [包含 Web 應用程式/Web API]  ，選取 [是]  。
7. 針對**回覆 URL**，請輸入 Azure AD B2C 應傳回您的應用程式所要求任何權杖的端點。 在本教學課程中，範例會在本機執行並在 `https://localhost:44332` 接聽。
8. 針對**應用程式識別碼 URI**，請輸入您的 Web API 所使用的識別碼。 系統會為您產生包含網域的完整識別碼 URI。 例如： `https://contosotenant.onmicrosoft.com/api` 。
9. 按一下頁面底部的 [新增]  。
10. 在 [屬性] 頁面上，記錄您會在設定 Web 應用程式時使用的應用程式識別碼。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]  篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]  。 或者，選取 [所有服務]  ，然後搜尋並選取 [Azure AD B2C]  。
1. 選取 [應用程式註冊 (預覽)]  ，然後選取 [新增註冊]  。
1. 輸入應用程式的 [名稱]  。 例如，*webapi1*。
1. 在 [重新導向 URI]  底下，選取 [Web]  ，然後輸入 Azure AD B2C 應傳回您應用程式所要求之任何權杖的端點。 在本教學課程中，範例會在本機執行並在 `https://localhost:44332` 接聽。
1. 選取 [註冊]  。
1. 記錄 [應用程式 (用戶端) 識別碼]  ，以便在稍後的步驟中使用。

* * *

## <a name="configure-scopes"></a>設定範圍

範圍可用來控管對受保護資源的存取。 Web API 可使用範圍來實作以範圍為基礎的存取控制。 例如，Web API 的使用者可以同時具有讀取和寫入權限，Web API 的使用者也可能只具有讀取權限。 在本教學課程中，您會使用範圍來定義 Web API 的讀取和寫入權限。

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>授與權限

若要從應用程式呼叫受保護的 Web API，您必須為應用程式授與對 API 的權限。 在必要的教學課程中，您已在 Azure AD B2C 中建立名為 *webapp1* 的 Web 應用程式。 您可使用此應用程式來呼叫 Web API。

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

您的應用程式會進行註冊，以呼叫受保護的 Web API。 使用者會透過 Azure AD B2C 進行驗證以使用應用程式。 該應用程式會從 Azure AD B2C 取得授權授與，以存取受保護的 Web API。

## <a name="configure-the-sample"></a>設定範例

現在已註冊 Web API 且已定義範圍，您可設定 Web API 以使用您的 Azure AD B2C 租用戶。 在本教學課程中，您會設定範例 Web API。 您在必要的教學課程中下載的專案包含範例 Web API。

範例方案中有兩個專案：

* **TaskWebApp** - 建立和編輯工作清單。 此範例會使用**註冊或登入**使用者流程來註冊或登入使用者。
* **TaskService** - 支援建立、讀取、更新和刪除工作清單功能。 此 API 會受到 Azure AD B2C 的保護，且由 TaskWebApp 呼叫。

### <a name="configure-the-web-application"></a>設定 Web 應用程式

1. 在 Visual Studio 中開啟 **B2C-WebAPI-DotNet** 方案。
1. 開啟 **TaskWebApp** 專案中的 **Web.config**。
1. 若要在本機執行 API，請使用 **api:TaskServiceUrl** 的 localhost 設定。 變更 Web.config，如下所示：

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

1. 設定 API 的 URI。 這是 Web 應用程式用來進行 API 要求的 URI。 此外，請設定要求的權限。

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

### <a name="configure-the-web-api"></a>設定 Web API

1. 開啟 **TaskService** 專案中的 **Web.config**。
1. 設定 API 以使用您的租用戶。

    ```csharp
    <add key="ida:AadInstance" value="https://<Your tenant name>.b2clogin.com/{0}/{1}/v2.0/.well-known/openid-configuration" />
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

1. 將用戶端識別碼設定為已註冊 Web API 應用程式 (webapi1)  的應用程式識別碼。

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

1. 使用註冊和登入使用者流程的名稱 B2C_1_signupsignin1  來更新使用者流程設定。

    ```csharp
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_signupsignin1" />
    ```

1. 進行範圍設定以符合您在入口網站中建立的項目。

    ```csharp
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

## <a name="run-the-sample"></a>執行範例

您必須同時執行 **TaskWebApp** 和 **TaskService** 專案。

1. 在方案總管中，以滑鼠右鍵按一下方案，然後選取 [設定啟始專案]  。
1. 選取 [多個啟始專案]  。
1. 將兩個專案的 [動作]  變更為 [啟動]  。
1. 按一下 [確定]  以儲存組態。
1. 按 **F5** 鍵執行兩個應用程式。 每個應用程式會在自己的瀏覽器視窗中啟。
    * `https://localhost:44316/`是 Web 應用程式。
    * `https://localhost:44332/` 為 Web API。

1. 在 Web 應用程式中，選取 [註冊 / 登入]  ，以登入 Web 應用程式。 使用您先前建立的帳戶。
1. 登入之後，選取 [待辦事項清單]  並建立待辦事項清單項目。

在您建立待辦事項清單項目時，Web 應用程式會對 Web API 提出要求，以產生待辦事項清單項目。 您受保護的 Web 應用程式會呼叫由 Azure AD B2C 保護的 Web API。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 新增 Web API 應用程式
> * 設定 Web API 的範圍
> * 授與 Web API 的權限
> * 設定範例以使用應用程式

> [!div class="nextstepaction"]
> [教學課程：在 Azure Active Directory B2C 中將識別提供者新增至您的應用程式](tutorial-add-identity-providers.md)
