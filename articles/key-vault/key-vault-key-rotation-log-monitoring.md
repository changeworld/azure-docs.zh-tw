---
title: 使用端對端金鑰輪替和稽核設定 Azure 金鑰保存庫 | Microsoft Docs
description: 使用此操作指南可説明您設置金鑰輪換並監視金鑰保存庫日誌。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 6962a264787bd8a55b6f6a2ebdb6eeb615c33d5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218408"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>使用金鑰輪替和稽核設定 Azure Key Vault

## <a name="introduction"></a>簡介

當您具有金鑰保存庫之後，即可開始用它來儲存金鑰和秘密。 應用程式不再需要保存您的金鑰或秘密，而是可視需要從保存庫要求取得。 金鑰保存庫允許您更新金鑰和機密，而不會影響應用程式的行為，這為金鑰和金鑰管理開闢了廣泛的可能性。

本文將介紹如何實現存儲帳戶金鑰的定期輪換、監視金鑰保存庫稽核記錄以及發出意外請求時發出警報。 

您必須首先使用您選擇的方法創建金鑰保存庫：

- [使用 Azure CLI 從 Azure Key Vault 設定及擷取祕密](quick-create-cli.md)
- [使用 Azure PowerShell 從 Azure 金鑰保存庫設置和檢索機密](quick-create-powershell.md)
- [使用 Azure 門戶從 Azure 金鑰保存庫設置和檢索機密](quick-create-portal.md)


## <a name="store-a-secret"></a>存儲機密

若要讓應用程式能夠從金鑰保存庫擷取密碼，您必須先建立此密碼，並上傳至保存庫。

開始 Azure PowerShell 工作階段，並使用下列命令登入您的 Azure 帳戶：

```powershell
Connect-AzAccount
```

在彈出瀏覽器視窗中，輸入 Azure 帳戶的使用者名和密碼。 PowerShell 會取得與此帳戶相關聯的所有訂用帳戶。 PowerShell 使用預設第一個訂用帳戶。

如果您有多個訂用帳戶，您可能必須指定用來建立金鑰保存庫的那一個訂用帳戶。 輸入下列命令以查看您帳戶的訂用帳戶：

```powershell
Get-AzSubscription
```

要指定與要記錄的金鑰保存庫關聯的訂閱，請輸入：

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

因為本文會示範如何將儲存體帳戶金鑰儲存為密碼，所以您必須取得該儲存體帳戶金鑰。

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

檢索機密（在本例中為存儲帳戶金鑰）後，必須將該金鑰轉換為安全字串，然後在金鑰保存庫中創建具有該值的機密。

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

接下來，取得您建立之密碼的 URI。 在後續步驟中，您需要此 URI 來調用金鑰保存庫並檢索您的機密。 運行以下 PowerShell 命令並記下 ID 值，該 ID 值是機密的 URI：

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>設定範例應用程式

現在您已存儲了機密，您可以在執行更多步驟後使用代碼來檢索和使用它。

首先，您必須將應用程式註冊到 Azure 活動目錄。 然後告訴金鑰保存庫您的應用程式資訊，以便它可以允許來自應用程式的請求。

> [!NOTE]
> 應用程式必須建立在與金鑰保存庫相同的 Azure Active Directory 租用戶上。

1. 打開**Azure 活動目錄**。
2. 選擇**應用註冊**。 
3. 選擇 **"新建應用程式註冊**"以將應用程式添加到 Azure 活動目錄。

    ![在 Azure Active Directory 中開啟應用程式](./media/keyvault-keyrotation/azure-ad-application.png)

4. 在 **"創建**"下，將應用程式類型保留為**Web 應用/API，** 並為您的應用程式指定名稱。 為您的應用程式提供**登錄 URL。** 此 URL 可以是本演示所需的任何內容。

    ![建立應用程式註冊](./media/keyvault-keyrotation/create-app.png)

