---
title: 此 PowerShell 指令碼用來變更具有單一寫入區域的 Azure Cosmos 帳戶的容錯移轉優先順序
description: Azure PowerShell 指令碼範例 - 對具有單一寫入區域的 Azure Cosmos 帳戶變更容錯移轉優先順序或觸發容錯移轉
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: ef9b16ae56c560d033cb18de6a3ccf3b87fbc25a
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684335"
---
# <a name="change-failover-priority-or-trigger-failover-for-an-azure-cosmos-account-with-single-write-region-by-using-powershell"></a>使用 PowerShell 對具有單一寫入區域的 Azure Cosmos 帳戶變更容錯移轉優先順序或觸發容錯移轉
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

此範例需要 Azure PowerShell Az 5.4.0 或更新版本。 執行 `Get-Module -ListAvailable Az` 可查看已安裝的版本。
如果您需要安裝，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

執行 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 來登入 Azure。

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
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | 列出 Cosmos DB 帳戶，或取得指定的 Cosmos DB 帳戶。 |
| [Update-AzCosmosDBAccountFailoverPriority](/powershell/module/az.cosmosdb/update-azcosmosdbaccountfailoverpriority) | 更新 Cosmos DB 帳戶區域的容錯移轉優先順序。 |
|**Azure 資源群組**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/)。