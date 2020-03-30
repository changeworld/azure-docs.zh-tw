---
title: 為 Azure 事件網格主題或域配置 IP 防火牆（預覽）
description: 本文介紹如何為事件網格主題或域配置防火牆設置。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: b195872ca1002970fa96ae133d5eb47a9267796d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299864"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains-preview"></a>為 Azure 事件網格主題或域配置 IP 防火牆（預覽）
預設情況下，只要請求附帶有效的身份驗證和授權，主題和域都可以從 Internet 訪問。 使用 IP 防火牆，您可以進一步將其限制為[CIDR（無類域間路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)標記法中的一組 IPv4 位址或 IPv4 位址範圍。 來自任何其他 IP 位址的發行者將被拒絕，並將收到 403（禁止）回應。 有關事件網格支援的網路安全功能的詳細資訊，請參閱[事件網格的網路安全](network-security.md)。

本文介紹如何為 Azure 事件網格主題或域配置 IP 防火牆設置。

## <a name="use-azure-portal"></a>使用 Azure 入口網站
本節介紹如何使用 Azure 門戶創建入站 IP 防火牆規則。 本節中顯示的步驟適用于主題。 您可以使用類似的步驟為**域**創建入站 IP 規則。 

1. 在[Azure 門戶](https://portal.azure.com)中，導航到事件網格主題或域，然後切換到 **"網路**"選項卡。
2. 選擇**公共網路**以允許所有網路（包括互聯網）訪問資源。 

    您可以使用基於 IP 的防火牆規則限制流量。 在無類域間路由 （CIDR） 標記法中指定單個 IPv4 位址或一系列 IP 位址。 

    ![公共網路頁面](./media/configure-firewall/public-networks-page.png)
3. 僅選擇**專用終結點**，以便僅允許專用終結點連接訪問此資源。 使用此頁上的 **"專用終結點連接**"選項卡來管理連接。 

    ![公共網路頁面](./media/configure-firewall/private-endpoints-page.png)
4. 在工具列上選取 [儲存]****。 



## <a name="use-azure-cli"></a>使用 Azure CLI
本節介紹如何使用 Azure CLI 命令使用入站 IP 規則創建主題。 本節中顯示的步驟適用于主題。 您可以使用類似的步驟為**域**創建入站 IP 規則。 


### <a name="enable-public-network-access-for-an-existing-topic"></a>為現有主題啟用公共網路訪問
預設情況下，為主題和域啟用公共網路訪問。 您可以通過配置入站 IP 防火牆規則來限制流量。 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Enabled\""}}"
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>禁用現有主題的公共網路訪問
當禁用主題或域的公共網路訪問時，不允許通過公共 Internet 進行流量訪問。 只允許專用終結點連接訪問這些資源。 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Disabled\""}}"
```

### <a name="create-topic-with-inbound-ip-rules"></a>使用入站 IP 規則創建主題
以下示例 CLI 命令在一個步驟中創建具有入站 IP 規則的事件網格主題。 

```azurecli-interactive
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"",\""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```

### <a name="create-topic-first-and-then-add-inbound-ip-rules"></a>先創建主題，然後添加入站 IP 規則
本示例首先創建事件網格主題，然後在單獨的命令中為該主題添加入站 IP 規則。 它還更新在第二個命令中設置的入站 IP 規則。 

```azurecli-interactive

# create the event grid topic first
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\""}

# add inbound IP rules
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}

# later, update topic with additional ip rules or remove them. 
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""}, {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```


## <a name="use-powershell"></a>使用 PowerShell
本節介紹如何使用 Azure PowerShell 命令使用入站 IP 防火牆規則創建 Azure 事件網格主題。 本節中顯示的步驟適用于主題。 您可以使用類似的步驟為**域**創建入站 IP 規則。 

### <a name="prerequisite"></a>必要條件
按照["如何操作"中的說明：使用門戶創建 Azure AD 應用程式和服務主體，該應用程式和服務主體可以訪問資源](../active-directory/develop/howto-create-service-principal-portal.md)以創建 Azure 活動目錄應用程式，並記下以下值：

- 目錄 (租用戶) 識別碼
- 應用程式（用戶端）ID
- 應用程式（用戶端）機密

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>為 REST API 呼叫準備權杖和標頭 
運行以下先決條件命令，獲取用於 REST API 呼叫的身份驗證權杖以及授權和其他標頭資訊。 

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

### <a name="enable-public-network-access-for-an-existing-topic"></a>為現有主題啟用公共網路訪問
預設情況下，為主題和域啟用公共網路訪問。 您可以通過配置入站 IP 防火牆規則來限制流量。 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>禁用現有主題的公共網路訪問
當禁用主題或域的公共網路訪問時，不允許通過公共 Internet 進行流量訪問。 只允許專用終結點連接訪問這些資源。 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>在一個步驟中創建包含入站規則的事件網格主題

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

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


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>先創建事件網格主題，然後添加入站 IP 規則

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
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"}, @{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

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
