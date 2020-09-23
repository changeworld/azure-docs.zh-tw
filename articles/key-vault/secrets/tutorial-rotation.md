---
title: 具有一組驗證認證 (儲存在 Azure Key Vault 中) 的資源輪替教學課程
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
ms.custom: devx-track-csharp
ms.openlocfilehash: c2d1a46a35ef38791b6a3b47c300aa1b47f70324
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90086850"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>針對使用一組驗證認證的資源，將秘密的輪替自動化

向 Azure 服務進行驗證的最佳方式是使用[受控識別](../general/authentication.md)，但在某些情況下並無法使用此選項。 在這類情況下，應使用存取金鑰或祕密。 您應該定期輪替存取金鑰或祕密。

本教學課程示範如何針對使用一組驗證認證的資料庫和服務自動執行定期的秘密輪替。 具體而言，此教學課程會使用 Azure 事件方格通知所觸發的函式，來輪替儲存在 Azure Key Vault 中的 SQL Server 密碼：

![輪替解決方案圖表](../media/rotate-1.png)

1. 在祕密到期日的 30 天前，Key Vault 會將「即將到期」事件發佈至事件方格。
1. 事件方格會檢查事件訂閱，並使用 HTTP POST 呼叫訂閱此事件的函式應用程式端點。
1. 函式應用程式會接收秘密資訊、產生新的隨機密碼，並使用 Key Vault 中的新密碼建立秘密的新版本。
1. 函式應用程式會以新密碼更新 SQL Server。

> [!NOTE]
> 步驟 3 和 4 之間可能會有延遲。 在這段期間內，Key Vault 中的祕密無法向 SQL Server 進行驗證。 若有任何步驟失敗，事件方格會重試 2 小時。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Azure 金鑰保存庫
* SQL Server

如果您沒有現有的 Key Vault 和 SQL Server，可以使用下列部署連結：

