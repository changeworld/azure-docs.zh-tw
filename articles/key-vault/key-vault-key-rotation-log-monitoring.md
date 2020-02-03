---
title: 使用端對端金鑰輪替和稽核設定 Azure 金鑰保存庫 | Microsoft Docs
description: 使用本操作指南可協助您設定金鑰輪替和監視金鑰保存庫記錄。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: a0aa20a8d1ddecfe401a4e099a4f298971779501
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720107"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>使用金鑰輪替和稽核設定 Azure Key Vault

## <a name="introduction"></a>簡介

當您具有金鑰保存庫之後，即可開始用它來儲存金鑰和秘密。 應用程式不再需要保存您的金鑰或秘密，而是可視需要從保存庫要求取得。 金鑰保存庫可讓您更新金鑰和密碼，而不會影響應用程式的行為，這會開啟您的金鑰和密碼管理的各種可能性。

>[!IMPORTANT]
> 本文中的範例僅供說明之用。 它們不適合用于生產環境使用。 

本文將逐步解說：

- 使用 Azure Key Vault 來儲存秘密的範例。 在本文中，儲存的密碼是應用程式所存取的 Azure 儲存體帳戶金鑰。 
- 如何執行該儲存體帳戶金鑰的排程輪替。
- 如何監視金鑰保存庫的 audit 記錄，並在提出未預期的要求時引發警示。

