---
title: 管理防火牆規則-Azure CLI-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 本文說明如何使用 Azure CLI 命令列來建立和管理適用於 PostgreSQL 的 Azure 資料庫單一伺服器中的防火牆規則。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 9b2c1021f88eab15a1f8803a4439dc0cb9b6ed97
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86117639"
---
# <a name="create-and-manage-firewall-rules-in-azure-database-for-postgresql---single-server-using-azure-cli"></a>使用 Azure CLI 在適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中建立和管理防火牆規則
伺服器層級防火牆規則可用來管理從特定 IP 位址或 IP 位址範圍的適用於 PostgreSQL 的 Azure 資料庫伺服器存取。 透過方便的 Azure CLI 命令，您可以建立、更新、刪除、列出及顯示防火牆規則，以管理您的伺服器。 如需適用於 PostgreSQL 的 Azure 資料庫防火牆規則的總覽，請參閱[適用於 PostgreSQL 的 Azure 資料庫伺服器防火牆規則](concepts-firewall-rules.md)。

虛擬網路（VNet）規則也可以用來保護對您伺服器的存取。 深入瞭解如何[使用 Azure CLI 建立和管理虛擬網路服務端點和規則](howto-manage-vnet-using-cli.md)。

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要︰
- 安裝 [Azure CLI](/cli/azure/install-azure-cli) 命令列公用程式，或在瀏覽器中使用 Azure Cloud Shell。
- [「適用於 PostgreSQL 的 Azure 資料庫」伺服器和資料庫](quickstart-create-server-database-azure-cli.md)。

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>設定「適用於 PostgreSQL 的 Azure 資料庫」的防火牆規則
[az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) 命令可用於設定防火牆規則。

## <a name="list-firewall-rules"></a>列出防火牆規則 
若要列出現有的伺服器防火牆規則，請執行 [az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule) 命令。
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
輸出預設會以 JSON 格式列出防火牆規則 (若有的話)。 您可以使用參數 `--output table`，以更容易閱讀的資料表格式呈現輸出。
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>建立防火牆規則
若要在伺服器上建立新的防火牆規則，請執行 [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) 命令。 

```
To allow access to a singular IP address, provide the same address in the `--start-ip-address` and `--end-ip-address`, as in this example, replacing the IP shown here with your specific IP.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
若要讓來自 Azure IP 位址的應用程式連線到您適用於 PostgreSQL 的 Azure 資料庫伺服器，請提供 IP 位址 0.0.0.0 作為起始 IP 和結束 IP，如此範例所示。
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> 這個選項會設定防火牆，以允許所有來自 Azure 的連線，包括來自其他客戶訂用帳戶的連線。 選取這個選項時，請確定您的登入和使用者權限會限制為只有授權的使用者才能存取。
> 

成功時，命令輸出會列出您已建立之防火牆規則的詳細資料，預設為 JSON 格式。 如果失敗，輸出就會改為顯示錯誤訊息。

## <a name="update-firewall-rule"></a>更新防火牆規則 
請使用 [az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule) 命令更新伺服器上現有的防火牆規則。 提供現有防火牆規則的名稱作為輸入，以及要更新的起始 IP 和結束 IP 屬性。
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
成功時，命令輸出會列出您已更新之防火牆規則的詳細資料，預設為 JSON 格式。 如果失敗，輸出就會改為顯示錯誤訊息。
> [!NOTE]
> 如果防火牆規則不存在，更新命令則會相關規則。

## <a name="show-firewall-rule-details"></a>顯示防火牆規則詳細資料
您也可以執行 [az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule) 命令，來顯示現有伺服器層級防火牆規則的詳細資料。
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
成功時，命令輸出會列出您已指定之防火牆規則的詳細資料，預設為 JSON 格式。 如果失敗，輸出就會改為顯示錯誤訊息。

## <a name="delete-firewall-rule"></a>刪除防火牆規則
若要撤銷某個 IP 範圍對伺服器的存取權，請執行 [az postgres server firewall-rule delete](/cli/azure/postgres/server/firewall-rule) 命令來刪除現有的防火牆規則。 提供現有防火牆規則的名稱。
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
成功時，沒有任何輸出。 發生錯誤時，就會傳回錯誤訊息文字。

## <a name="next-steps"></a>後續步驟
- 同樣地，您也可以透過網頁瀏覽器，[使用 Azure 入口網站建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則](howto-manage-firewall-using-portal.md)。
- 進一步了解[適用於 PostgreSQL 的 Azure 資料庫伺服器防火牆規則](concepts-firewall-rules.md)。
- 藉由[使用 Azure CLI 建立和管理虛擬網路服務端點和規則](howto-manage-vnet-using-cli.md)，進一步保護您的伺服器存取。
- 如需連接到適用於 PostgreSQL 的 Azure 資料庫伺服器的說明，請參閱適用於 PostgreSQL 的 Azure 資料庫的連線[程式庫](concepts-connection-libraries.md)。
