---
title: 註冊可登入使用者的 web 應用程式-Microsoft 身分識別平臺 |Azure
description: 瞭解如何註冊可登入使用者的 web 應用程式
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7d2eb5356b1abc54508fd6bf8d35fd9fc39d02ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80881574"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>登入使用者的 Web 應用程式：應用程式註冊

本文說明登入使用者之 web 應用程式的應用程式註冊細節。

若要註冊您的應用程式，您可以使用：

- [Web 應用程式快速入門](#register-an-app-by-using-the-quickstarts)。 除了是建立應用程式的絕佳第一個體驗，Azure 入口網站中的快速入門包含名為 [**為我進行這項變更**] 的按鈕。 您可以使用此按鈕來設定所需的屬性，即使是現有的應用程式也是如此。 您必須將這些屬性的值調整為您自己的案例。 特別是，您應用程式的 Web API URL 可能會與建議的預設值不同，這也會影響登出 URI。
- 要[手動註冊應用程式](#register-an-app-by-using-the-azure-portal)的 Azure 入口網站。
- PowerShell 和命令列工具。

## <a name="register-an-app-by-using-the-quickstarts"></a>使用快速入門註冊應用程式

您可以使用這些連結來啟動建立 web 應用程式：

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>使用 Azure 入口網站註冊應用程式

> [!NOTE]
> 要使用的入口網站會根據您的應用程式是在 Microsoft Azure 公用雲端或全國或主權雲端執行而有所不同。 如需詳細資訊，請參閱[國家/地區](./authentication-national-cloud.md#app-registration-endpoints)雲端。


1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。 或者，登入國家雲端的選擇 Azure 入口網站。
1. 如果您的帳戶可讓您存取多個租使用者，請在右上角選取您的帳戶。 然後，將您的入口網站會話設定為所需的 Azure Active Directory （Azure AD）租使用者。
1. 在左窗格中選取 [ **Azure Active Directory** ] 服務，然後選取 [**應用程式註冊** > **新增註冊**]。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. 當 [註冊應用程式]  頁面出現時，輸入您應用程式的註冊資訊：
   1. 為您的應用程式選擇支援的帳戶類型。 （請參閱[支援的帳戶類型](./v2-supported-account-types.md)。）
   1. 在 [名稱]**** 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱。 例如，輸入**AspNetCore-WebApp**。
   1. 針對 [重新**導向 URI**]，新增應用程式類型和 URI 目的地，以在成功驗證後接受傳回的權杖回應。 例如，輸入**https://localhost:44321**。 接著，選取 [註冊] ****。
1. 選取 [驗證]  功能表，然後新增下列資訊：
   1. 針對 [**回復 URL**] **https://localhost:44321/signin-oidc** ，新增類型為 [ **Web**]。
   1. 在 [ **Advanced settings** ] 區段中，將 [ **https://localhost:44321/signout-oidc****登出 URL** ] 設定為。
   1. 在 [隱含授與]**** 底下，選取 [識別碼權杖]****。
   1. 選取 [儲存]  。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. 當 [註冊應用程式]  頁面出現時，輸入您應用程式的註冊資訊：
   1. 為您的應用程式選擇支援的帳戶類型。 （請參閱[支援的帳戶類型](./v2-supported-account-types.md)。）
   1. 在 [名稱]**** 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱。 例如，輸入**MailApp-openidconnect-v2**。
   1. 在 [重新**導向 URI （選擇性）** ] 區段中，選取下拉式方塊中的 [ **Web** ]，然後**https://localhost:44326/** 輸入下列重新導向 URI：。
1. 選取 [註冊]  以建立應用程式。
1. 選取 [**驗證**] 功能表。
1. 在 [**高級設定** | ] [**隱含授**與] 區段中，選取 [**識別碼權杖**]。 此範例需要啟用[隱含授與流程](v2-oauth2-implicit-grant-flow.md)，才能登入使用者。
1. 選取 [儲存]  。

# <a name="java"></a>[Java](#tab/java)

1. 當 [**註冊應用程式] 頁面**出現時，輸入應用程式的顯示名稱。 例如，輸入**java-webapp**。
1. 選取**任何組織目錄中的帳戶和個人 Microsoft 帳戶（例如 Skype、Xbox、Outlook.com）**，然後選取 [ **Web 應用程式/API** ] 作為 [**應用程式類型**]。
1. 選取 [**註冊**] 以註冊應用程式。
1. 在左側功能表上，選取 [**驗證**]。 在 [重新**導向 uri**] 底下，選取 [ **Web**]。

1. 輸入兩個重新導向 Uri：一個用於登入頁面，另一個用於圖形頁面。 針對這兩個，使用相同的主機和埠號碼，後面接著 **/msal4jsample/secure/aad**作為登入頁面，然後按 [ **msal4jsample/graph/me]** 作為 [使用者資訊] 頁面。

   根據預設，此範例會使用：

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  然後選取 [**儲存**]。

1. 從功能表中選取 [**憑證 & 密碼**]。
1. 在 [**用戶端密碼**] 區段中，選取 [**新增用戶端密碼**]，然後：

   1. 輸入 [金鑰描述]。
   1. 選取**1 年**的金鑰持續時間。
   1. 選取 [新增]  。
   1. 當金鑰值出現時，請複製它以供稍後查看。 此值將不會再次顯示，也不會透過任何其他方式來抓取。

# <a name="python"></a>[Python](#tab/python)

1. 當 [註冊應用程式]  頁面出現時，輸入您應用程式的註冊資訊：
   1. 在 [名稱]**** 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱。 例如，輸入**python-webapp**。
   1. 將**支援的帳戶類型**變更為**任何組織目錄中的帳戶和個人 Microsoft 帳戶（例如 Skype、Xbox、Outlook.com）**。
   1. 在 [重新**導向 URI （選擇性）** ] 區段中，選取下拉式方塊中的 [ **Web** ]，然後**http://localhost:5000/getAToken**輸入下列重新導向 URI：。
1. 選取 [註冊]  以建立應用程式。
1. 在應用程式的 [概觀]  頁面上，尋找 [應用程式 (用戶端) 識別碼]  值並將它記下供稍後使用。 您必須用此識別碼來設定此專案的 Visual Studio 組態檔。
1. 在左側功能表上，選取 [**憑證 & 密碼**]。
1. 在 [**用戶端密碼**] 區段中，選取 [**新增用戶端密碼**]，然後：

   1. 輸入 [金鑰描述]。
   1. 選取 [1 年]  作為 [金鑰持續時間]。
   1. 選取 [新增]  。
   1. 當金鑰值出現時，複製它。 稍後您將會用到此資訊。
---

## <a name="register-an-app-by-using-powershell"></a>使用 PowerShell 註冊應用程式

> [!NOTE]
> 目前，Azure AD PowerShell 只會建立具有下列受支援帳戶類型的應用程式：
>
> - MyOrg （僅限此組織目錄中的帳戶）
> - AnyOrg （任何組織目錄中的帳戶）
>
> 您可以建立應用程式，以使用其個人 Microsoft 帳戶（例如 Skype、Xbox 或 Outlook.com）來登入使用者。 首先，建立多租使用者應用程式。 支援的帳戶類型是任何組織目錄中的帳戶。 然後，從 Azure 入口網站`signInAudience`變更應用程式資訊清單中的屬性。 如需詳細資訊，請參閱 ASP.NET Core 教學課程中的[步驟 1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) 。 您可以將此步驟一般化為任何語言的 web 應用程式。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式的程式碼設定](scenario-web-app-sign-user-app-configuration.md)
