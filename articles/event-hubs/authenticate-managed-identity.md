---
title: 使用 Azure 活動目錄對託管標識進行身份驗證
description: 本文提供有關使用 Azure 活動目錄驗證託管標識以訪問 Azure 事件中心資源的資訊
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 672b663a9cab72d465ea00e0a5ade364eadbf64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251520"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>使用 Azure 活動目錄對託管標識進行身份驗證以訪問事件中心資源
Azure 事件中心支援 Azure 活動目錄 （Azure AD） 身份驗證，具有[Azure 資源的託管標識](../active-directory/managed-identities-azure-resources/overview.md)。 Azure 資源的託管標識可以使用 Azure 虛擬機器 （VM）、功能應用、虛擬機器縮放集和其他服務中運行的應用程式中的 Azure AD 憑據授權訪問事件中心資源。 通過將 Azure 資源的託管標識與 Azure AD 身份驗證一起使用，可以避免將憑據存儲在雲中運行的應用程式。

本文演示如何使用 Azure VM 中的託管標識授權對事件中心的訪問。

## <a name="enable-managed-identities-on-a-vm"></a>在 VM 上啟用受控識別
在可以使用 Azure Resources 的託管標識從 VM 授權事件中心資源之前，必須首先在 VM 上為 Azure 資源啟用託管標識。 若要了解如何啟用 Azure 資源的受控識別，請參閱下列其中一篇文章：