5. 將應用程式添加到 Azure 活動目錄後，將打開應用程式頁。 選擇 **"設置**"，然後選擇 **"屬性**"。 複製 [應用程式識別碼]**** 值。 在後面的步驟中，您將需要它。

接下來，為應用程式生成金鑰，以便它可以與 Azure 活動目錄進行交互。 要創建鍵，請在 **"設置"** 下選擇 **"鍵**"。 記下 Azure 活動目錄應用程式新生成的金鑰。 您在後續的步驟中將需要此資訊。 離開本節後，該金鑰將不可用。 

![Azure 活動目錄應用鍵](./media/keyvault-keyrotation/create-key.png)

在將應用程式的任何調用建立到金鑰保存庫之前，必須告訴金鑰保存庫有關應用程式及其許可權。 以下命令使用 Azure 活動目錄應用中的保存庫名稱和應用程式 ID 來授予應用程式**獲取**對金鑰保存庫的存取權限。

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

現在，您已準備好開始構建應用程式調用。 在應用程式中，必須安裝與 Azure 金鑰保存庫和 Azure 活動目錄交互所需的 NuGet 包。 從 Visual Studio Package Manager Console 輸入下列命令。 在撰寫本文時，Azure 活動目錄包的當前版本為 3.10.30.305231913，因此請根據需要確認最新版本並進行更新。

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

在應用程式程式碼中，建立類別以保有Azure Active Directory 驗證的方法。 在此示例中，該類稱為**Utils**。 加入下列 `using` 陳述式：

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

接著，新增下列方法，以從 Azure Active Directory 擷取 JWT 權杖。 為便於維護，您可能希望將硬編碼字串值移動到 Web 或應用程式佈建中。

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

新增必要的程式碼，以呼叫金鑰保存庫並擷取密碼值。 首先，必須添加以下`using`語句：

```csharp
using Microsoft.Azure.KeyVault;
```

新增方法呼叫來叫用金鑰保存庫，並擷取密碼。 在這個方法中，您會提供您在先前步驟中儲存的密碼 URI。 請注意，使用以前創建的**Utils**類中的**GetToken**方法。

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

當您執行應用程式時，您現在應該向 Azure Active Directory 進行驗證，然後從 Azure 金鑰保存庫中擷取密碼值。

## <a name="key-rotation-using-azure-automation"></a>使用 Azure 自動化的金鑰輪替

> [!IMPORTANT]
> Azure 自動化運行簿仍然需要使用該`AzureRM`模組。

現在，您可以為存儲為金鑰保存庫機密的值設置輪換策略。 機密可以通過多種方式旋轉：

- 作為手動過程的一部分
- 使用 API 呼叫以程式設計方式
- 通過 Azure 自動化腳本

在本文中，您將使用 PowerShell 與 Azure 自動化相結合來更改 Azure 存儲帳戶的訪問金鑰。 然後，您將使用該新金鑰更新金鑰保存庫金鑰。

要允許 Azure 自動化在金鑰保存庫中設置機密值，必須獲取名為**AzureRunAsConnection**的連接的用戶端 ID。 此連接是在您建立 Azure 自動化實例時創建的。 要查找此 ID，請從 Azure 自動化實例中選擇 **"資源**"。 在此處選擇**連接**，然後選擇 Azure **RunAsConnection**服務主體。 記下**應用程式 Id**值。

