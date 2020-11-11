---
title: 設定伺服器參數-Azure CLI 適用於 MySQL 的 Azure 資料庫彈性伺服器
description: 本文說明如何使用 Azure CLI 命令列公用程式，在適用於 MySQL 的 Azure 資料庫彈性的伺服器中設定服務參數。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 11/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 58e7c024d6494aee745884997e42b527c51ab237
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489534"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>使用 Azure CLI 在適用於 MySQL 的 Azure 資料庫彈性的伺服器中設定伺服器參數

> [!IMPORTANT] 
> 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器目前處於公開預覽狀態。

您可以使用 Azure 命令列公用程式 Azure CLI，來列出、顯示及更新適用於 MySQL 的 Azure 資料庫彈性伺服器的參數。 當您建立伺服器時，伺服器參數會設定為預設值和建議值。  

本文描述如何使用 Azure CLI 來列出、顯示和補救伺服器參數。

>[!Note]
> 伺服器參數可以在伺服器層級進行全域更新、使用 [Azure CLI](./how-to-configure-server-parameters-cli.md) 或 [Azure 入口網站](./how-to-configure-server-parameters-portal.md)

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要︰
- [適用於 MySQL 的 Azure 資料庫彈性的伺服器](quickstart-create-server-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) 命令列公用程式，或在瀏覽器中使用 Azure Cloud Shell。

## <a name="list-server-parameters-for-azure-database-for-mysql-flexible-server"></a>列出適用於 MySQL 的 Azure 資料庫彈性伺服器的伺服器參數
若要列出伺服器中的所有參數及其值，請執行 [az mysql 彈性伺服器參數清單](/cli/azure/mysql/flexible-server/parameter) 命令。

您可以在 [資源群組 **myresourcegroup** ] 下，列出伺服器 **mydemoserver.mysql.database.azure.com** 的伺服器參數。
```azurecli-interactive
az mysql flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```
如需每個列出參數的定義，請參閱[伺服器系統變數](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html) \(英文\) 中的 MySQL 參考小節。

## <a name="show-server-parameter-details"></a>顯示伺服器參數詳細資料
若要顯示有關伺服器特定參數的詳細資料，請執行 [az mysql 彈性伺服器參數 show](/cli/azure/mysql/flexible-server/parameter) 命令。

此範例會顯示 [資源群組 myresourcegroup] 下 [伺服器 **mydemoserver.mysql.database.azure.com** ] **慢速 \_ 查詢 \_ 記錄** 伺服器參數的詳細資料 **。**
```azurecli-interactive
az mysql flexible-server parameter show --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
## <a name="modify-a-server-parameter-value"></a>修改伺服器參數值
您也可以修改特定伺服器參數的值，以更新 MySQL 伺服器引擎的基礎設定值。 若要補救伺服器參數，請使用 [az mysql 彈性伺服器參數集](/cli/azure/mysql/flexible-server/parameter) 命令。 

更新 [資源群組 myresourcegroup] 下 [伺服器 **mydemoserver.mysql.database.azure.com** ] 的 **慢速 \_ 查詢 \_ 記錄** 伺服器參數 **。**
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver --value ON
```
如果您想要重設參數的值，請省略選擇性 `--value` 參數，而服務會套用預設值。 針對上述範例，看起來應該像這樣：
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
此程式碼會將 **慢速 \_ 查詢 \_ 記錄** 重設為預設值 **OFF** 。 

## <a name="setting-non-modifiable-server-parameters"></a>設定不可修改的伺服器參數

如果您要更新的伺服器參數不可修改，您可以選擇性地使用在連接層級設定參數 `init_connect` 。 這會為每個連接到伺服器的用戶端設定伺服器參數。 

更新 [資源群組 **myresourcegroup** ] 下的 [伺服器 **mydemoserver.mysql.database.azure.com** ] 的 [ **init connect 伺服器 \_ ]** 參數，以設定值，例如 [字元集]。
```azurecli-interactive
az mysql flexible-server parameter set --name init_connect --resource-group myresourcegroup --server-name mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```
>[!Note]
> `init_connect` 可用來變更在工作階段層級不需要 SUPER 權限的參數。 若要確認您是否可以使用 `init_connect` 來設定參數，請執行 `set session parameter_name=YOUR_DESIRED_VALUE;` 命令；如果出現「拒絕存取；您需要 SUPER 權限」錯誤，表示您無法使用 'init_connect' 來設定參數。

## <a name="working-with-the-time-zone-parameter"></a>使用時區參數

### <a name="populating-the-time-zone-tables"></a>填入時區資料表

伺服器上的時區資料表，可以藉由從 MySQL 命令列或 MySQL Workbench 等工具呼叫 `mysql.az_load_timezone` 預存程序來填入。

> [!NOTE]
> 如果您是從 MySQL Workbench 執行 `mysql.az_load_timezone` 命令，您可能需要先執行 `SET SQL_SAFE_UPDATES=0;` 以關閉安全更新模式。

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> 建議重新開機伺服器，以確保正確填入時區資料表。<!-- fIX me To restart the server, use the [Azure portal](howto-restart-server-portal.md) or [CLI](howto-restart-server-cli.md). -->

若要檢視可用的時區值，請執行以下命令：

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>設定全域層級時區

全域層級時區可以使用 [az mysql 彈性伺服器參數集](/cli/azure/mysql/flexible-server/parameter) 命令來設定。

下列命令會將 [資源群組 **myresourcegroup** ] 下的 [伺服器 **mydemoserver.mysql.database.azure.com** ] 的 **時區 \_** 伺服器參數更新為 **美國/太平洋** 。

```azurecli-interactive
az mysql flexible-server parameter set --name time_zone --resource-group myresourcegroup --server-name mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>設定工作階段層級時區

工作階段層級時區可以藉由從 MySQL 命令列或 MySQL Workbench 等工具呼叫 `SET time_zone` 命令來設定。 以下範例將時區設為 **US/Pacific** 時區。  

```sql
SET time_zone = 'US/Pacific';
```

如需[日期和時間函式](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz) \(英文\) 的詳細資訊，請參閱 MySQL 文件。


## <a name="next-steps"></a>後續步驟

- 如何設定 [Azure 入口網站中的伺服器參數](./how-to-configure-server-parameters-portal.md)
