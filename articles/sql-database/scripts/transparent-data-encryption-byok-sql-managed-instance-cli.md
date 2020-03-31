---
title: CLI 示例 - 啟用 BYOK TDE - Azure SQL 資料庫託管實例
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
ms.openlocfilehash: 8e8c0e2db1f87cca52c44d33ce14d7ce4f00e895
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061723"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>從 Azure Key Vault 使用自己的金鑰管理受控執行個體中的透明資料加密

此 Azure CLI 腳本示例使用 Azure 金鑰保存庫的金鑰配置 Azure SQL 託管實例的客戶託管金鑰的透明資料加密 （TDE）。 這通常稱為 TDE 自帶金鑰方案。 要瞭解有關使用客戶管理的金鑰的 TDE 的更多內容，請參閱[TDE 將您自己的金鑰帶到 Azure SQL](../transparent-data-encryption-byok-azure-sql.md)。

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>範例指令碼

### <a name="prerequisites"></a>Prerequisites

現有的託管實例，請參閱[使用 Azure CLI 創建 Azure SQL 資料庫託管實例](sql-database-create-configure-managed-instance-cli.md)。

### <a name="sign-in-to-azure"></a>登入 Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>執行指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.sh "Set up BYOK TDE for SQL Managed Instance")]

### <a name="clean-up-deployment"></a>清除部署

使用以下命令刪除資源組及其關聯的所有資源。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>樣本參考

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| | |
|---|---|
| [az sql db](/cli/azure/sql/db) | 資料庫命令。 |
| [az sql 容錯移轉組](/cli/azure/sql/failover-group) | 容錯移轉組命令。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

其他的 SQL Database CLI 指令碼範例可於 [Azure SQL Database 文件](../sql-database-cli-samples.md)中找到。
