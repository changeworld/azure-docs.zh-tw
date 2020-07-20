---
title: 適用於 Azure Cosmos DB 的 Azure PowerShell 範例 - Gremlin API
description: 取得 Azure PowerShell 範例，以在 Azure Cosmos DB Gremlin API 帳戶中執行各種常見工作
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2020
ms.author: mjbrown
ms.openlocfilehash: 62036a507061a956febb1eb63e6607abcce7467c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563888"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-gremlin-api"></a>適用於 Azure Cosmos DB 的 Azure PowerShell 範例 - Gremlin API

下表包含適用於 Azure Cosmos DB for Gremlin API 的範例 Azure PowerShell 指令碼的連結。

> [!NOTE]
> 這些範例會使用 [Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) 管理 Cmdlet。 請定期檢查是否有 `Az.CosmosDB` 的更新。

|Task | 描述 |
|---|---|
|[建立帳戶、資料庫和圖表](scripts/powershell/gremlin/ps-gremlin-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立 Azure Cosmos 帳戶、資料庫和圖表。 |
|[列出或取得資料庫或圖表](scripts/powershell/gremlin/ps-gremlin-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 列出或取得資料庫或圖表。 |
|[取得 RU/s](scripts/powershell/gremlin/ps-gremlin-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 取得資料庫或圖表的 RU/秒。 |
|[更新 RU/秒](scripts/powershell/gremlin/ps-gremlin-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新資料庫或圖表的 RU/秒。 |
|[更新帳戶或新增區域](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 將區域新增至 Cosmos 帳戶。 也可以用來修改其他帳戶屬性，但這些必須與區域的變更分開。 |
|[變更容錯移轉優先順序或觸發容錯移轉](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 變更 Azure Cosmos 帳戶的區域容錯移轉優先順序或觸發手動容錯移轉。 |
|[帳戶金鑰或連接字串](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 取得主要和次要金鑰、連接字串，或重新產生 Azure Cosmos 帳戶的帳戶金鑰。 |
|[建立具有 IP 防火牆的 Cosmos 帳戶](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立已啟用 IP 防火牆的 Azure Cosmos 帳戶。 |
|[鎖定資源避免刪除](scripts/powershell/gremlin/powershell-gremlin-lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 使用資源鎖定避免刪除資源。 |
|||
