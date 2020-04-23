---
title: 更新 Azure 事件方格主題或網域的定價層
description: 本文說明如何使用 Azure 入口網站、Azure CLI 和 Azure PowerShell，將 Azure 事件方格主題或網域的定價層（基本到高階，premium 到基本）更新。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: spelluru
ms.openlocfilehash: caea8d515964510fce432eb0497e3af19ecc1369
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101632"
---
# <a name="update-pricing-tier"></a>更新定價層 
本文說明如何使用 Azure 入口網站、Azure CLI 和 Azure PowerShell 來更新 Azure 事件方格主題或網域的定價層。 

## <a name="use-azure-portal"></a>使用 Azure 入口網站
本節說明如何在 Azure 入口網站中變更主題或網域的定價層。 

### <a name="overview-page"></a>概觀分頁
您可以在 [**總覽**] 頁面上變更主題或網域的定價層。 下列範例顯示如何將主題從基本層升級至進階層。 從高階層降級至基本層的步驟很類似。

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，流覽至 [主題] 或 [網域] 頁面。 
2. 在 [**總覽**] 頁面上，選取目前的定價層（在下列範例中為 [**基本**]）。
    
    ![選取目前的定價層](./media/update-tier/select-tier.png)
3. 在 [**定價層**] 頁面上，變更層級，然後選取 **[確定]**。 

    ![更新定價層](./media/update-tier/change-tier.png)
4. 在 [**通知**] 頁面中，檢查作業的狀態。

    ![更新狀態](./media/update-tier/status.png)    
5. 確認您在 [**總覽**] 頁面上看到 [已更新] 層。 

    ![更新狀態](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>網路功能頁面
您可以從基本層**升級**至 [**網路**功能] 頁面上的 [進階層]。 不過，您無法從進階層降級到此頁面上的基本層。 

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，流覽至 [主題] 或 [網域] 頁面。 
2. 在 [**網路**功能] 頁面上，切換至 [**私人端點連接（預覽）** ] 索引標籤。 
3. 如果目前的定價層是「**基本**」，您會看到下列訊息。 請選取它。 

    ![[私人端點連接] 頁面上的 [更新層]](./media/update-tier/private-endpoint-connections-page.png)
4. 在 [**更新為 premium 定價層**] 頁面上，選取 **[是]**。 
    
    ![確認升級](./media/update-tier/message-private-endpoint-connection.png)
5. 在 [**通知**] 頁面中，檢查作業的狀態。

    ![更新狀態](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>使用 Azure CLI
本節說明如何使用 Azure CLI 命令來變更主題的定價層。 若要更新網域的定價層，請以`az eventgrid domain update`類似的方式使用命令。

### <a name="prerequisites"></a>Prerequisites
執行下列命令來更新適用于 CLI 的 Azure 事件方格延伸模組： 

```azurecli-interactive
az extension update -n eventgrid
```

如果未安裝延伸模組，請執行下列命令來安裝它： 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="upgrade-a-topic-from-basic-to-premium"></a>將主題從基本升級至 premium

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --sku "Premium" 
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>將主題從 premium 降級為基本

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --sku "Basic" 
```


## <a name="use-azure-powershell"></a>使用 Azure PowerShell
本節說明如何使用 PowerShell 命令來變更主題或網域的定價層。 

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

### <a name="upgrade-a-topic-from-basic-to-premium"></a>將主題從基本升級至 premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>將主題從 premium 降級為基本

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>將網域從基本升級至 premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>將網域從 premium 降級為基本

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>後續步驟
針對高階層主題和網域，您可以設定私人端點，以限制只能從選取的虛擬網路存取。 如需逐步指示，請參閱[設定私人端點](configure-private-endpoints.md)。
