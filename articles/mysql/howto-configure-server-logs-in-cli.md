---
title: 存取慢速查詢紀錄 ─ Azure CLI - MySQL 的 Azure 資料庫
description: 本文介紹如何使用 Azure CLI 訪問 MySQL Azure 資料庫中的慢速查詢日誌。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: 87db1a2af0bfdc854c909ef4221a3d97f9bf10d5
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270667"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>使用 Azure CLI 設定及存取慢速查詢紀錄
可以使用 Azure 命令列實用程式 Azure CLI 下載 MySQL 慢速查詢日誌的 Azure 資料庫。

## <a name="prerequisites"></a>Prerequisites
若要逐步執行本作法指南，您需要︰
- [MySQL 伺服器的 Azure 資料庫](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) 或瀏覽器中的 Azure Cloud Shell

## <a name="configure-logging"></a>設定記錄
您可以採取下列步驟，設定伺服器以存取 MySQL 慢速查詢記錄檔：
1. 通過將**慢速查詢\_\_日誌**參數設置為 ON,打開慢速查詢日誌記錄。
2. 選擇將日誌輸出到使用**日誌\_輸出**的位置。 要將紀錄傳送到本地儲存和 Azure 監視器診斷紀錄,請選擇「**檔案**」。 要僅將日誌發送到 Azure 監視器日誌,請選擇 **"無"**
3. 調整其他參數，例如 **long\_query\_time** 和 **log\_slow\_admin\_statements**。

若要了解如何透過 Azure CLI 設定這些參數的值，請參閱[如何設定伺服器參數](howto-configure-server-parameters-using-cli.md)。

例如，下列 CLI 命令會開啟慢速查詢記錄檔、將長時間查詢的時間設定為 10 秒，並且會關閉慢速管理陳述式的記錄。 最後，它會列出設定選項供您檢閱。
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>列出適用於 MySQL 的 Azure 資料庫伺服器之記錄
如果**log_output**設定為「檔」,則可以直接從伺服器的本地存儲訪問日誌。 要列出伺服器的可用慢速查詢日誌檔,請運行[az mysql 伺服器日誌清單](/cli/azure/mysql/server-logs#az-mysql-server-logs-list)命令。

您可以針對資源群組 **myresourcegroup** 下的伺服器 **mydemoserver.mysql.database.azure.com** 列出記錄檔。 然後將記錄檔的清單導向名為 **log\_files\_list.txt** 的文字檔。
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>從伺服器下載記錄
如果**log_output**設定為「檔」,您可以使用[az mysql 伺服器日誌下載](/cli/azure/mysql/server-logs#az-mysql-server-logs-download)命令從伺服器下載單個紀錄檔。

使用下列範例，針對資源群組 **myresourcegroup** 下的伺服器 **mydemoserver.mysql.database.azure.com**，將特定的記錄檔下載至您的本機環境。
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>後續步驟
- 瞭解[MySQL 的 Azure 資料庫中查詢紀錄速度慢](concepts-server-logs.md)。
