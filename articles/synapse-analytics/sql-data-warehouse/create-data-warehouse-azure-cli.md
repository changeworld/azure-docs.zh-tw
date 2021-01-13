---
title: 快速入門：使用 Azure CLI 建立 Synapse SQL 集區
description: 使用 Azure CLI，透過伺服器層級的防火牆規則快速建立 Synapse SQL 集區。
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: Kevin
ms.custom: azure-synapse
ms.openlocfilehash: 2920c96d6a2e505347edb51780101327cb884e08
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120167"
---
# <a name="quickstart-create-a-synapse-sql-pool-with-azure-cli"></a>快速入門：使用 Azure CLI 建立 Synapse SQL 集區

使用 Azure CLI 在 Azure Synapse Analytics 中建立 Synapse SQL 集區 (資料倉儲)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="getting-started"></a>開始使用

使用下列命令，登入 Azure 並設定資源群組。

1. 如果您使用本機安裝，請執行 [az login](/cli/azure/reference-index#az_login) 命令以登入 Azure：

   ```azurecli
   az login
   ```

1. 如有需要，請使用 [az account set](/cli/azure/account#az_account_set) 命令選取您的訂用帳戶：

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. 執行 [az group create](/cli/azure/group#az_group_create) 命令以建立資源群組：

   ```azurecli
   az group create --name myResourceGroup --location WestEurope
   ```

1. 使用 [az sql server create](/cli/azure/sql/server#az_sql_server_create) 命令建立[邏輯 SQL Server](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)：

   ```azurecli
   az sql server create --resource-group myResourceGroup --name mysqlserver \
      --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
   ```

   一個伺服器會包含一組以群組方式管理的資料庫。

## <a name="configure-a-server-level-firewall-rule"></a>設定伺服器層級防火牆規則

建立[伺服器層級防火牆規則](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。 伺服器層級防火牆規則可讓外部應用程式 (例如 SQL Server Management Studio 或 SQLCMD 公用程式) 穿過 SQL 集區服務防火牆連線到 SQL 集區。

執行 [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) 命令，以建立防火牆規則：

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --name AllowSome \
   --server mysqlserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

在此範例中，只會針對其他 Azure 資源開啟防火牆。 若要啟用外部連線，請將 IP 位址變更為適合您環境的地址。 若要開啟所有 IP 位址，請使用 0.0.0.0 作為起始 IP 位址，並使用 255.255.255.255 作為結束位址。

> [!NOTE]
> SQL 端點會透過連接埠 1433 進行通訊。 如果您嘗試從公司網路連線，您網路的防火牆可能不允許透過連接埠 1433 的連出流量。 若是如此，除非 IT 部門開啟連接埠 1433，否則您將無法連線到您的伺服器。
>

## <a name="create-and-manage-your-sql-pool"></a>建立和管理您的 SQL 集區

建立 SQL 集區。 此範例會使用 DW100c 作為服務目標，這對 SQL 集區而言是成本較低的起點。

> [!NOTE]
> 您需要先前建立的工作區。 如需詳細資訊，請參閱[快速入門：使用 Azure CLI 建立 Azure Synapse 工作區](../quickstart-create-workspace-cli.md)。

使用 [az synapse sql pool create](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_create) 命令建立 SQL 集區：

```azurecli
az synapse sql pool create --resource-group myResourceGroup --name mySampleDataWarehouse \
   --performance-level "DW1000c" --workspace-name testsynapseworkspace
```

如需參數選項的詳細資訊，請參閱 [az synapse sql pool](/cli/azure/ext/synapse/synapse/sql/pool)。

您可以使用 [az synapse sql pool list](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_list) 命令來查看您的 SQL 集區：

```azurecli
az synapse sql pool list --resource-group myResourceGroup --workspace-name testsynapseworkspace
```

使用 [az synapse sql pool update](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_update) 命令更新現有的集區：

```azurecli
az synapse sql pool update --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

使用 [az synapse sql pool pause](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_pause) 命令暫停集區：

```azurecli
az synapse sql pool pause --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

使用 [az synapse sql pool resume](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_resume) 命令啟動暫停的集區：

```azurecli
az synapse sql pool resume --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

若要移除現有的 SQL 集區，請使用 [az synapse SQL pool delete](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_delete) 命令：

```azurecli
az synapse sql pool delete --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

## <a name="clean-up-resources"></a>清除資源

此集合中的其他快速入門教學課程會以此快速入門為基礎。

> [!TIP]
> 如果您打算繼續進行後續的快速入門教學課程，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，請使用 [az group delete](/cli/azure/group#az_group_delete) 命令刪除本快速入門中建立的所有資源。
>

```azurecli
az group delete --ResourceGroupName MyResourceGroup
```

## <a name="next-steps"></a>後續步驟

您現在已建立了 SQL 集區、防火牆規則並連線到您的 SQL 集區。 若要深入了解，請繼續閱讀[將資料載入 SQL 集區](./load-data-from-azure-blob-storage-using-copy.md)一文。