---
title: 具有一組驗證認證的資源輪替教學課程
description: 使用此教學課程來了解如何針對使用一組驗證認證的資源，自動輪替祕密。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 9bff8c040f4cfed612278dd83ebb354b31a3a1f3
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85801439"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>針對使用一組驗證認證的資源，將秘密的輪替自動化

向 Azure 服務進行驗證的最佳方式是使用[受控識別](../general/managed-identity.md)，但在某些情況下並無法使用此選項。 在這類情況下，應使用存取金鑰或祕密。 您應該定期輪替存取金鑰或祕密。

本教學課程示範如何針對使用一組驗證認證的資料庫和服務自動執行定期的秘密輪替。 具體而言，此教學課程會使用 Azure 事件方格通知所觸發的函式，來輪替儲存在 Azure Key Vault 中的 SQL Server 密碼：

![輪替解決方案圖表](../media/rotate1.png)

1. 在祕密到期日的 30 天前，Key Vault 會將「即將到期」事件發佈至事件方格。
1. 事件方格會檢查事件訂閱，並使用 HTTP POST 呼叫訂閱此事件的函式應用程式端點。
1. 函式應用程式會接收秘密資訊、產生新的隨機密碼，並使用 Key Vault 中的新密碼建立秘密的新版本。
1. 函式應用程式會以新密碼更新 SQL Server。

> [!NOTE]
> 步驟 3 和 4 之間可能會有延遲。 在這段期間內，Key Vault 中的祕密無法向 SQL Server 進行驗證。 若有任何步驟失敗，事件方格會重試 2 小時。

## <a name="create-a-key-vault-and-sql-server-instance"></a>建立金鑰保存庫和 SQL Server 執行個體

第一個步驟是建立一個金鑰保存庫、 SQL Server 執行個體和資料庫，然後將 SQL Server 管理員密碼儲存在 Key Vault 中。

本教學課程使用現有的 Azure Resource Manager 範本來建立元件。 您可以在這裡找到程式碼：[基本祕密輪替範本範例](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates)。

1. 選取 Azure 範本部署連結：
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. 在 [資源群組] 下方，選取 [新建]。 將群組命名為 **simplerotation**。
1. 選取 [購買]。

    ![建立資源群組](../media/rotate2.png)

您現在會有金鑰保存庫、SQL Server 執行個體，以及 SQL 資料庫。 您可以執行下列命令，在 Azure CLI 中驗證這項設定：

```azurecli
az resource list -o table
```

結果看起來會像下列輸出：

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation      eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation      eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-a-function-app"></a>建立函數應用程式

接下來，使用系統管理的身分識別以及其他必要元件建立函式應用程式。

函式應用程式需要以下元件：
- Azure App Service 方案
- 儲存體帳戶
- 透過函式應用程式受控識別存取 Key Vault 所含秘密的存取原則

1. 選取 Azure 範本部署連結：
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. 在 [資源群組] 清單中，選取 [simplerotation]。
1. 選取 [購買]。

   ![選取購買](../media/rotate3.png)

完成上述步驟之後，您將會有儲存體帳戶、伺服器陣列和函式應用程式。 您可以執行下列命令，在 Azure CLI 中驗證這項設定：

```azurecli
az resource list -o table
```

