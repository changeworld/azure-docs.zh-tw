---
title: 為 Azure 事件方格主題或網域設定 IP 防火牆（預覽）
description: 本文說明如何設定事件方格主題或網域的防火牆設定。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: spelluru
ms.openlocfilehash: 4aa86b3619897c310473f12e1c28101185ebf3ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82100986"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains-preview"></a>為 Azure 事件方格主題或網域設定 IP 防火牆（預覽）
根據預設，只要要求隨附有效的驗證和授權，就可以從網際網路存取主題和網域。 使用 IP 防火牆，您可以將它進一步限制為[CIDR （無類別網域間路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)標記法中的一組 ipv4 位址或 ipv4 位址範圍。 源自任何其他 IP 位址的發行者將會遭到拒絕，並會收到403（禁止）回應。 如需事件方格所支援之網路安全性功能的詳細資訊，請參閱[事件方格的網路安全性](network-security.md)。

本文說明如何設定 Azure 事件方格主題或網域的 IP 防火牆設定。

## <a name="use-azure-portal"></a>使用 Azure 入口網站
本節說明如何使用 Azure 入口網站來建立輸入 IP 防火牆規則。 本節所示的步驟適用于主題。 您可以使用類似的步驟來建立**網域**的輸入 IP 規則。 

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，流覽至您的事件方格主題或網域，並切換至 [**網路**功能] 索引標籤。
2. 選取 [**公用網路**] 以允許所有網路（包括網際網路）存取資源。 

    您可以使用以 IP 為基礎的防火牆規則來限制流量。 以無類別網域間路由（CIDR）標記法指定單一 IPv4 位址或 IP 位址範圍。 

    ![[公用網路] 頁面](./media/configure-firewall/public-networks-page.png)
3. 選取 [**僅限私人端點**] 僅允許私人端點連線存取此資源。 使用此頁面上的 [**私人端點連接**] 索引標籤來管理連接。 

    ![[公用網路] 頁面](./media/configure-firewall/private-endpoints-page.png)
4. 在工具列上選取 [儲存]  。 



## <a name="use-azure-cli"></a>使用 Azure CLI
本節說明如何使用 Azure CLI 命令來建立具有輸入 IP 規則的主題。 本節所示的步驟適用于主題。 您可以使用類似的步驟來建立**網域**的輸入 IP 規則。 


### <a name="prerequisites"></a>先決條件
執行下列命令來更新適用于 CLI 的 Azure 事件方格延伸模組： 

```azurecli-interactive
az extension update -n eventgrid
```

如果未安裝延伸模組，請執行下列命令來安裝它： 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="enable-or-disable-public-network-access"></a>啟用或停用公用網路存取
根據預設，會啟用主題和網域的公用網路存取。 您也可以明確地加以啟用或停用它。 您可以藉由設定輸入 IP 防火牆規則來限制流量。 

#### <a name="enable-public-network-access-while-creating-a-topic"></a>建立主題時啟用公用網路存取

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled
```


#### <a name="disable-public-network-access-while-creating-a-topic"></a>建立主題時停用公用網路存取

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access disabled
```

> [!NOTE]
> 停用主題或網域的公用網路存取時，不允許透過公用網際網路的流量。 只有私人端點連線才會允許存取這些資源。 


#### <a name="enable-public-network-access-for-an-existing-topic"></a>針對現有主題啟用公用網路存取

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled 
```

#### <a name="disable-public-network-access-for-an-existing-topic"></a>停用現有主題的公用網路存取 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```

### <a name="create-a-topic-with-single-inbound-ip-rule"></a>建立具有單一輸入 ip 規則的主題
下列範例 CLI 命令會使用輸入 IP 規則來建立事件方格主題。 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow 
```

### <a name="create-a-topic-with-multiple-inbound-ip-rules"></a>建立具有多個輸入 ip 規則的主題

下列範例 CLI 命令會在一個步驟中建立一個事件方格主題兩個輸入 IP 規則： 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="update-an-existing-topic-to-add-inbound-ip-rules"></a>更新現有的主題以新增輸入 IP 規則
這個範例會先建立事件方格主題，然後將主題的輸入 IP 規則新增至個別的命令。 它也會更新第二個命令中設定的輸入 IP 規則。 

```azurecli-interactive

# create the event grid topic first
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# add inbound IP rules to an existing topic
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow

# later, update topic with additional ip rules
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="remove-an-inbound-ip-rule"></a>移除輸入 IP 規則
下列命令會在更新設定時只指定第一個規則，以移除您在上一個步驟中建立的第二個規則。 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow
```


## <a name="use-powershell"></a>使用 PowerShell
本節說明如何使用 Azure PowerShell 命令來建立具有輸入 IP 防火牆規則的 Azure 事件方格主題。 本節所示的步驟適用于主題。 您可以使用類似的步驟來建立**網域**的輸入 IP 規則。 

### <a name="prerequisites"></a>先決條件
依照[如何：使用入口網站建立可存取資源的 Azure AD 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md)中的指示，以建立 Azure Active Directory 應用程式，並記下下列值：

- 目錄 (租用戶) 識別碼
- 應用程式（用戶端）識別碼
- 應用程式（用戶端）密碼

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>準備 REST API 呼叫的權杖和標頭 
執行下列必要條件命令，以取得用於 REST API 呼叫的驗證權杖，以及授權和其他標頭資訊。 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="enable-public-network-access-for-an-existing-topic"></a>針對現有主題啟用公用網路存取
根據預設，會啟用主題和網域的公用網路存取。 您可以藉由設定輸入 IP 防火牆規則來限制流量。 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>停用現有主題的公用網路存取
停用主題或網域的公用網路存取時，不允許透過公用網際網路的流量。 只有私人端點連線才會允許存取這些資源。 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>在一個步驟中建立包含輸入規則的事件方格主題

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>先建立事件方格主題，然後再新增輸入 ip 規則

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"}, @{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>後續步驟

* 如需關於監視事件傳遞的資訊，請參閱[監視 Event Grid 訊息傳遞](monitor-event-delivery.md)。
* 如需驗證金鑰的詳細資訊，請參閱 [Event Grid 安全性和驗證](security-authentication.md)。
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
