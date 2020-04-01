---
title: 如何使用系統分配的託管識別存取 Azure Cosmos 資料庫資料
description: 瞭解如何配置 Azure AD 系統分配的託管標識以從 Azure Cosmos DB 訪問金鑰。 msi, 託管服務識別, aad, azure 活動目錄, 識別
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 102efba5d028eef621f392ef1739ea9ebeca0b44
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80417229"
---
# <a name="how-to-use-a-system-assigned-managed-identity-to-access-azure-cosmos-db-data"></a>如何使用系統分配的託管識別存取 Azure Cosmos 資料庫資料

在本文中,您將設置一個**健壯的、與密鑰輪換無關的解決方案,** 以便利用[託管標識](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)訪問 Azure Cosmos DB 金鑰。 本文中的範例使用 Azure 函數。 但是,您可以使用支援託管標識的任何服務來實現此解決方案。 

您將學習如何創建 Azure 函數,該函數可以造訪 Azure Cosmos DB,而無需複製任何 Azure Cosmos DB 鍵。 該功能將每分鐘醒來,並記錄水族館魚缸的當前溫度。 要瞭解如何設定觸發的 Azure 函數的計時器,請參閱[在 Azure 中建立由計時器文章觸發的函數](../azure-functions/functions-create-scheduled-function.md)。

為了簡化方案,舊溫度文檔的清理由已配置[的「即時存留時間」](./time-to-live.md)設定處理。 

## <a name="assign-a-system-assigned-managed-identity-to-an-azure-function"></a>將系統分配的託管識別分配給 Azure 函數

在此步驟中,您將為 Azure 函數分配系統分配的託管標識。

1. 在[Azure 門戶](https://portal.azure.com/)中,打開**Azure 函數**窗格並導航到函數應用。 

1. 開啟**平台功能** > **識別**選項卡: 

   ![識別碼索引標籤](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. 在 **「識別」** 選項卡**上,打開****「系統識別」** 狀態。 請務必選擇 **「保存**」,並確認要打開系統標識。 最後,"**系統標識"** 窗格應如下所示:  

   ![系統識別已開啟](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-the-managed-identity-access-to-your-azure-cosmos-account"></a>授予 Azure Cosmos 帳號的託管識別存取權限

在此步驟中,您將為 Azure 函數的系統分配的託管標識分配角色。 Azure Cosmos DB 具有多個內建角色,您可以將其分配給託管標識。 對於此解決方案,您將使用以下兩個角色:

|內建角色  |描述  |
|---------|---------|
|[DocumentDB 帳戶參與者](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|可以管理 Azure Cosmos DB 帳戶。 允許檢索讀/寫金鑰。 |
|[宇宙資料庫帳戶讀取器](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|可以讀取 Azure Cosmos DB 帳戶資料。 允許檢索讀取密鑰。 |

> [!IMPORTANT]
> Azure Cosmos DB 中的 RBAC 支援僅適用於控制平面操作。 使用主鍵或資源權杖保護數據平面操作。 要瞭解更多資訊,請參閱[安全訪問數據](secure-access-to-data.md)一文。

> [!TIP] 
> 分配角色時,僅分配所需的訪問許可權。 如果您的服務只需要讀取數據,則將託管標識分配給**Cosmos DB帳戶讀取器**角色。 有關最低特權訪問的重要性的詳細資訊,請參閱[特權帳戶的較低曝光](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)率一文。

對於方案,您將讀取溫度,然後將該數據寫回 Azure Cosmos DB 中的容器。 由於您必須寫入數據,因此將使用**DocumentDB 帳戶參與者**角色。 

1. 登錄到 Azure 門戶並導航到 Azure Cosmos 資料庫帳戶。 開啟**存取管理 (IAM) 窗格**,然後開啟**角色分配**選項卡:

   ![IAM 窗格](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. 選擇 **+ 新增**按鍵,然後**新增角色分配**。

1. **新增角色分配面板**將向右開啟:

   ![加入角色](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **角色**- 選擇**文件資料庫帳戶參與者**
   * 在選擇**系統分配的託管識別**子部份下**分配存取權限**,選擇**函數應用**。
   * **選擇**- 窗格將填充訂閱中的所有具有**託管系統識別**的功能應用。 在我們的案例中,我選擇**摘要服務**功能應用: 

      ![選擇配置](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. 選擇函數應用的標識後,按一下「**保存**」。

## <a name="programmatically-access-the-azure-cosmos-db-keys-from-the-azure-function"></a>從 Azure 函數以程式設計方式存取 Azure 宇宙資料庫金鑰

現在,我們有一個函數應用,具有系統分配的託管標識。 該標識在 Azure Cosmos DB 許可權中被賦予**DocumentDB 帳戶參與者**角色。 以下函數應用代碼將獲取 Azure Cosmos DB 鍵,創建 CosmosClient 物件,獲取溫度,然後將該參數資料庫保存到 Cosmos DB。

此範例使用[清單鍵 API](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys)存取 Azure Cosmos DB 帳戶密鑰。

> [!IMPORTANT] 
> 如果要[配置**Cosmos DB 帳號讀取器**](#grant-the-managed-identity-access-to-your-azure-cosmos-account)角色,則需要使用唯讀[清單金鑰 api](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys)。 這將僅填充唯讀金鑰。

清單鍵 API`DatabaseAccountListKeysResult`傳回 物件。 此類型未在 C# 庫中定義。 以下代碼顯示了此類的實現:  

```csharp 
namespace SummarizationService 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

該範例還使用名為「TemperatureRecord」的簡單文件,該文檔的定義如下:

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

您將使用[Microsoft.Azure.Services.App 身份驗證](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)庫來獲取系統分配的託管標識權杖。 要瞭解獲取權杖的其他方法以及有關`Microsoft.Azure.Service.AppAuthentication`庫的詳細資訊,請參閱[服務到服務身份驗證](../key-vault/service-to-service-authentication.md)一文。

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class TemperatureMonitor
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("TemperatureMonitor")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // In order to get the Service Managed token we need to authenticate to the Azure Resource Manager.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // To get the Azure Cosmos DB keys setup the List Keys API:
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the Result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // fake the temperature sensor for this demo
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

現在,您已準備好[部署 Azure 函數](../azure-functions/functions-create-first-function-vs-code.md)。

## <a name="next-steps"></a>後續步驟

* [使用 Azure Cosmos DB 與活動目錄進行驗證](certificate-based-authentication.md)
* [使用 Azure Key Vault 保護 Azure Cosmos 金鑰](access-secrets-from-keyvault.md)
* [Azure 宇宙資料庫的安全基線](security-baseline.md)
