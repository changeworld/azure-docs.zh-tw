---
title: CLI 範例 - 在 Azure SQL Database 中建立受控執行個體
description: 在 Azure SQL Database 中建立受控執行個體的 Azure CLI 範例指令碼
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 998bd8e39863dd9520d1a05e7fff52095c3be6c1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067436"
---
# <a name="use-cli-to-create-an-azure-sql-database-managed-instance"></a>使用 CLI 建立 Azure SQL Database 受控執行個體

此 Azure CLI 指令碼範例會在新的虛擬網路內的專用子網路中建立 Azure SQL Database 受控執行個體。 其也會設定虛擬網路的路由表與網路安全性群組。 一旦成功執行指令碼，便可從虛擬網路內或從內部部署環境存取受控執行個體。 請參閱[設定 Azure VM 以連線到 Azure SQL Database 受控執行個體](../sql-database-managed-instance-configure-vm.md)和[設定從內部部署連線至 Azure SQL Database 受控執行個體的點對站連線](../sql-database-managed-instance-configure-p2s.md)。

> [!IMPORTANT]
> 如需了解限制，請參閱[支援的區域](../sql-database-managed-instance-resource-limits.md#supported-regions)和[支援的訂用帳戶類型](../sql-database-managed-instance-resource-limits.md#supported-subscription-types)。

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
| [az sql mi](/cli/azure/sql/mi) | 受控執行個體命令。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

其他的 SQL Database CLI 指令碼範例可於 [Azure SQL Database 文件](../sql-database-cli-samples.md)中找到。