[![顯示標示為「部署至 Azure」的按鈕影像。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. 在 [資源群組] 下方，選取 [新建]。 將群組命名為 **akvrotation**。
1. 在 [Sql 管理員登入] 底下，輸入 Sql 管理員登入名稱。 
1. 選取 [檢閱 + 建立]。
1. 選取 [建立]

    ![建立資源群組](../media/rotate-2.png)

您現在會有 Key Vault 和 SQL Server 執行個體。 您可以執行下列命令，在 Azure CLI 中驗證這項設定：

```azurecli
az resource list -o table
```

結果看起來會像下列輸出：

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv          akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotation-sql         akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql/master  akvrotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-and-deploy-sql-server-password-rotation-function"></a>建立及部署 SQL Server 密碼輪替函式

接下來，使用系統管理的身分識別以及其他必要元件建立函數應用程式，並部署 SQL Server 密碼輪替函式

函式應用程式需要以下元件：
- Azure App Service 方案
- 具有 Sql 密碼輪替函式 (含事件觸發程序和 http 觸發程序) 的函數應用程式 
- 函數應用程式觸發程式管理所需的儲存體帳戶
- 供函數應用程式受控識別存取 Key Vault 中秘密的存取原則
- **SecretNearExpiry** 事件的 EventGrid 事件訂用帳戶

1. 選取 Azure 範本部署連結： 

   [![顯示標示為「部署至 Azure」的按鈕影像。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json)

1. 在 [資源群組] 清單中，選取 [akvrotation]。
1. 在 [Sql Server 名稱] 中，輸入要輪替密碼的 Sql Server 名稱
1. 在 [Key Vault 名稱] 中，輸入金鑰保存庫名稱
1. 在 [函數應用程式名稱] 中，輸入函數應用程式名稱
1. 在 [秘密名稱] 中，輸入要儲存密碼的秘密名稱
1. 在 [存放庫 URL] 中，輸入函式程式碼 GitHub 位置 ( **https://github.com/jlichwa/KeyVault-Rotation-SQLPassword-Csharp.git** )
1. 選取 [檢閱 + 建立]。
1. 選取 [建立]。

   ![選取 [檢閱 + 建立]](../media/rotate-3.png)

完成上述步驟之後，您將會有儲存體帳戶、伺服器陣列和函式應用程式。 您可以執行下列命令，在 Azure CLI 中驗證這項設定：

```azurecli
az resource list -o table
```

結果看起來會像下列輸出：

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation       eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation       eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation       eastus      Microsoft.Sql/servers/databases
cfogyydrufs5wazfunctions akvrotation       eastus      Microsoft.Storage/storageAccounts
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/serverFarms
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/sites
akvrotation-fnapp        akvrotation       eastus      Microsoft.insights/components
```

如需如何建立函式應用程式和使用受控識別來存取 Key Vault 的相關資訊，請參閱[從 Azure 入口網站建立函式應用程式](/azure/azure-functions/functions-create-function-app-portal)、[如何使用 App Service 和 Azure Functions 的受控識別](/azure/app-service/overview-managed-identity)以及[使用 Azure 入口網站指派 Key Vault 存取金鑰](../general/assign-access-policy-portal.md)。

### <a name="rotation-function"></a>輪替函式
前一個步驟中部署的函式會透過更新 Key Vault 和 SQL 資料庫，使用事件來觸發密碼輪替。 

#### <a name="function-trigger-event"></a>函式觸發事件

此函式會讀取事件資料並執行輪替邏輯：

```csharp
public static class SimpleRotationEventHandler
{
   [FunctionName("AKVSQLRotation")]
   public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
   {
      log.LogInformation("C# Event trigger function processed a request.");
      var secretName = eventGridEvent.Subject;
      var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
      var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
      log.LogInformation($"Key Vault Name: {keyVaultName}");
      log.LogInformation($"Secret Name: {secretName}");
      log.LogInformation($"Secret Version: {secretVersion}");

      SecretRotator.RotateSecret(log, secretName, keyVaultName);
   }
}
```

#### <a name="secret-rotation-logic"></a>秘密輪替邏輯
此輪替方法會從密碼讀取資料庫資訊、建立新的秘密版本，並以新的祕密更新資料庫：

```csharp
    public class SecretRotator
    {
        private const string CredentialIdTag = "CredentialId";
        private const string ProviderAddressTag = "ProviderAddress";
        private const string ValidityPeriodDaysTag = "ValidityPeriodDays";

        public static void RotateSecret(ILogger log, string secretName, string keyVaultName)
        {
            //Retrieve Current Secret
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";
            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
            KeyVaultSecret secret = client.GetSecret(secretName);
            log.LogInformation("Secret Info Retrieved");

            //Retrieve Secret Info
            var credentialId = secret.Properties.Tags.ContainsKey(CredentialIdTag) ? secret.Properties.Tags[CredentialIdTag] : "";
            var providerAddress = secret.Properties.Tags.ContainsKey(ProviderAddressTag) ? secret.Properties.Tags[ProviderAddressTag] : "";
            var validityPeriodDays = secret.Properties.Tags.ContainsKey(ValidityPeriodDaysTag) ? secret.Properties.Tags[ValidityPeriodDaysTag] : "";
            log.LogInformation($"Provider Address: {providerAddress}");
            log.LogInformation($"Credential Id: {credentialId}");

            //Check Service Provider connection
            CheckServiceConnection(secret);
            log.LogInformation("Service  Connection Validated");
            
            //Create new password
            var randomPassword = CreateRandomPassword();
            log.LogInformation("New Password Generated");

            //Add secret version with new password to Key Vault
            CreateNewSecretVersion(client, secret, randomPassword);
            log.LogInformation("New Secret Version Generated");

            //Update Service Provider with new password
            UpdateServicePassword(secret, randomPassword);
            log.LogInformation("Password Changed");
            log.LogInformation($"Secret Rotated Successfully");
        }
}
```
您可以在 [GitHub](https://github.com/jlichwa/KeyVault-Rotation-SQLPassword-Csharp) 上找到完整的程式碼。

## <a name="add-the-secret-to-key-vault"></a>將秘密新增至 Key Vault
設定存取原則，將「管理秘密」權限授與使用者：

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

建立新祕密，其中標籤包含 SQL Server 資源識別碼、SQL Server 登入名稱，以及祕密的有效期間 (以天為單位)。 提供密碼的名稱、SQL 資料庫中的初始密碼 (我們範例中的 "Simple123")，並包含設為明天到期的到期日。

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqlPassword --vault-name akvrotation-kv --value "Simple123" --tags "CredentialId=sqlAdmin" "ProviderAddress=<sql-database-resource-id>" "ValidityPeriodDays=90" --expires $tomorrowDate
```

建立具有簡短到期日的祕密，系統會在 15 分鐘內發佈 `SecretNearExpiry` 事件，進而觸發函式進行秘密輪替。

## <a name="test-and-verify"></a>測試和驗證

若要確認密碼是否已輪替，請移至 **Key Vault** > **秘密**：

![移至秘密](../media/rotate-8.png)

開啟 **sqlPassword** 秘密，並檢視原始和已輪替的版本：

![開啟 sqluser 秘密](../media/rotate-9.png)

### <a name="create-a-web-app"></a>建立 Web 應用程式

若要確認 SQL 認證，請建立 Web 應用程式。 此 Web 應用程式會從 Key Vault 中取得秘密、從祕密中解壓縮 SQL 資料庫資訊和認證，並測試 SQL 的連線。

Web 應用程式需要以下元件：
- 具有系統管理身分識別的 Web 應用程式
- 透過 Web 應用程式受控識別存取 Key Vault 所含秘密的存取原則

1. 選取 Azure 範本部署連結： 

   [![顯示標示為「部署至 Azure」的按鈕影像。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-SQLPassword-Csharp-WebApp%2Fmaster%2Farm-templates%2FWeb-App%2Fazuredeploy.json)

1. 選取 **akvrotation** 資源群組。
1. 在 [Sql Server 名稱] 中，輸入要輪替密碼的 Sql Server 名稱
1. 在 [Key Vault 名稱] 中，輸入金鑰保存庫名稱
1. 在 [秘密名稱] 中，輸入儲存密碼的秘密名稱
1. 在 [存放庫 URL] 中，輸入 Web 應用程式碼 GitHub 位置 ( **https://github.com/jlichwa/KeyVault-Rotation-SQLPassword-Csharp-WebApp.git** )
1. 選取 [檢閱 + 建立]。
1. 選取 [建立]。


### <a name="open-the-web-app"></a>開啟 Web 應用程式

移至已部署的應用程式 URL：
 
https://akvrotation-app.azurewebsites.net/

當應用程式在瀏覽器中開啟時，您會看到 [產生的密碼值] 以及值為 *true* 的 [資料庫已連線]。

## <a name="learn-more"></a>深入了解

- 教學課程：[具有兩組認證的資源輪替](tutorial-rotation-dual.md)
- 概觀：[使用 Azure 事件方格監視 Key Vault](../general/event-grid-overview.md)
- 如何：[在金鑰保存庫祕密變更時收到電子郵件](../general/event-grid-logicapps.md)
- [Azure Key Vault 的 Azure 事件方格事件結構描述](../../event-grid/event-schema-key-vault.md)
