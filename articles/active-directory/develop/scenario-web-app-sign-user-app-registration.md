---
title: 註冊可登入使用者的 web 應用程式-Microsoft 身分識別平臺 |蔚藍
description: 瞭解如何註冊可登入使用者的 web 應用程式
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 29f5d2960a678204387b2bd1dfd6d4acdc4f9c3d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442509"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>登入使用者的 Web 應用程式：應用程式註冊

本文說明適用于登入使用者的 web 應用程式的應用程式註冊細節。

若要註冊您的應用程式，您可以使用：

- [Web 應用程式快速入門](#register-an-app-by-using-the-quickstarts)。 除了建立應用程式的絕佳首次體驗之外，Azure 入口網站中的快速入門還包含名為 [ **為我進行這項變更** ] 的按鈕。 您可以使用此按鈕來設定所需的屬性，即使是針對現有的應用程式。 您必須將這些屬性的值調整為您自己的案例。 尤其是，您應用程式的 web API URL 可能會與建議的預設值不同，這也會影響登出 URI。
- Azure 入口網站 [手動註冊您的應用程式](#register-an-app-by-using-the-azure-portal)。
- PowerShell 和命令列工具。

## <a name="register-an-app-by-using-the-quickstarts"></a>使用快速入門註冊應用程式

您可以使用下列連結來啟動 web 應用程式的建立：

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>使用 Azure 入口網站註冊應用程式

> [!NOTE]
> 使用的入口網站會根據您的應用程式是否在 Microsoft Azure 公用雲端或在全國或主權雲端中執行而有所不同。 如需詳細資訊，請參閱 [國家](./authentication-national-cloud.md#app-registration-endpoints)雲端。


1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。 或者，登入國家雲端的 [選擇 Azure 入口網站](./authentication-national-cloud.md#app-registration-endpoints) 。
2. 如果您的帳戶可讓您存取一個以上的租使用者，請在右上角選取您的帳戶。 然後，將您的入口網站會話設定為所需的 Azure Active Directory (Azure AD) 租使用者。
3. 在左窗格中，選取 **Azure Active Directory** 服務，然後選取 [ **應用程式註冊**  >  **新增註冊** ]。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. 當 [註冊應用程式] 頁面出現時，輸入您應用程式的註冊資訊：
   1. 為您的應用程式選擇支援的帳戶類型。  (查看 [支援的帳戶類型](./v2-supported-account-types.md)。 ) 
   1. 在 [名稱] 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱。 例如，輸入 **AspNetCore-WebApp** 。
   1. 在 [重新 **導向 URI** ] 中，新增應用程式的類型，以及在成功驗證之後將接受傳回之權杖回應的 URI 目的地。 例如，輸入 **https://localhost:44321** 。 接著，選取 [註冊]  。
   ![螢幕擷取畫面顯示 [註冊應用程式] 頁面，您可以在其中選取 [註冊]。](media/scenario-webapp/scenario-webapp-app-registration-1.png)
1. 選取 [驗證] 功能表，然後新增下列資訊：
   1. 針對 [ **回復 URL** ]，新增 [ **https://localhost:44321/signin-oidc** **Web** ] 類型。
   1. 在 [進階設定] 區段中，將 [登出 URL] 設定為 **https://localhost:44321/signout-oidc** 。
   1. 在 [隱含授與] 底下，選取 [識別碼權杖]。
   1. 選取 [儲存]。
  ![螢幕擷取畫面顯示驗證選項，您可以在其中進行變更描述。](media/scenario-webapp/scenario-webapp-app-registration-2.png)
 
# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. 當 [註冊應用程式] 頁面出現時，輸入您應用程式的註冊資訊：
   1. 為您的應用程式選擇支援的帳戶類型。  (查看 [支援的帳戶類型](./v2-supported-account-types.md)。 ) 
   1. 在 [名稱] 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱。 例如，輸入 **MailApp-openidconnect-v2** 。
   1. 在 [重新 **導向 URI] (選擇性)** 區段中，選取下拉式方塊中的 [ **Web** ]，然後輸入下列重新導向 URI： **https://localhost:44326/** 。
1. 選取 [註冊] 以建立應用程式。
1. 選取 [ **驗證** ] 功能表。
1. 在 [ **Advanced settings**  |  **隱含授** 與] 區段中，選取 [ **識別碼權杖** ]。 此範例需要啟用 [隱含授與流程](v2-oauth2-implicit-grant-flow.md) 才能登入使用者。
1. 選取 [儲存]。

# <a name="java"></a>[Java](#tab/java)

1. 當 [ **註冊應用程式] 頁面** 出現時，輸入應用程式的顯示名稱。 例如，輸入 **java-webapp** 。
1. 選取 **任何組織目錄中的帳戶及個人的 Microsoft 帳戶 (例如 Skype、Xbox、Outlook.com)** ，然後選取 [ **應用程式類型** 的 **Web 應用程式/API** ]。
1. 選取 [ **註冊** ] 以註冊應用程式。
1. 在左側功能表中，選取 [ **驗證** ]。 在 [重新 **導向 uri** ] 下，選取 [ **Web** ]。

1. 輸入兩個重新導向 Uri：一個用於登入頁面，另一個用於圖形頁面。 針對這兩種方式，使用相同的主機和埠號碼，接著 **/msal4jsample/secure/aad** 登入頁面，並針對 [使用者資訊] 頁面輸入 **msal4jsample/graph/me** 。

   根據預設，此範例會使用：

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  然後選取 [儲存]。

1. 從功能表選取 [ **憑證 & 秘密** ]。
1. 在 [ **用戶端密碼** ] 區段中，選取 [ **新增用戶端密碼** ]，然後：

   1. 輸入金鑰描述。
   1. 選取 **1 年** 的金鑰持續時間。
   1. 選取 [新增]  。
   1. 出現金鑰值時，將其複製以供稍後進行。 此值將不會再次顯示，也無法透過任何其他方式來抓取。

# <a name="python"></a>[Python](#tab/python)

1. 當 [註冊應用程式] 頁面出現時，輸入您應用程式的註冊資訊：
   1. 在 [名稱] 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱。 例如，輸入 **python-webapp** 。
   1. 將 **支援的帳戶類型** 變更為 **任何組織目錄中的帳戶及個人的 Microsoft 帳戶， (例如 Skype、Xbox、Outlook.com)** 。
   1. 在 [重新 **導向 URI] (選擇性)** 區段中，選取下拉式方塊中的 [ **Web** ]，然後輸入下列重新導向 URI： **http://localhost:5000/getAToken** 。
1. 選取 [註冊] 以建立應用程式。
1. 在應用程式的 [概觀] 頁面上，尋找 [應用程式 (用戶端) 識別碼] 值並將它記下供稍後使用。 您必須用此識別碼來設定此專案的 Visual Studio 組態檔。
1. 在左側功能表中，選取 [ **憑證 & 秘密** ]。
1. 在 [ **用戶端密碼** ] 區段中，選取 [ **新增用戶端密碼** ]，然後：

   1. 輸入金鑰描述。
   1. 選取 [1 年]  作為 [金鑰持續時間]。
   1. 選取 [新增]  。
   1. 出現金鑰值時，將它複製。 稍後您將會用到此資訊。
---

## <a name="register-an-app-by-using-powershell"></a>使用 PowerShell 註冊應用程式

> [!NOTE]
> 目前，Azure AD PowerShell 會使用下列支援的帳戶類型來建立應用程式：
>
> - 僅在此組織目錄中 MyOrg (帳戶) 
> - AnyOrg 任何組織目錄中的 (帳戶) 
>
> 您可以建立使用其個人 Microsoft 帳戶登入使用者的應用程式 (例如 Skype、Xbox 或 Outlook.com) 。 首先，建立多租使用者應用程式。 支援的帳戶類型是任何組織目錄中的帳戶。 然後，在 [`accessTokenAcceptedVersion`](./reference-app-manifest.md#accesstokenacceptedversion-attribute) **2** [`signInAudience`](./reference-app-manifest.md#signinaudience-attribute) `AzureADandPersonalMicrosoftAccount` [應用程式資訊清單](./reference-app-manifest.md)中，將 Azure 入口網站的屬性變更為2，並將屬性變更為。 如需詳細資訊，請參閱 ASP.NET Core 教學課程中的 [步驟 1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) 。 您可以用任何語言將此步驟一般化至 web 應用程式。

## <a name="next-steps"></a>後續步驟

請移至本案例中的下一篇文章，以 [應用程式的程式碼](scenario-web-app-sign-user-app-configuration.md)設定。