---
title: CLI 範例-啟用 BYOK TDE-Azure SQL 受控執行個體
description: 了解如何設定 Azure SQL 受控執行個體，以透過 PowerShell 開始使用 BYOK 透明資料加密 (TDE) 進行待用資料加密。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: azurecli
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: 08f0531a99a7e0af42332e349989ce12722be42c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518787"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>從 Azure Key Vault 使用自己的金鑰管理受控執行個體中的透明資料加密

此 Azure CLI 腳本範例會使用 Azure Key Vault 中的金鑰，為 Azure SQL 受控執行個體設定透明資料加密（TDE）與客戶管理的金鑰。 這通常稱為 TDE 的攜帶您自己的金鑰案例。 若要深入瞭解具有客戶管理金鑰的 TDE，請參閱[TDE 攜帶您自己的金鑰至 AZURE SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md)。

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>範例指令碼

### <a name="prerequisites"></a>必要條件

現有的受控執行個體，請參閱[使用 Azure CLI 建立 AZURE SQL 受控執行個體](sql-database-create-configure-managed-instance-cli.md)。

### <a name="sign-in-to-azure"></a>登入 Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>執行指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.sh "Set up BYOK TDE for SQL Managed Instance")]

### <a name="clean-up-deployment"></a>清除部署

使用下列命令來移除資源群組及其所有相關聯的資源。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>範例參考

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 描述 |
|---|---|
| [az sql db](/cli/azure/sql/db) | 資料庫命令。 |
| [az sql failover-group](/cli/azure/sql/failover-group) | 容錯移轉群組命令。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

其他的 SQL Database CLI 指令碼範例可於 [Azure SQL Database 文件](../../azure-sql/database/az-cli-script-samples-content-guide.md)中找到。
