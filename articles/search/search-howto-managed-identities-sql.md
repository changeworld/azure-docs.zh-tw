---
title: 使用受控識別設定與 Azure SQL Database 的連接
titleSuffix: Azure Cognitive Search
description: 瞭解如何使用受控識別來設定 Azure SQL Database 的索引子連接
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 9f90125edeee453dc9e8b8b80f8eb09d9fc6e84c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90971529"
---
# <a name="set-up-an-indexer-connection-to-azure-sql-database-using-a-managed-identity"></a>使用受控識別設定 Azure SQL Database 的索引子連接

此頁面描述如何使用受控識別來設定 Azure SQL Database 的索引子連接，而不是在資料來源物件連接字串中提供認證。

在深入了解此功能之前，建議您先了解索引子是什麼，以及如何設定資料來源的索引子。 您可以在下列連結找到更多資訊：

* [索引子概觀](search-indexer-overview.md)
* [Azure SQL 索引子](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>使用受控識別設定連線

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - 開啟系統指派的受控識別

當系統指派的受控識別啟用時，Azure 會為您的搜尋服務建立身分識別，以便用來向相同租用戶和訂用帳戶內的其他 Azure 服務進行驗證。 接著，您可以在角色型存取控制 (RBAC) 指派中使用此身分識別，以允許在編制索引期間存取資料。

![開啟系統指派的受控識別](./media/search-managed-identities/turn-on-system-assigned-identity.png "開啟系統指派的受控識別")

選取 [儲存] 之後，您會看到已指派給搜尋服務的物件識別碼。

![物件識別碼](./media/search-managed-identities/system-assigned-identity-object-id.png "物件識別碼")

### <a name="2---provision-azure-active-directory-admin-for-sql-server"></a>2 - 佈建 SQL Server 的 Azure Active Directory 系統管理員

在下一個步驟中連線到資料庫時，您將必須使用具有資料庫系統管理員存取權的 Azure Active Directory (Azure AD) 帳戶連線，才能授與您的搜尋服務存取資料庫的權限。

依照[這裡](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-database) \(英文\) 的指示，將資料庫的系統管理員存取權授與您的 Azure AD 帳戶。

### <a name="3---assign-the-search-service-permissions"></a>3 - 指派搜尋服務權限

請遵循下列步驟來指派搜尋服務權限以讀取資料庫。

1. 連線到 Visual Studio

    ![連線到 Visual Studio](./media/search-managed-identities/connect-with-visual-studio.png "連線到 Visual Studio")

2. 使用您的 Azure AD 帳戶進行驗證

    ![驗證](./media/search-managed-identities/visual-studio-authentication.png "Authenticate")

3. 執行下列命令：

    在您的搜尋服務名稱前後加上括弧。
    
    ```
    CREATE USER [your search service name here] FROM EXTERNAL PROVIDER;
    EXEC sp_addrolemember 'db_datareader', [your search service name here];
    ```

    ![新查詢](./media/search-managed-identities/visual-studio-new-query.png "新增查詢")

    ![執行查詢](./media/search-managed-identities/visual-studio-execute-query.png "執行查詢")

>[!NOTE]
> 如果步驟 1 中的搜尋服務識別在完成此步驟之後變更，則您必須移除角色成員資格並移除 SQL 資料庫中的使用者，然後再次完成步驟 3，以再次新增權限。
> 您可以執行下列命令來移除角色成員資格和使用者：
> ```
> sp_droprolemember 'db_datareader', [your search service name];
> DROP USER IF EXISTS [your search service name];
> ```

### <a name="4---add-a-role-assignment"></a>4 - 新增角色指派

在此步驟中，您將提供您 Azure 認知搜尋服務的權限，以從您的 SQL Server 讀取資料。

1. 在 Azure 入口網站瀏覽至您的 Azure SQL Server 頁面。
2. 選取 [存取控制 (IAM)]
3. 選取 [新增]，然後選取 [新增角色指派]

    ![新增角色指派](./media/search-managed-identities/add-role-assignment-sql-server.png "新增角色指派")

4. 選取適當的**讀者**角色。
5. 將 [存取權指派對象為] 保留為 [Azure AD 使用者、群組或服務主體]
6. 搜尋您的搜尋服務，加以選取，然後選取 [儲存]

    ![新增讀者角色指派](./media/search-managed-identities/add-role-assignment-sql-server-reader-role.png "新增讀者角色指派")

### <a name="5---create-the-data-source"></a>5 - 建立資料來源

[REST API](/rest/api/searchservice/create-data-source)、Azure 入口網站和[.net SDK](/dotnet/api/microsoft.azure.search.models.datasource)都支援受控識別連接字串。 以下範例說明如何建立資料來源，以使用 [REST API](/rest/api/searchservice/create-data-source) 和受控識別連接字串來編制 Azure SQL Database 的資料索引。 REST API、.NET SDK 和 Azure 入口網站的受控識別連接字串格式都相同。

使用 [REST API](/rest/api/searchservice/create-data-source)建立資料來源時，資料來源必須具有下列必要屬性：

* **名稱**是搜尋服務中資料來源的唯一名稱。
* **type** 是 `azuresql`
* **credentials**
    * 使用受控識別進行驗證時，**credentials** 格式會與未使用受控身分識別時的格式不同。 在這裡，您將提供初始類別目錄或資料庫名稱，以及沒有帳戶金鑰或密碼的 ResourceId。 ResourceId 必須包含 Azure SQL Database 的訂用帳戶識別碼、SQL Database 的資源群組，以及 SQL Database 的名稱。 
    * 受控識別連接字串格式：
        * *初始類別目錄|資料庫=**資料庫名稱**;ResourceId=/訂用帳戶/**您的訂用帳戶識別碼**/resourceGroups/**您的資源群組名稱**/providers/Microsoft.Sql/servers/**SQL Server 名稱**/;連線逾時=**連線逾時時間長度**;*
* **container** 指定您想要編制索引的資料表或檢視表的名稱。

如何使用 [REST API](/rest/api/searchservice/create-data-source) \(機器翻譯\) 建立 Azure SQL 資料來源物件的範例：

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { "connectionString" : "Database=sql-database;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azure-sql-resource-group/providers/Microsoft.Sql/servers/sql-server-search-demo;Connection Timeout=30;" },
    "container" : { "name" : "my-table" }
} 
```

### <a name="6---create-the-index"></a>6 - 建立索引

索引會指定文件、屬性和其他建構中可形塑搜尋體驗的欄位。

以下說明如何使用可搜尋的 `booktitle` 欄位建立索引︰   

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

如需建立索引的詳細資訊，請參閱[建立索引](/rest/api/searchservice/create-index)

### <a name="7---create-the-indexer"></a>7 - 建立索引子

索引子會以目標搜尋索引連接資料來源，並提供排程來自動重新整理資料。

建立索引和資料來源之後，您就可以開始建立索引子。

Azure SQL 索引子的索引子定義範例：

```
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-indexer",
    "dataSourceName" : "sql-datasource",
    "targetIndexName" : "my-target-index",
    "schedule" : { "interval" : "PT2H" }
}
```    

這個索引子每隔兩小時就會執行一次 (已將排程間隔設為 "PT2H")。 若每隔 30 分鐘就要執行索引子，可將間隔設為 "PT30M"。 支援的最短間隔為 5 分鐘。 排程為選擇性 - 如果省略，索引子只會在建立時執行一次。 不過，您隨時都可依需求執行索引子。   

如需建立索引子 API 的詳細資訊，請參閱 [建立索引子](/rest/api/searchservice/create-indexer)。

如需有關定義索引子排程的詳細資訊，請參閱[如何排程 Azure 認知搜尋的索引子](search-howto-schedule-indexers.md)。

## <a name="troubleshooting"></a>疑難排解

如果您在索引子嘗試連線到資料來源時收到錯誤，指出不允許用戶端存取伺服器，請參閱[常見的索引子錯誤](./search-indexer-troubleshooting.md) \(部分機器翻譯\)。

## <a name="see-also"></a>另請參閱

深入了解 Azure SQL 索引子：
* [Azure SQL 索引子](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)