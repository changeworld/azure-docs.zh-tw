---
title: 如何使用 Azure AD Graph API
description: Azure Active Directory (Azure AD) Graph API 是透過 OData REST API 端點，以程式設計方式存取 Azure AD。 應用程式可以使用 Azure AD 圖形 API 來執行有關目錄資料和物件的建立、讀取、更新及刪除 (CRUD) 作業。
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 701aadcbfe03c82f6a4c341ef9698f8a1bf1a347
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476596"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>操作說明：使用 Azure AD Graph API

> [!IMPORTANT]
> 我們強烈建議您使用[Microsoft 圖形](https://developer.microsoft.com/graph)而不是 Azure AD 圖形 API 來存取 Azure 活動目錄 (Azure AD) 資源。 我們的開發工作現在是針對 Microsoft Graph，並沒有針對 Azure AD Graph API 規劃的進一步增強功能 。 Azure AD 圖形 API 可能仍然適合的方案數量非常有限;有關詳細資訊,請參閱 Microsoft[圖形或 Azure AD 圖形](https://developer.microsoft.com/office/blogs/microsoft-graph-or-azure-ad-graph/)部落格文章,並將[Azure AD 圖形應用遷移到 Microsoft 圖形](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview)。

Azure AD 圖形 API 通過 OData REST API 終結點提供對 Azure AD 的程式設計存取。 應用程式可以使用 Azure AD 圖形 API 來執行有關目錄資料和物件的建立、讀取、更新及刪除 (CRUD) 作業。 例如,可以使用 Azure AD 圖形 API 創建新使用者、查看或更新使用者的屬性、更改使用者的密碼、檢查組成員身份以進行基於角色的訪問、禁用或刪除使用者。 有關 Azure AD 圖形 API 功能與應用程式方案的詳細資訊,請參閱[Azure AD 圖形 API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)和 Azure AD 圖形 API[先決條件](https://msdn.microsoft.com/library/hh974476.aspx)。 Azure AD 圖形 API 僅適用於工作或學校/組織帳戶。

本文適用於 Azure AD Graph API。 如需與 Microsoft Graph API 相關的類似資訊，請參閱[使用 Microsoft Graph API](https://developer.microsoft.com/graph/docs/concepts/use_the_api)。

## <a name="how-to-construct-a-graph-api-url"></a>如何建構 Graph API URL

在 Graph API 中，若要存取您想要對其執行 CRUD 作業的目錄資料和物件 (亦即，資源或實體)，您可使用以「開放式資料 (OData) 通訊協定」為基礎的 URL。 Graph API 中使用的 URL 是由下列四個主要部分所組成：服務根目錄、租用戶識別碼、資源路徑和查詢字串選項： `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`。 在下列 URL 中取得範例： `https://graph.windows.net/contoso.com/groups?api-version=1.6`。

* **服務根目錄**：在 Azure AD Graph API 中，服務根目錄一律為 https://graph.windows.net。
* **租用戶識別碼**：此區段可以是已驗證 (已註冊) 的網域名稱，在上述範例中為 contoso.com。 它也可以是租戶對象 ID 或「我的組織」或「我」別名。 有關詳細資訊,請參閱在[Azure AD 圖形 API 中定址實體和操作](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)。
* **資源路徑**:網址的此部份識別要與之互動的資源(使用者、組、特定使用者或特定群組等)在上面的示例中,它是解決該資源集的頂層"組"。 您還可以處理特定實體,例如"使用者/[對象 Id]"或"使用者/使用者主要名稱"。
* **查詢參數**：問號 (?) 可區隔資源路徑區段和查詢參數區段。 Azure AD 圖形 API 中的所有請求都需要"api-version"查詢參數。 Azure AD Graph API 也支援下列 OData 查詢選項：**$filter**、**$orderby**、**$expand**、**$top**及 **$format**。 目前不支援下列查詢選項：**$count**、**$inlinecount** 和 **$skip**。 如需詳細資訊，請參閱 [Azure AD Graph API 中支援的查詢、篩選和分頁選項](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options)。

## <a name="graph-api-versions"></a>Graph API 版本

在「api 版本」查詢參數中指定圖形 API 請求的版本。 如果是 1.5 版和更新版本，您可以使用數字版本值；api-version=1.6。 如果是較早的版本，您可以使用遵守 YYYY-MM-DD 格式的日期字串；例如，api-version=2013-11-08。 對於預覽功能,請使用字串「Beta」;例如,api 版本_beta。 有關圖形 API 版本之間的差異的詳細資訊,請參閱[Azure AD 圖形 API 版本版本。](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning)

## <a name="graph-api-metadata"></a>Graph API 中繼資料

要返回 Azure AD Graph API 元資料檔,請在 URL 中的租戶標識符`https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`之後添加"$metadata" 段。 您可以在 Web 瀏覽器的網址列中輸入這個 URL 來查看中繼資料。 傳回的 CSDL 中繼資料文件會說明實體和複雜類型、其屬性，以及由您要求的 Graph API 版本所公開的函式和動作。 省略 api-version 參數會傳回適用於最新版本的中繼資料。

## <a name="common-queries"></a>常用查詢

[Azure AD 圖形 API 常見查詢](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries)列出了可與 Azure AD 圖形一起使用的常見查詢,包括可用於存取目錄中頂級資源的查詢和用於在目錄中執行操作的查詢。

例如， `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` 會傳回目錄 contoso.com 的公司資訊。

或者， `https://graph.windows.net/contoso.com/users?api-version=1.6` 會列出目錄 contoso.com 中的所有使用者物件。

## <a name="using-the-azure-ad-graph-explorer"></a>使用 Azure AD Graph 總管
當您建置應用程式時，可以使用適用於 Azure AD Graph API 的「Azure AD Graph 總管」來查詢目錄資料。

如果您瀏覽至「Azure AD Graph 總管」、登入，然後輸入 `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` 來顯示登入使用者之目錄中的所有使用者，以下螢幕擷取畫面即是您會看到的輸出：

![Azure AD 圖像 API 資源管理員中的範例輸出](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**載入 Azure AD Graph 總管**：若要載入此工具，請瀏覽至 [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)。 按一下 [登入]****，然後使用 Azure AD 帳戶認證來登入，以針對您的租用戶執行「Azure AD Graph 總管」。 如果您針對自己的租用戶執行「Azure AD Graph 總管」，則您或系統管理員將必須在登入期間表示同意。 如果您擁有 Office 365 訂用帳戶，就會自動擁有 Azure AD 租用戶。 事實上，您用來登入 Office 365 的認證就是 Azure AD 帳戶，而您可以將這些認證與「Azure AD Graph 總管」搭配使用。

**執行查詢**：若要執行查詢，可在要求文字方塊中輸入查詢，然後按一下 [GET]**** 或 **Enter** 鍵。 結果即會顯示於回應方塊中。 例如，`https://graph.windows.net/myorganization/groups?api-version=1.6` 會列出登入使用者之目錄中的所有群組物件。

請注意，「Azure AD Graph 總管」具有下列功能與限制：

* 資源集上的自動完成功能。 若要查看此功能，請按一下要求文字方塊 (公司 URL 出現的位置)。 您可以從下拉式清單中選取資源集。
* 要求歷程記錄。
* 支援"我"和"我的組織"位址別名。 例如，您可以使用 `https://graph.windows.net/me?api-version=1.6` 來傳回登入使用者的使用者物件，或者使用 `https://graph.windows.net/myorganization/users?api-version=1.6` 來傳回登入使用者之目錄中的所有使用者。
* 支援使用 `POST`、`GET`、`PATCH` 及 `DELETE`，針對您自己的目錄執行完整的 CRUD 作業。
* 回應標頭區段 對於執行查詢時所發生的問題，此區段有助於進行疑難排解。
* 適用於回應的 JSON 檢視器，並具備展開和摺疊功能。
* 不支援顯示或上傳縮圖相片。

## <a name="using-fiddler-to-write-to-the-directory"></a>使用 Fiddler 寫入目錄

為此,您可以使用 Fiddler Web 調試器練習針對 Azure AD 目錄執行「寫入」 操作。 例如，您可以取得及上傳使用者的設定檔相片 (使用「Azure AD Graph 總管」時無法這麼做)。 有關詳細資訊並安裝 Fiddler,請參[https://www.telerik.com/fiddler](https://www.telerik.com/fiddler)閱 。

在下面的範例中,使用Fiddler Web除錯器在Azure AD目錄中創建新的安全組"MyTestGroup"。

**取得存取權杖**：若要存取 Azure AD Graph，用戶端必須先順利通過 Azure AD 的驗證。 有關詳細資訊,請參閱[Azure AD 的身份驗證方案](authentication-scenarios.md)。

**撰寫並執行查詢**:完成以下步驟:

1. 開啟 Fiddler Web Debugger 並切換至 [Composer]**** 索引標籤。
2. 因為您想要建立新的安全性群組，請從下拉功能表中選取 [Post]**** 做為 HTTP 方法。 有關群組物件的操作和權限的詳細資訊,請參考 Azure AD 圖形[REST API 參考](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)中的[群組](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity)。
3. 在 [Post]**** 旁邊的欄位中，輸入以下要求 URL：`https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`。
   
   > [!NOTE]
   > 您必須使用自己的 Azure AD 目錄網域名稱來取代 {mytenantdomain}。

4. 在 [Post] 下拉式清單正下方的欄位中，輸入下列 HTTP 標頭：
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > 使用 Azure AD 目錄的存取權杖來取代 &lt;您的存取權杖&gt;。

5. 在 [要求本文] **** 欄位中，輸入下列 JSON：
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    如需建立群組的詳細資訊，請參閱 [建立群組](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup)。

有關透過圖形公開的 Azure AD 實體和類型的詳細資訊,以及有關可以使用圖形對它們執行的操作的資訊,請參閱 Azure AD[圖形 REST API 參考](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)。

## <a name="next-steps"></a>後續步驟

* 深入了解 [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* 瞭解有關[Azure AD 圖形 API 權限範圍的更多資訊](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
