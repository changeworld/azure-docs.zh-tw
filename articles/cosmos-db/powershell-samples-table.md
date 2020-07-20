---
title: 適用於 Azure Cosmos DB 的 Azure PowerShell 範例 - 資料表 API
description: 取得 Azure PowerShell 範例，以在 Azure Cosmos DB 資料表 API 帳戶中執行各種常見工作
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2020
ms.author: mjbrown
ms.openlocfilehash: 751fa9daabaaef05abb5df8229e84975b87cffc1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563835"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---table-api"></a>適用於 Azure Cosmos DB 的 Azure PowerShell 範例 - 資料表 API

針對適用於資料表 API 的 Azure Cosmos DB，下表包含範例 Azure PowerShell 指令碼的連結。

> [!NOTE]
> 這些範例會使用 [Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) 管理 Cmdlet。 請定期檢查是否有 `Az.CosmosDB` 的更新。

|Task | 描述 |
|---|---|
|[建立帳戶和資料表](scripts/powershell/table/ps-table-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立 Azure Cosmos 帳戶和資料表。 |
|[列出或取得資料表](scripts/powershell/table/ps-table-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 列出或取得資料表 |
|[取得 RU/s](scripts/powershell/table/ps-table-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 取得資料表的 RU/秒。 |
|[更新 RU/秒](scripts/powershell/table/ps-table-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新資料表的 RU/秒。 |
|[更新帳戶或新增區域](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 將區域新增至 Cosmos 帳戶。 也可以用來修改其他帳戶屬性，但這些必須與區域的變更分開。 |
|[變更容錯移轉優先順序或觸發容錯移轉](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 變更 Azure Cosmos 帳戶的區域容錯移轉優先順序或觸發手動容錯移轉。 |
|[帳戶金鑰或連接字串](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 取得主要和次要金鑰、連接字串，或重新產生 Azure Cosmos 帳戶的帳戶金鑰。 |
|[建立具有 IP 防火牆的 Cosmos 帳戶](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立已啟用 IP 防火牆的 Azure Cosmos 帳戶。 |
|[鎖定資源避免刪除](scripts/powershell/table/powershell-table-lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 使用資源鎖定避免刪除資源。 |
|||
