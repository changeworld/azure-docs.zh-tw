---
title: 訪問慢速查詢日誌 - Azure CLI - MariaDB 的 Azure 資料庫
description: 本文介紹如何使用 Azure CLI 命令列實用程式訪問 MariaDB Azure 資料庫中的慢速日誌。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: f33a02ff0e287c135a7d63277cf3d8d3c0cd13d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527651"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>使用 Azure CLI 配置和訪問慢速查詢日誌
可以使用 Azure 命令列實用程式 Azure CLI 下載 MariaDB 慢速查詢日誌的 Azure 資料庫。

## <a name="prerequisites"></a>Prerequisites
若要逐步執行本作法指南，您需要︰
- [適用於 MariaDB 的 Azure 資料庫伺服器](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) 或瀏覽器中的 Azure Cloud Shell

## <a name="configure-logging-for-azure-database-for-mariadb"></a>設定適用於 MariaDB 的 Azure 資料庫的記錄
您可以採取下列步驟，設定伺服器以存取 MariaDB 慢速查詢記錄檔：
1. 藉由將 **slow\_query\_log** 參數設為 ON 來開啟記錄功能。
2. 調整其他參數，例如 **long\_query\_time** 和 **log\_slow\_admin\_statements**。

若要了解如何透過 Azure CLI 設定這些參數的值，請參閱[如何設定伺服器參數](howto-configure-server-parameters-cli.md)。

例如，下列 CLI 命令會開啟慢速查詢記錄檔、將長時間查詢的時間設定為 10 秒，並且會關閉慢速管理陳述式的記錄。 最後，它會列出設定選項供您檢閱。
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>列出適用於 MariaDB 的 Azure 資料庫伺服器之記錄
要列出伺服器的可用慢速查詢日誌檔，請運行[az mariadb 伺服器日誌清單](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list)命令。

您可以針對資源群組 **myresourcegroup** 下的伺服器 **mydemoserver.mariadb.database.azure.com** 列出記錄檔。 然後將記錄檔的清單導向名為 **log\_files\_list.txt** 的文字檔。
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>從伺服器下載記錄
使用 [az mariadb server-logs download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) 命令，即可下載您伺服器適用的個別記錄。

使用下列範例，針對資源群組 **myresourcegroup** 下的伺服器 **mydemoserver.mariadb.database.azure.com**，將特定的記錄檔下載至您的本機環境。
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>後續步驟
- 瞭解[MariaDB 的 Azure 資料庫中的查詢日誌速度較慢](concepts-server-logs.md)。
