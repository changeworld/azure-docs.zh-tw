---
title: 取得事件方格資源的存取金鑰
description: 本文說明如何取得事件方格主題或網域的存取金鑰
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 06/25/2020
ms.author: spelluru
ms.openlocfilehash: 75ab09de40a4305ebd215985f2f9f181bfd69a24
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85414832"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>取得事件方格資源的存取金鑰（主題或網域）
存取金鑰是用來向 Azure 事件方格資源（主題和網域）驗證應用程式發佈事件。 我們建議您定期重新產生金鑰，並將其安全地儲存。 系統會提供兩個存取金鑰，讓您可以使用一個金鑰來維護連線，同時重新產生另一個金鑰。

本文說明如何使用 Azure 入口網站、PowerShell 或 CLI，取得事件方格資源（主題或網域）的存取金鑰。 

## <a name="azure-portal"></a>Azure 入口網站
在 [Azure 入口網站中，針對您的主題或網域，切換到**事件方格主題**或**事件方格網域**頁面的 [**存取金鑰**] 索引標籤。  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="[存取金鑰] 頁面":::

## <a name="azure-powershell"></a>Azure PowerShell
使用[AzEventGridTopicKey](/powershell/module/az.eventgrid/get-azeventgridtopickey?view=azps-4.3.0)命令來取得主題的存取金鑰。 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

使用[AzEventGridDomainKey](/powershell/module/az.eventgrid/get-azeventgriddomainkey?view=azps-4.3.0)命令取得網域的存取金鑰。 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Azure CLI
使用[az eventgrid 主題索引鍵清單](/cli/azure/eventgrid/topic/key?view=azure-cli-latest#az-eventgrid-topic-key-list)來取得主題的存取金鑰。 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

使用[az eventgrid domain key list](/cli/azure/eventgrid/domain/key?view=azure-cli-latest#az-eventgrid-domain-key-list)來取得網域的存取金鑰。 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>後續步驟
請參閱下列文章：[驗證發佈用戶端](security-authenticate-publishing-clients.md)。 
