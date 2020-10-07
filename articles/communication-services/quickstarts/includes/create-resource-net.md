---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 258908ed1118b0463e8c824cd8c699fb460dfff2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943932"
---
## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/dotnet/)。
- 適用於您的作業系統的最新版本 [.NET Core 用戶端程式庫](https://dotnet.microsoft.com/download/dotnet-core)。
- 取得最新版本的 [.NET Identity 用戶端程式庫](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet)。
- 取得最新版本的 [.NET Management 用戶端程式庫](../../concepts/sdk-options.md)。

## <a name="installing-the-client-library"></a>安裝用戶端程式庫

首先，將通訊服務管理用戶端程式庫包含在您的 C# 專案中：

```csharp
using Azure.ResourceManager.Communication;
```

## <a name="subscription-id"></a>訂用帳戶識別碼

您必須知道 Azure 訂用帳戶的識別碼。 可以從入口網站取得：

1.  登入您的 Azure 帳戶
2.  選取左邊側邊欄的 [訂用帳戶]
3.  選取所需的任何訂用帳戶
4.  按一下 [概觀]
5.  選取訂用帳戶識別碼

在本快速入門中，我們假設您已將訂用帳戶識別碼儲存在名為 `AZURE_SUBSCRIPTION_ID` 的環境變數中。

## <a name="authentication"></a>驗證

若要與 Azure 通訊服務通訊，您必須先向 Azure 驗證您自己。 您通常會使用服務主體身分識別來執行此動作。

### <a name="option-1-managed-identity"></a>選項 1：受控識別

如果您的程式碼在 Azure 中是以服務的形式執行，則驗證的最簡單方式是從 Azure 取得受控識別。 深入了解[受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

[支援受控識別的 Azure 服務](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

[如何使用 App Service 和 Azure Functions 的受控身分識別](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)

#### <a name="system-assigned-managed-identity"></a>[系統指派的受控識別](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity)

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var acsClient = new CommunicationManagementClient(subscriptionId, new ManagedIdentityCredential());
```

#### <a name="user-assigned-managed-identity"></a>[使用者指派的受控識別](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-user-assigned-identity)

您所建立受控識別的 ClientId 必須明確地傳遞給 `ManagedIdentityCredential`。

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var managedIdentityCredential = new ManagedIdentityCredential("AZURE_CLIENT_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, managedIdentityCredential);
```

### <a name="option-2-service-principal"></a>選項 2：服務主體

您可能會想要使用自己管理的服務主體來向 Azure 進行驗證，而不是使用受控識別。 若要深入了解，請參閱[在 Azure Active Directory 中建立和管理服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)上的文件。

建立服務主體之後，您必須從 Azure 入口網站收集下列相關資訊：

- **用戶端識別碼**
- **用戶端祕密**
- **租用戶識別碼**

分別將這些值儲存在名為 `AZURE_CLIENT_ID`、`AZURE_CLIENT_SECRET` 和 `AZURE_TENANT_ID` 的環境變數中。 接著，您可以建立通訊服務管理用戶端，如下所示：

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, new EnvironmentCredential());
```

### <a name="option-3-user-identity"></a>選項 3：使用者身分識別

如果您想要代表互動使用者呼叫 Azure，而不是使用服務身分識別，您可以使用下列程式碼來建立 Azure 通訊服務管理用戶端。 這會開啟瀏覽器視窗，提示使用者輸入其 MSA 或 Azure AD 認證。

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var communicationServiceClient = new CommunicationManagementClient(subscriptionId, new InteractiveBrowserCredential());
```

## <a name="managing-communication-services-resources"></a>管理通訊服務資源

### <a name="interacting-with-azure-resources"></a>與 Azure 資源互動

現在您已通過驗證，您可以使用管理用戶端來進行 API 呼叫。

針對下列每個範例，我們會將我們的通訊服務資源指派給現有的資源群組。

如果您需要建立資源群組，您可以使用 [Azure 入口網站](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal)或 [Azure Resource Manager 用戶端程式庫](https://github.com/Azure/azure-sdk-for-net/blob/master/doc/mgmt_preview_quickstart.md)來執行此動作。

### <a name="create-and-manage-a-communication-services-resource"></a>建立和管理通訊服務資源

我們的通訊服務管理用戶端程式庫用戶端 (``Azure.ResourceManager.Communication.CommunicationManagementClient``) 執行個體可以用來對通訊服務資源執行作業。

#### <a name="create-a-communication-services-resource"></a>建立通訊服務資源

建立通訊服務資源時，您將指定資源群組名稱和資源名稱。 請注意，`Location` 屬性一律是 `global`，而在公開預覽期間，`DataLocation` 值必須是 `UnitedStates`。

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates"  };
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="update-a-communication-services-resource"></a>更新通訊服務資源

```csharp
...
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates" };
resource.Tags.Add("environment","test");
resource.Tags.Add("department","tech");
// Use existing resource name and new resource object
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="list-all-communication-services-resources"></a>列出所有通訊服務資源

```csharp
var resources = acsClient.CommunicationService.ListBySubscription();
foreach (var resource in resources)
{
    Console.WriteLine(resource.Name);
}
```

#### <a name="delete-a-communication-services-resource"></a>刪除通訊服務資源

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
await acsClient.CommunicationService.StartDeleteAsync(resourceGroupName, resourceName);
```

## <a name="managing-keys-and-connection-strings"></a>管理金鑰和連接字串

每個通訊服務資源都有一對存取金鑰和對應的連接字串。 這些金鑰可以使用管理用戶端程式庫來存取，然後由其他通訊服務用戶端程式庫用來向 Azure 通訊服務進行驗證。

#### <a name="get-access-keys-for-a-communication-services-resource"></a>取得通訊服務資源的存取金鑰

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keys = await acsClient.CommunicationService.ListKeysAsync(resourceGroupName, resourceName);

Console.WriteLine(keys.Value.PrimaryConnectionString);
Console.WriteLine(keys.Value.SecondaryConnectionString);
```

#### <a name="regenerate-an-access-key-for-a-communication-services-resource"></a>重新產生通訊服務資源的存取金鑰

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keyParams = new RegenerateKeyParameters { KeyType = KeyType.Primary };
var keys = await acsClient.CommunicationService.RegenerateKeyAsync(resourceGroupName, resourceName, keyParams);

Console.WriteLine(keys.Value.PrimaryKey);
```
