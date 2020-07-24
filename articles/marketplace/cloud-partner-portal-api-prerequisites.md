---
title: API 必要條件-Azure Marketplace
description: 使用 Cloud Partner 入口網站 Api 的必要條件。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: dsindona
ms.author: dsindona
ms.date: 07/14/2020
ms.openlocfilehash: b672afcd8f52d8293b1bb81e30097062e2ee49c3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87053589"
---
<a name="api-prerequisites"></a>API 先決條件
================

> [!NOTE]
> Cloud Partner 入口網站 Api 會與整合，並會繼續在合作夥伴中心運作。 轉換會引進微小的變更。 請參閱[CLOUD PARTNER 入口網站 API 參考](./cloud-partner-portal-api-overview.md)中所列的變更，以確保您的程式碼在轉換至合作夥伴中心後繼續運作。 只有在轉換至合作夥伴中心之前已整合的現有產品，才應該使用 CPP Api;新產品應使用合作夥伴中心提交 Api。

有兩個您需要使用 Cloud Partner 入口網站 API 的必要程式設計資產：服務主體和 Azure Active Directory (Azure AD) 存取權杖。


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>在 Azure Active Directory 租用戶中建立服務主體
----------------------------------------------------------------

首先，您必須在 Azure AD 租用戶中建立一個服務主體。 此租用戶將會在 Cloud Partner 入口網站中獲指派一組自己的權限。 您的程式碼將會使用此租用戶 (而非個人認證) 呼叫 API。  如需建立服務主體的完整說明，請參閱[如何：使用入口網站建立可存取資源的 Azure AD 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。


<a name="add-the-service-principal-to-your-account"></a>將服務主體新增至您的帳戶
-----------------------------------------

既然您已經在租用戶中建立服務主體，您可以將其當作使用者，新增至 Cloud Partner 入口網站帳戶。 服務主體就像使用者一樣，可以是入口網站的擁有者或參與者。

使用下列步驟新增服務主體：

1. 登入 Cloud Partner 入口網站。 
2. 按一下功能表列左側的 [使用者]****，然後選擇 [新增使用者]****。

   ![將使用者新增至入口網站](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. 從 [類型]**** 下拉式清單中，選取 [服務主體]****，並新增下列詳細資料：

-   服務主體的**易記名稱**，例如 `spAccount`。
-   **應用程式識別碼**。 若要尋找此識別碼，請移至[Azure 入口網站](https://portal.azure.com)，按一下 [ **Azure Active Directory**，選擇 [**應用程式註冊**]，然後按一下您的應用程式。
-   Azure AD 租用戶的**租用戶識別碼**也稱為**目錄識別碼**。 您可以在 [Azure 入口網站](https://portal.azure.com) [Azure Active Directory] 頁面中 [屬性]**** 底下找到此識別碼。
-   服務主體物件的**物件識別碼**。 您可以從 Azure 入口網站中取得此識別碼。 移至 [Azure Active Directory]****，選擇 [應用程式註冊]****，按一下您的應用程式，然後按一下 [本機目錄中受控的應用程式]**** 底下的應用程式名稱。 接著，移至 [屬性]**** 頁面，尋找物件識別碼。 請確定您不會抓取初始位於您的應用程式，而是在受管理的應用程式中的物件識別碼的物件識別碼。
-   與帳戶相關聯的**角色**，將用於 RBAC。

     ![將受控應用程式新增至入口網站](./media/cloud-partner-portal-api-prerequisites/managed-app.png)

1. 按一下 [新增]****，將服務主體新增至您的帳戶。

   ![新增服務主體](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>取得 Azure AD 存取權杖
----------------------------

Cloud Partner 入口網站 API 會在驗證期間使用下列資產和通訊協定：

- JSON Web 權杖 (JWT) 持有人權杖，可要求資源的存取權
- [OpenID Connect](https://openid.net/connect/) (OIDC) 通訊協定，可驗證身分識別
- [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md)，作為身分識別授權單位

使用程式設計方式取得 JWT 權杖有兩種主要方法：

- 使用適用於 .NET 的 Microsoft Authentication Library ([MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet))。  這種較高層級的方法建議供 .NET 開發人員使用。 
- 向 Azure AD oauth **權杖**端點提出 **HTTP POST** 要求，採用下列格式：

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

現在，您可以傳遞此權杖作為 API 要求的授權標頭一部分。

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> 對於此參考中的所有 API，授權標頭一律都假設為已傳遞，因此未明確提及。

如果您在要求中遇到驗證錯誤，請參閱[針對驗證錯誤進行疑難排解](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)。
