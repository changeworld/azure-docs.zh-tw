---
title: 使用 Batch Management .NET 程式庫來管理帳戶資源
description: 使用 Batch Management .NET 程式庫建立、刪除和修改 Azure Batch 帳戶資源。
ms.topic: how-to
ms.date: 01/26/2021
ms.custom: seodec18, has-adal-ref, devx-track-csharp
ms.openlocfilehash: f6acf23742b8d4c5c31a5ea952aba5c76b64cae0
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896726"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>使用適用於 .NET 的 Batch 管理用戶端程式庫來管理 Batch 帳戶和配額

您可以使用 [Batch Management .NET](/dotnet/api/overview/azure/batch) 程式庫，將 Batch 帳戶的建立、刪除、金鑰管理和配額探索自動化，以降低 Azure Batch 應用程式中的維護負擔。

- **建立和刪除 Batch 帳戶** 。 比方說，身為獨立軟體廠商 (ISV)，您為每個獲派不同 Batch 帳戶的客戶針對計費目的提供服務，您可以將帳戶建立和刪除功能加入至客戶入口網站。
- **擷取和重新產生帳戶金鑰** 。 這可協助您符合強制定期變換帳戶金鑰或讓帳戶金鑰過期的安全性原則。 當您在各種不同的 Azure 區域中有數個 Batch 帳戶時，將此變換程序自動化可增加解決方案的效率。
- **檢查帳戶配額** 並採取反復試驗的猜測，判斷哪一個 Batch 帳戶有哪些限制。 藉由在開始作業、建立集區或新增計算節點之前檢查您的帳戶配額，您可以主動地調整建立計算資源的位置或時機。 您可以決定在帳戶中配置其他資源之前，哪些帳戶需要增加配額。
- **結合其他 Azure 服務的功能** ，在相同的應用程式中使用 Batch management .net、 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)和 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 一起提供功能齊全的管理體驗。 透過使用這些功能和其 API，您可以提供順暢的驗證體驗、建立和刪除資源群組的能力，以及上面所述的功能，以獲得端對端管理解決方案。

> [!NOTE]
> 雖然本文著重于以程式設計方式管理 Batch 帳戶、金鑰和配額，但您也可以 [使用 Azure 入口網站](batch-account-create-portal.md)來執行許多活動。

## <a name="create-and-delete-batch-accounts"></a>建立和刪除 Batch 帳戶

Batch 管理 API 的其中一個主要功能是在 Azure 區域中建立和刪除 [batch 帳戶](accounts.md) 。 若要這樣做，請使用 [BatchManagementClient.Account.CreateAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.createasync) 和 [DeleteAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.deleteasync)，或其同步的對應項目。

下列程式碼片段會建立一個帳戶、從 Batch 服務取得新建立的帳戶，然後將它刪除。 在此程式碼片段和本文的其他程式碼片段中，`batchManagementClient` 是完整初始化的 [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient) 執行個體。

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> 使用 Batch Management .NET 程式庫和其 BatchManagementClient 類別的應用程式需要服務管理員或共同管理員存取權，以使用擁有要管理的 Batch 帳戶的訂用帳戶。 如需詳細資訊，請參閱 Azure Active Directory 一節和 [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) 程式碼範例。

## <a name="retrieve-and-regenerate-account-keys"></a>擷取和重新產生帳戶金鑰

使用 [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync)，從訂用帳戶內的任何 Batch 帳戶取得主要和次要帳戶金鑰。 您可以使用 [RegenerateKeyAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.regeneratekeyasync) 重新產生這些金鑰。

