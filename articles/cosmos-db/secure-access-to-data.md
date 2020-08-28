---
title: 了解如何安全存取 Azure Cosmos DB 中的資料
description: 深入了解 Azure Cosmos DB 中的存取控制概念，其中包括主要金鑰、唯讀金鑰、使用者和權限。
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4714ec9773b98887de483b7353eea9f4416eec19
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017748"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>安全存取 Azure Cosmos DB 中的資料

本文概述如何保護 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)中儲存之資料的存取。

Azure Cosmos DB 會使用兩種類型的金鑰來驗證使用者，以允許存取其資料和資源。 

|金鑰類型|資源|
|---|---|
|[主要金鑰](#master-keys) |用於系統管理資源︰資料庫帳戶、資料庫、使用者和權限|
|[資源權杖](#resource-tokens)|用於應用程式資源︰容器、文件、附件、預存程序、觸發程序和 UDF|

<a id="master-keys"></a>

## <a name="master-keys"></a>主要金鑰

主要金鑰提供資料庫帳戶的所有管理資源的存取權。 主要金鑰：

- 允許存取帳戶、資料庫、使用者和權限。 
- 無法用來提供容器和文件的更細微的存取權。
- 在帳戶建立期間建立。
- 可隨時重新產生。

每個帳戶包含兩個主要金鑰︰主要金鑰和次要金鑰。 雙重金鑰的目的是讓您可以重新產生或輸替金鑰，以持續存取您的帳戶和資料。

除了 Cosmos DB 帳戶的兩個主要金鑰，還有兩個唯讀金鑰。 這些唯讀金鑰只允許帳戶上的讀取作業。 唯讀金鑰不提供存取權來讀取權限資源。

您可以使用 Azure 入口網站來擷取和重新產生主要、次要、唯讀和讀寫主要金鑰。 相關指示請參閱[檢視、複製和重新產生存取金鑰](manage-with-cli.md#regenerate-account-key)。

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Azure 入口網站中的存取控制 (IAM) - 示範 NoSQL 資料庫安全性":::

### <a name="key-rotation"></a>金鑰輪替<a id="key-rotation"></a>

輸替主要金鑰的程序很簡單。 

1. 流覽至 Azure 入口網站，以取得您的次要金鑰。
2. 將您的主要金鑰取代為應用程式中的次要金鑰。 確定所有部署中的所有 Cosmos DB 用戶端都會立即重新開機，並且會開始使用更新的金鑰。
3. 輪替 Azure 入口網站中的主要金鑰。
4. 驗證新的主要金鑰適用于所有資源。 視 Cosmos DB 帳戶的大小而定，金鑰輪替程式可能會從小於一分鐘到數小時的時間進行。
5. 將次要金鑰取代為新的主要金鑰。

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png" alt-text="Azure 入口網站中的主要金鑰輪替 - 示範 NoSQL 資料庫安全性" border="false":::

### <a name="code-sample-to-use-a-master-key"></a>使用主要金鑰的程式碼範例

下列程式碼範例說明如何使用 Cosmos DB 帳戶端點和主要金鑰來具現化 DocumentClient，並建立資料庫：

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

下列程式碼範例說明如何使用 Azure Cosmos DB 帳戶端點和主要金鑰來具現化 `CosmosClient` 物件：

:::code language="python" source="~/cosmosdb-python-sdk/sdk/cosmos/azure-cosmos/samples/access_cosmos_with_resource_token.py" id="configureConnectivity":::

## <a name="resource-tokens"></a>資源權杖 <a id="resource-tokens"></a>

資源權杖允許存取資料庫內的應用程式資源。 資源權杖：

- 允許存取特定的容器、分割索引鍵、文件、附件、預存程序、觸發程序和 UDF。
- 在[使用者](#users)被授與特定資源的[權限](#permissions)時建立。
- 使用 POST、GET 或 PUT 呼叫處理權限資源時重新建立。
- 使用特別為使用者、資源和權限建構的雜湊資源權杖。
- 是與可自訂的有效期間繫結的時間。 預設有效時間範圍是一小時。 但可以明確指定權杖存留期，最多五小時。
- 提供安全的替代方式來分發主要金鑰。
- 可讓用戶端根據所授與的權限，讀取、寫入和刪除 Cosmos DB 帳戶中的資源。

若要對無法託付主要金鑰的用戶端提供 Cosmos DB 帳戶內資源的存取權，您可以使用資源權杖 (方法是建立 Cosmos DB 使用者和權限)。  

Cosmos DB 資源權杖提供一個安全的替代方式，無需主要或唯讀金鑰，便可讓用戶端根據授與他們的權限，以讀取、寫入和刪除 Cosmos DB 帳戶中的資源。

以下是典型的設計模式，其中資源權杖可能會被要求、產生和傳遞給用戶端：

1. 設定中間層服務，以協助行動應用程式分享使用者相片。
2. 中間層服務擁有 Cosmos DB 帳戶的主要金鑰。
3. 使用者的行動裝置上安裝相片應用程式。
4. 登入時，相片應用程式會建立中間層服務的使用者身分識別。 這項身分識別建立的機制完全取決於應用程式。
5. 建立身分識別後，中間層服務會根據身分識別要求權限。
6. 中間層服務會將資源權杖送回電話應用程式。
7. 電話應用程式可以繼續使用資源權杖，利用資源權杖所定義的權限並在資源權杖所允許的間隔內，直接存取 Cosmos DB 資源。
8. 資源權杖過期時，後續要求會收到 401 未經授權的例外狀況。  此時，電話應用程式會重新建立身分識別，並要求新的資源權杖。

    :::image type="content" source="./media/secure-access-to-data/resourcekeyworkflow.png" alt-text="Azure Cosmos DB 資源權杖工作流程" border="false":::

資源權杖的產生和管理由原生 Cosmos DB 用戶端程式庫處理。不過，如果您使用 REST，您必須建構要求/驗證標頭。 如需有關建立 REST 驗證標頭的詳細資訊，請參閱 [Cosmos DB 資源的存取控制](/rest/api/cosmos-db/access-control-on-cosmosdb-resources) 或 [.net SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/AuthorizationHelper.cs) 或 [Node.js SDK](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts)的原始程式碼。

如需用來產生或代理資源權杖的中間層服務的範例，請參閱 [ResourceTokenBroker 應用程式](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers)。

## <a name="users"></a>使用者<a id="users"></a>

Azure Cosmos DB 的使用者與 Cosmos 資料庫相關聯。  每個資料庫都包含零個或多個 Cosmos DB 使用者。 下列程式碼範例示範如何使用 [Azure Cosmos DB .NET SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement)來建立 Cosmos DB 使用者。

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> 每個 Cosmos DB 使用者都有一個 System.io.stream.readasync ( # A1 方法，可用來取得與使用者相關聯的 [許可權](#permissions) 清單。

## <a name="permissions"></a>許可權<a id="permissions"></a>

許可權資源會與使用者建立關聯，並在容器和分割區索引鍵層級指派。 每位使用者可能包含零個或更多的許可權。 許可權資源可讓您存取使用者嘗試存取特定資料分割索引鍵中的特定容器或資料時所需的安全性權杖。 權限資源可能提供兩種可用的存取等級：

- 全部：使用者具有資源的完整權限。
- 讀取：使用者只能讀取資源的內容，但無法執行資源的寫入、更新或刪除作業。

> [!NOTE]
> 若要執行預存程式，使用者必須擁有將在其中執行預存程式之容器的 All 許可權。

### <a name="code-sample-to-create-permission"></a>建立權限的程式碼範例

下列程式碼範例示範如何建立權限資源，讀取權限資源的資源權杖，並將權限與先前所建立的[使用者](#users)產生關聯。

```csharp
// Create a permission on a container and specific partition key value
Container container = client.GetContainer("SalesDatabase", "OrdersContainer");
user.CreatePermissionAsync(
    new PermissionProperties(
        id: "permissionUser1Orders",
        permissionMode: PermissionMode.All,
        container: benchmark.container,
        resourcePartitionKey: new PartitionKey("012345")));
```

### <a name="code-sample-to-read-permission-for-user"></a>使用者的讀取權限程式碼範例

下列程式碼片段示範如何抓取與上面建立的使用者相關聯的許可權，並代表使用者具現化新的 CosmosClient，範圍為單一分割區索引鍵。

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="add-users-and-assign-roles"></a>新增使用者和指派角色

若要將 Azure Cosmos DB 帳戶讀者存取權新增至您的使用者帳戶，請在 Azure 入口網站執行下列步驟，以取得訂用帳戶擁有者。

1. 開啟 Azure 入口網站，選取您的 Azure Cosmos DB 帳戶。
2. 按一下 [存取控制 (IAM)]**** 索引標籤，然後按一下 [+ 新增角色指派]****。
3. 在 [新增角色指派]**** 窗格的 [角色]**** 方塊中，選取 [Cosmos DB 帳戶讀者角色]****。
4. 在 [ **指派存取權**] 方塊中，選取 [ **Azure AD 使用者、群組或應用程式**]。
5. 選取目錄中您要為其授與存取權的使用者、群組或應用程式。  您可以依顯示名稱、電子郵件地址或物件識別碼來搜尋目錄。
    選取的使用者、群組或應用程式會出現在選取的成員清單中。
6. 按一下 [儲存]。

實體現在已可讀取 Azure Cosmos DB 資源。

## <a name="delete-or-export-user-data"></a>刪除或匯出使用者資料

Azure Cosmos DB 可讓您搜尋、選取、修改和刪除資料庫或集合中的任何個人資料。 Azure Cosmos DB 會提供可尋找和刪除個人資料的 API，不過，您必須負責使用 API，並定義清除個人資料時所需的邏輯。 每個多模型 API (SQL、MongoDB、Gremlin、Cassandra、資料表) 皆會提供不同的語言 SDK，其中包含搜尋和刪除個人資料的方法。 您也可以啟用[存留時間 (TTL)](time-to-live.md) 功能，在指定期間後自動刪除資料，完全不會產生任何額外的費用。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解 Cosmos 資料庫安全性，請參閱 [Cosmos DB 資料庫安全性](database-security.md)。
- 若要了解如何建構 Cosmos DB 授權權杖，請參閱 [Cosmos DB 資源的存取控制 (英文)](/rest/api/cosmos-db/access-control-on-cosmosdb-resources)。
- 使用者管理範例（含使用者和許可權）， [.NET SDK v3 使用者管理範例](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)
