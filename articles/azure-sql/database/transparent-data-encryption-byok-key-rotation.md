---
title: '將 TDE 保護裝置旋轉 (PowerShell & Azure CLI) '
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: 瞭解如何使用 PowerShell 和 Azure Synapse Analytics，針對 Azure SQL Database 和 Azure CLI 所使用的 Azure 中的伺服器，旋轉透明資料加密 (TDE) 保護裝置。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: c24c82e6e013734798b75d7c2cfa5ca126e32bc4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92788599"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector"></a>輪替透明資料加密 (TDE) 保護裝置
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


本文說明如何使用 Azure Key Vault 中的 TDE 保護裝置來輪替 [伺服器](logical-servers.md) 的金鑰。 輪替伺服器的邏輯 TDE 保護裝置，表示切換至保護伺服器上資料庫的新非對稱金鑰。 金鑰輪替是一項線上作業，應該只需要幾秒鐘的時間才能完成，因為這只會解密並重新加密資料庫的資料加密金鑰，而不是整個資料庫。

本指南將討論用來在伺服器上輪替 TDE 保護裝置的兩個選項。

> [!NOTE]
> 已暫停的 Azure Synapse Analytics SQL 集區必須在金鑰輪替之前繼續。

> [!IMPORTANT]
> 在變換之後，請勿刪除舊版本的金鑰。 在金鑰變換後，部分資料仍會以先前的金鑰加密，例如較舊的資料庫備份。

## <a name="prerequisites"></a>必要條件

- 本操作指南假設您已經使用 Azure Key Vault 中的金鑰作為 Azure SQL Database 或 Azure Synapse Analytics 的 TDE 保護裝置。 請參閱[具有 BYOK 支援的透明資料加密](transparent-data-encryption-byok-overview.md)。
- 您必須已安裝且正在執行 Azure PowerShell。
- [建議執行的選擇性作業] 先在硬體安全性模組 (HSM) 或本機金鑰存放區中建立 TDE 保護裝置的金鑰內容，然後將金鑰內容匯入至 Azure Key Vault。 請依照[使用硬體安全性模組 (HSM) 與 Key Vault 的指示](../../key-vault/general/overview.md)操作，以深入了解。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

如需 Az 模組安裝指示，請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。 如需特定的 Cmdlet，請參閱[AzureRM。](/powershell/module/AzureRM.Sql/)

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) 模組仍受支援，但所有未來的開發都是針對 Az.Sql 模組進行的。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到 Bug 修正。  Az 模組和 AzureRm 模組中命令的引數本質上完全相同。 如需其相容性的詳細資訊，請參閱[新的 Azure PowerShell Az 模組簡介](/powershell/azure/new-azureps-module-az)。

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

如需安裝，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。

* * *

## <a name="manual-key-rotation"></a>手動金鑰輪替

手動金鑰輪替會使用下列命令來新增全新的金鑰，該金鑰可能位於新的金鑰名稱或甚至是其他金鑰保存庫。 使用此方法，可支援將相同金鑰新增至不同的金鑰保存庫，以支援高可用性和異地災害復原案例。

> [!NOTE]
> 金鑰保存庫名稱和金鑰名稱的合併長度不可超過 94 個字元。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 [AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey)、 [add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)和 [AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) Cmdlet。

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create)、 [az sql server key create](/cli/azure/sql/server/key#az-sql-server-key-create)和 [az sql server tde-key set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) 命令。

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="switch-tde-protector-mode"></a>切換 TDE 保護裝置模式

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- 若要將 TDE 保護裝置從 Microsoft 管理切換至 BYOK 模式，請使用 [AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) Cmdlet。

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- 若要將 TDE 保護裝置從 BYOK 模式切換至 Microsoft 管理的，請使用 [AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) Cmdlet。

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

下列範例會使用 [az sql server tde-key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)。

- 若要將 TDE 保護裝置從 Microsoft 管理切換至 BYOK 模式，

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- 若要將 TDE 保護裝置從 BYOK 模式切換至 Microsoft 管理的，

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>後續步驟

- 如果有安全性風險，請瞭解如何移除可能遭盜用的 TDE 保護裝置： [移除可能遭盜用的金鑰](transparent-data-encryption-byok-remove-tde-protector.md)。

- 開始使用 Azure Key Vault 整合和針對 TDE 的攜帶您自己的金鑰支援： [使用 PowerShell 從 Key Vault 中使用您自己的金鑰來開啟 TDE](transparent-data-encryption-byok-configure.md)。