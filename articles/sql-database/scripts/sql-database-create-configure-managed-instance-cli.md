---
title: Azure CLI：建立受控執行個體
description: 在 Azure SQL 受控執行個體中建立受控執行個體的 Azure CLI 範例指令碼
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: fd2c7a33c0747aa21a8bda53ad5437c9e79a6557
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84115418"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance"></a>使用 CLI 建立 Azure SQL 受控執行個體

此 Azure CLI 指令碼範例會在新的虛擬網路內的專用子網路中建立 Azure SQL 受控執行個體。 其也會設定虛擬網路的路由表與網路安全性群組。 一旦成功執行指令碼，便可從虛擬網路內或從內部部署環境存取受控執行個體。 請參閱 [設定 Azure VM 以連線到 Azure SQL 受控執行個體]../../azure-sql/managed-instance/connect-vm-instance-configure.md) 和[設定從內部部署至 Azure SQL 受控執行個體的點對點連線](../../azure-sql/managed-instance/point-to-site-p2s-configure.md)。

> [!IMPORTANT]
> 如需了解限制，請參閱[支援的區域](../../azure-sql/managed-instance/resource-limits.md#supported-regions)和[支援的訂用帳戶類型](../../azure-sql/managed-instance/resource-limits.md#supported-subscription-types)。

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>範例指令碼

### <a name="sign-in-to-azure"></a>登入 Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>執行指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>清除部署

使用下列命令來移除資源群組及其所有相關聯的資源。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>範例參考

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| | |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | 虛擬網路命令。 |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | 虛擬網路子網路命令。 |
| [az network route-table](/cli/azure/network/route-table) | 網路路由表命令。 |
| [az sql mi](/cli/azure/sql/mi) | SQL 受控執行個體命令。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

其他的 SQL Database CLI 指令碼範例可於 [Azure SQL Database 文件](../../azure-sql/database/az-cli-script-samples-content-guide.md)中找到。
