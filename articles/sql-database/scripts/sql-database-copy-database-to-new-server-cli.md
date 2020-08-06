---
title: Azure CLI：將 Azure SQL Database 中的資料庫複製到新伺服器
description: 將 Azure SQL Database 中的資料庫複製到新伺服器的 Azure CLI 範例指令碼
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: e22d28215c06de96d31faec71f4fb595bb74a4a6
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495262"
---
# <a name="use-cli-to-copy-a-database-in-azure-sql-database-to-a-new-server"></a>使用 CLI 將 Azure SQL Database 中的資料庫複製到新伺服器

此 Azure CLI 指令碼範例會在新的伺服器中建立現有資料庫的複本。

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>範例指令碼

### <a name="sign-in-to-azure"></a>登入 Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>執行指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/copy-database-to-new-server/copy-database-to-new-server.sh "Copy database to new server")]

### <a name="clean-up-deployment"></a>清除部署

使用下列命令來移除資源群組及其所有相關聯的資源。

```azurecli-interactive
az group delete --name $resource
az group delete --name $targetResource
```

## <a name="sample-reference"></a>範例參考

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 描述 |
|---|---|
| [az sql db copy](/cli/azure/sql/db#az-sql-db-copy) | 為使用目前快照集的資料庫建立複本。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

其他的 SQL Database CLI 指令碼範例可於 [Azure SQL Database 文件](../../azure-sql/database/az-cli-script-samples-content-guide.md)中找到。
