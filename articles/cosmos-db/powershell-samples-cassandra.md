---
title: 適用於 Azure Cosmos DB 的 Azure PowerShell 範例- Cassandra API
description: 取得 Azure PowerShell 範例，以在 Azure Cosmos DB Cassandra API 帳戶中執行各種常見工作
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2020
ms.author: mjbrown
ms.openlocfilehash: 2efc652c936e7949147bf77ccc1548308734a467
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85260673"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---cassandra-api"></a>適用於 Azure Cosmos DB 的 Azure PowerShell 範例- Cassandra API

下表包含適用於 Azure Cosmos DB for Cassandra API 的範例 Azure PowerShell 指令碼的連結。

> [!NOTE]
> 這些範例會使用 [Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) 管理 Cmdlet。 請定期檢查是否有 `Az.CosmosDB` 的更新。

| | |
|---|---|
|[建立帳戶、keyspace 和資料表](scripts/powershell/cassandra/ps-cassandra-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立 Azure Cosmos 帳戶、keyspace 和資料表。 |
|[列出或取得 keyspace 或資料表](scripts/powershell/cassandra/ps-cassandra-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 列出或取得 keyspace 或資料表。 |
|[取得 RU/s](scripts/powershell/cassandra/ps-cassandra-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 取得 keyspace 或資料表的 RU/秒。 |
|[更新 RU/秒](scripts/powershell/cassandra/ps-cassandra-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新 keyspace 或資料表的 RU/秒。 |
|[更新帳戶或新增區域](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 將區域新增至 Cosmos 帳戶。 也可以用來修改其他帳戶屬性，但這些必須與區域的變更分開。 |
|[變更容錯移轉優先順序或觸發容錯移轉](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 變更 Azure Cosmos 帳戶的區域容錯移轉優先順序或觸發手動容錯移轉。 |
|[帳戶金鑰或連接字串](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 取得主要和次要金鑰、連接字串，或重新產生 Azure Cosmos 帳戶的帳戶金鑰。 |
|[建立具有 IP 防火牆的 Cosmos 帳戶](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立已啟用 IP 防火牆的 Azure Cosmos 帳戶。 |
|[鎖定資源避免刪除](scripts/powershell/cassandra/powershell-cassandra-lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 使用資源鎖定避免刪除資源。 |
|||
