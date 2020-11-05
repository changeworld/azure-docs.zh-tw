---
title: 在 Azure 防火牆中建立 IP 群組
description: IP 群組可讓您為 Azure 防火牆規則分組及管理 IP 位址。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c53b35351108717c7b597e052a66e9902be5ec6c
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394521"
---
# <a name="create-ip-groups"></a>建立 IP 群組

IP 群組可讓您為 Azure 防火牆規則分組及管理 IP 位址。 它們可以有單一 IP 位址、多個 IP 位址，或一或多個 IP 位址範圍。

## <a name="create-an-ip-group---azure-portal"></a>建立 IP 群組-Azure 入口網站

若要使用 Azure 入口網站建立 IP 群組：

1. 在 Azure 入口網站的首頁上，選取 [ **建立資源** ]。
1. 在 [搜尋] 方塊中，輸入 **Ip 群組** ，然後選取 [ **ip 群組** ]。
1. 選取 [建立]  。
1. 選取您的訂用帳戶。
1. 選取資源群組或建立新的資源群組。
1. 為您的 IP 群組輸入唯一的名稱，然後選取區域。
1. 完成時，選取 下一步: **IP 位址** 。
1. 輸入 IP 位址、多個 IP 位址或 IP 位址範圍。

   有兩種方式可以輸入 IP 位址：
   - 您可以手動輸入
   - 您可以從檔案匯入它們

   若要從檔案匯入，請選取 [從檔案匯 **入** ]。 您可以將檔案拖曳到方塊中，或選取 **[流覽檔案]** 。 如有必要，您可以檢查和編輯已上傳的 IP 位址。

   當您輸入 IP 位址時，入口網站會進行驗證以檢查重迭、重複和格式化問題。

1. 完成時，選取 [ **審核 + 建立** ]。
1. 選取 [建立]  。

## <a name="create-an-ip-group---azure-powershell"></a>建立 IP 群組-Azure PowerShell

此範例會使用 Azure PowerShell 來建立具有位址首碼和 IP 位址的 IP 群組：

```azurepowershell
$ipGroup = @{
    Name              = 'ipGroup'
    ResourceGroupName = 'ipGroupRG'
    Location          = 'West US'
    IpAddress         = @('10.0.0.0/24', '192.168.1.10') 
}

New-AzIpGroup @ipGroup
```

## <a name="create-an-ip-group---azure-cli"></a>建立 IP 群組-Azure CLI

此範例會使用 Azure CLI，建立具有位址首碼和 IP 位址的 IP 群組：

```azurecli-interactive
az network ip-group create \
    --name ipGroup \ 
    --resource-group ipGroupRG \
    --location westus \
    --ip-addresses '10.0.0.0/24' '192.168.1.10'
```

## <a name="next-steps"></a>後續步驟

- [深入瞭解 IP 群組](ip-groups.md)
