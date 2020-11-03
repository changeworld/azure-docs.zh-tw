---
title: 適用於 Azure Cosmos DB Gremlin API 的 Azure CLI 範例
description: 適用於 Azure Cosmos DB Gremlin API 的 Azure CLI 範例
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: bb992b81318c33e9b06637f8e3ad52f63fa8ed33
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93073155"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-gremlin-api"></a>適用於 Azure Cosmos DB Gremlin API 的 Azure CLI 範例
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

下表包含適用於 Azure Cosmos DB 之範例 Azure CLI 指令碼的連結。 使用右側的連結瀏覽至 API 特定範例。 所有 API 的通用範例皆相同。 您可以在 [Azure CLI 參考](/cli/azure/cosmosdb)中取得所有 Azure Cosmos DB CLI 命令的參考頁面。 您也可以在 [Azure Cosmos DB CLI GitHub 存放庫](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)中找到 Azure Cosmos DB CLI 指令碼範例。

這些範例需要 Azure CLI 2.12.1 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)

## <a name="common-samples"></a>通用範例

這些範例適用於所有 Azure Cosmos DB API

|Task | 描述 |
|---|---|
| [新增或容錯移轉區域](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | 新增區域、變更容錯移轉優先順序、觸發手動容錯移轉。|
| [帳戶金鑰和連接字串](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | 列出帳戶金鑰、唯讀金鑰、重新產生金鑰及列出連接字串。|
| [使用 IP 防火牆保護安全](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| 建立已設定 IP 防火牆的 Cosmos 帳戶。|
| [使用服務端點保護新的帳戶](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| 建立 Cosmos 帳戶並使用服務端點來保護其安全。|
| [使用服務端點保護現有帳戶](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| 終於設定好子網路後，將 Cosmos 帳戶更新為使用服務端點來保護其安全。|
|||

## <a name="gremlin-api-samples"></a>Gremlin API 範例

|Task | 描述 |
|---|---|
| [建立 Azure Cosmos 帳戶、資料庫和圖表](scripts/cli/gremlin/create.md?toc=%2fcli%2fazure%2ftoc.json)| 建立 Gremlin API 的 Azure Cosmos DB 帳戶、資料庫和圖形。 |
| [建立具有自動調整功能的 Azure Cosmos 帳戶、資料庫和圖表](scripts/cli/gremlin/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| 針對 Gremlin API，建立具有自動調整功能的 Azure Cosmos DB 帳戶、資料庫和圖表。 |
| [輸送量作業](scripts/cli/gremlin/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | 在資料庫和表格上的自動調整和標準輸送量之間進行讀取、更新及遷移。|
| [鎖定資源避免刪除](scripts/cli/gremlin/lock.md?toc=%2fcli%2fazure%2ftoc.json)| 使用資源鎖定避免刪除資源。|
|||
