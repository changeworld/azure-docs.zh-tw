---
title: 可為 Azure Cosmos Cassandra API Keyspace 與資料表建立資源鎖定的 PowerShell 指令碼
description: 為 Azure Cosmos Cassandra API Keyspace 與資料表建立資源鎖定
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: f8e1e2172137c1c6a569fb169c59191ab11cedfe
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684353"
---
# <a name="create-a-resource-lock-for-azure-cosmos-cassandra-api-keyspace-and-table-using-azure-powershell"></a>使用 Azure PowerShell 為 Azure Cosmos Cassandra API Keyspace 與資料表建立資源鎖定
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

此範例需要 Azure PowerShell Az 5.4.0 或更新版本。 執行 `Get-Module -ListAvailable Az` 可查看已安裝的版本。
如果您需要安裝，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

執行 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 來登入 Azure。

> [!IMPORTANT]
> 除非先啟用 `disableKeyBasedMetadataWriteAccess` 屬性鎖定 Cosmos DB 帳戶，否則資源鎖定對使用任何 Cassandra SDK、CQL Shell 或 Azure 入口網站進行連線之使用者所做的變更無效。 若要深入了解如何啟用此屬性，請參閱[防止來自 SDK 的變更](../../../role-based-access-control.md#prevent-sdk-changes) (機器翻譯)。

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>清除部署

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
|**Azure 資源**| |
| [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) | 建立資源鎖定。 |
| [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock) | 取得資源鎖定，或列出資源鎖定。 |
| [Remove-AzResourceLock](/powershell/module/az.resources/remove-azresourcelock) | 移除資源鎖定。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/)。