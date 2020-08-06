---
title: Azure CLI：將受控執行個體新增到容錯移轉群組
description: Azure CLI 範例指令碼，用以建立 Azure SQL 受控執行個體、將其新增至容錯移轉群組，以及測試容錯移轉。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: b29fcd33c8181ba61f57d7105d08e7e4956d80bc
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497251"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance-to-a-failover-group"></a>使用 CLI 在容錯移轉群組中建立 Azure SQL 受控執行個體

此 Azure CLI 範例會建立兩個受控執行個體，並將其新增至容錯移轉群組，然後測試是否能從主要受控執行個體容錯移轉至次要受控執行個體。

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-scripts"></a>範例指令碼

### <a name="sign-in-to-azure"></a>登入 Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>執行指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>清除部署

使用下列命令來移除資源群組及其所有相關聯的資源。 您將需要移除資源群組兩次。 第一次移除資源群組時，將會移除受控執行個體和虛擬叢集，但接著會失敗並出現錯誤訊息：`az group delete : Long running operation failed with status 'Conflict'.`。 請再次執行 az group delete 命令，以移除所有剩餘的資源及資源群組。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>範例參考

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 說明 |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | 虛擬網路命令。  |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | 虛擬網路子網路命令。 |
| [az network nsg](/cli/azure/network/nsg) | 網路安全性群組命令。 |
| [az network nsg rule](/cli/azure/network/nsg/rule)| 網路安全性規則命令。 |
| [az network route-table](/cli/azure/network/route-table) | 路由表命令。 |
| [az sql mi](/cli/azure/sql/mi) | SQL 受控執行個體命令。 |
| [az network public-ip](/cli/azure/network/public-ip) | 網路公用 IP 位址命令。 |
| [az network vnet-gateway](/cli/azure/network/vnet-gateway) | 虛擬網路閘道命令 |
| [az sql instance-failover-group](/cli/azure/sql/instance-failover-group) | SQL 受控執行個體容錯移轉群組命令。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

其他的 SQL Database CLI 指令碼範例可於 [Azure SQL Database 文件](../../azure-sql/database/az-cli-script-samples-content-guide.md)中找到。
