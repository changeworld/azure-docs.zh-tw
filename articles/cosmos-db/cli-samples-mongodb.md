---
title: Azure Cosmos DB MongoDB API 的 Azure CLI 範例
description: Azure Cosmos DB MongoDB API 的 Azure CLI 範例
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: 7536a2e3fc32aeb330aad52625a5946e856d6646
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85556045"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-mongodb-api"></a>Azure Cosmos DB MongoDB API 的 Azure CLI 範例

下表包含適用於 Azure Cosmos DB MongoDB API 之範例 Azure CLI 指令碼的連結。 您可以在 [Azure CLI 參考](/cli/azure/cosmosdb)中取得所有 Azure Cosmos DB CLI 命令的參考頁面。 您可以在 [Azure Cosmos DB CLI GitHub 存放庫](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)中找到所有 Azure Cosmos DB CLI 指令碼範例。

> [!NOTE]
> 目前，您只能使用 PowerShell、CLI 和 Resource Manager 範本，為 MongoDB 帳戶建立 3.2 版 (也就是帳戶使用的是格式為 `*.documents.azure.com` 的端點) Azure Cosmos DB 的 API。 若要建立 3.6 版的帳戶，請改用 Azure 入口網站。

|Task | 描述 |
|---|---|
| [建立 Azure Cosmos 帳戶、資料庫和集合](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| 建立適用於 MongoDB API 的 Azure Cosmos DB 帳戶、資料庫和集合。 |
| [變更輸送量](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | 更新資料庫或集合的 RU/秒。|
| [新增或容錯移轉區域](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | 新增區域、變更容錯移轉優先順序、觸發手動容錯移轉。|
| [帳戶金鑰和連接字串](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | 列出帳戶金鑰、唯讀金鑰、重新產生金鑰及列出連接字串。|
| [使用 IP 防火牆保護安全](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| 建立已設定 IP 防火牆的 Cosmos 帳戶。|
| [使用服務端點保護新的帳戶](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| 建立 Cosmos 帳戶並使用服務端點來保護其安全。|
| [使用服務端點保護現有帳戶](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| 終於設定好子網路後，將 Cosmos 帳戶更新為使用服務端點來保護其安全。|
| [鎖定資源避免刪除](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| 使用資源鎖定來防止刪除資源。|
|||
