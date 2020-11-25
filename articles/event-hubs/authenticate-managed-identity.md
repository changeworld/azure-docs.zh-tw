---
title: 使用 Azure Active Directory 驗證受控識別
description: 本文提供的資訊會說明如何使用 Azure Active Directory 來驗證受控識別，以存取 Azure 事件中樞資源
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: c6b43cc48663be28d12fa788d92286be6f47ef08
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993528"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>使用 Azure Active Directory 來驗證受控識別，以存取事件中樞資源
Azure 事件中樞支援使用 [適用于 Azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md)識別 Azure Active Directory (Azure AD) 驗證。 適用于 Azure 資源的受控識別可以從 Azure 虛擬機器中執行的應用程式（ (Vm) 、函式應用程式、虛擬機器擴展集和其他服務），使用 Azure AD 認證來授權存取事件中樞資源。 藉由使用適用于 Azure 資源的受控識別搭配 Azure AD authentication，您可以避免將認證儲存在雲端中執行的應用程式。

本文說明如何使用 Azure VM 的受控識別，來授權事件中樞的存取權。

## <a name="enable-managed-identities-on-a-vm"></a>在 VM 上啟用受控識別
您必須先在 VM 上啟用 Azure 資源的受控識別，才能使用 Azure 資源的受控識別來授權 VM 中的事件中樞資源。 若要了解如何啟用 Azure 資源的受控識別，請參閱下列其中一篇文章：

- [Azure 入口網站](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 範本](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager 用戶端程式庫](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>在 Azure AD 中將許可權授與受控識別
若要從您應用程式中的受控識別授權事件中樞服務的要求，請先為該受控識別設定 Azure 角色型存取控制 (Azure RBAC) 設定。 Azure 事件中樞定義的 Azure 角色包含從事件中樞傳送和讀取的許可權。 當 Azure 角色指派給受控識別時，受控識別會獲得適當範圍內事件中樞資料的存取權。

如需指派 Azure 角色的詳細資訊，請參閱 [使用 Azure Active Directory 進行驗證以存取事件中樞資源](authorize-access-azure-active-directory.md)。

## <a name="use-event-hubs-with-managed-identities"></a>搭配使用事件中樞與受控識別
若要使用具有受控識別的事件中樞，您需要將角色和適當的範圍指派給身分識別。 本節中的程式會使用以受控身分識別執行的簡單應用程式，並存取事件中樞資源。

在這裡，我們會使用 [Azure App Service](https://azure.microsoft.com/services/app-service/)中裝載的範例 web 應用程式。 如需建立 web 應用程式的逐步指示，請參閱[在 Azure 中建立 ASP.NET Core web 應用](../app-service/quickstart-dotnetcore.md)程式

建立應用程式之後，請遵循下列步驟： 

1. 移至 [ **設定** ]，然後選取 [身分 **識別**]。 
1. 選取要 **開啟** 的 **狀態**。 
1. 選取 [儲存]  以儲存設定。 

    ![Web 應用程式的受控識別](./media/authenticate-managed-identity/identity-web-app.png)

一旦您啟用此設定，就會在 Azure Active Directory (Azure AD) 中建立新的服務識別，並將其設定為 App Service 主機。

現在，將此服務識別指派給事件中樞資源中所需範圍內的角色。

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>使用 Azure 入口網站指派 Azure 角色
若要將角色指派給事件中樞資源，請流覽至 Azure 入口網站中的該資源。 顯示資源的存取控制 (IAM) 設定，並遵循下列指示來管理角色指派：

> [!NOTE]
> 下列步驟會將服務身分識別角色指派給您的事件中樞命名空間。 您可以遵循相同的步驟，指派範圍為任何事件中樞資源的角色。 

1. 在 Azure 入口網站中，流覽至您的事件中樞命名空間，並顯示命名空間的 **總覽** 。 
1. 選取左側功能表上的 [ **存取控制] (IAM)** ，以顯示事件中樞的存取控制設定。
1.  選取 [角色指派] 索引標籤，以查看角色指派的清單。
3.  選取 **[新增]** 以加入新的角色。
4.  在 [ **新增角色指派** ] 頁面上，選取您要指派的事件中樞角色。 然後搜尋以找出您已註冊的服務身分識別，以指派角色。
    
    ![[新增角色指派] 頁面](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  選取 [儲存]。 您對其指派角色的身分識別會出現在該角色下方。 例如，下圖顯示服務識別有事件中樞資料擁有者。
    
    ![指派給角色的身分識別](./media/authenticate-managed-identity/role-assigned.png)

一旦您指派角色之後，web 應用程式就可以存取定義範圍下的事件中樞資源。 

### <a name="test-the-web-application"></a>測試 Web 應用程式
1. 建立事件中樞命名空間和事件中樞。 
2. 將 web 應用程式部署到 Azure。 請參閱下列索引標籤式區段，以取得 GitHub 上 web 應用程式的連結。 
3. 確定已將 SendReceive 設定為 web 應用程式的預設檔。 
3. 啟用 web 應用程式的身分 **識別** 。 
4. 將此身分識別指派給命名空間層級或事件中樞層級的 **事件中樞資料擁有** 者角色。 
5. 執行 web 應用程式，輸入命名空間名稱和事件中樞名稱、訊息，然後選取 [ **傳送**]。 若要接收事件，請選取 [ **接收**]。 

#### <a name="azuremessagingeventhubs-latest"></a>[EventHubs (最新) ](#tab/latest)
您現在可以啟動 web 應用程式，並將瀏覽器指向範例 aspx 頁面。 您可以在 [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)存放庫中找到從事件中樞資源傳送和接收資料的範例 web 應用程式。

從 [NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)安裝最新的套件，並使用 **EventHubConsumerClient** 來開始傳送事件至事件中樞，並使用 **EventHubProducerClient** 和接收事件。 

> [!NOTE]
> 如需使用受控識別將事件發佈至事件中樞的 JAVA 範例，請參閱 [GitHub 上的使用 Azure 身分識別範例發佈事件](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)。

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
您現在可以啟動 web 應用程式，並將瀏覽器指向範例 aspx 頁面。 您可以在 [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp)存放庫中找到從事件中樞資源傳送和接收資料的範例 web 應用程式。

從 [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)安裝最新的套件，並使用 >eventhubclient 來開始傳送和接收事件中樞的資料，如下列程式碼所示： 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>適用於 Kafka 的事件中樞
您可以使用 Apache Kafka 應用程式，使用受控識別 OAuth，將訊息傳送至 Azure 事件中樞並接收訊息。 請參閱 GitHub 上的下列範例： [適用于 Kafka 的事件中樞-使用受控識別 OAuth 來傳送和接收訊息](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)。

## <a name="samples"></a>範例
- **EventHubs** 範例
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [EventHubs 範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
    
    這些範例會使用舊的 **EventHubs** 程式庫，但您可以輕鬆地將其更新為使用最新的 **EventHubs** 程式庫。 若要將範例從使用舊的程式庫移至新的程式庫，請參閱 [從 EventHubs 遷移至 EventHubs 的指南](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)。
    此範例已更新為使用最新的 **EventHubs** 程式庫。
- [適用于 Kafka 的事件中樞-使用受控識別 OAuth 來傳送和接收訊息](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>後續步驟
- 請參閱下列文章，以瞭解適用于 Azure 資源的受控識別： [什麼是適用于 azure 資源的受控識別？](../active-directory/managed-identities-azure-resources/overview.md)
- 請參閱下列相關文章：
    - [使用 Azure Active Directory 驗證從應用程式 Azure 事件中樞的要求](authenticate-application.md)
    - [使用共用存取簽章驗證 Azure 事件中樞的要求](authenticate-shared-access-signature.md)
    - [使用 Azure Active Directory 授權存取事件中樞資源](authorize-access-azure-active-directory.md)
    - [使用共用存取簽章授權存取事件中樞資源](authorize-access-shared-access-signature.md)
