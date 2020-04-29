---
title: 管理防火牆規則-Azure CLI-適用於 MariaDB 的 Azure 資料庫
description: 本文描述如何使用 Azure CLI 命令列，建立和管理適用於 MariaDB 的 Azure 資料庫防火牆規則。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 898b65f07140bca04bd97ff7314b01920b783914
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79530626"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>使用 Azure CLI 建立和管理適用於 MariaDB 的 Azure 資料庫防火牆規則
伺服器層級防火牆規則可用來管理從特定 IP 位址或 IP 位址範圍的適用於 MariaDB 的 Azure 資料庫伺服器存取。 透過方便的 Azure CLI 命令，您可以建立、更新、刪除、列出及顯示防火牆規則，以管理您的伺服器。 如需適用於 MariaDB 的 Azure 資料庫防火牆的總覽，請參閱[適用於 MariaDB 的 Azure 資料庫伺服器防火牆規則](./concepts-firewall-rules.md)。

虛擬網路（VNet）規則也可以用來保護對您伺服器的存取。 深入瞭解如何[使用 Azure CLI 建立和管理虛擬網路服務端點和規則](howto-manage-vnet-cli.md)。

## <a name="prerequisites"></a>先決條件
* [安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。
* [適用於 MariaDB 的 Azure 資料庫的伺服器和資料庫](quickstart-create-mariadb-server-database-using-azure-cli.md)。

## <a name="firewall-rule-commands"></a>防火牆規則命令：
**az mariadb server firewall-rule** 命令是從 Azure CLI 中用來建立、刪除、列出、顯示及更新防火牆規則。

命令：
- **create**︰建立 Azure MariaDB 伺服器防火牆規則。
- **delete**︰刪除 Azure MariaDB 伺服器防火牆規則。
- **list**：列出 Azure MariaDB 伺服器防火牆規則。
- **show**︰顯示 Azure MariaDB 伺服器防火牆規則的詳細資料。
- **update**︰更新 Azure MariaDB 伺服器防火牆規則。

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>登入 Azure 並列出您的適用於 MariaDB 的 Azure 資料庫伺服器
使用 **az login** 命令，安全地連接 Azure CLI 與 Azure 帳戶。

1. 從命令列中執行下列命令：
   ```azurecli
   az login
   ```
   此命令會輸出下一個步驟中要使用的代碼。

2. 使用網頁瀏覽器開啟頁面[https://aka.ms/devicelogin](https://aka.ms/devicelogin)，然後輸入程式碼。

3. 在提示字元中，使用您的 Azure 認證登入。

4. 當您的登入獲得授權之後，主控台就會列印訂用帳戶清單。 複製所需訂用帳戶的識別碼，以設定目前要使用的訂用帳戶。 使用 [az account set](/cli/azure/account#az-account-set) 命令。
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. 如果您不確定名稱，請列出您的訂用帳戶和資源群組的「適用於 MariaDB 的 Azure 資料庫」伺服器。 使用 [az mariadb server list](/cli/azure/mariadb/server#az-mariadb-server-list) 命令。

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   請注意清單中的名稱屬性，您需要指定要使用的 MariaDB 伺服器。 如有需要，請確認該伺服器的詳細資料，並使用名稱屬性來確認名稱正確。 使用 [az mariadb server show](/cli/azure/mariadb/server#az-mariadb-server-show) 命令。

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>在適用於 MariaDB 的 Azure 資料庫伺服器上列出防火牆規則 
使用伺服器名稱和資源群組名稱，列出伺服器上現有的伺服器防火牆規則。 使用 [az mariadb server firewall list](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-list) 命令。  請注意，伺服器名稱屬性是在 **--server** 參數中指定，而不是 **--name** 參數。 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
輸出會以 JSON 格式 (依預設) 列出規則 (若有的話)。 您可以使用 **--output table** 參數，以更容易閱讀的表格格式來輸出結果。
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>在適用於 MariaDB 的 Azure 資料庫伺服器上列出防火牆規則
使用 Azure MariaDB 伺服器名稱和資源群組名稱，在伺服器上建立新的防火牆規則。 使用 [az mariadb server firewall create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) 命令。 提供規則的名稱，以及規則的起始 IP 和結尾 IP (以提供 IP 位址範圍的存取)。
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

若要允許存取單一 IP 位址，請提供相同的 IP 位址作為起始 IP 和結尾 IP，如這個範例所示。
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

若要讓來自 Azure IP 位址的應用程式連線到您適用於 MariaDB 的 Azure 資料庫伺服器，請提供 IP 位址 0.0.0.0 作為起始 IP 和結束 IP，如此範例所示。
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> 這個選項會設定防火牆，以允許所有來自 Azure 的連線，包括來自其他客戶訂用帳戶的連線。 選取這個選項時，請確定您的登入和使用者權限會限制為只有授權的使用者才能存取。
> 

成功時，每個建立命令輸出會以 JSON 格式 (依預設) 列出您已建立之防火牆規則的詳細資料。 如果失敗，輸出就會改為顯示錯誤訊息文字。

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>在適用於 MariaDB 的 Azure 資料庫伺服器上更新防火牆規則 
使用 Azure MariaDB 伺服器名稱和資源群組名稱，在伺服器上更新現有的防火牆規則。 使用 [az mariadb server firewall update](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-update) 命令。 提供現有防火牆規則的名稱作為輸入，以及要更新的起始 IP 和結尾 IP 屬性。
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
成功時，命令輸出會以 JSON 格式 (依預設) 列出您已更新之防火牆規則的詳細資料。 如果失敗，輸出就會改為顯示錯誤訊息文字。

> [!NOTE]
> 如果防火牆規則不存在，更新命令就會建立規則。

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>在適用於 MariaDB 的 Azure 資料庫伺服器上顯示防火牆規則詳細資料
使用 Azure MariaDB 伺服器名稱和資源群組名稱，顯示伺服器上現有的防火牆規則詳細資料。 使用 [az mariadb server firewall show](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-show) 命令。 提供現有防火牆規則的名稱作為輸入。
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
成功時，命令輸出會以 JSON 格式 (依預設) 列出您已指定之防火牆規則的詳細資料。 如果失敗，輸出就會改為顯示錯誤訊息文字。

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>在適用於 MariaDB 的 Azure 資料庫伺服器上刪除防火牆規則
使用 Azure MariaDB 伺服器名稱和資源群組名稱，從伺服器上移除現有的防火牆規則。 使用 [az mariadb server firewall delete](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-delete) 命令。 提供現有防火牆規則的名稱。
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
成功時，沒有任何輸出。 發生錯誤時，就會顯示錯誤訊息文字。

## <a name="next-steps"></a>後續步驟
- 進一步了解[適用於 MariaDB 的 Azure 資料庫伺服器防火牆規則](./concepts-firewall-rules.md)。
- [使用 Azure 入口網站建立和管理適用於 MariaDB 的 Azure 資料庫防火牆規則](./howto-manage-firewall-portal.md)。
- 藉由[使用 Azure CLI 建立和管理虛擬網路服務端點和規則](howto-manage-vnet-cli.md)，進一步保護您的伺服器存取。
