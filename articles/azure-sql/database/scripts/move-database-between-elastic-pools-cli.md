---
title: Azure CLI：在彈性集區之間移動資料庫
description: 使用 Azure CLI 範例指令碼來建立兩個彈性集區，並將 SQL Database 中的資料庫從一個彈性集區移動到另一個。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 7da5e25624a0e3a323e60cdf91660b29f58f31d7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86514742"
---
# <a name="use-the-azure-cli-to-move-a-database-in-sql-database-in-a-sql-elastic-pool"></a>使用 Azure CLI 在 SQL 彈性集區中移動 SQL Database 中的資料庫

此 Azure CLI 指令碼範例會建立兩個彈性集區，將 SQL Database 中的集區資料庫從一個 SQL 彈性集區移到另一個 SQL 彈性集區，然後將集區資料庫從 SQL 彈性集區移出，成為 SQL Database 中的單一資料庫。

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="sample-script"></a>範例指令碼

### <a name="sign-in-to-azure"></a>登入 Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>執行指令碼

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Move database between pools")]

### <a name="clean-up-deployment"></a>清除部署

使用下列命令來移除資源群組及其所有相關聯的資源。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>範例參考

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 描述 |
|---|---|
| [az sql server](/cli/azure/sql/server) | 伺服器命令。 |
| [az sql elastic-pools](/cli/azure/sql/elastic-pool) | 彈性集區命令。 |
| [az sql db](/cli/azure/sql/db) | 資料庫命令。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

其他的 SQL Database CLI 指令碼範例可於 [Azure SQL Database 文件](../az-cli-script-samples-content-guide.md)中找到。
