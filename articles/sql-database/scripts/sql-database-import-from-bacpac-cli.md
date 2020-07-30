---
title: Azure CLI：將 BACPAC 檔案匯入到 Azure SQL Database 中的資料庫
description: 將 BACPAC 檔案匯入 Azure SQL Database 中資料庫的 Azure CLI 範例指令碼
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: load & move data
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 05/24/2019
ms.openlocfilehash: 6477d8f59d66c053ced4ceebed98082d51f92c2f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075993"
---
# <a name="use-cli-to-import-a-bacpac-file-into-a-database-in-sql-database"></a>使用 CLI 將 BACPAC 檔案匯入 SQL Database 中的資料庫

此 Azure CLI 指令碼範例會從 *.bacpac* 檔案將資料庫匯入 SQL Database 中的資料庫。  

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>範例指令碼

### <a name="sign-in-to-azure"></a>登入 Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>執行指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/import-from-bacpac/import-from-bacpac.sh "Create SQL Database")]

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
| [az sql db import](/cli/azure/sql/db#az-sql-db-import) | 資料庫匯入命令。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

其他的 SQL Database CLI 指令碼範例可於 [Azure SQL Database 文件](../../azure-sql/database/az-cli-script-samples-content-guide.md)中找到。
