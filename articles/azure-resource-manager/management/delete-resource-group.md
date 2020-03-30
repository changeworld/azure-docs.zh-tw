---
title: 刪除資源組和資源
description: 描述如何刪除資源組和資源。 它描述了 Azure 資源管理器在刪除資源組時如何命令刪除資源。 其會描述回應碼和資源管理員的處理方式，以判斷刪除作業是否成功。
ms.topic: conceptual
ms.date: 09/03/2019
ms.custom: seodec18
ms.openlocfilehash: db56cf0897cd90f1e6e51199032d0d9712530f1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274017"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure 資源管理器資源組和資源刪除

本文演示如何刪除資源組和資源。 它描述了 Azure 資源管理器在刪除資源組時如何命令刪除資源。

## <a name="how-order-of-deletion-is-determined"></a>如何確定刪除順序

當您刪除資源群組時，Resource Manager 會決定刪除資源的順序。 它使用下列順序：

1. 刪除所有子 (巢狀) 資源。

2. 接著刪除管理其他資源的資源。 資源可以設定 `managedBy` 屬性，指出由不同的資源來管理它。 設定此屬性時，會先刪除管理其他資源的資源，再刪除這些資源。

3. 在前兩個類別之後，刪除其餘資源。

決定順序之後，Resource Manager 會針對每個資源發出 DELETE 作業。 它會等候任何相依性完成，再繼續進行。

針對同步作業，預期的成功回應碼為：

* 200
* 204
* 404

針對非同步作業，預期的成功回應碼為 202。 Resource Manager 會追蹤位置標頭和 azure-async 作業標頭，以判斷非同步刪除作業的狀態。
  
### <a name="deletion-errors"></a>刪除錯誤

當刪除作業傳回錯誤時，Resource Manager 會重試 DELETE 呼叫。 若狀態碼為 5xx、429 和 408，則會發生重試。 根據預設，重試的時間週期為 15 分鐘。

## <a name="after-deletion"></a>刪除後

Resource Manager 會在嘗試刪除的每個資源上發出 GET 呼叫。 此 GET 呼叫的回應必須是 404。 當 Resource Manager 取得 404 時，則刪除已成功完成。 Resource Manager 會從其快取中移除資源。

不過，如果資源上的 GET 呼叫傳回 200 或 201，Resource Manager 會重新建立資源。

如果 GET 作業傳回錯誤，Resource Manager 會針對下列錯誤碼重試 GET：

* 小於 100
* 408
* 429
* 大於 500

若是其他錯誤碼，則 Resource Manager 無法刪除資源。

## <a name="delete-resource-group"></a>刪除資源群組

使用以下方法之一刪除資源組。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 在[門戶](https://portal.azure.com)中，選擇要刪除的資源組。

1. 選取 [刪除資源群組]****。

   ![刪除資源群組](./media/delete-resource-group/delete-group.png)

1. 要確認刪除，請鍵入資源組的名稱

---

## <a name="delete-resource"></a>刪除資源

使用以下方法之一刪除資源。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 在[門戶](https://portal.azure.com)中，選擇要刪除的資源。

1. 選擇 **"刪除**"。 以下螢幕截圖顯示了虛擬機器的管理選項。

   ![刪除資源](./media/delete-resource-group/delete-resource.png)

1. 出現提示時，確認刪除。

---


## <a name="next-steps"></a>後續步驟

* 若要了解 Resource Manager 概念，請參閱 [Azure Resource Manager 概觀](overview.md)。
* 如需刪除命令，請參閱 [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup)、[Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete) 和 [REST API](/rest/api/resources/resourcegroups/delete)。
