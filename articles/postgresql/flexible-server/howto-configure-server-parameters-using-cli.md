---
title: 設定參數-適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器
description: 本文說明如何使用 Azure CLI，在適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器中設定 Postgres 參數。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 43b14858cc99cac41e277b03171fd4cac4d6eafa
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936772"
---
# <a name="customize-server-parameters-for-azure-database-for-postgresql---flexible-server-using-azure-cli"></a>使用 Azure CLI 為適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器自訂伺服器參數

您可以使用命令列介面 (Azure CLI)，來列出、顯示和更新 Azure PostgreSQL 伺服器的設定參數。 引擎參數的子集會在伺服器層級公開，而且可以修改。 

## <a name="prerequisites"></a>必要條件

若要逐步執行本作法指南，您需要︰
- 遵循[建立適用於 PostgreSQL 的 Azure 資料庫](quickstart-create-server-cli.md)，來建立適用於 PostgreSQL 的 Azure 資料庫伺服器和資料庫。
- 在電腦上安裝 [Azure CLI](/cli/azure/install-azure-cli) 命令列介面，或透過您的瀏覽器在 Azure 入口網站中使用 [Azure Cloud Shell](../../cloud-shell/overview.md)。

## <a name="list-server-parameters-for-a-flexible-server"></a>列出彈性伺服器的伺服器參數

若要列出伺服器中所有可修改的參數及其值，請執行 [az postgres 彈性伺服器參數清單](/cli/azure/postgres/flexible-server/parameter) 命令。

您可以在 [資源群組**myresourcegroup**] 下，列出伺服器**mydemoserver.postgres.database.azure.com**的伺服器參數。

```azurecli-interactive
az postgres flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="show-server-parameter-details"></a>顯示伺服器參數詳細資料

若要顯示有關伺服器特定參數的詳細資料，請執行 [az postgres 彈性伺服器參數 show](/cli/azure/postgres/flexible-server/parameter)  命令。

此範例顯示 [資源群組 myresourcegroup] 下 [伺服器**mydemoserver.postgres.database.azure.com** ] 的 [**記錄檔 \_ 最小 \_ 訊息**] 伺服器參數的詳細資料 **。**

```azurecli-interactive
az postgres flexible-server parameter show --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="modify-server-parameter-value"></a>修改伺服器參數值

您也可以修改特定伺服器參數的值，以更新于 postgresql 伺服器引擎的基礎設定值。 若要更新參數，請使用 [az postgres 彈性伺服器參數集](/cli/azure/postgres/flexible-server/parameter) 命令。 

更新 [資源群組 myresourcegroup] 下 [伺服器**mydemoserver.postgres.database.azure.com** ] 的 [**記錄檔 \_ 最小 \_ 訊息**] 伺服器參數 **。**

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --value INFO --resource-group myresourcegroup --server-name mydemoserver
```

如果您想要重設參數的值，只需選擇省略選擇性 `--value` 參數，服務就會套用預設值。 在上述範例中，看起來應該像這樣：

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

此命令會將 **log \_ min \_ messages** 參數重設為預設值 **WARNING**。 如需有關伺服器參數和允許值的詳細資訊，請參閱于 postgresql [設定參數](https://www.postgresql.org/docs/12/config-setting.html)的檔。

## <a name="next-steps"></a>下一步

- 若要設定及存取伺服器記錄，請參閱[適用於 PostgreSQL 的 Azure 資料庫中的伺服器記錄](concepts-logging.md)