```csharp
// Get and print the primary and secondary keys
BatchAccountGetKeyResult accountKeys =
    await batchManagementClient.Account.GetKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> 您可以為您的管理應用程式建立簡化的連線工作流程。 首先，取得您想要使用 [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync)管理的 Batch 帳戶的帳戶金鑰。 接著，在初始化 Batch .NET 程式庫的 [BatchSharedKeyCredentials](/dotnet/api/microsoft.azure.batch.auth.batchsharedkeycredentials) 類別時 (初始化 [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) 時使用)，使用此金鑰。

## <a name="check-azure-subscription-and-batch-account-quotas"></a>檢查 Azure 訂用帳戶和 Batch 帳戶配額

Azure 訂用帳戶和 Batch 之類的個別 Azure 服務均具有預設配額，限制其中特定實體的數目。 如需 Azure 訂用帳戶的預設配額，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-resource-manager/management/azure-subscription-service-limits.md)。 如需 Batch 服務的預設配額，請參閱 [Azure Batch 服務的配額和限制](batch-quota-limit.md)。 藉由使用 Batch Management .NET 程式庫，您可以檢查您的應用程式中的這些配額。 這可讓您在加入帳戶或計算資源 (如集區和計算節點) 之前進行配置決策。

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>檢查 Azure 訂用帳戶和 Batch 帳戶配額

在區域中建立 Batch 帳戶之前，您可以檢查您的 Azure 訂用帳戶，以查看您是否能夠將帳戶加入該區域中。

在下列程式碼片段中，我們會先使用 **metrics.listasync** 取得訂用帳戶內所有 Batch 帳戶的集合。 在我們取得此集合後，我們會判斷目標區域中有多少個帳戶。 接著，我們會使用 **GetQuotasAsync** 來取得 Batch 帳戶配額，並判斷在該區域中是否可以建立任何)  (的帳戶數目。

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.BatchAccount.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Location.GetQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

在上述程式碼片段中， `creds` 是 **>microsoft.rest.tokencredentials** 的實例。 若要查看建立此物件的範例，請參閱 GitHub 上的 [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) 程式碼範例。

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>檢查 Batch 帳戶的計算資源配額

在 Batch 方案中增加計算資源之前，您可以檢查以確定您要配置的資源不會超過帳戶的配額。 在下列程式碼片段中，我們會列印名為 `mybatchaccount` 的 Batch 帳戶配額資訊。 在您自己的應用程式中，您可以使用這類資訊來判斷帳戶是否可以處理要建立的其他資源。

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> 雖然 Azure 訂用帳戶和服務有預設配額，但有許多限制可以藉由 [要求 Azure 入口網站增加配額](batch-quota-limit.md#increase-a-quota)來引發。

## <a name="use-azure-ad-with-batch-management-net"></a>搭配 Batch Management .NET 使用 Azure AD

Batch Management .NET 程式庫是 Azure 資源提供者用戶端，並與 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 搭配使用，以程式設計方式管理帳戶資源。 驗證透過任何 Azure 資源提供者用戶端 (包括 Batch Management .NET 程式庫)，以及透過 Azure Resource Manager 所提出的要求時，都需要 Azure AD。 如需搭配 Batch Management .NET 程式庫使用 Azure AD 的詳細資訊，請參閱[使用 Azure Active Directory 驗證 Batch 方案](batch-aad-auth.md)。

## <a name="sample-project-on-github"></a>GitHub 上的範例專案

若要查看 Batch Management .NET 的實際運作，請查看 GitHub 上的 [AccountManagment](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) 範例專案。 AccountManagment 範例應用程式會示範下列作業：

1. 使用 [ADAL](../active-directory/azuread-dev/active-directory-authentication-libraries.md) 從 Azure AD 取得安全性權杖。 如果使用者尚未登入，系統會提示使用者輸入其 Azure 認證。
2. 使用從 Azure AD 取得的安全性權杖，建立 [SubscriptionClient](/dotnet/api/microsoft.azure.management.resourcemanager.subscriptionclient) 來查詢 Azure，以取得與帳戶相關聯的訂用帳戶清單。 如果清單包含多個訂用帳戶，使用者可以從清單中選取一個訂用帳戶。
3. 取得與所選訂用帳戶相關聯的認證。
4. 使用認證來建立 [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) 物件。
5. 使用 [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) 物件來建立資源群組。
6. 使用 [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient) 物件來執行數個 Batch 帳戶作業：
   - 在新的資源群組中建立 Batch 帳戶。
   - 從 Batch 服務取得新建立的帳戶。
   - 列印新帳戶的帳戶金鑰。
   - 重新產生帳戶的新主要金鑰。
   - 列印帳戶的配額資訊。
   - 列印訂用帳戶的配額資訊。
   - 列印訂用帳戶內的所有帳戶。
   - 刪除新建立的帳戶。
7. 刪除資源群組。

若要成功執行範例應用程式，必須先向 Azure 入口網站中的 Azure AD 租用戶註冊範例應用程式，並將權限授與 Azure Resource Manager API。 請依照[使用 Active Directory 驗證 Batch Management 解決方案](batch-aad-auth-management.md)中所提供的步驟執行。

## <a name="next-steps"></a>後續步驟

- 深入了解 [Batch 服務工作流程和主要資源](batch-service-workflow-features.md)，例如集區、節點、作業和工作。
- 了解使用 [Batch .NET 用戶端程式庫](quick-run-dotnet.md)或 [Python](quick-run-python.md) 開發啟用 Batch 之應用程式的基本概念。 這些快速入門會引導您使用 Batch 服務在多個計算節點上執行工作負載的範例應用程式，並使用工作負載檔案預備和抓取 Azure 儲存體。 git pus
