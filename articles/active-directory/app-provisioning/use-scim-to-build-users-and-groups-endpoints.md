---
title: 建立 SCIM 端點，以從 Azure AD 將使用者布建至應用程式
description: 跨網域身分識別管理系統 (SCIM) 可將自動使用者佈建標準化。 了解如何開發 SCIM 端點、整合您的 SCIM API 與 Azure Active Directory，並開始自動將使用者和群組佈建至您的雲端應用程式。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ae36af981b113d44ac1b8fd45a1d084760b0294
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900125"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>教學課程：開發範例 SCIM 端點

沒有人想要從頭開始建立新的端點，因此我們建立了一些 [參考程式碼](https://aka.ms/scimreferencecode) ，讓您開始使用 [SCIM](https://aka.ms/scimoverview)。 本教學課程說明如何在 Azure 中部署 SCIM 的參考程式碼，並使用 postman 或整合 Azure AD 的 SCIM 用戶端來測試它。 您可以在5分鐘內讓您的 SCIM 端點啟動並執行，而不需任何程式碼。 本教學課程適用于想要開始使用 SCIM 的開發人員，或想要測試 SICM 端點的其他人。 

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 下載參考程式碼
> * 在 Azure 中部署您的 SCIM 端點
> * 測試您的 SCIM 端點

端點功能包括：

|端點|描述|
|---|---|
|`/User`|對使用者資源執行 CRUD 作業： **建立**、 **更新**、 **刪除**、 **取得**、 **列出**、 **篩選**|
|`/Group`|對群組資源執行 CRUD 作業： **建立**、 **更新**、 **刪除**、 **取得**、 **列出**、 **篩選**|
|`/Schemas`|取得一或多個支援的架構。<br/><br/>每個服務提供者所支援之資源的一組屬性可能會有所不同，例如服務提供者 A 支援「名稱」、「標題」和「電子郵件」，而服務提供者 B 則支援使用者的「名稱」、「標題」和「phoneNumbers」。|
|`/ResourceTypes`|取出支援的資源類型。<br/><br/>每個服務提供者所支援的資源數目和類型可能有所不同，例如服務提供者 A 支援使用者，而服務提供者 B 支援使用者和群組。|
|`/ServiceProviderConfig`|取得服務提供者的 SCIM 設定<br/><br/>每個服務提供者所支援的 SCIM 功能可能會有所不同，例如服務提供者 A 支援修補作業，而服務提供者 B 支援修補作業和架構探索。|

## <a name="download-the-reference-code"></a>下載參考程式碼

要下載的 [參考程式碼](https://github.com/AzureAD/SCIMReferenceCode) 包含下列專案：

- **Microsoft.SystemForCrossDomainIdentityManagement**，這是用來建立和布建 SCIM API 的 .NET Core MVC web API
- **SCIM WebHostSample**，這是 SCIM 端點的實用範例

專案包含下列資料夾和檔案：

|檔案/資料夾|描述|
|-|-|
|**架構** 資料夾| **使用者** 和 **群組** 資源的模型，以及某些抽象類別，例如共用功能的架構化。<br/><br/> [ **屬性** ] 資料夾，其中包含 **使用者** 和 **群組** （例如位址）之複雜屬性的類別定義。|
|**服務** 資料夾 | 包含與查詢和更新資源方式相關之動作的邏輯。<br/><br/> 參考程式碼具有可傳回使用者和群組的服務。<br/><br/>[ **控制器** ] 資料夾包含各種 SCIM 端點。 資源控制器包含可在資源上執行 CRUD 作業的 HTTP 指令動詞 (**GET**、 **POST**、 **PUT**、 **PATCH**、 **DELETE**) 。 控制器會依賴服務來執行動作。|
|**通訊協定** 資料夾|包含根據 SCIM RFC （如：）傳回資源之方式的相關動作邏輯。<br/><ul><li>以清單形式傳回多個資源。</li><li>只傳回以篩選為基礎的特定資源。</li><li>將查詢轉換成單一篩選器的連結清單清單。</li><li>使用與值路徑有關的屬性，將修補程式要求轉換成作業。</li><li>定義可以用來將變更套用至資源物件的作業類型。</li></ul>|
|`Microsoft.SystemForCrossDomainIdentityManagement`| 範例原始程式碼。|
|`Microsoft.SCIM.WebHostSample`| SCIM 程式庫的範例執行。|
|*. .gitignore*|定義認可時要忽略的內容。|
|*CHANGELOG.md*|範例的變更清單。|
|*CONTRIBUTING.md*|參與範例的指導方針。|
|*README.md*|此 **自述** 檔。|
|*許可證*|範例的授權。|

> [!NOTE]
> 此程式碼旨在協助您開始建立 SCIM 端點，並依原樣 **提供。** 包含的參考無法保證主動維護或支援。
>
> 此專案採用了 [Microsoft 開放原始碼管理辦法](https://opensource.microsoft.com/codeofconduct/)。 當您想要協助建立和維護存放庫，並與其他開放原始 [碼貢獻一樣](https://github.com/AzureAD/SCIMReferenceCode/wiki/Contributing-Overview) ，您將會同意 (CLA) 的參與者授權合約。 本合約聲明您擁有並授與使用您投稿的權利，如需詳細資訊，請參閱 [Microsoft 開放原始](https://cla.opensource.microsoft.com)碼。
>
> 如需詳細資訊，請參閱[管理辦法常見問題集](https://opensource.microsoft.com/codeofconduct/faq/)，如有其他問題或意見，請連絡 [opencode@microsoft.com](mailto:opencode@microsoft.com)。

###  <a name="use-multiple-environments"></a>使用多重環境

包含的 SCIM 程式碼會使用 ASP.NET Core 環境來控制其授權以在開發和部署後使用，請參閱 [在 ASP.NET Core 中使用多個環境](https://docs.microsoft.com/aspnet/core/fundamentals/environments?view=aspnetcore-3.1)。

```csharp
private readonly IWebHostEnvironment _env;
...

public void ConfigureServices(IServiceCollection services)
{
    if (_env.IsDevelopment())
    {
        ...
    }
    else
    {
        ...
    }
```

## <a name="deploy-your-scim-endpoint-in-azure"></a>在 Azure 中部署您的 SCIM 端點

此處提供的步驟會使用 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 和 [Azure App 服務](https://docs.microsoft.com/azure/app-service/)，將 SCIM 端點部署至服務。 SCIM 參考程式碼也可以在本機執行、由內部部署伺服器裝載，或部署到其他外部服務。 

### <a name="open-solution-and-deploy-to-azure-app-service"></a>開啟方案並部署至 Azure App Service

1. 移至 GitHub 的 [參考程式碼](https://github.com/AzureAD/SCIMReferenceCode) ，然後選取 [複製] 或 [ **下載**]。

1. 選擇 **在 Desktop 中開啟**，或複製連結、開啟 **Visual Studio**，然後選取 [複製 **或簽出程式碼** ] 輸入複製的連結，並建立本機複本。

1. 在 **Visual Studio** 中，請務必登入可存取主機資源的帳戶。

1. 在 **方案總管** 中，開啟 *SCIM* ，然後以滑鼠右鍵按一下 *SCIM WebHostSample* 檔案。 選取 [發佈]。

    ![雲端發佈](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > 若要在本機執行此方案，請按兩下專案，然後選取 [ **IIS Express** ]，將專案啟動為具有本機主機 URL 的網頁。

1. 選取 [ **建立設定檔** ]，確定已選取 **App Service** 並 **建立新** 的。

    ![雲端發佈2](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. 逐步執行對話方塊選項，然後將應用程式重新命名為您選擇的名稱。 此名稱會用於應用程式和 SCIM 端點 URL。

    ![雲端發佈3](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. 選取要使用的資源群組，然後選擇 [ **發行**]。

1. 流覽至 **Azure App Services** 設定中的應用程式  >  ，然後選取 [**新增應用程式設定****]，** 以新增具有值的 *Token__TokenIssuer* 設定 `https://sts.windows.net/<tenant_id>/` 。 將取代 `<tenant_id>` 為您的 Azure AD tenant_id，如果您想要使用 [POSTMAN](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)測試 SCIM 端點，請同時新增具有值的 *ASPNETCORE_ENVIRONMENT* 設定 `Development` 。 

   ![appservice 設定](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   使用 Azure 入口網站中的企業應用程式測試您的端點時，請選擇將環境保留為 `Development` ，並提供從端點產生的權杖以 `/scim/token` 測試或變更環境， `Production` 並在 [Azure 入口網站](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)的企業應用程式中將 [權杖] 欄位保留空白。 

大功告成！ 現在已發佈您的 SCIM 端點，可讓您使用 Azure App Service URL 來測試 SCIM 端點。

## <a name="test-your-scim-endpoint"></a>測試您的 SCIM 端點

對 SCIM 端點的要求需要授權，而 SCIM 標準會保留多個驗證和授權選項，例如 cookie、基本驗證、TLS 用戶端驗證，或 [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2)中所列的任何方法。

請務必避免不安全的方法（例如使用者名稱/密碼），以改用更安全的方法，例如 OAuth。 Azure AD 支援 (用於資源庫和非資源庫應用程式的長期持有人權杖，) 和 OAuth 授權授與 (應用程式資源庫中發佈的應用程式) 。

> [!NOTE]
> 存放庫中提供的授權方法僅供測試之用。 與 Azure AD 整合時，您可以查看授權指引，請參閱 [規劃 SCIM 端點](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#authorization-for-provisioning-connectors-in-the-application-gallery)的布建。 

開發環境會為生產環境啟用不安全的功能，例如可控制安全性權杖驗證行為的參考程式碼。 權杖驗證程式代碼會設定為使用自我簽署的安全性權杖，而簽署金鑰會儲存在設定檔中，請參閱 *appsettings.Development.js* 檔案中的 **token： IssuerSigningKey** 參數。

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> 藉由將 **GET** 要求傳送至 `/scim/token` 端點，就會使用設定的金鑰來發出權杖，並可作為後續授權的持有人權杖。

預設權杖驗證程式代碼會設定為使用 Azure Active Directory 所簽發的權杖，且需要使用 *appsettings.json* 檔案中的 **token： TokenIssuer** 參數來設定發行租使用者。

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>使用 Postman 來測試端點

部署 SCIM 端點之後，您可以進行測試以確保其符合 SCIM RFC 規範。 這個範例會在 **Postman** 中提供一組測試，以驗證使用者和群組的 CRUD 作業、篩選、群組成員資格的更新，以及停用使用者。

端點位於 `{host}/scim/` 目錄中，而且可以使用標準 HTTP 要求進行互動。 若要修改 `/scim/` 路由，請參閱 **AzureADProvisioningSCIMreference**  >  **ScimReferenceApi**  >  **控制器** 中的 ControllerConstant.cs。

> [!NOTE]
> 您只能使用 HTTP 端點進行本機測試，因為 Azure AD 布建服務需要您的端點支援 HTTPS。

#### <a name="open-postman-and-run-tests"></a>開啟 Postman 並執行測試

1. 下載 [Postman](https://www.getpostman.com/downloads/) 並啟動應用程式。
1. 複製連結 [https://aka.ms/ProvisioningPostman](https://aka.ms/ProvisioningPostman) 並貼到 Postman 中，以匯入測試集合。

    ![postman 集合](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. 使用下列變數來建立測試環境：

   |環境|變數|值|
   |-|-|-|
   |使用 IIS Express 在本機執行專案|||
   ||**伺服器**|`localhost`|
   ||**通訊埠**|`:44359`*(別忘了 **：**)*|
   ||**Api**|`scim`|
   |使用 Kestrel 在本機執行專案|||
   ||**伺服器**|`localhost`|
   ||**通訊埠**|`:5001`*(別忘了 **：**)*|
   ||**Api**|`scim`|
   |在 Azure 中裝載端點|||
   ||**伺服器**|*(輸入您的 SCIM URL)*|
   ||**通訊埠**|*(保留空白)*|
   ||**Api**|`scim`|

1. 使用 Postman 集合中的 **Get Key** ，將 **get** 要求傳送至權杖端點，並取出安全性權杖，以供後續要求儲存在 **權杖** 變數中。 

   ![postman 取得金鑰](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > 若要讓 SCIM 端點安全，您必須先有安全性權杖，然後再進行連線，本教學課程會使用 `{host}/scim/token` 端點來產生自我簽署的權杖。

大功告成！ 您現在可以執行 **Postman** 集合來測試 SCIM 端點功能。

## <a name="next-steps"></a>後續步驟

若要使用用戶端的互通性來開發符合 SCIM 規範的使用者和群組端點，請參閱 [SCIM 用戶端執行](http://www.simplecloud.info/#Implementations2)。

> [!div class="nextstepaction"]
> [教學課程：開發和規劃 SCIM 端點](use-scim-to-provision-users-and-groups.md) 
>  的布建[教學課程：設定資源庫應用程式的](configure-automatic-user-provisioning-portal.md)布建