- [Azure 門戶](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure 電源外殼](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure 資源管理器範本](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 資源管理器用戶端庫](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>向 Azure AD 中的託管標識授予許可權
要授權從應用程式中的託管標識請求事件中心服務，請先為該託管標識配置基於角色的存取控制 （RBAC） 設置。 Azure 事件中心定義 RBAC 角色，這些角色包括從事件中心發送和讀取的許可權。 將 RBAC 角色指派給託管標識時，將授予託管標識對相應作用域的事件中心資料的訪問。

有關分配 RBAC 角色的詳細資訊，請參閱[使用 Azure 活動目錄進行身份驗證以訪問事件中心資源](authorize-access-azure-active-directory.md)。

## <a name="use-event-hubs-with-managed-identities"></a>搭配使用事件中樞與受控識別
要使用具有託管標識的事件中心，您需要為標識分配角色和適當的作用域。 本節中的過程使用在託管標識下運行並訪問事件中心資源的簡單應用程式。

在這裡，我們使用在[Azure 應用服務](https://azure.microsoft.com/services/app-service/)中託管的示例 Web 應用程式。 有關創建 Web 應用程式的分步說明，請參閱在 Azure[中創建ASP.NET核心 Web 應用](../app-service/app-service-web-get-started-dotnet.md)

創建應用程式後，按照以下步驟操作： 

1. 轉到 **"設置"** 並選擇 **"標識**"。 
1. 選擇"**狀態**為**打開**"。 
1. 選取 [儲存]**** 以儲存設定。 

    ![Web 應用的託管標識](./media/authenticate-managed-identity/identity-web-app.png)

啟用此設置後，將在 Azure 活動目錄 （Azure AD） 中創建新的服務標識，並將其配置到應用服務主機中。

現在，將此服務標識分配給事件中心資源中所需作用域中的角色。

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>使用 Azure 門戶分配 RBAC 角色
要將角色指派給事件中心資源，請導航到 Azure 門戶中的該資源。 顯示資源的存取控制 （IAM） 設置，並按照以下說明管理角色指派：

> [!NOTE]
> 以下步驟將服務標識角色指派給事件中心命名空間。 您可以按照相同的步驟將範圍限定為任何事件中心資源的角色。 

1. 在 Azure 門戶中，導航到事件中心命名空間並顯示命名空間的**概述**。 
1. 選擇左側功能表上**的存取控制 （IAM）** 以顯示事件中心的存取控制設置。
1.  選取 [角色指派]**** 索引標籤，以查看角色指派的清單。
3.  選擇 **"添加"** 以添加新角色。
4.  在"**添加角色指派"** 頁上，選擇要分配的事件中心角色。 然後搜索以查找已註冊的服務標識以分配角色。
    
    ![添加角色指派頁](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  選取 [儲存]****。 您對其指派角色的身分識別會出現在該角色下方。 例如，下圖顯示服務標識具有事件中心資料擁有者。
    
    ![分配給角色的標識](./media/authenticate-managed-identity/role-assigned.png)

分配角色後，Web 應用程式將有權訪問定義範圍內的事件中心資源。 

### <a name="test-the-web-application"></a>測試 Web 應用程式
1. 建立事件中樞命名空間和事件中樞。 
2. 將 Web 應用部署到 Azure。 有關 GitHub 上的 Web 應用程式的連結，請參閱以下選項卡式部分。 
3. 確保 SendReceive.aspx 設置為 Web 應用的預設文件。 
3. 為 Web 應用啟用**標識**。 
4. 在命名空間級別或事件中心級別將此標識分配給**事件中心資料擁有者**角色。 
5. 運行 Web 應用程式，輸入命名空間名稱和事件中心名稱、消息，然後選擇 **"發送**"。 要接收事件，請選擇"**接收**"。 

#### <a name="azuremessagingeventhubs-latest"></a>[Azure.消息傳遞.事件中心（最新）](#tab/latest)
您現在可以啟動 Web 應用程式並將瀏覽器指向示例 aspx 頁面。 您可以在[GitHub 存儲庫](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)中查找從事件中心資源發送和接收資料的示例 Web 應用程式。

從[NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)安裝最新的包，並開始使用**EventHub 生產者用戶端**向事件中心發送事件，並使用**EventHub消費者用戶端**接收事件。 

> [!NOTE]
> 有關使用託管標識將事件發佈到事件中心的 JAVA 示例，請參閱[在 GitHub 上使用 Azure 標識示例發佈事件](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)。

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs (舊版)](#tab/old)
您現在可以啟動 Web 應用程式並將瀏覽器指向示例 aspx 頁面。 您可以在[GitHub 存儲庫](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp)中查找從事件中心資源發送和接收資料的示例 Web 應用程式。

從[NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)安裝最新的包，並開始使用 EventHubClient 向事件中心發送和接收資料，如下代碼所示： 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>適用於 Kafka 的事件中樞
您可以使用 Apache Kafka 應用程式使用託管標識 OAuth 向 Azure 事件中心發送消息並從 Azure 事件中心接收消息。 請參閱 GitHub 上的以下示例[：Kafka 的事件中心 - 使用託管標識 OAuth 發送和接收消息](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)。

## <a name="samples"></a>範例
- **Azure.消息傳遞.事件中心**示例
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [JAVA](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [微軟.Azure.事件中心示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
    
    這些示例使用舊的**Microsoft.Azure.EventHubs**庫，但可以輕鬆地將其更新為使用最新的**Azure.消息.事件中心**庫。 要將示例從使用舊庫移動到新庫，請參閱[從 Microsoft.Azure.事件中心遷移到 Azure.消息集的指南](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md)。
    此示例已更新為使用最新的**Azure.消息.事件中心**庫。
- [卡夫卡的事件中心 - 使用託管標識 OAuth 發送和接收消息](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>後續步驟
- 請參閱以下文章以瞭解 Azure 資源的託管標識[：Azure 資源的託管標識是什麼？](../active-directory/managed-identities-azure-resources/overview.md)
- 請參閱以下相關文章：
    - [使用 Azure 活動目錄對應用程式對 Azure 事件中心的請求進行身份驗證](authenticate-application.md)
    - [使用共用訪問簽名對 Azure 事件中心的請求進行身份驗證](authenticate-shared-access-signature.md)
    - [使用 Azure 活動目錄授權訪問事件中心資源](authorize-access-azure-active-directory.md)
    - [使用共用訪問簽名授權訪問事件中心資源](authorize-access-shared-access-signature.md)