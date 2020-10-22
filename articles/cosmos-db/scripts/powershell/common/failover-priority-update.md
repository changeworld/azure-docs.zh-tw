---
title: 此 PowerShell 指令碼用來變更具有單一寫入區域的 Azure Cosmos 帳戶的容錯移轉優先順序
description: Azure PowerShell 指令碼範例 - 對具有單一寫入區域的 Azure Cosmos 帳戶變更容錯移轉優先順序或觸發容錯移轉
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 72302f5b757bc95488cbf38888cae120611ef9e8
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92280759"
---
# <a name="change-failover-priority-or-trigger-failover-for-an-azure-cosmos-account-with-single-write-region-by-using-powershell"></a>使用 PowerShell 對具有單一寫入區域的 Azure Cosmos 帳戶變更容錯移轉優先順序或觸發容錯移轉

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>範例指令碼

> [!NOTE]
> 對 `failoverPriority=0` 的區域進行任何變更都會觸發手動容錯移轉，而且只能對設定為手動容錯移轉的帳戶進行。 所有其他區域的變更只會變更 Cosmos 帳戶的容錯移轉優先順序。
> [!NOTE]
> 此範例會示範如何使用 SQL (核心) API 帳戶。 若要針對其他 API 使用此範例，請複製相關的屬性，並套用至您的 API 專屬指令碼

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-failover-priority-update.ps1 "Update failover priority for an Azure Cosmos account or trigger a manual failover")]

## <a name="clean-up-deployment"></a>清除部署

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | 列出 Cosmos DB 帳戶，或取得指定的 Cosmos DB 帳戶。 |
| [Update-AzCosmosDBAccountFailoverPriority](https://docs.microsoft.com/powershell/module/az.cosmosdb/update-azcosmosdbaccountfailoverpriority) | 更新 Cosmos DB 帳戶區域的容錯移轉優先順序。 |
|**Azure 資源群組**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。
