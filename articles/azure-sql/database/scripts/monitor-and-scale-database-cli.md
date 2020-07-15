---
title: Azure CLI：監視和調整 Azure SQL Database 中的單一資料庫
description: 使用 Azure CLI 範例指令碼監視和調整 Azure SQL Database 中的單一資料庫。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: bd2f8005300aeb77a88be2609246f2d760154e36
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231993"
---
# <a name="use-the-azure-cli-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>使用 Azure CLI 監視和調整 Azure SQL Database 中的單一資料庫

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

此 Azure CLI 指令碼範例會在查詢 Azure SQL Database 中的單一資料庫的大小資訊後，將其調整為不同的計算大小。

如果您選擇在本機安裝和使用 Azure CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>範例指令碼

### <a name="sign-in-to-azure"></a>登入 Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>執行指令碼

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale a database in Azure SQL Database")]

> [!TIP]
> 使用 [az sql db op list](/cli/azure/sql/db/op?#az-sql-db-op-list) 來取得在資料庫上執行的作業清單，以及使用 [az sql db op cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel) 來取消資料庫的更新作業。

### <a name="clean-up-deployment"></a>清除部署

使用下列命令來移除資源群組及其所有相關聯的資源。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>範例參考

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 指令碼 | 描述 |
|---|---|
| [az sql server](/cli/azure/sql/server) | 伺服器命令。 |
| [az sql db show-usage](/cli/azure/sql#az-sql-show-usage) | 顯示資料庫的大小使用量資訊。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure CLI 範例指令碼](../az-cli-script-samples-content-guide.md)中找到其他的 CLI 指令碼範例。
