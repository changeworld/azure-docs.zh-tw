---
title: 註冊在使用者中簽名的 Web 應用 - 微軟身份平臺 |蔚藍
description: 瞭解如何註冊在使用者中簽名的 Web 應用
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f2513b1fee022199f040ba0dcf83110c0b3b1365
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701564"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>登錄使用者的 Web 應用：應用註冊

本文介紹了在使用者中簽名的 Web 應用的應用註冊細節。

要註冊應用程式，可以使用：

- [網路應用程式快速啟動](#register-an-app-by-using-the-quickstarts)。 除了創建應用程式的第一次體驗之外，Azure 門戶中的快速啟動還包含一個名為 **"為我進行此更改"的**按鈕。 您可以使用此按鈕設置所需的屬性，即使對於現有應用也是如此。 您需要根據您自己的情況調整這些屬性的值。 特別是，你的應用的 Web API URL 可能與建議的預設值不同，這也會影響登出 URI。
- 要[手動註冊應用程式的](#register-an-app-by-using-the-azure-portal)Azure 門戶。
- 電源外殼和命令列工具。

## <a name="register-an-app-by-using-the-quickstarts"></a>使用快速入門註冊應用

您可以使用以下連結引導 Web 應用程式的創建：

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>使用 Azure 門戶註冊應用

> [!NOTE]
> 要使用的門戶因應用程式是在 Microsoft Azure 公共雲中運行還是在國家雲或主權雲中運行而異。 有關詳細資訊，請參閱[國家雲](./authentication-national-cloud.md#app-registration-endpoints)。


1. 使用工作或學校帳戶或個人 Microsoft 帳戶登錄到[Azure 門戶](https://portal.azure.com)。 或者，登錄到國家雲的首選 Azure 門戶。
1. 如果您的帳戶允許您訪問多個租戶，請在右上角選擇您的帳戶。 然後，將門戶會話設置為所需的 Azure 活動目錄 （Azure AD） 租戶。
1. 在左側窗格中，選擇**Azure 活動目錄**服務，然後選擇**應用註冊** > **"新註冊**"。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. 當 **"註冊應用程式**"頁出現時，輸入應用程式的註冊資訊：
   1. 為應用程式選擇受支援的帳戶類型。 （請參閱[受支援的帳戶類型](./v2-supported-account-types.md)。
   1. 在 [名稱]**** 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱。 例如，輸入**AspNetCore-WebApp**。
   1. 對於**重定向 URI**，添加應用程式類型和 URI 目標，這些目標將在成功身份驗證後接受返回的權杖回應。 例如，輸入**https://localhost:44321**。 接著，選取 [註冊] ****。
1. 選取 [驗證]**** 功能表，然後新增下列資訊：
   1. 對於**回復 URL，** 添加**https://localhost:44321/signin-oidc**類型**Web**。
   1. 在 [進階設定]**** 區段中，將 [登出 URL]**** 設定為 **https://localhost:44321/signout-oidc**。
   1. 在 [隱含授與]**** 底下，選取 [識別碼權杖]****。
   1. 選取 [儲存]****。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. 當 **"註冊應用程式"頁**出現時，輸入應用程式的註冊資訊：
   1. 為應用程式選擇受支援的帳戶類型。 （請參閱[受支援的帳戶類型](./v2-supported-account-types.md)。
   1. 在 [名稱]**** 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱。 例如，輸入**郵件應用程式-打開的id連接-v2。**
   1. 在**重定向 URI（可選）** 部分中，在下拉式列示方塊中選擇**Web**並輸入以下重定向 URI： **https://localhost:44326/**。
1. 選取 [註冊]**** 以建立應用程式。
1. 選擇 **"身份驗證"** 功能表。
1. 在 **"高級設置** | **"隱式授予**部分中，選擇**ID 權杖**。 此示例要求啟用[隱式授予流](v2-oauth2-implicit-grant-flow.md)才能登錄使用者。
1. 選取 [儲存]****。

# <a name="java"></a>[JAVA](#tab/java)

1. 當 **"註冊應用程式"頁**出現時，輸入應用程式的顯示名稱。 例如，輸入**java-webapp**。
1. 選擇**任何組織目錄和個人 Microsoft 帳戶中的帳戶（例如 Skype、Xbox、Outlook.com），** 然後為**應用程式類型**選擇**Web 應用/API** 。
1. 選擇 **"註冊"** 以註冊應用程式。
1. 在左側功能表上，選擇 **"身份驗證**"。 在**重定向 URI**下，選擇**Web**。

1. 輸入兩個重定向 URI：一個用於登錄頁，一個用於圖形頁。 對於這兩個，使用相同的主機和埠號，後跟 **/msal4jsample/安全/aad**的登錄頁和**msal4jsample/graph/me**的使用者資訊頁。

   預設情況下，該示例使用：

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  然後，選擇 **"保存**"。

1. 從功能表**中選擇證書&機密**。
1. 在 **"用戶端機密**"部分中，選擇 **"新用戶端機密**"，然後：

   1. 輸入關鍵說明。
   1. 選擇 1**年內**的關鍵持續時間。
   1. 選取 [加入]****。
   1. 當出現鍵值時，請複製它供以後使用。 此值將不會再次顯示，也不會通過任何其他方式檢索。

# <a name="python"></a>[Python](#tab/python)

1. 當 **"註冊應用程式"頁**出現時，輸入應用程式的註冊資訊：
   1. 在 [名稱]**** 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱。 例如，輸入**python webapp**。
   1. 將**受支援的帳戶類型**更改為**任何組織目錄和個人 Microsoft 帳戶中的帳戶（例如 Skype、Xbox、Outlook.com）。**
   1. 在**重定向 URI（可選）** 部分中，在下拉式列示方塊中選擇**Web**並輸入以下重定向 URI： **http://localhost:5000/getAToken**。
1. 選取 [註冊]**** 以建立應用程式。
1. 在應用程式的 [概觀]**** 頁面上，尋找 [應用程式 (用戶端) 識別碼]**** 值並將它記下供稍後使用。 您必須用此識別碼來設定此專案的 Visual Studio 組態檔。
1. 在左側功能表中，選擇 **"證書&機密**"。
1. 在 **"用戶端機密**"部分中，選擇 **"新用戶端機密**"，然後：

   1. 輸入關鍵說明。
   1. 選取 [1 年]**** 作為 [金鑰持續時間]。
   1. 選取 [加入]****。
   1. 當出現鍵值時，複製它。 稍後您將會用到此資訊。
---

## <a name="register-an-app-by-using-powershell"></a>使用 PowerShell 註冊應用

> [!NOTE]
> 目前，Azure AD PowerShell 創建的應用程式僅具有以下受支援的帳戶類型：
>
> - MyOrg（僅限此組織目錄中的帳戶）
> - 任何組織（任何組織目錄中的帳戶）
>
> 您可以創建使用其個人 Microsoft 帳戶（例如，Skype、Xbox 或Outlook.com）登錄使用者的應用程式。 首先，創建一個多租戶應用程式。 支援的帳戶類型是任何組織目錄中的帳戶。 然後，從`signInAudience`Azure 門戶更改應用程式清單中的屬性。 有關詳細資訊，請參閱 ASP.NET 核心教程中[的步驟 1.3。](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) 您可以將此步驟概括為任何語言的 Web 應用。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用的代碼配置](scenario-web-app-sign-user-app-configuration.md)
