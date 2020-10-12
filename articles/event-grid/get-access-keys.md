---
title: 取得事件方格資源的存取金鑰
description: 本文說明如何取得事件方格主題或網域的存取金鑰
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: e5694fe0b5f22f7f76285c344627005ea727ae3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86105858"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>取得事件方格資源 (主題或網域的存取金鑰) 
存取金鑰是用來驗證應用程式將事件發佈至 Azure 事件方格資源 (主題和網域) 。 建議您定期重新產生金鑰，並安全地加以儲存。 您會提供兩個存取金鑰，讓您可以使用一個金鑰來維護連接，同時重新產生另一個金鑰。

本文說明如何使用 Azure 入口網站、PowerShell 或 CLI，取得事件方格資源 (主題或網域) 的存取金鑰。 

## <a name="azure-portal"></a>Azure 入口網站
在 [Azure 入口網站中，針對您的主題或網域，切換到**事件方格主題**或**事件方格網域**頁面的 [**存取金鑰**] 索引標籤。  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="存取金鑰頁面":::

## <a name="azure-powershell"></a>Azure PowerShell
使用 [AzEventGridTopicKey](/powershell/module/az.eventgrid/get-azeventgridtopickey?view=azps-4.3.0) 命令取得主題的存取金鑰。 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

使用 [AzEventGridDomainKey](/powershell/module/az.eventgrid/get-azeventgriddomainkey?view=azps-4.3.0) 命令取得網域的存取金鑰。 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Azure CLI
使用 [az eventgrid 主題索引鍵清單](/cli/azure/eventgrid/topic/key?view=azure-cli-latest#az-eventgrid-topic-key-list) 取得主題的存取金鑰。 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

使用 [az eventgrid domain key list](/cli/azure/eventgrid/domain/key?view=azure-cli-latest#az-eventgrid-domain-key-list) 來取得網域的存取金鑰。 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>接下來的步驟
請參閱下列文章： [驗證發行用戶端](security-authenticate-publishing-clients.md)。 
