---
title: 使用 Azure 資料資源管理器 C# SDK 創建策略
description: 在本文中，您將學習如何使用 C# 創建策略。
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 17312840b0081056ad04723f2b2c241c47902021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667297"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-c"></a>使用 C 為 Azure 資料資源管理器創建資料庫和表策略#

> [!div class="op_single_selector"]
> * [C#](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 在本文中，您將使用 C# 為 Azure 資料資源管理器創建資料庫和表策略。

## <a name="prerequisites"></a>Prerequisites

* Visual Studio 2019。 如果你沒有視覺工作室2019，你可以下載和使用*免費*[的視覺工作室社區2019。](https://www.visualstudio.com/downloads/) 請務必在視覺化工作室設置期間選擇**Azure 開發**。
* Azure 訂用帳戶。 如果需要，可以在開始之前創建[一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。
* [測試群集和資料庫](create-cluster-database-csharp.md)。
* [測試表](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)。

## <a name="install-c-nuget"></a>安裝 C# NuGet

* 安裝[Azure 資料資源管理器 （Kusto） NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安裝[微軟.Azure.Kusto.Data.NET標準NuGet套裝軟體](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/)。 （可選，用於更改表策略。
* 安裝[Microsoft.身份模型.用戶端.ActiveDirectory NuGet 包](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)，用於身份驗證。

## <a name="authentication"></a>驗證
要運行本文中的示例，需要一個可以訪問資源的 Azure 活動目錄 （Azure AD） 應用程式和服務主體。 您可以使用同一 Azure AD 應用程式從[測試群集和資料庫](create-cluster-database-csharp.md#authentication)進行身份驗證。 如果要使用其他 Azure AD 應用程式，請參閱[創建 Azure AD 應用程式](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)以創建免費的 Azure AD 應用程式，並在訂閱範圍內添加角色指派。 本文還演示如何獲取`Directory (tenant) ID`、`Application ID`和`Client secret`。 您可能需要將新的 Azure AD 應用程式添加為資料庫中的主體。 有關詳細資訊，請參閱管理[Azure 資料資源管理器資料庫許可權](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions)。

## <a name="alter-database-retention-policy"></a>更改資料庫保留原則
設置具有 10 天虛刪除期的保留原則。
    
```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(softDeletePeriod: TimeSpan.FromDays(10)));
```

## <a name="alter-database-cache-policy"></a>更改資料庫緩存策略
為資料庫設置緩存策略。 前五天的資料將位於群集 SSD 上。

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(hotCachePeriod: TimeSpan.FromDays(5)));
```

## <a name="alter-table-cache-policy"></a>更改表緩存策略
為表設置緩存策略。 前五天的資料將位於群集 SSD 上。

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var databaseName = "<DatabaseName>";
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var tableName = "<TableName>"

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = databaseName,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    //dataHotSpan and indexHotSpan should have the same value
    var hotSpan = TimeSpan.FromDays(5);
    var command1 = CslCommandGenerator.GenerateAlterTableCachingPolicyCommand(tableName: tableName,
                    dataHotSpan: hotSpan, indexHotSpan: hotSpan);

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="add-a-new-principal-for-the-database"></a>為資料庫添加新主體
添加新的 Azure AD 應用程式作為資料庫的管理員主體。

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var clientIdToAdd = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.AddPrincipalsAsync(resourceGroupName, clusterName, databaseName,
                new DatabasePrincipalListRequest()
                {
                    Value = new List<DatabasePrincipal>()
                    {
                        new DatabasePrincipal("Admin", "<database_principle_name>", "App", appId: clientIdToAdd, tenantName:tenantId)
                    }
                });
```
## <a name="next-steps"></a>後續步驟

* [閱讀有關資料庫和表策略的詳細資訊](https://docs.microsoft.com/azure/kusto/management/policies)
