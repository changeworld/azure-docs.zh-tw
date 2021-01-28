---
title: 如何使用系統指派的受控識別來存取 Azure Cosmos DB 資料
description: 瞭解如何設定 Azure Active Directory (Azure AD) 系統指派的受控識別 (受控服務識別) ，以從 Azure Cosmos DB 存取金鑰。
author: j-patrick
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 4d9845fad8c9013bd20499c45a8d1714e30e9dbf
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927418"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>使用系統指派的受控識別來存取 Azure Cosmos DB 資料
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

在本文中，您將使用 [受控](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)識別來設定 *健全、金鑰輪替中立* 的解決方案，以存取 Azure Cosmos DB 金鑰。 本文中的範例使用 Azure Functions，但您可以使用任何支援受控識別的服務。 

您將瞭解如何建立可存取 Azure Cosmos DB 資料的函式應用程式，而不需要複製任何 Azure Cosmos DB 金鑰。 函數應用程式會每分鐘喚醒一次，並記錄水族箱魚箱的目前溫度。 若要瞭解如何設定計時器觸發的函式應用程式，請參閱在 [Azure 中建立由計時器觸發的函數](../azure-functions/functions-create-scheduled-function.md) 。

為了簡化此案例，已設定生存 [時間](./time-to-live.md) 設定來清除較舊的溫度檔。 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>將系統指派的受控識別指派給函數應用程式

在此步驟中，您會將系統指派的受控識別指派給您的函數應用程式。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，開啟 [ **Azure** 函式] 窗格，並移至您的函式應用程式。 

1. 開啟 [**平臺功能**  >  **識別**] 索引標籤： 

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-selection.png" alt-text="顯示函數應用程式平臺功能和識別選項的螢幕擷取畫面。":::

1. 在 [身分 **識別** ] 索引卷 **標上，** 開啟 [系統識別] **狀態** ，然後選取 [ **儲存**]。 [身分 **識別** ] 窗格看起來應該如下所示：  

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-system-managed-on.png" alt-text="顯示 [系統身分識別狀態] 設為 [開啟] 的螢幕擷取畫面。":::

## <a name="grant-access-to-your-azure-cosmos-account"></a>將存取權授與您的 Azure Cosmos 帳戶

在此步驟中，您會將角色指派給函式應用程式系統指派的受控識別。 Azure Cosmos DB 具有多個內建角色，可供您指派給受控識別。 針對此解決方案，您將使用下列兩個角色：

|內建角色  |描述  |
|---------|---------|
|[DocumentDB 帳戶參與者](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|可以管理 Azure Cosmos DB 帳戶。 允許抓取讀取/寫入金鑰。 |
|[Cosmos DB 帳戶讀者角色](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|可以讀取 Azure Cosmos DB 帳戶資料。 允許抓取讀取金鑰。 |

> [!IMPORTANT]
> Azure Cosmos DB 中的角色型存取控制支援僅適用于控制平面作業。 資料平面作業會透過主要金鑰或資源權杖來保護。 若要深入瞭解，請參閱 [安全的資料存取](secure-access-to-data.md) 文章。

> [!TIP] 
> 當您指派角色時，只會指派所需的存取權。 如果您的服務只需要讀取資料，則將 **Cosmos DB 帳戶讀者** 角色指派給受控識別。 如需最低許可權存取重要性的詳細資訊，請參閱 [低許可權帳戶的公開風險](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts) 文章。

在此案例中，函數應用程式會讀取水族箱的溫度，然後將該資料寫回至 Azure Cosmos DB 中的容器。 因為函數應用程式必須寫入資料，所以您需要指派 **DocumentDB 帳戶參與者** 角色。 

### <a name="assign-the-role-using-azure-portal"></a>使用 Azure 入口網站指派角色

1. 登入 Azure 入口網站並移至您的 Azure Cosmos DB 帳戶。 開啟 [ **存取控制] (IAM)** 窗格，然後開啟 [ **角色指派** ] 索引標籤：

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab.png" alt-text="顯示 [存取控制] 窗格和 [角色指派] 索引標籤的螢幕擷取畫面。":::

1. 選取 [+新增] > [新增角色指派]。

1. [ **新增角色指派** ] 面板會開啟至右側：

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png" alt-text="顯示 [新增角色指派] 窗格的螢幕擷取畫面。":::

   * **角色**：選取 **DocumentDB 帳戶參與者**
   * **將存取權指派給**：在 [ **選取系統指派的受控識別** ] 子區段下，選取 [ **函數應用程式**]。
   * **選取**：此窗格將會填入訂用帳戶中具有 **受控系統身分識別** 的所有函式應用程式。 在此情況下，請選取 **FishTankTemperatureService** 函式應用程式： 

      :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png" alt-text="螢幕擷取畫面，其中顯示已填入範例的 [新增角色指派] 窗格。":::

1. 當您選取函數應用程式之後，請選取 [ **儲存**]。

### <a name="assign-the-role-using-azure-cli"></a>使用 Azure CLI 指派角色

若要使用 Azure CLI 指派角色，請開啟 Azure Cloud Shell 並執行下列命令：

```azurecli-interactive

scope=$(az cosmosdb show --name '<Your_Azure_Cosmos_account_name>' --resource-group '<CosmosDB_Resource_Group>' --query id)

principalId=$(az webapp identity show -n '<Your_Azure_Function_name>' -g '<Azure_Function_Resource_Group>' --query principalId)

az role assignment create --assignee $principalId --role "DocumentDB Account Contributor" --scope $scope
```

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>以程式設計方式存取 Azure Cosmos DB 金鑰

現在我們有一個函式應用程式，其具有系統指派的受控識別，且具有 Azure Cosmos DB 許可權中的 **DocumentDB 帳戶參與者** 角色。 下列函式應用程式程式碼會取得 Azure Cosmos DB 的金鑰、建立 CosmosClient 物件、取得水族箱的溫度，然後將其儲存至 Azure Cosmos DB。

此範例會使用 [列出金鑰 API](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listkeys) 來存取您的 Azure Cosmos DB 帳戶金鑰。

> [!IMPORTANT] 
> 如果您想要 [指派 Cosmos DB 帳戶讀取](#grant-access-to-your-azure-cosmos-account) 者角色，您必須使用「 [唯讀金鑰」 API 清單](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listreadonlykeys)。 這只會填入唯讀金鑰。

列出金鑰 API 會傳回 `DatabaseAccountListKeysResult` 物件。 此類型不是在 c # 程式庫中定義。 下列程式碼顯示此類別的實作為：  

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

此範例也會使用名為 "TemperatureRecord" 的簡單檔，其定義如下：

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

您將使用 [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 程式庫來取得系統指派的受控識別權杖。 若要瞭解其他取得權杖的方法，並深入瞭解程式庫的詳細資訊 `Microsoft.Azure.Service.AppAuthentication` ，請參閱 [服務對服務驗證](/dotnet/api/overview/azure/service-to-service-authentication) 文章。


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

您現在已準備好 [部署函數應用程式](../azure-functions/create-first-function-vs-code-csharp.md)。

## <a name="next-steps"></a>後續步驟

* [以憑證為基礎的驗證與 Azure Cosmos DB 和 Azure Active Directory](certificate-based-authentication.md)
* [使用 Azure Key Vault 保護 Azure Cosmos DB 金鑰](access-secrets-from-keyvault.md)
* [Azure Cosmos DB 的安全性基準](security-baseline.md)