---
title: 旋轉 TDE 保護器 - 電源外殼
description: 了解如何輪替 Azure SQL Server 的透明資料加密 (TDE) 保護裝置。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: aaed06ac086893f63fde530e46b936b3fb637766
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067175"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>使用 PowerShell 輪替透明資料加密 (TDE) 保護裝置

本文說明如何使用 TDE 保護裝置從 Azure Key Vault 進行 Azure SQL Server 的金鑰輪替。 旋轉 Azure SQL 伺服器的 TDE 保護器意味著切換到新的非對稱金鑰，以保護伺服器上的資料庫。 金鑰輪換是線上作業，只需幾秒鐘即可完成，因為這僅解密並重新加密資料庫的資料加密金鑰，而不是整個資料庫。

本指南將討論用來在伺服器上輪替 TDE 保護裝置的兩個選項。

> [!NOTE]
> 進行金鑰輪替之前，必須先恢復暫停的 SQL 資料倉儲。

> [!IMPORTANT]
> 在翻轉後，不要刪除金鑰的早期版本。 在金鑰變換後，部分資料仍會以先前的金鑰加密，例如較舊的資料庫備份。

## <a name="prerequisites"></a>Prerequisites

- 本操作指南假設您已使用 Azure Key Vault 中的金鑰作為 Azure SQL Database 或資料倉儲的 TDE 保護裝置。 請參閱[具有 BYOK 支援的透明資料加密](transparent-data-encryption-byok-azure-sql.md)。
- 您必須安裝並運行 Azure PowerShell。
- [建議執行的選擇性作業] 先在硬體安全性模組 (HSM) 或本機金鑰存放區中建立 TDE 保護裝置的金鑰內容，然後將金鑰內容匯入至 Azure Key Vault。 請依照[使用硬體安全性模組 (HSM) 與 Key Vault 的指示](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)操作，以深入了解。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

如需 Az 模組安裝指示，請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。 有關特定的 Cmdlet，請參閱[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。

> [!IMPORTANT]
> PowerShell Azure 資源管理器 （RM） 模組仍受 Azure SQL 資料庫支援，但所有後續開發都針對 Az.Sql 模組。 AzureRM 模組將繼續接收錯誤修復，至少直到 2020 年 12 月。  Az 模組和 AzureRm 模組中命令的參數基本相同。 有關其相容性的更多資訊，請參閱[介紹新的 Azure PowerShell Az 模組](/powershell/azure/new-azureps-module-az)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

有關安裝，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

* * *

## <a name="manual-key-rotation"></a>手動金鑰輪替

手動金鑰輪換使用以下命令添加全新的金鑰，該金鑰可能位於新金鑰名稱下，甚至位於另一個金鑰保存庫下。 使用此方法，可支援將相同金鑰新增至不同的金鑰保存庫，以支援高可用性和異地災害復原案例。

> [!NOTE]
> 金鑰保存庫名稱和金鑰名稱的合併長度不可超過 94 個字元。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用[添加-AzKeyVault鍵](/powershell/module/az.keyvault/Add-AzKeyVaultKey)、[添加-AzSqlServerKeyVaultKey鍵](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)，以及[集-AzSqlServer透明資料加密保護器](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)Cmdlet。

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用[az 鍵庫鍵創建](/cli/azure/keyvault/key#az-keyvault-key-create) [、az sql 伺服器金鑰創建](/cli/azure/sql/server/key#az-sql-server-key-create)和[az sql server tde 鍵集](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set)命令。

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="useful-powershell-cmdlets"></a>PowerShell Cmdlet

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

- 要將 TDE 保護器從 Microsoft 管理的保護器切換到 BYOK 模式，請使用[Set-AzSqlServer 透明資料加密保護器](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)Cmdlet。

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- 要將 TDE 保護器從 BYOK 模式切換到 Microsoft 管理，請使用[Set-AzSqlServer 透明資料加密保護器](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)Cmdlet。

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

以下示例使用[az sql server tde 鍵集](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)。

- 要將 TDE 保護器從 Microsoft 管理切換到 BYOK 模式，

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- 要將 TDE 保護器從 BYOK 模式切換到 Microsoft 管理，

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>後續步驟

- 如有安全性風險，請了解如何移除可能遭破壞的 TDE 保護裝置：[移除可能遭破壞的金鑰](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)

- 開始使用 Azure 金鑰保存庫集成，並自帶對 TDE 的金鑰支援：[使用 PowerShell 使用金鑰保存庫的自帶金鑰打開 TDE](transparent-data-encryption-byok-azure-sql-configure.md)
