---
title: 更新 Azure 事件網格主題或域的定價層
description: 本文介紹如何使用 Azure 門戶、Azure CLI 和 Azure PowerShell 更新 Azure 事件網格主題或域（基本到高級、基本溢價）的定價層。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 1e92a9f0b9ba007b289c634995b04f4f1575310a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300137"
---
# <a name="update-pricing-tier"></a>更新定價層 
本文演示如何使用 Azure 門戶、Azure CLI 和 Azure PowerShell 更新 Azure 事件網格主題或域的定價層。 

## <a name="use-azure-portal"></a>使用 Azure 入口網站
本節演示如何更改 Azure 門戶中主題或域的定價層。 

### <a name="overview-page"></a>概觀分頁
您可以在 **"概述"** 頁上更改主題或域的定價層。 下面的示例演示如何將主題從基本層升級到高級層。 從高級層降級到基本層的步驟類似。

1. 在[Azure 門戶](https://portal.azure.com)中，導航到主題或域頁。 
2. 在 **"概述"** 頁上，選擇當前定價層（在下面的示例中，它是**基本**.）
    
    ![選擇當前定價層](./media/update-tier/select-tier.png)
3. 在 **"定價層"** 頁上，更改層，然後選擇 **"確定**"。 

    ![更新定價層](./media/update-tier/change-tier.png)
4. 檢查 **"通知"** 頁中操作的狀態。

    ![更新狀態](./media/update-tier/status.png)    
5. 確認在 **"概述"** 頁上看到更新的層。 

    ![更新狀態](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>網路功能頁面
您可以在 **"網路"** 頁上從基本層**升級到**高級層。 但是，您無法將從高級層降級到此頁面上的基本層。 

1. 在[Azure 門戶](https://portal.azure.com)中，導航到主題或域頁。 
2. 在 **"網路"** 頁上，切換到 **"專用終結點連接（預覽）"** 選項卡。 
3. 如果當前定價層是**基本**，您將看到以下消息。 請選取它。 

    ![更新專用終結點連接頁上的層](./media/update-tier/private-endpoint-connections-page.png)
4. 在 **"更新到高級定價層"層**頁上，選擇 **"是**"。 
    
    ![確認升級](./media/update-tier/message-private-endpoint-connection.png)
5. 檢查 **"通知"** 頁中操作的狀態。

    ![更新狀態](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>使用 Azure CLI
本節介紹如何使用 Azure CLI 命令更改主題或域的定價層。 

### <a name="upgrade-a-topic-from-basic-to-premium"></a>將主題從基本升級到高級

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>將主題從高級降級為基本

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>將域從基本域升級到高級域

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>將域從高級降級為基本域

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```



## <a name="use-azure-powershell"></a>使用 Azure PowerShell
本節介紹如何使用 PowerShell 命令更改主題或域的定價層。 

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

### <a name="upgrade-a-topic-from-basic-to-premium"></a>將主題從基本升級到高級

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>將主題從高級降級為基本

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>將域從基本域升級到高級域

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>將域從高級降級為基本域

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>後續步驟
對於高級層主題和域，您可以配置專用終結點以僅限制從選定的虛擬網路進行訪問。 有關分步說明，請參閱[配置專用終結點](configure-private-endpoints.md)。
