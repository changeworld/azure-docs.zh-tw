---
title: 刪除資源群組和資源
description: 說明如何刪除資源群組和資源。 其中說明如何 Azure Resource Manager 在刪除資源群組時，對資源的刪除進行排序。 其會描述回應碼和資源管理員的處理方式，以判斷刪除作業是否成功。
ms.topic: conceptual
ms.date: 09/03/2019
ms.custom: seodec18
ms.openlocfilehash: db56cf0897cd90f1e6e51199032d0d9712530f1c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274017"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure Resource Manager 資源群組和資源刪除

本文說明如何刪除資源群組和資源。 其中說明當您刪除資源群組時，Azure Resource Manager 如何排序資源刪除。

## <a name="how-order-of-deletion-is-determined"></a>如何判斷刪除的順序

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

使用下列其中一種方法來刪除資源群組。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 在[入口網站](https://portal.azure.com)中，選取您想要刪除的資源群組。

1. 選取 [刪除資源群組]。

   ![刪除資源群組](./media/delete-resource-group/delete-group.png)

1. 若要確認刪除，請輸入資源群組的名稱

---

## <a name="delete-resource"></a>刪除資源

使用下列其中一種方法來刪除資源。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

1. 在[入口網站](https://portal.azure.com)中，選取您想要刪除的資源。

1. 選取 [刪除]。 下列螢幕擷取畫面顯示虛擬機器的管理選項。

   ![刪除資源](./media/delete-resource-group/delete-resource.png)

1. 出現提示時，確認刪除。

---


## <a name="next-steps"></a>後續步驟

* 若要了解 Resource Manager 概念，請參閱 [Azure Resource Manager 概觀](overview.md)。
* 如需刪除命令，請參閱 [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup)、[Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete) 和 [REST API](/rest/api/resources/resourcegroups/delete)。
