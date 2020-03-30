---
title: 為 Azure 事件網格主題或域配置專用終結點
description: 本文介紹如何為 Azure 事件網格主題或域配置專用終結點。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: d08afe00c13a3f96b9526c3cb29804cfad688ddc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299903"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains-preview"></a>為 Azure 事件網格主題或域配置專用終結點（預覽）
您可以使用[專用終結點](../private-link/private-endpoint-overview.md)允許事件直接從虛擬網路通過[專用鏈路](../private-link/private-link-overview.md)安全地進入您的主題和域，而無需通過公共互聯網。 專用終結點使用主題或域的 VNet 位址空間中的 IP 位址。 有關詳細資訊概念資訊，請參閱[網路安全](network-security.md)。

本文介紹如何為主題或域配置專用終結點。

> [!IMPORTANT]
> 專用終結點功能僅適用于高級層中的主題和域。 要從基本層升級到高級層，請參閱[更新定價層](update-tier.md)一文。 

## <a name="use-azure-portal"></a>使用 Azure 入口網站 
本節介紹如何使用 Azure 門戶為主題或域創建專用終結點。

> [!NOTE]
> 本節中顯示的步驟主要針對主題。 您可以使用類似的步驟為**域**創建專用終結點。 

1. 登錄到 Azure[門戶](https://portal.azure.com)並導航到主題或域。
2. 切換到主題頁面的 **"網路**"選項卡。 選擇 **" 工具列上的專用終結點**"。

    ![添加專用終結點](./media/configure-private-endpoints/add-button.png)
2. 其中一個**基礎知識**頁面，按照以下步驟操作： 
    1. 選擇要在其中創建專用終結點的**Azure 訂閱**。 
    2. 為專用終結點選擇**Azure 資源組**。 
    3. 輸入終結點**的名稱**。 
    4. **選擇終結點**的區域。 專用終結點必須與虛擬網路位於同一區域，但可以位於與專用連結資源不同的區域（在此示例中為事件網格主題）。 
    5. 然後，選擇**頁面底部的"下一步：資源>"** 按鈕。 

      ![專用終結點 - 基礎知識頁面](./media/configure-private-endpoints/basics-page.png)
3. 在 **"資源"** 頁上，按照以下步驟操作： 
    1. 對於連接方法，如果選擇 **"連接到目錄中的 Azure 資源**"，請按照以下步驟操作。 此示例演示如何連接到目錄中的 Azure 資源。 
        1. 選擇**主題/域**存在的**Azure 訂閱**。 
        1. 對於**資源類型**，選擇**Microsoft.eventGrid/主題**或"**資源類型****"的"事件網格/域**"。
        2. 對於**資源**，從下拉清單中選擇主題/域。 
        3. 確認**目標子資源**設置為**主題**或**域**（基於您選擇的資源類型）。    
        4. 選擇 **"下一步"：頁面底部的"配置>"** 按鈕。 

            ![專用終結點 - 資源頁](./media/configure-private-endpoints/resource-page.png)
    2. 如果選擇 **"使用資源識別碼 或別名連接到資源**"，請按照以下步驟操作：
        1. 輸入資源的 ID。 例如：`/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`。  
        2. 對於**資源**，輸入**主題**或**域**。 
        3. （可選）添加請求消息。 
        4. 選擇 **"下一步"：頁面底部的"配置>"** 按鈕。 

            ![專用終結點 - 資源頁](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. 在 **"配置"** 頁上，選擇虛擬網路中的子網，以選擇要部署專用終結點的位置。 
    1. 選擇**虛擬網路**。 下拉清單中僅列出當前選擇的訂閱和位置中的虛擬網路。 
    2. 在所選虛擬網路中選擇**子網**。 
    3. 選擇 **"下一步"：在頁面底部標記>** 按鈕。 

    ![專用終結點 - 配置頁](./media/configure-private-endpoints/configuration-page.png)
5. 在 **"標記"** 頁上，創建要與專用終結點資源關聯的任何標記（名稱和值）。 然後，選擇頁面底部的 **"查看 + 創建**"按鈕。 
6. 在 **"審閱 + 創建**"上，查看所有設置，然後選擇 **"創建"** 以創建專用終結點。 

    ![專用終結點 - 查看&創建頁面](./media/configure-private-endpoints/review-create-page.png)
    

## <a name="manage-private-link-connection"></a>管理私人連結連線

當您建立私人端點時，必須核准連線。 如果要為其創建專用終結點的資源位於目錄中，則可以批准連接請求，前提是您具有足夠的許可權。 如果要連接到另一個目錄中的 Azure 資源，則必須等待該資源的擁有者批准連接請求。

佈建狀態有四種：

| 服務操作 | 服務取用者私人端點狀態 | 描述 |
|--|--|--|
| None | Pending | 連接是手動創建的，正在等待專用連結資源擁有者的批准。 |
| 核准 | 已核准 | 已自動或手動核准連線並可供使用。 |
| 拒絕 | 已拒絕 | 私人連結資源擁有者已拒絕連線。 |
| 移除 | 已中斷連接 | 私人連結資源擁有者已移除連線，而私人端點變成參考性，且應該刪除以進行清除。 |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>如何管理專用終結點連接
以下各節介紹如何批准或拒絕專用終結點連接。 

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在搜索欄中，鍵入**事件網格主題**或**事件網格域**。
1. 選擇要管理**的主題**或**域**。
1. 選擇 **"網路"** 選項卡。
1. 如果存在任何掛起的連接，您將看到與 **"掛起"** 處於預配狀態的連接。 

### <a name="to-approve-a-private-endpoint"></a>批准專用終結點
您可以批准處於掛起狀態的私有終結點。 要批准，請按照以下步驟操作： 

> [!NOTE]
> 本節中顯示的步驟主要針對主題。 您可以使用類似的步驟來批准**域**的專用終結點。 

1. 選擇要批准的**專用終結點**，並在工具列上選擇 **"批准**"。

    ![專用終結點 - 掛起狀態](./media/configure-private-endpoints/pending.png)
1. 在 **"批准連接**"對話方塊中，輸入注釋（可選），然後選擇 **"是**"。 

    ![專用終結點 - 批准](./media/configure-private-endpoints/approve.png)
1. 確認將終結點的狀態視為 **"已批准**"。 

    ![專用終結點 - 已批准狀態](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>拒絕專用終結點
您可以拒絕處於掛起狀態或已批准狀態的私有終結點。 要拒絕，請按照以下步驟操作： 

> [!NOTE]
> 本節中顯示的步驟適用于主題。 您可以使用類似的步驟來拒絕**域**的專用終結點。 

1. 選擇要拒絕**的專用終結點**，並在工具列上選擇 **"拒絕**"。

    ![專用終結點 - 拒絕](./media/configure-private-endpoints/reject-button.png)
1. 在 **"拒絕連接**"對話方塊中，輸入注釋（可選），然後選擇 **"是**"。 

    ![專用終結點 - 拒絕](./media/configure-private-endpoints/reject.png)
1. 確認將終結點的狀態視為 **"已拒絕**"。 

    ![專用終結點 - 拒絕狀態](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > 拒絕 Azure 門戶中的專用終結點後，無法批准該終結點。 


## <a name="use-azure-cli"></a>使用 Azure CLI
要創建專用終結點，請使用[az 網路專用終結點創建](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create)方法，如以下示例所示：

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

有關示例中使用的參數的說明，請參閱[az 網路專用終結點創建](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create)的文檔。 此示例中需要注意的幾點包括： 

- 對於`private-connection-resource-id`，請指定**主題**或**域**的資源識別碼。 前面的示例使用類型： 主題。
- 指定`group-ids``topic`或`domain`。 在前面的示例中，`topic`使用了。 

要刪除專用終結點，請使用[az 網路專用終結點刪除](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-delete)方法，如以下示例所示：

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> 本節中顯示的步驟適用于主題。 您可以使用類似的步驟為**域**創建專用終結點。 

### <a name="create-a-private-endpoint"></a>建立私人端點
下面是創建以下 Azure 資源的示例腳本：

- 資源群組
- 虛擬網路
- 虛擬網路中的子網
- Azure 事件網格主題（高級層）
- 主題的專用終結點

> [!NOTE]
> 本節中顯示的步驟適用于主題。 可以使用類似的步驟為域創建專用終結點。

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# URI for the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicUri="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>?api-version=2020-04-01-preview"

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az rest --method put \
    --uri $topicUri \
    --body "{\""location\"":\""LOCATION\"", \""sku\"": {\""name\"": \""premium\""}, \""properties\"": {\""publicNetworkAccess\"":\""Disabled\""}}"

# verify that the topic was created.
az rest --method get \
    --uri $topicUri

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az rest --method get \
    --uri $topicUri

```

### <a name="approve-a-private-endpoint-connection"></a>批准專用終結點連接
以下示例 CLI 程式碼片段演示如何批准專用終結點連接。 

```azurecli-interactive
az rest --method put --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" --body "{\""properties\"":{\""privateLinkServiceConnectionState\"": {\""status\"":\""approved\"",\""description\"":\""connection approved\"", \""actionsRequired\"": \""none\""}}}"
```


### <a name="reject-a-private-endpoint-connection"></a>拒絕專用終結點連接
以下示例 CLI 程式碼片段演示如何拒絕專用終結點連接。 

```azurecli-interactive
az rest --method put --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" --body "{\""properties\"":{\""privateLinkServiceConnectionState\"": {\""status\"":\""rejected\"",\""description\"":\""connection rejected\"", \""actionsRequired\"": \""none\""}}}"
```


## <a name="use-powershell"></a>使用 PowerShell
本節介紹如何使用 PowerShell 為主題或域創建專用終結點。 

### <a name="prerequisite"></a>必要條件
按照["如何操作"的說明：使用門戶創建 Azure AD 應用程式和服務主體，該應用程式和服務主體可以訪問資源](../active-directory/develop/howto-create-service-principal-portal.md)以創建 Azure 活動目錄應用程式，並記下**目錄（租戶）ID、****應用程式（用戶端）ID**和**應用程式（用戶端）機密**的值。 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>為 REST API 呼叫準備權杖和標頭 
運行以下先決條件命令，獲取用於 REST API 呼叫和授權和其他標頭資訊的身份驗證權杖。 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>創建禁用終結點網路原則的子網

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>使用專用終結點創建事件網格主題

> [!NOTE]
> 本節中顯示的步驟適用于主題。 您可以使用類似的步驟為**域**創建專用終結點。 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="premium"}; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-04-01-preview"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

驗證終結點是否已創建時，應看到類似于以下內容的結果：

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>批准專用終結點連接
下面的示例 PowerShell 程式碼片段演示如何批准專用終結點。 

> [!NOTE]
> 本節中顯示的步驟適用于主題。 您可以使用類似的步驟來批准**域**的專用終結點。 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>拒絕專用終結點連接
下面的示例演示如何使用 PowerShell 拒絕私有終結點。 可以從上一個 GET 命令的結果獲取私有終結點的 GUID。 

> [!NOTE]
> 本節中顯示的步驟適用于主題。 您可以使用類似的步驟來拒絕**域**的專用終結點。 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" ` 
    -Headers $Headers
```

即使在通過 API 拒絕連接後，您也可以批准該連接。 如果使用 Azure 門戶，則無法批准已被拒絕的終結點。 

## <a name="next-steps"></a>後續步驟
要瞭解如何配置 IP 防火牆設置，請參閱[為 Azure 事件網格主題或域配置 IP 防火牆](configure-firewall.md)。