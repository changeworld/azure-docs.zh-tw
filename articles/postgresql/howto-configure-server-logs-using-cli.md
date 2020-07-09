---
title: 記錄管理-Azure CLI 適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 本文說明如何使用 Azure CLI，在適用於 PostgreSQL 的 Azure 資料庫單一伺服器中設定和存取伺服器記錄檔（.log 檔案）。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 1346d1402b46088ce0bed015c723cf817d982317
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118098"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>使用 Azure CLI 設定和存取伺服器記錄
您可以使用命令列介面 (Azure CLI) 來下載 PostgreSQL 伺服器錯誤記錄。 不過，不支援存取交易記錄。 

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要︰
- [適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-database-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) 命令列公用程式，或瀏覽器中的 Azure Cloud Shell

## <a name="configure-logging"></a>設定記錄
您可以設定伺服器來存取查詢記錄和錯誤記錄。 錯誤記錄可包含自動清空、連線和檢查點資訊。
1. 開啟記錄功能。
2. 若要啟用查詢記錄，請更新 **log\_statement** 和 **log\_min\_duration\_statement**。
3. 更新保留期限。

如需詳細資訊，請參閱[自訂伺服器設定參數](howto-configure-server-parameters-using-cli.md)。

## <a name="list-logs"></a>列出記錄
若要列出伺服器的可用記錄，請執行 [az postgres server-logs list](/cli/azure/postgres/server-logs) 命令。

您可以針對資源群組 **myresourcegroup** 下的伺服器 **mydemoserver.postgres.database.azure.com** 列出記錄檔。 然後將記錄檔的清單導向名為 **log\_files\_list.txt** 的文字檔。
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>從伺服器將記錄下載至本機
使用 [az postgres server-logs download](/cli/azure/postgres/server-logs) 命令，即可下載您伺服器適用的個別記錄。 

使用下列範例，針對資源群組 **myresourcegroup** 下的伺服器 **mydemoserver.postgres.database.azure.com**，將特定的記錄檔下載至您的本機環境。
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>後續步驟
- 若要深入瞭解伺服器記錄，請參閱[適用於 PostgreSQL 的 Azure 資料庫中的伺服器記錄](concepts-server-logs.md)。
- 如需伺服器參數的詳細資訊，請參閱[使用 Azure CLI 自訂伺服器設定參數](howto-configure-server-parameters-using-cli.md)。