![Azure 自動化用戶端識別碼](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

在 **"資產**"中，選擇**模組**。 選擇**庫**，然後搜索並導入以下每個模組的更新版本：

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> 在撰寫本文的當時，就下列指令碼而言，需要更新的只有先前所述的模組。 如果自動化作業失敗，請確認已導入所有必要的模組及其依賴項。

檢索 Azure 自動化連接的應用程式 ID 後，必須告訴金鑰保存庫此應用程式具有更新保存庫中機密的許可權。 使用下列 PowerShell 命令：

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

接下來，在 Azure 自動化實例下選擇**Runbook，** 然後選擇 **"添加 Runbook**"。 選擇**快速建立**。 命名您的 Runbook，然後選擇**PowerShell**作為 Runbook 類型。 您可以添加說明。 最後，選取 [建立]****。

![建立 Runbook](./media/keyvault-keyrotation/Create_Runbook.png)

在新的 Runbook 的編輯器窗格中，貼上下列 PowerShell 指令碼：

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Optionally you can set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

在編輯器窗格中，選擇 **"測試"窗格**以測試腳本。 腳本運行後沒有錯誤，您可以選擇 **"發佈**"，然後在 Runbook 配置窗格中應用 Runbook 的計畫。

## <a name="key-vault-auditing-pipeline"></a>金鑰保存庫稽核管線

當您設定金鑰保存庫時，您可以開啟稽核功能，以收集對金鑰保存庫提出的存取要求的記錄。 這些日誌存儲在指定的 Azure 存儲帳戶中，可以提取、監視和分析。 以下方案使用 Azure 函數、Azure 邏輯應用和金鑰保存庫稽核記錄創建管道，當與 Web 應用的應用 ID 不匹配的應用從保存庫中檢索機密時，該管道會發送電子郵件。

首先，您必須在金鑰保存庫上啟用記錄功能。 使用以下 PowerShell 命令。 （您可以在本文中查看[有關金鑰保存庫日誌記錄](key-vault-logging.md)的完整詳細資訊。

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

啟用日誌記錄後，稽核記錄開始存儲在指定的存儲帳戶中。 這些記錄包含有關金鑰保存庫存取方式、時間和存取者的事件。

> [!NOTE]
> 在金鑰保存庫作業 10 分鐘後，您就可以存取記錄資訊。 它通常比這更快可用。

下一步是 [建立 Azure 服務匯流排佇列](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)。 此佇列是推送金鑰保存庫稽核記錄的位置。 當稽核記錄消息在佇列中時，邏輯應用會選取它們並對其進行操作。 使用以下步驟創建服務匯流排實例：

1. 創建服務匯流排命名空間（如果您已有要使用的命名空間，請跳到步驟 2）。
2. 流覽到 Azure 門戶中的服務匯流排實例，然後選擇要在其中創建佇列的命名空間。
3. 選擇 **"創建資源** > **企業集成** > **服務匯流排**"，然後輸入所需的詳細資訊。
4. 通過選擇命名空間，然後選擇**連接資訊**，查找服務匯流排連接資訊。 下一節需要此資訊。

接下來，[創建 Azure 函數](../azure-functions/functions-create-first-azure-function.md)以輪詢存儲帳戶中的關鍵保存庫日誌並拾取新事件。 此函數將按計劃觸發。

要創建 Azure 函數應用，請選擇 **"創建資源**"，在**市場搜索函數應用**，然後選擇"**創建**"。 在建立期間，您可以使用現有的主控方案，或建立新的方案。 您還可以選擇動態託管。 有關 Azure 函數的託管選項的詳細資訊，請參閱[如何縮放 Azure 函數](../azure-functions/functions-scale.md)。

創建 Azure 函數應用後，轉到它，然後選擇語言的**計時器**方案和**C。\# ** 然後選擇 **"創建此函數**"。

![Azure Functions 啟動刀鋒視窗](./media/keyvault-keyrotation/Azure_Functions_Start.png)

在 [開發]**** 索引標籤上，以下列內容取代 run.csx 程式碼︰

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didn't have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //Required to order by time as they might not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

> [!NOTE]
> 更改上述代碼中的變數，以指向編寫金鑰保存庫日誌的存儲帳戶、您之前創建的服務匯流排實例以及金鑰保存庫存儲日誌的特定路徑。

此函式會挑選金鑰保存庫記錄所寫入到的儲存體帳戶中最新的記錄、取得來自該檔案的最新事件，並推送到服務匯流排佇列。 

由於單個檔可以有多個事件，因此應創建一個 sync.txt 檔，該函數也查看該檔以確定拾取的最後一個事件的時間戳記。 使用此檔可確保不會多次推送同一事件。 

sync.txt 檔包含上次遇到的事件的時間戳記。 載入日誌時，必須根據時間戳記對日誌進行排序，以確保正確排序。

對於此功能，我們引用 Azure 函數中開箱即用的兩個附加庫。 要包括這些庫，我們需要 Azure 函數使用 NuGet 來提取它們。 在 **"代碼"** 框下，選擇 **"查看檔**"。

!["查看檔"選項](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

添加名為 project.json 的檔，包含以下內容：

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```

選擇 **"保存"** 後，Azure 函數將下載所需的二進位檔案。

切換至 [整合] **** 索引標籤，然後為計時器參數提供有意義的名稱以在函式內使用。 在前面的代碼中，函數期望計時器稱為*myTimer*。 為計時器指定[CRON 運算式](../app-service/webjobs-create.md#CreateScheduledCRON)，如下所示： `0 * * * * *`。 此運算式將導致函數每分鐘運行一次。

在同一 **"集成"** 選項卡上，添加 Azure **Blob 存儲**類型的輸入。 此輸入將指向 sync.txt 檔，該檔包含函數查看的最後一個事件的時間戳記。 將使用參數名稱在函數中訪問此輸入。 在前面的代碼中，Azure Blob 存儲輸入要求參數名稱為輸入*Blob*。 選擇 sync.txt 檔所在的存儲帳戶（它可以是相同的帳戶或不同的存儲帳戶）。 在路徑欄位中，以 格式`{container-name}/path/to/sync.txt`提供檔的路徑。

添加 Azure Blob**存儲**類型的輸出。 此輸出將指向您在輸入中定義的 sync.txt 檔。 函數使用此輸出來編寫查看的最後一個事件的時間戳記。 前面的程式碼預期此參數名稱為 outputBlob**。

函數現已準備就緒。 請確保切換回 **"開發"** 選項卡並保存代碼。 檢查輸出視窗是否有任何編譯錯誤，並根據需要進行更正。 如果代碼編譯，則代碼現在應該每分鐘檢查金鑰保存庫日誌，並將任何新事件推送到定義的服務匯流排佇列中。 您應該會在每次觸發函式時看到記錄資訊寫入到 [記錄檔] 視窗。

### <a name="azure-logic-app"></a>Azure 邏輯應用程式

接下來，必須創建一個 Azure 邏輯應用，該應用將拾取函數推送到服務匯流排佇列的事件，分析內容，並根據匹配的條件發送電子郵件。

通過選擇 **"創建資源** > **集成** > **邏輯應用**"來[創建邏輯](../logic-apps/quickstart-create-first-logic-app-workflow.md)應用。

創建邏輯應用後，轉到它並選擇 **"編輯**"。 在邏輯應用編輯器中，選擇**服務匯流排佇列**並輸入服務匯流排憑據以將其連接到佇列。

![Azure 邏輯應用程式服務匯流排](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

選取 [新增條件] ****。 在這種情況下，切換到高級編輯器並輸入以下代碼。 將*APP_ID*替換為 Web 應用的實際應用 ID：

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

如果傳入事件（即服務匯流排消息的正文）的*appid*不是應用的應用*ID，* 則此運算式實質上返回**false。**

現在，在 IF NO 下創建一個操作 **，執行"不執行"。**

![Azure 邏輯應用選擇操作](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

對於操作，請選擇**Office 365 - 發送電子郵件**。 填寫欄位，以建立定義的條件傳回 **false** 時要傳送的電子郵件。 如果沒有 Office 365，則查找實現相同結果的替代方法。

現在，您有一個端到端管道，每分鐘查找一次新的金鑰保存庫稽核記錄。 它將找到的新日誌推送到服務匯流排佇列。 新訊息放入佇列時就會觸發邏輯應用程式。 如果事件中的*appid*與調用應用程式的應用 ID 不匹配，它將發送電子郵件。
