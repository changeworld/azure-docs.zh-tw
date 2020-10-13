---
title: 設定 Azure 事件方格主題或網域的私人端點
description: 本文說明如何設定 Azure 事件方格主題或網域的私人端點。
ms.topic: how-to
ms.date: 07/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: e2e164d55f61f7a08e689aea106eac678b553c82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324139"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains"></a>設定 Azure 事件方格主題或網域的私人端點
您可以使用 [私人端點](../private-link/private-endpoint-overview.md) 來安全地透過 [私人連結](../private-link/private-link-overview.md) ，直接從您的虛擬網路將事件輸入至主題和網域，而不需經過公用網際網路。 私人端點會使用您主題或網域的 VNet 位址空間中的 IP 位址。 如需詳細的概念資訊，請參閱 [網路安全性](network-security.md)。

本文說明如何設定主題或網域的私人端點。

## <a name="use-azure-portal"></a>使用 Azure 入口網站 
本節說明如何使用 Azure 入口網站來建立主題或網域的私人端點。

> [!NOTE]
> 本節所示的步驟大多適用于主題。 您可以使用類似的步驟來建立 **網域**的私人端點。 

1. 登入 [Azure 入口網站](https://portal.azure.com) ，然後流覽至您的主題或網域。
2. 切換至主題頁面的 [ **網路** 功能] 索引標籤。 選取工具列上的 [ **+ 私人端點** ]。

    ![新增私人端點](./media/configure-private-endpoints/add-button.png)
2. 其中一個 **基本** 頁面，請遵循下列步驟： 
    1. 選取您要在其中建立私人端點的 **Azure 訂** 用帳戶。 
    2. 選取私人端點的 **Azure 資源群組** 。 
    3. 輸入端點的 **名稱** 。 
    4. 選取端點的 **區域** 。 您的私人端點必須位於與您虛擬網路相同的區域中，但可位於與私人連結資源不同的區域 (在此範例中，事件方格主題) 。 
    5. 然後，選取頁面底部的 **[下一步：資源 >] ** 按鈕。 

      ![私人端點-基本頁面](./media/configure-private-endpoints/basics-page.png)
3. 在 [資源] 頁面上，遵循下列步驟： 
    1. 針對連線方式，如果您選取 **[連線到我目錄中的 Azure 資源]**，請遵循下列步驟。 此範例示範如何連接至您目錄中的 Azure 資源。 
        1. 選取您的**主題/網域**所在的**Azure 訂**用帳戶。 
        1. 針對 [ **資源類型**]，選取 [ **EventGrid]/[主題** ] 或 [ **EventGrid/網域** ] 作為 **資源類型**。
        2. 針對 [ **資源**]，從下拉式清單中選取主題/網域。 
        3. 確認 [ **目標 subresource** ] 設定為 [ **主題** ] 或 [ **網域** (]，取決於您) 選取的資源類型。    
        4. 完成時，選取 [下一步:設定 >] 按鈕 (位於頁面底部)。 

            ![顯示 [建立私人端點-資源] 頁面的螢幕擷取畫面。](./media/configure-private-endpoints/resource-page.png)
    2. 如果您選取 **[使用資源識別碼或別名連線至資源]**，請遵循下列步驟：
        1. 輸入資源的識別碼。 例如： `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>` 。  
        2. 針對 [ **資源**]，輸入 **主題** 或 **網域**。 
        3.  (選擇性) 新增要求訊息。 
        4. 完成時，選取 [下一步:設定 >] 按鈕 (位於頁面底部)。 

            ![私人端點-資源頁面](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. 在 [設定] 頁面上，您會選取在虛擬網路中要部署私人端點的子網路。 
    1. 選取**虛擬網路**。 下拉式清單只會列出目前所選訂閱與位置中的虛擬網路。 
    2. 選取您所選虛擬網路中的**子網路**。 
    3. 完成時，選取 [下一步:標記 >] 按鈕 (位於頁面底部)。 

    ![私人端點-設定頁面](./media/configure-private-endpoints/configuration-page.png)
5. 在 [標記] 頁面上，建立想要與私人端點資源建立關聯的任何標記 (名稱和值)。 然後選取頁面底部的 [檢閱 + 建立] 按鈕。 
6. 在 [檢閱 + 建立] 上，檢閱所有設定，然後選取 [建立] 來建立私人端點。 

    ![私人端點-評論 & 建立頁面](./media/configure-private-endpoints/review-create-page.png)
    

### <a name="manage-private-link-connection"></a>管理私人連結連線

當您建立私人端點時，必須核准連線。 如果正要建立私人端點的資源位於目錄中，則在具有足夠權限的情況下，即可核准連線要求。 如果正要連線到位於另一個目錄中的 Azure 資源，則必須等候該資源的擁有者核准連線要求。

佈建狀態有四種：

| 服務動作 | 服務取用者私人端點狀態 | 描述 |
|--|--|--|
| None | Pending | 連接是以手動方式建立，且正在等待私人連結資源擁有者進行核准。 |
| 核准 | 已核准 | 已自動或手動核准連線並可供使用。 |
| 拒絕 | 已拒絕 | 私人連結資源擁有者已拒絕連線。 |
| 移除 | 已中斷連接 | 私人連結資源擁有者已移除連線，而私人端點變成參考性，且應該刪除以進行清除。 |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>如何管理私人端點連線
下列各節說明如何核准或拒絕私人端點連線。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在搜尋列中，輸入 **事件方格主題** 或 **事件方格網域**。
1. 選取您要管理的 **主題** 或 **網域** 。
1. 選取 [網路] 索引標籤。
1. 如果有任何擱置中的連線，您將會在布建狀態中看到 [ **擱置** 中] 所列出的連線。 

### <a name="to-approve-a-private-endpoint"></a>核准私人端點
您可以核准處於擱置狀態的私人端點。 若要核准，請遵循下列步驟： 

> [!NOTE]
> 本節所示的步驟大多適用于主題。 您可以使用類似的步驟來核准 **網域**的私人端點。 

1. 選取您要核准的 **私人端點** ，然後選取工具列上的 [ **核准** ]。

    ![私人端點-暫止狀態](./media/configure-private-endpoints/pending.png)
1. 在 [ **核准連接** ] 對話方塊中，輸入批註 (選擇性) ，然後選取 **[是]**。 

    ![私人端點-核准](./media/configure-private-endpoints/approve.png)
1. 確認您看到端點的狀態為 [ **已核准**]。 

    ![私人端點-已核准狀態](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>拒絕私人端點
您可以拒絕處於擱置狀態或已核准狀態的私人端點。 若要拒絕，請遵循下列步驟： 

> [!NOTE]
> 本節所示的步驟適用于主題。 您可以使用類似的步驟來拒絕 **網域**的私人端點。 

1. 選取您要拒絕的 **私人端點** ，然後在工具列上選取 [ **拒絕** ]。

    ![顯示已選取 [拒絕] 的 [網路-私人端點連線 (預覽) ] 的螢幕擷取畫面。](./media/configure-private-endpoints/reject-button.png)
1. 在 [ **拒絕連接** ] 對話方塊中，輸入批註 (選擇性) ，然後選取 **[是]**。 

    ![私人端點-拒絕](./media/configure-private-endpoints/reject.png)
1. 確認您看到端點的狀態為 [已 **拒絕**]。 

    ![私人端點-已拒絕狀態](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > 一旦遭到拒絕，您就無法在 Azure 入口網站核准私人端點。 


## <a name="use-azure-cli"></a>使用 Azure CLI
若要建立私人端點，請使用 [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) 方法，如下列範例所示：

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

如需範例中所使用參數的說明，請參閱 [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create)的檔。 在此範例中，有幾點要注意： 

- 針對 `private-connection-resource-id` ，指定 **主題** 或 **網域**的資源識別碼。 上述範例使用類型：主題。
- 若為 `group-ids` ，請指定 `topic` 或 `domain` 。 在上述範例中， `topic` 會使用。 

若要刪除私人端點，請使用 [az network private-endpoint delete](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-delete) 方法，如下列範例所示：

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> 本節所示的步驟適用于主題。 您可以使用類似的步驟來建立 **網域**的私人端點。 



### <a name="prerequisites"></a>必要條件
執行下列命令，以更新適用于 CLI 的 Azure 事件方格延伸模組： 

```azurecli-interactive
az extension update -n eventgrid
```

如果未安裝此延伸模組，請執行下列命令加以安裝： 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="create-a-private-endpoint"></a>建立私人端點
若要建立私人端點，請使用 [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) 方法，如下列範例所示：

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

如需範例中所使用參數的說明，請參閱 [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create)的檔。 在此範例中，有幾點要注意： 

- 針對 `private-connection-resource-id` ，指定 **主題** 或 **網域**的資源識別碼。 上述範例使用類型：主題。
- 若為 `group-ids` ，請指定 `topic` 或 `domain` 。 在上述範例中， `topic` 會使用。 

若要刪除私人端點，請使用 [az network private-endpoint delete](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-delete) 方法，如下列範例所示：

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> 本節所示的步驟適用于主題。 您可以使用類似的步驟來建立 **網域**的私人端點。 

#### <a name="sample-script"></a>範例指令碼
以下是範例腳本，可建立下列 Azure 資源：

- 資源群組
- 虛擬網路
- 虛擬網路中的子網
- Azure 事件方格主題
- 主題的私人端點

> [!NOTE]
> 本節所示的步驟適用于主題。 您可以使用類似的步驟來建立網域的私人端點。

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

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
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# verify that the topic was created.
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

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
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

```

### <a name="approve-a-private-endpoint"></a>核准私人端點
下列範例 CLI 程式碼片段會說明如何核准私人端點連線。 

```azurecli-interactive
az eventgrid topic private-endpoint-connection approve \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name  $endpointName \
    --description "connection approved"
```


### <a name="reject-a-private-endpoint"></a>拒絕私人端點
下列範例 CLI 程式碼片段說明如何拒絕私人端點連線。 

```azurecli-interactive
az eventgrid topic private-endpoint-connection reject \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name $endpointName \
    --description "Connection rejected"
```

### <a name="disable-public-network-access"></a>停用公用網路存取
根據預設，事件方格主題或網域會啟用公用網路存取。 若只允許透過私人端點存取，請執行下列命令來停用公用網路存取：  

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```


## <a name="use-powershell"></a>使用 PowerShell
本節說明如何使用 PowerShell 建立主題或網域的私人端點。 

### <a name="prerequisite"></a>必要條件
遵循 [如何：使用入口網站來建立可存取資源的 Azure AD 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md) ，以建立 Azure Active Directory 應用程式，並記下 **目錄 (租使用者) 識別碼**、 **應用程式 (用戶端) 識別碼**和 **應用程式 (秘密**的值。 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>準備 REST API 呼叫的權杖和標頭 
執行下列必要條件命令以取得要搭配 REST API 呼叫和授權和其他標頭資訊使用的驗證權杖。 

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

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>建立已停用端點網路原則的子網

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

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>使用私人端點建立事件方格主題

> [!NOTE]
> 本節所示的步驟適用于主題。 您可以使用類似的步驟來建立 **網域**的私人端點。 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"   `
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
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-06-01"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

當您確認已建立端點時，您應該會看到如下所示的結果：

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

### <a name="approve-a-private-endpoint-connection"></a>核准私人端點連線
下列範例 PowerShell 程式碼片段會說明如何核准私人端點。 

> [!NOTE]
> 本節所示的步驟適用于主題。 您可以使用類似的步驟來核准 **網域**的私人端點。 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>拒絕私人端點連線
下列範例說明如何使用 PowerShell 來拒絕私人端點。 您可以從上一個 GET 命令的結果取得私人端點的 GUID。 

> [!NOTE]
> 本節所示的步驟適用于主題。 您可以使用類似的步驟來拒絕 **網域**的私人端點。 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01" ` 
    -Headers $Headers
```

即使在透過 API 拒絕連線之後，您也可以核准連線。 如果您使用 Azure 入口網站，就無法核准已拒絕的端點。 

## <a name="next-steps"></a>後續步驟
* 若要瞭解如何設定 IP 防火牆設定，請參閱 [設定 Azure 事件方格主題或網域的 ip 防火牆](configure-firewall.md)。
* 若要疑難排解網路連線問題，請參閱 [疑難排解網路連線問題](troubleshoot-network-connectivity.md)
