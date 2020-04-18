---
title: 如何使用系統分配的託管識別存取 Azure Cosmos 資料庫資料
description: 瞭解如何設定 Azure 活動目錄 (Azure AD) 系統分配的託管識別(託管服務識別)以從 Azure Cosmos DB 存取金鑰。
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 8136ad7a1fe29bc3394e959c10aafc52988c0a23
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641184"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>使用系統配置的託管識別存取 Azure Cosmos 資料庫資料

在本文中,您將設置一個*健壯的密鑰旋轉無關*解決方案,以便使用[託管標識](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)訪問 Azure Cosmos DB 金鑰。 本文中的示例使用 Azure 函數,但可以使用支援託管標識的任何服務。 

您將學習如何創建一個函數應用,該應用可以造訪 Azure Cosmos DB 資料,而無需複製任何 Azure Cosmos 資料庫鍵。 功能應用程式將每分鐘醒來,並記錄水族館魚缸的當前溫度。 要瞭解如何設定計時器觸發的函數應用,請參閱[在 Azure 中建立由計時器文章觸發的函數](../azure-functions/functions-create-scheduled-function.md)。

為了簡化方案,已配置[「即時到即時」](./time-to-live.md)設置以清理較舊的溫度文檔。 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>將系統配置的託管識別分配給函數應用

在此步驟中,您將為函數應用分配系統分配的託管標識。

1. 在[Azure 門戶](https://portal.azure.com/)中,打開**Azure 函數**窗格並轉到函數應用。 

1. 開啟**平台功能** > **識別**選項卡: 

   ![顯示功能應用的平臺功能和標識選項的屏幕截圖。](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. 在 **「識別」** 選項卡**上,打開**系統識別**狀態**並選擇「**儲存**」 。 **"標識"** 窗格應如下所示:  

   ![顯示系統標識狀態設置為"打開"的屏幕截圖。](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-access-to-your-azure-cosmos-account"></a>授予對 Azure Cosmos 帳號的存取權限

在此步驟中,您將為函數應用的系統分配的託管標識分配角色。 Azure Cosmos DB 具有多個內建角色,您可以將其分配給託管標識。 對於此解決方案,您將使用以下兩個角色:

|內建角色  |描述  |
|---------|---------|
|[DocumentDB 帳戶參與者](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|可以管理 Azure Cosmos DB 帳戶。 允許檢索讀/寫金鑰。 |
|[宇宙資料庫帳戶讀取器](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|可以讀取 Azure Cosmos DB 帳戶資料。 允許檢索讀取密鑰。 |

> [!IMPORTANT]
> Azure Cosmos DB 中對基於角色的存取控制的支援僅適用於控制平面操作。 數據平面操作通過主鍵或資源權杖進行保護。 要瞭解更多資訊,請參閱[安全訪問數據](secure-access-to-data.md)一文。

> [!TIP] 
> 分配角色時,請僅分配所需的訪問許可權。 如果您的服務只需要讀取數據,則將**Cosmos DB帳戶讀取器**角色分配給託管標識。 有關最低特權訪問的重要性的詳細資訊,請參閱[特權帳戶的"低曝光"](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)一文。

在這種情況下,函數應用將讀取水族館的溫度,然後將該數據寫回 Azure Cosmos DB 中的容器。 由於函數應用必須寫入數據,因此您需要分配**DocumentDB 帳戶參與者**角色。 

1. 登錄到 Azure 門戶並轉到 Azure Cosmos 資料庫帳戶。 開啟 **'存取控制 (IAM)'** 窗格,然後開啟**角色分配**選項卡:

   ![顯示「訪問控制」窗格和角色分配選項卡的螢幕截圖。](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. 選擇 **=** > **新增角色分配**。

1. **新增角色分配**「面板將向右開啟:

   ![顯示「添加角色分配」窗格的螢幕截圖。](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **角色**: 選擇**文件資料庫帳戶參與者**
   * 在 **「選擇系統分配託管標識**」子部分下**分配訪問權限**,選擇 **「函數應用**」。
   * **選擇**:窗格將填充具有**託管系統標識**的訂閱中的所有功能應用。 在這種情況下,選擇**魚缸溫度服務**功能應用程式: 

      ![顯示填充範例的「添加角色分配」窗格的螢幕截圖。](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. 選擇函數應用後,選擇 **「保存**」。

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>以程式設計模式存取 Azure 宇宙資料庫金鑰

現在,我們有一個函數應用,該函數應用具有在 Azure Cosmos DB 許可權中具有**DocumentDB 帳戶參與者**角色的系統分配的託管標識。 以下函數應用代碼將獲取 Azure Cosmos DB 鍵、創建 CosmosClient 物件、獲取水族館的溫度,然後將其保存到 Azure Cosmos DB。

此範例使用[清單鍵 API](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys)存取 Azure Cosmos DB 帳戶密鑰。

> [!IMPORTANT] 
> 如果要[配置 Cosmos DB 帳號讀取器](#grant-access-to-your-azure-cosmos-account)角色,則需要使用[清單唯讀金鑰 API](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys)。 這將只填充唯讀金鑰。

清單鍵 API`DatabaseAccountListKeysResult`傳回 物件。 此類型未在 C# 庫中定義。 以下代碼顯示了此類的實現:  

```csharp 
namespace Monitor 
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

您將使用[Microsoft.Azure.Services.App 身份驗證](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)庫來獲取系統分配的託管標識權杖。 要瞭解獲取權杖的其他方法並瞭解有關庫的詳細資訊,`Microsoft.Azure.Service.AppAuthentication`請參閱[服務到服務身份驗證](../key-vault/general/service-to-service-authentication.md)一文。


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
    public static class FishTankTemperatureService
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

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
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
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

現在,您可以[部署函數應用](../azure-functions/functions-create-first-function-vs-code.md)。

## <a name="next-steps"></a>後續步驟

* [使用 Azure Cosmos DB 與 Azure 活動目錄進行基於憑證的身份驗證](certificate-based-authentication.md)
* [使用 Azure 金鑰保管庫保護 Azure 宇宙資料庫金鑰](access-secrets-from-keyvault.md)
* [Azure 宇宙資料庫的安全基線](security-baseline.md)