> [!NOTE]
> 本文不會詳細說明金鑰保存庫的初始設定。 如需此資訊，請參閱[什麼是 Azure Key Vault？](key-vault-overview.md)。 如需跨平臺命令列介面的指示，請參閱[使用 Azure CLI 管理 Key Vault](key-vault-manage-with-cli2.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>設定金鑰保存庫

若要讓應用程式能夠從金鑰保存庫擷取密碼，您必須先建立此密碼，並上傳至保存庫。

開始 Azure PowerShell 工作階段，並使用下列命令登入您的 Azure 帳戶：

```powershell
Connect-AzAccount
```

在快顯瀏覽器視窗中，輸入您 Azure 帳戶的使用者名稱和密碼。 PowerShell 會取得與此帳戶相關聯的所有訂用帳戶。 PowerShell 使用預設第一個訂用帳戶。

如果您有多個訂用帳戶，您可能必須指定用來建立金鑰保存庫的那一個訂用帳戶。 輸入下列命令以查看您帳戶的訂用帳戶：

```powershell
Get-AzSubscription
```

若要指定與您要記錄之金鑰保存庫相關聯的訂用帳戶，請輸入：

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

因為本文會示範如何將儲存體帳戶金鑰儲存為密碼，所以您必須取得該儲存體帳戶金鑰。

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

在抓取您的秘密（在此案例中，您的儲存體帳戶金鑰）之後，您必須將該金鑰轉換成安全字串，然後在金鑰保存庫中建立具有該值的秘密。

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

接下來，取得您建立之密碼的 URI。 在稍後的步驟中，您將需要此 URI，以呼叫金鑰保存庫並抓取您的密碼。 執行下列 PowerShell 命令，並記下識別碼值，也就是密碼的 URI：

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>設定範例應用程式

現在您已儲存密碼，您可以在執行幾個步驟之後，使用程式碼來抓取並使用它。

首先，您必須向 Azure Active Directory 註冊應用程式。 然後告訴 Key Vault 您的應用程式資訊，讓它可以允許來自您應用程式的要求。

> [!NOTE]
> 應用程式必須建立在與金鑰保存庫相同的 Azure Active Directory 租用戶上。

1. 開啟**Azure Active Directory**。
2. 選取 [應用程式註冊]。 
3. 選取 [**新增應用程式註冊**]，將應用程式新增至 Azure Active Directory。

    ![在 Azure Active Directory 中開啟應用程式](./media/keyvault-keyrotation/azure-ad-application.png)

4. 在 [**建立**] 底下，將 [應用程式類型] 保留為 [ **Web 應用程式/API** ]，並為應用程式命名。 為您的應用程式提供登**入 URL**。 此 URL 可以是您想要用於此示範的任何專案。

    ![建立應用程式註冊](./media/keyvault-keyrotation/create-app.png)

5. 將應用程式新增至 Azure Active Directory 之後，[應用程式] 頁面隨即開啟。 選取 [**設定**]，然後選取 [**屬性**]。 複製 [應用程式識別碼] 值。 您在後續的步驟中將會用到它。

接下來，為您的應用程式產生金鑰，使其可以與 Azure Active Directory 互動。 若要建立金鑰，請選取 [**設定**] 底下的 [**金鑰**]。 請記下 Azure Active Directory 應用程式的新產生金鑰。 您在後續的步驟中將需要此資訊。 離開此區段之後，將無法使用金鑰。 

![Azure Active Directory 應用程式金鑰](./media/keyvault-keyrotation/create-key.png)

在您建立從應用程式到金鑰保存庫的任何呼叫之前，您必須告訴 key vault 有關您的應用程式及其許可權。 下列命令會使用您的 Azure Active Directory 應用程式中的保存庫名稱和應用程式識別碼，**將存取權**授與您的金鑰保存庫。

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

您現在已經準備好開始建立應用程式呼叫。 在您的應用程式中，您必須安裝與 Azure Key Vault 和 Azure Active Directory 互動所需的 NuGet 套件。 從 Visual Studio Package Manager Console 輸入下列命令。 在撰寫本文時，目前版本的 Azure Active Directory 套件是3.10.305231913，因此請確認最新版本，並視需要進行更新。

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

在應用程式程式碼中，建立類別以保有Azure Active Directory 驗證的方法。 在此範例中，該類別稱為 **Utils**。 加入下列 `using` 陳述式：

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

接著，新增下列方法，以從 Azure Active Directory 擷取 JWT 權杖。 針對可維護性，您可能會想要將硬式編碼的字串值移到您的 web 或應用程式設定中。

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

新增必要的程式碼，以呼叫金鑰保存庫並擷取密碼值。 首先，您必須加入下列 `using` 語句：

```csharp
using Microsoft.Azure.KeyVault;
```

新增方法呼叫來叫用金鑰保存庫，並擷取密碼。 在這個方法中，您會提供您在先前步驟中儲存的密碼 URI。 請注意，使用您先前建立的**Utils**類別中的**GetToken**方法。

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

當您執行應用程式時，您現在應該向 Azure Active Directory 進行驗證，然後從 Azure 金鑰保存庫中擷取密碼值。

## <a name="key-rotation-using-azure-automation"></a>使用 Azure 自動化的金鑰輪替

> [!IMPORTANT]
> Azure 自動化 runbook 仍需要使用 `AzureRM` 模組。

您現在已準備好為您儲存為 Key Vault 秘密的值設定旋轉策略。 秘密可以透過數種方式旋轉：

- 做為手動程式的一部分
- 使用 API 呼叫以程式設計方式
- 透過 Azure 自動化腳本

基於本文的目的，您將使用 PowerShell 與 Azure 自動化結合，以變更 Azure 儲存體帳戶的存取金鑰。 接著，您將使用該新的金鑰來更新金鑰保存庫密碼。

若要允許 Azure 自動化在金鑰保存庫中設定秘密值，您必須取得名為**AzureRunAsConnection**之連線的用戶端識別碼。 當您建立 Azure 自動化實例時，就會建立此連接。 若要尋找此識別碼，請從您的 Azure 自動化實例中選取 [**資產**]。 從**該處選取 [** 連線]，然後選取 [ **AzureRunAsConnection** ] 服務主體。 記下**ApplicationId**值。

![Azure 自動化用戶端識別碼](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

在 [**資產**] 中，選取 [**模組**]。 選取 [資源**庫**]，然後搜尋並匯入下列每個模組的更新版本：

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> 在撰寫本文的當時，就下列指令碼而言，需要更新的只有先前所述的模組。 如果您的自動化作業失敗，請確認您已匯入所有必要的模組及其相依性。

在您抓取 Azure 自動化連線的應用程式識別碼之後，您必須告知金鑰保存庫，此應用程式有權更新保存庫中的秘密。 使用下列 PowerShell 命令：

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

接下來，選取您 Azure 自動化實例底下的 [ **runbook** ]，然後選取 [**新增 Runbook**]。 選取 [快速建立]。 為您的 runbook 命名，然後選取 [ **PowerShell** ] 做為 runbook 類型。 您可以加入描述。 最後，選取 [建立]。

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

在 [編輯器] 窗格中，選取 [**測試窗格]** 來測試您的腳本。 執行腳本而不發生錯誤之後，您可以選取 [**發佈**]，然後在 [runbook 設定] 窗格中套用 runbook 的排程。

## <a name="key-vault-auditing-pipeline"></a>金鑰保存庫稽核管線

當您設定金鑰保存庫時，您可以開啟稽核功能，以收集對金鑰保存庫提出的存取要求的記錄。 這些記錄會儲存在指定的 Azure 儲存體帳戶中，而且可以提取、監視和分析。 下列案例使用 Azure 函式、Azure 邏輯應用程式，以及金鑰保存庫 audit 記錄來建立管線，當應用程式不符合 web 應用程式的應用程式識別碼時，會從保存庫中抓取秘密，以傳送電子郵件。

首先，您必須在金鑰保存庫上啟用記錄功能。 使用下列 PowerShell 命令。 （您可以在本文中查看[關於金鑰保存庫-記錄](key-vault-logging.md)的完整詳細資料）。

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

啟用記錄功能之後，就會開始將 audit 記錄儲存在指定的儲存體帳戶中。 這些記錄包含有關金鑰保存庫存取方式、時間和存取者的事件。

> [!NOTE]
> 在金鑰保存庫作業 10 分鐘後，您就可以存取記錄資訊。 它通常會比這麼快。

下一步是 [建立 Azure 服務匯流排佇列](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)。 此佇列是金鑰保存庫 audit 記錄推送的位置。 當審核記錄訊息在佇列中時，邏輯應用程式會挑選它們，並對其採取動作。 使用下列步驟建立服務匯流排實例：

1. 建立服務匯流排命名空間（如果您已經有想要使用的命名空間，請跳至步驟2）。
2. 流覽至 Azure 入口網站中的服務匯流排實例，然後選取您想要在其中建立佇列的命名空間。
3. 選取 [**建立資源**] > [**企業整合**] [ > **服務匯流排**]，然後輸入必要的詳細資料。
4. 選取命名空間，然後選取 [**連接資訊**]，以尋找服務匯流排連接資訊。 下一節將需要此資訊。

接下來，[建立 Azure 函式](../azure-functions/functions-create-first-azure-function.md)以輪詢儲存體帳戶中的金鑰保存庫記錄，並挑選新的事件。 此函式會依排程觸發。

若要建立 Azure 函數應用程式，請選取 **建立資源**，在 marketplace 中搜尋**函數應用程式**，然後選取 **建立**。 在建立期間，您可以使用現有的主控方案，或建立新的方案。 您也可以選擇動態裝載。 如需 Azure Functions 之裝載選項的詳細資訊，請參閱[如何調整 Azure Functions](../azure-functions/functions-scale.md)。

建立 Azure 函數應用程式之後，請移至它，然後選取該語言的**計時器**案例和**C\#** 。 然後選取 [**建立此函數**]。

![Azure Functions 啟動刀鋒視窗](./media/keyvault-keyrotation/Azure_Functions_Start.png)

在 [開發] 索引標籤上，以下列內容取代 run.csx 程式碼︰

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
> 變更上述程式碼中的變數，以指向將金鑰保存庫記錄寫入其中的儲存體帳戶、您稍早建立的服務匯流排實例，以及金鑰保存庫儲存體記錄的特定路徑。

此函式會挑選金鑰保存庫記錄所寫入到的儲存體帳戶中最新的記錄、取得來自該檔案的最新事件，並推送到服務匯流排佇列。 

由於單一檔案可以有多個事件，因此您應該建立函式也會查看的 sync .txt 檔案，以判斷所挑選的最後一個事件的時間戳記。 使用此檔案可確保您不會多次推送相同的事件。 

同步 .txt 檔案包含最後發生之事件的時間戳記。 載入記錄檔時，必須根據其時間戳記排序，以確保正確排序。

針對此函式，我們會參考不在 Azure Functions 中現成可用的幾個額外程式庫。 若要包含這些程式庫，我們需要 Azure Functions 使用 NuGet 來提取它們。 在 [程式**代碼**] 方塊中，選取 [ **View Files**]。

![[View files] 選項](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

使用下列內容新增名為 [專案] 的檔案：

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

選取 [**儲存**] 之後，Azure Functions 將會下載所需的二進位檔。

切換至 [整合] 索引標籤，然後為計時器參數提供有意義的名稱以在函式內使用。 在上述程式碼中，函式預期計時器會呼叫*名稱為 mytimer*。 指定計時器的[CRON 運算式](../app-service/webjobs-create.md#CreateScheduledCRON)，如下所示： `0 * * * * *`。 這個運算式會使函式每分鐘執行一次。

在相同的 [**整合**] 索引標籤上，新增類型為 [ **Azure Blob 儲存體**] 的輸入。 此輸入會指向包含函式所查看之最後一個事件時間戳記的 sync .txt 檔案。 在函式中，會使用參數名稱來存取此輸入。 在上述程式碼中，Azure Blob 儲存體輸入預期參數名稱必須是*inputBlob*。 選取將在其中尋找同步 .txt 檔案的儲存體帳戶（可能是相同或不同的儲存體帳戶）。 在 [路徑] 欄位中，以 `{container-name}/path/to/sync.txt`格式提供檔案的路徑。

新增「 **Azure Blob 儲存體**」類型的輸出。 此輸出會指向您在輸入中定義的 sync .txt 檔案。 函式會使用這個輸出來寫入最後一個事件的時間戳記。 前面的程式碼預期此參數名稱為 outputBlob。

函式現在已就緒。 請務必切換回 [開發] 索引標籤並儲存程式碼。 檢查 [輸出] 視窗中是否有任何編譯錯誤，並視需要加以更正。 如果程式碼進行編譯，則程式碼現在應該每分鐘檢查一次金鑰保存庫記錄，並將任何新事件推送到已定義的服務匯流排佇列中。 您應該會在每次觸發函式時看到記錄資訊寫入到 [記錄檔] 視窗。

### <a name="azure-logic-app"></a>Azure 邏輯應用程式

接下來，您必須建立 Azure 邏輯應用程式，以挑選函式推送至服務匯流排佇列的事件、剖析內容，以及根據符合的條件傳送電子郵件。

選取 [**建立資源**] > [**整合** > **邏輯應用程式**] 來[建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

建立邏輯應用程式之後，請移至它，然後選取 [**編輯**]。 在邏輯應用程式編輯器中，選取 [**服務匯流排佇列**]，然後輸入您的服務匯流排認證，以將其連接到佇列。

![Azure 邏輯應用程式服務匯流排](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

選取 [新增條件]。 在 [條件] 中，切換至 [advanced editor]，然後輸入下列程式碼。 將*APP_ID*取代為您 web 應用程式的實際應用程式識別碼：

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

如果來自傳入事件（也就是服務匯流排訊息的主體）的*appid*不是應用程式的*appid* ，則此運算式基本上會傳回**false** 。

現在，在 [**如果否，不執行任何**動作] 底下建立一個動作。

![Azure Logic Apps 選擇動作](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

在 [動作] 中，選取 [ **Office 365-傳送電子郵件**]。 填寫欄位，以建立定義的條件傳回 **false** 時要傳送的電子郵件。 如果您沒有 Office 365，請尋找可達到相同結果的替代方案。

您現在有端對端管線，會每分鐘尋找一次新的金鑰保存庫 audit 記錄。 它會將找到的新記錄推送至服務匯流排的佇列。 新訊息放入佇列時就會觸發邏輯應用程式。 如果事件內的*appid*不符合呼叫應用程式的應用程式識別碼，則會傳送電子郵件。