結果看起來會像下列輸出：

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation       eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation       eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation       eastus      Microsoft.Sql/servers/databases
simplerotationstrg         simplerotation       eastus      Microsoft.Storage/storageAccounts
simplerotation-plan        simplerotation       eastus      Microsoft.Web/serverFarms
simplerotation-fn          simplerotation       eastus      Microsoft.Web/sites
```

如需如何建立函式應用程式和使用受控識別來存取 Key Vault 的相關資訊，請參閱[從 Azure 入口網站建立函式應用程式](../../azure-functions/functions-create-function-app-portal.md)和[使用受控識別提供金鑰保存庫驗證](../general/managed-identity.md)。

### <a name="rotation-function"></a>輪替函式
函式會透過更新 Key Vault 和 SQL 資料庫，使用事件來觸發密碼輪替。

#### <a name="function-trigger-event"></a>函式觸發事件

此函式會讀取事件資料並執行輪替邏輯：

```csharp
public static class SimpleRotationEventHandler
{
    [FunctionName("SimpleRotation")]
       public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
       {
            log.LogInformation("C# Event trigger function processed a request.");
            var secretName = eventGridEvent.Subject;
            var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
            var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
            log.LogInformation($"Key Vault Name: {keyVaultName}");
            log.LogInformation($"Secret Name: {secretName}");
            log.LogInformation($"Secret Version: {secretVersion}");

            SeretRotator.RotateSecret(log, secretName, secretVersion, keyVaultName);
        }
}
```

#### <a name="secret-rotation-logic"></a>秘密輪替邏輯
此輪替方法會從密碼讀取資料庫資訊、建立新的秘密版本，並以新的祕密更新資料庫：

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve current secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve secret info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //Create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check DB connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update DB password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
您可以在 [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function) 上找到完整的程式碼。

#### <a name="function-deployment"></a>函式部署

1. 從 [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip) 下載函式應用程式 zip 檔案。

1. 將 simplerotationsample-fn.zip 檔案上傳至 Azure Cloud Shell。

   ![上傳檔案](../media/rotate4.png)
1. 使用此 Azure CLI 命令將 zip 檔案部署至函式應用程式：

   ```azurecli
   az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
   ```

部署函式之後，您應該會在 simplerotation-fn 下看到兩個函式：

![SimpleRotation 和 SimpleRotationHttpTest 函式](../media/rotate5.png)

## <a name="add-an-event-subscription-for-the-secretnearexpiry-event"></a>新增 SecretNearExpiry 事件的事件訂閱

複製函式應用程式的 `eventgrid_extension` 金鑰：

   ![選取函數應用程式設定](../media/rotate6.png)

   ![eventgrid_extension 金鑰](../media/rotate7.png)

在下列命令中使用複製的 `eventgrid_extension` 金鑰和您的訂用帳戶識別碼，為 `SecretNearExpiry` 事件建立事件方格訂閱：

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

## <a name="add-the-secret-to-key-vault"></a>將秘密新增至 Key Vault
設定存取原則，將「管理秘密」權限授與使用者：

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

使用包含 SQL 資料庫資料來源和使用者識別碼的標籤，建立新的秘密。 包含設為明天到期的到期日。

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

建立具有簡短到期日的祕密，系統會立即發佈 `SecretNearExpiry` 事件，進而觸發函式進行秘密輪替。

## <a name="test-and-verify"></a>測試和驗證
幾分鐘後，`sqluser` 秘密應該就會自動輪替。

若要確認密碼是否已輪替，請移至 **Key Vault** > **秘密**：

![移至秘密](../media/rotate8.png)

開啟 **sqluser** 秘密，並檢視原始和已輪替的版本：

![開啟 sqluser 秘密](../media/rotate9.png)

### <a name="create-a-web-app"></a>建立 Web 應用程式

若要確認 SQL 認證，請建立 Web 應用程式。 此 Web 應用程式會從 Key Vault 中取得秘密、從祕密中解壓縮 SQL 資料庫資訊和認證，並測試 SQL 的連線。

Web 應用程式需要以下元件：
- 具有系統管理身分識別的 Web 應用程式
- 透過 Web 應用程式受控識別存取 Key Vault 所含秘密的存取原則

1. 選取 Azure 範本部署連結：
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. 選取 [simplerotation] 資源群組。
1. 選取 [購買]。

### <a name="deploy-the-web-app"></a>部署 Web 應用程式

您可以在 [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp) 上找到 Web 應用程式的原始程式碼。

若要部署 Web 應用程式，請完成下列步驟：

1. 從 [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip) 下載函式應用程式 zip 檔案。
1. 將 simplerotationsample-app.zip 檔案上傳至 Azure Cloud Shell。
1. 使用此 Azure CLI 命令將 zip 檔案部署至函式應用程式：

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

### <a name="open-the-web-app"></a>開啟 Web 應用程式

移至已部署的應用程式，然後選取 URL：
 
![選取 URL](../media/rotate10.png)

當應用程式在瀏覽器中開啟時，您會看到 [產生的密碼值] 以及值為 *true* 的 [資料庫已連線]。

## <a name="learn-more"></a>深入了解

- 概觀：[使用 Azure 事件方格監視 Key Vault (預覽)](../general/event-grid-overview.md)
- 如何：[在金鑰保存庫祕密變更時收到電子郵件](../general/event-grid-logicapps.md)
- [Azure Key Vault 的 Azure 事件方格事件結構描述 (預覽)](../../event-grid/event-schema-key-vault.md)
