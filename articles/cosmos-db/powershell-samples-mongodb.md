---
title: 適用於 Azure Cosmos DB 的 Azure PowerShell 範例 - MongoDB API
description: 取得 Azure PowerShell 範例，以在 MongoDB 的 Azure Cosmos DB API 中執行各種常見工作
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: ca93b4fa85f5ef3ab87d108242e9cbfbd12036ab
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "77521858"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>適用於 Azure Cosmos DB MongoDB API 的 Azure PowerShell 範例

下表包含適用於 Azure Cosmos DB for MongoDB API 的範例 Azure PowerShell 指令碼的連結。

> [!NOTE]
> 目前，您只能使用 PowerShell、CLI 和 Resource Manager 範本，為 MongoDB 帳戶建立 3.2 版 (也就是帳戶使用的是格式為 `*.documents.azure.com` 的端點) Azure Cosmos DB 的 API。 若要建立 3.6 版的帳戶，請改用 Azure 入口網站。

| | |
|---|---|
|[建立帳戶、資料庫和集合](scripts/powershell/mongodb/ps-mongodb-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立 Azure Cosmos 帳戶、資料庫和集合。 |
|[列出或取得資料庫或集合](scripts/powershell/mongodb/ps-mongodb-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 列出或取得資料庫或集合。 |
|[取得 RU/s](scripts/powershell/mongodb/ps-mongodb-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 取得資料庫或集合的 RU/秒。 |
|[更新 RU/秒](scripts/powershell/mongodb/ps-mongodb-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新資料庫或集合的 RU/秒。 |
|[更新帳戶或新增區域](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 將區域新增至 Cosmos 帳戶。 也可以用來修改其他帳戶屬性，但這些必須與區域的變更分開。 |
|[變更容錯移轉優先順序或觸發容錯移轉](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 變更 Azure Cosmos 帳戶的區域容錯移轉優先順序或觸發手動容錯移轉。 |
|[帳戶金鑰或連接字串](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 取得主要和次要金鑰、連接字串，或重新產生 Azure Cosmos 帳戶的帳戶金鑰。 |
|[建立具有 IP 防火牆的 Cosmos 帳戶](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立已啟用 IP 防火牆的 Azure Cosmos 帳戶。 |
|||
