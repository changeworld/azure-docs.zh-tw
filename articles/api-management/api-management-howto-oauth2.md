---
title: 在 API 管理中使用 OAuth 2.0 授權開發人員帳戶
titleSuffix: Azure API Management
description: 了解如何在 API 管理中使用 OAuth 2.0 來授權給使用者。 OAuth 2.0 可保護 API，讓使用者只能存取其所擁有的資源。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 4a0e8c6eb87b053c0e3ee524c60435dc45d81ff3
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87902252"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>如何在 Azure API 管理中使用 OAuth 2.0 授權開發人員帳戶

許多 API 都支援使用 [OAuth 2.0](https://oauth.net/2/) 來保護 API，並確保只有有效的使用者才能存取，而且他們只能存取獲授權的資源。 為了搭配使用 Azure API 管理的互動式開發人員主控台與這類 API，服務可讓您設定服務執行個體來使用已啟用 OAuth 2.0 的 API。

> [!IMPORTANT]
> 新開發人員入口網站的互動式主控台尚未提供 OAuth 2.0 授權。

## <a name="prerequisites"></a><a name="prerequisites"> </a>必要條件

本指南將示範如何設定 API 管理服務執行個體，以便使用開發人員帳戶適用的 OAuth 2.0 授權，但並未示範如何設定 OAuth 2.0 提供者。 儘管步驟相似，且用來在 API 管理服務執行個體中設定 OAuth 2.0 所需的資訊也相同，但每個 OAuth 2.0 提供者的組態並不相同。 本主題演示的範例將 Azure Active Directory 當做 OAuth 2.0 提供者。

> [!NOTE]
> 如需使用 Azure Active Directory 設定 OAuth 2.0 的詳細資訊，請參閱 [WebApp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet] (英文) 範例。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-an-oauth-20-authorization-server-in-api-management"></a><a name="step1"> </a>在 API 管理中設定 OAuth 2.0 授權伺服器

> [!NOTE]
> 如果您尚未建立 API 管理服務執行個體，請參閱[建立 API 管理服務執行個體][Create an API Management service instance]。

1. 按一下左側功能表中的 [OAuth 2.0] 索引標籤，然後按一下 [+ 新增]****。

    ![OAuth 2.0 功能表](./media/api-management-howto-oauth2/oauth-01.png)

2. 在 [名稱]**** 和 [說明]**** 欄位中輸入名稱和選擇性的說明。

    > [!NOTE]
    > 這些欄位可用來在目前的 API 管理服務執行個體中識別 OAuth 2.0 授權伺服器，且欄位的值並非來自 OAuth 2.0 伺服器。

3. 輸入 [用戶端註冊頁面 URL]****。 此頁面可供使用者建立及管理帳戶，並且會因為使用的 OAuth 2.0 提供者不同而有所差異。 [**用戶端註冊頁面 URL** ] 指向的頁面，可讓使用者用來建立及設定自己的 OAuth 2.0 提供者帳戶，以支援帳戶的使用者管理，例如 `https://contoso.com/login` 。 即使 OAuth 2.0 提供者支援這項功能，有些組織還是未設定或未使用這項功能。 如果您的 OAuth 2.0 提供者未設定使用者帳戶管理，請在這裡輸入預留位置 URL (例如您公司的 URL) 或 `https://placeholder.contoso.com` 這類 URL。

    ![OAuth 2.0 新伺服器](./media/api-management-howto-oauth2/oauth-02.png)

4. 表單的下一個區段含有 [授權授與類型]****、[授權端點 URL]**** 及 [授權要求方法]**** 等設定。

    勾選需要的類型以指定 [授權授與類型]****。 **** 。

    輸入 [Authorization endpoint URL] ****。 針對 Azure Active Directory，此 URL 將與下列 URL 類似，其中 `<tenant_id>` 會以您的 Azure AD 租使用者識別碼取代。

    `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    [授權要求方法]**** 能指定將授權要求傳送到 OAuth 2.0 伺服器的方法。 預設會選取 **GET**。

5. 接著，必須指定 [權杖端點 URL]****、[用戶端驗證方法]****、[存取權杖傳送方法]**** 及 [預設範圍]**** 的地方。

    ![OAuth 2.0 新伺服器](./media/api-management-howto-oauth2/oauth-03.png)

    對於 Azure Active Directory OAuth 2.0 伺服器，[權杖端點 URL]**** 將具有以下格式；其中 `<TenantID>` 的格式為 `yourapp.onmicrosoft.com`。

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    [用戶端驗證方法]**** 的預設設定是 [基本]****，而 [存取權杖傳送方法]**** 則是 [授權標頭]****。 這些值和 [預設範圍] **** 均是在此表單區段設定的。

6. [用戶端認證]**** 區段含有 [用戶端識別碼]**** 和 [用戶端密碼]****，這兩個項目可在 OAuth 2.0 伺服器的建立和組態程序中取得。 指定 [用戶端識別碼]**** 和 [用戶端密碼]**** 後，系統便會產生 [授權碼]**** 的 **redirect_uri**。 此 URI 可用來設定 OAuth 2.0 伺服器組態中的回覆 URL。

    在新的開發人員入口網站中，URI 尾碼的格式如下：

    - `/signin-oauth/code/callback/{authServerName}`授權碼授與流程
    - `/signin-oauth/implicit/callback`針對隱含授與流程

    ![OAuth 2.0 新伺服器](./media/api-management-howto-oauth2/oauth-04.png)

    如果將 [授權授與類型]**** 設定為 [資源擁有者密碼]****，您便需要使用 [資源擁有者密碼認證]**** 區段來指定認證，若不想這麼做，則可以將授與類型保持空白。

    完成表單後，按一下 [建立] **** 以儲存 API 管理 OAuth 2.0 授權伺服器組態。 儲存伺服器組態後，您便可以設定 API 以使用此組態，如下一個小節所述。

## <a name="configure-an-api-to-use-oauth-20-user-authorization"></a><a name="step2"> </a>設定 API 以使用 OAuth 2.0 使用者授權

1. 從左側 [API 管理]**** 功能表按一下 [API]****。

    ![OAuth 2.0 API](./media/api-management-howto-oauth2/oauth-05.png)

2. 按一下所需的 API 名稱，然後按一下 [設定]****。 捲動至 [安全性]**** 區段，然後核取 [OAuth 2.0]**** 方塊。

    ![OAuth 2.0 設定](./media/api-management-howto-oauth2/oauth-06.png)

3. 從下拉式清單選取需要的 [授權伺服器]****，然後按一下 [儲存]****。

    ![OAuth 2.0 設定](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="legacy-developer-portal---test-the-oauth-20-user-authorization"></a><a name="step3"> </a>舊版開發人員入口網站-測試 OAuth 2.0 使用者授權

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

設定好 OAuth 2.0 授權伺服器並將 API 設定為使用該伺服器後，您可以前往開發人員入口網站並呼叫 API 來進行測試。 從您的 Azure API 管理實例 [**總覽**] 頁面中，按一下頂端功能表中的 [**開發人員入口網站 (舊版) ** 。

在上方功能表中按一下 [API]****，然後選取 [Echo API]****。

![Echo API][api-management-apis-echo-api]

> [!NOTE]
> 如果您的帳戶只設定或只看見一個 API，按一下 API 將帶您直接前往該 API 的作業。

選取 [GET 資源]**** 作業、按一下 [開啟主控台]****，然後從下拉式功能表選取 [授權碼]****。

![Open console][api-management-open-console]

選取 [授權碼] **** 時，系統會顯示含有 OAuth 2.0 提供者之登入表單的快顯視窗。 在此範例中，登入表單是由 Azure Active Directory 提供。

> [!NOTE]
> 如果已停用快顯視窗，瀏覽器會提示您加以啟用。 啟用後，請再次選取 [授權碼] **** ，系統就會顯示登入表單。

![登入][api-management-oauth2-signin]

登入後，系統會將授權要求的 `Authorization : Bearer` 標頭填入 [要求標頭]****。

![Request header token][api-management-request-header-token]

此時，您可以針對剩餘的參數設定需要的值，然後再提交要求。

## <a name="next-steps"></a>後續步驟

如需使用 OAuth 2.0 和 API 管理的詳細資訊，請參閱下列影片以及隨附的 [文章](api-management-howto-protect-backend-with-aad.md)。

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps
