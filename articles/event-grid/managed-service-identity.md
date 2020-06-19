---
title: 使用受管理的服務識別進行事件傳遞
description: 本文說明如何為 Azure 事件方格主題啟用受管理的服務識別。 使用它來將事件轉送到支援的目的地。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 04/24/2020
ms.author: spelluru
ms.openlocfilehash: 4d96f28b98cccada2ac5c77589acc6df1430bb02
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700649"
---
# <a name="event-delivery-with-managed-identity"></a>使用受控識別進行事件傳遞
本文說明如何為事件方格主題或網域啟用[受管理的服務識別](../active-directory/managed-identities-azure-resources/overview.md)。 使用此功能將事件轉送到支援的目的地，例如，服務匯流排佇列和主題、事件中樞，以及儲存體帳戶。

以下是本文詳細說明的步驟：
1. 建立帶有系統指派的身分識別的主題或網域 (或) 更新現有主題或網域，以啟用身分識別。 
2. 將身分識別新增至目的地 (例如：服務匯流排佇列) 的適當角色中 (例如：服務匯流排資料傳送者)
3. 建立事件訂閱時，可讓您使用身分識別將事件傳遞至目的地。 

## <a name="create-a-topic-or-domain-with-an-identity"></a>使用身分識別建立主題或網域
首先，讓我們看看如何使用系統管理的身分識別來建立主題或網域。

### <a name="using-azure-portal"></a>使用 Azure 入口網站
在 Azure 入口網站建立主題/網域時，您可以為其啟用系統指派的身分識別。 下圖顯示如何為主題啟用系統管理的身分識別。 基本上，您會在主題建立精靈的 [進階] 頁面上，選取選項 [啟用系統指派的身分識別]。 您也會在網域建立精靈的 [進階] 頁面上，看到此選項。 

![建立主題時啟用身分識別](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>使用 Azure CLI
您也可以使用 Azure CLI，建立帶有系統指派的身分識別的主題或網域。 使用 `az eventgrid topic create` 命令，搭配設為 `systemassigned` 的 `--identity` 參數。 如果您未指定這個參數的值，則會使用預設值 `noidentity`。 

```azurecli-interactive
# create a topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

同樣地，您可以使用 `az eventgrid domain create` 命令，建立一個網域，帶有系統管理的身分識別。

## <a name="enable-identity-for-an-existing-topic-or-domain"></a>為現有主題或網域啟用身分識別
在上一節中，您已了解如何在建立主題或網域時，啟用系統管理的身分識別。 在本節中，您將了解如何為現有主題或網域，啟用系統管理的身分識別。 

### <a name="using-azure-portal"></a>使用 Azure 入口網站
1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)
2. 在搜尋列中搜尋 [事件方格主題]。
3. 選取您要啟用受控識別的 [主題]。 
4. 切換至 [身分識別] 索引標籤。 
5. 開啟切換按鈕以啟用身分識別。 

    您可以使用類似的步驟，以啟用事件方格網域的身分識別。

### <a name="using-azure-cli"></a>使用 Azure CLI
將 `az eventgrid topic update` 命令與設為 `systemassigned` 的 `--identity` 搭配使用，為現有主題啟用系統指派的身分識別。 如果您想要停用身分識別，請將 `noidentity` 指定為值。 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

更新現有網域的命令是類似的 (`az eventgrid domain update`)。

## <a name="supported-destinations-and-role-based-access-check-rbac-roles"></a>支援的目的地與角色型存取檢查 (RBAC) 角色
為您的事件方格主題或網域啟用身分識別之後，Azure 會自動在 Azure Active Directory (Azure AD) 中建立身分識別。 將此身分識別新增至適當的 RBAC 角色，以便主題或網域可將事件轉送至支援的目的地。 例如，為事件中樞命名空間將身分識別新增至 **Azure 事件中樞資料傳送者**角色，以便事件方格主題可將事件轉送至該命名空間中的事件中樞。  

目前，Azure 事件方格支援使用系統指派的受控識別設定的主題或網域，將事件轉送至下列目的地。 此表也提供您身分識別應該處於何種角色，讓主題可以轉送事件。

| Destination | RBAC 角色 | 
| ----------- | --------- | 
| 服務匯流排佇列與主題 | [Azure 服務匯流排資料傳送者](../service-bus-messaging/authenticate-application.md#built-in-rbac-roles-for-azure-service-bus) |
| 事件中樞 | [Azure 事件中樞資料傳送者](../event-hubs/authorize-access-azure-active-directory.md#built-in-rbac-roles-for-azure-event-hubs) | 
| Blob 儲存體 | [儲存體 Blob 資料參與者](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) |
| 佇列儲存體 |[儲存體佇列資料訊息傳送者](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) | 

## <a name="add-identity-to-rbac-roles-on-destinations"></a>將身分識別新增至目的地的 RBAC 角色
本節描述如何為您的主題或網域，將身分識別新增至 RBAC 角色。 

### <a name="using-azure-portal"></a>使用 Azure 入口網站
您可以使用 **Azure 入口網站**，將主題/網域身分識別新增至適當的角色，以便主題/網域可將事件轉送至目的地。 

下列範例會為名稱為 **msitesttopic** 的事件方格主題，針對包含佇列或主題資源的服務匯流排**命名空間**，將受控識別新增至 **Azure 服務匯流排資料傳送者**角色。 當您在命名空間層級新增至角色時，主題可以將事件轉送至該命名空間中的所有實體。 

1. 導覽至 [Azure 入口網站](https://portal.azure.com)中的**服務匯流排命名空間**。 
2. 在左側窗格中，選取 [存取控制]。 
3. 在 [新增角色指派] 區段中，選取 [新增]。 
4. 在 [新增角色指派] 頁面中，執行下列步驟：
    1. 選取角色。 在此案例中，該角色為 **Azure 服務匯流排資料傳送者**。 
    2. 為您的主題或網域選取 [身分識別]。 
    3. 選取 [儲存]，以儲存設定。

這些步驟類似於將身分識別新增至表格中所述的其他角色。 

### <a name="using-azure-cli"></a>使用 Azure CLI
本節中的範例示範如何使用 **Azure CLI**，將身分識別新增至 RBAC 角色。 此範例命令適用於事件方格主題。 事件方格網域的命令是類似的。 

#### <a name="get-principal-id-for-the-topics-system-identity"></a>取得主題系統身分識別的主體識別碼 
首先，取得主題的系統管理身分識別的主體識別碼，並將身分識別指派給適當的角色。

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>在各種範圍內建立事件中樞的角色指派 
下列 CLI 範例示範如何在命名空間層級或事件中樞層級，將主題的身分識別新增至 **Azure 事件中樞資料傳送者**角色。 如果您在命名空間建立角色指派，則主題可以將事件轉送至該命名空間中的所有事件中樞。 如果您在事件中樞層級建立，主題只能將事件轉送至該特定事件中樞。 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

#### <a name="create-a-role-assignment-for-service-bus-topic-at-various-scopes"></a>在各種範圍內建立服務匯流排主題的角色指派 
下列 CLI 範例示範如何在命名空間層級或服務匯流排主題層級，將主題的身分識別新增至 **Azure 服務匯流排資料傳送者**角色。 如果您在命名空間建立角色指派，事件方格主題可以將事件轉送至該命名空間內的所有實體 (服務匯流排佇列或主題)。 如果您在服務匯流排佇列或主題層級建立，事件方格主題只能將事件轉送到該特定服務匯流排佇列或主題。 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="create-event-subscriptions-that-use-identity"></a>建立使用身分識別的事件訂閱
當您的主題或網域具有系統管理的身分識別，並將身分識別新增至目的地的適當角色之後，您就可以建立使用該身分識別的訂閱。 

### <a name="using-azure-portal"></a>使用 Azure 入口網站
建立事件訂閱時，您會看到一個選項，可讓您為 **ENDPOINT DETAILS** 區段中的端點，使用系統指派的身分識別。 

![為服務匯流排佇列建立事件訂閱時啟用身分識別](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

您也可以在 [其他功能] 索引標籤中，啟用系統指派的身分識別，以用於進行無效信件處理。 

![啟用系統指派的身分識別以進行無效信件處理](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="using-azure-cli---service-bus-queue"></a>使用 Azure CLI - 服務匯流排佇列 
在本節中，您將了解如何利用 **Azure CLI**，以使用系統指派的身分識別，將事件傳遞至服務匯流排佇列。 身分識別必須是 **Azure 服務匯流排資料傳送者**角色的成員。 在用於進行無效信件處理的儲存體帳戶上，該身分識別也必須是**儲存體 Blob 資料參與者**角色的成員。 

#### <a name="define-variables"></a>定義變數
首先，針對要在 CLI 命令中使用的下列變數，指定值。 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery"></a>使用受控識別來建立事件訂閱以進行傳遞 
此範例命令會建立事件方格主題的事件訂閱，並將端點類型設為**服務匯流排佇列**。 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery-and-dead-lettering"></a>使用受控識別建立事件訂閱，以進行傳遞和無效信件處理
此範例命令會建立事件方格主題的事件訂閱，並將端點類型設為**服務匯流排佇列**。 它也會指定要用於無效信件的系統管理的身分識別。 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="azure-cli---event-hubs"></a>Azure CLI - 事件中樞 
在本節中，您將了解如何利用 **Azure CLI**，以使用系統指派的身分識別，將事件傳遞至事件中樞。 身分識別必須是 **Azure 事件中樞資料傳送者**角色的成員。 在用於進行無效信件處理的儲存體帳戶上，該身分識別也必須是**儲存體 Blob 資料參與者**角色的成員。 

#### <a name="define-variables"></a>定義變數
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>使用受控識別建立事件訂閱以進行傳遞 
此範例命令會建立事件方格主題的事件訂閱，並將端點類型設為**事件中樞**。 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>使用受控識別建立事件訂閱以進行傳遞 + 無效信件處理 
此範例命令會建立事件方格主題的事件訂閱，並將端點類型設為**事件中樞**。 它也會指定要用於無效信件的系統管理的身分識別。 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="azure-cli---azure-storage-queue"></a>Azure CLI - Azure 儲存體佇列 
在本節中，您將了解如何利用 **Azure CLI**，以使用系統指派的身分識別，將事件傳遞至 Azure 儲存體佇列。 此身分識別在儲存體帳戶上必須是**儲存體 Blob 資料參與者**角色的成員。

#### <a name="define-variables"></a>定義變數  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>使用受控識別建立事件訂閱以進行傳遞 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>使用受控識別建立事件訂閱以進行傳遞 + 無效信件處理 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```



## <a name="next-steps"></a>後續步驟
如需受管理的服務身分識別的詳細資訊，請參閱[什麼是適用於 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)。 