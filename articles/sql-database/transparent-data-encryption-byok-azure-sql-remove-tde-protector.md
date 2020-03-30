---
title: 卸下 TDE 保護器 - 電源外殼
description: 本操作指南說明 Azure SQL Database 或資料倉儲若是使用具有「攜帶您自己的金鑰」(BYOK) 支援的 TDE，在其 TDE 保護裝置可能遭破壞時，應如何因應。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: 5a89c3f7d52c5717b902a69e9c64b3fcc422c481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067203"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>使用 PowerShell 移除透明資料加密 (TDE) 保護裝置

## <a name="prerequisites"></a>Prerequisites

- 您必須具有 Azure 訂用帳戶，並且是該訂用帳戶的系統管理員
- 您必須安裝並運行 Azure PowerShell。
- 本操作指南假設您已使用 Azure Key Vault 中的金鑰作為 Azure SQL Database 或資料倉儲的 TDE 保護裝置。 若要深入了解，請參閱[具有 BYOK 支援的透明資料加密](transparent-data-encryption-byok-azure-sql.md)。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

 如需 Az 模組安裝指示，請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。 有關特定的 Cmdlet，請參閱[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。

> [!IMPORTANT]
> PowerShell Azure 資源管理器 （RM） 模組仍受 Azure SQL 資料庫支援，但所有後續開發都針對 Az.Sql 模組。 AzureRM 模組將繼續接收錯誤修復，至少直到 2020 年 12 月。  Az 模組和 AzureRm 模組中命令的參數基本相同。 有關其相容性的更多資訊，請參閱[介紹新的 Azure PowerShell Az 模組](/powershell/azure/new-azureps-module-az)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

有關安裝，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

* * *

## <a name="overview"></a>總覽

本操作指南說明 Azure SQL Database 或資料倉儲若是在 Azure Key Vault 中搭配由客戶管理的金鑰來使用 TDE (攜帶您自己的金鑰 (BYOK) 支援)，在其 TDE 保護裝置可能遭破壞時，應如何因應。 若要深入了解 TDE 的 BYOK 支援，請參閱[概觀頁面](transparent-data-encryption-byok-azure-sql.md)。

只有在極端情況下或測試環境中，才需要執行下列程序。 請仔細查看操作指南，因為從 Azure 金鑰保存庫中刪除已使用中的已使用 TDE 保護程式將導致**資料庫不可用**。

如果懷疑某個金鑰被洩露，因此服務或使用者未經授權訪問該金鑰，則最好刪除該金鑰。

請記住，一旦在金鑰保存庫中刪除 TDE 保護器，最多 10 分鐘內，所有加密資料庫將開始拒絕具有相應錯誤訊息的所有連接，並將其狀態更改為[不可訪問](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql#inaccessible-tde-protector)。

以下步驟概述了如何檢查給定資料庫的虛擬日誌檔 （VLF） 仍在使用的 TDE 保護器指紋。
通過運行資料庫的當前 TDE 保護器和資料庫 ID 的指紋：通過運行：

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint],
 FROM [sys].[dm_database_encryption_keys]
```

以下查詢返回正在使用的 VF 和加密器各自的指紋。 每個不同的指紋都指 Azure 金鑰保存庫 （AKV） 中不同的金鑰：

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

PowerShell 命令**Get-AzureSqlServerKeyVaultKey** 提供查詢中使用的 TDE 保護器的指紋，因此您可以看到要保留哪些金鑰以及要刪除哪些金鑰。 只有資料庫不再使用的金鑰才能安全地從 Azure 金鑰保存庫中刪除。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell 命令**az sql 伺服器金鑰顯示** 提供查詢中使用的 TDE 保護器的指紋，因此您可以看到要保留哪些金鑰以及要在 AKV 中刪除哪些金鑰。 只有資料庫不再使用的金鑰才能安全地從 Azure 金鑰保存庫中刪除。

* * *

本操作指南會根據事件回應之後的所需結果討論兩種方法：

- 保持 Azure SQL 資料庫/資料倉儲**的可訪問性**
- 使 Azure SQL 資料庫/資料倉儲**無法訪問**

## <a name="to-keep-the-encrypted-resources-accessible"></a>將加密資源保持為可供存取

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

1. 建立 [Key Vault 中的新金鑰](/powershell/module/az.keyvault/add-azkeyvaultkey)。 請務必將此新金鑰建立在與可能遭破壞的 TDE 保護裝置不同的金鑰保存庫中，因為存取控制佈建於保存庫層級上。

2. 使用[Add-AzSqlServerKeyVaultKey 鍵](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)和[集-AzSqlServer透明資料加密保護器](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)Cmdlet 將新金鑰添加到伺服器，並將其更新為伺服器的新 TDE 保護程式。

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. 確保伺服器和任何副本已使用[Get-AzSqlServer 透明資料加密保護器](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector)Cmdlet 更新到新的 TDE 保護器。

   > [!NOTE]
   > 新的 TDE 保護裝置可能需要幾分鐘的時間才能傳播至伺服器下的所有資料庫和次要資料庫。

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. 在 Key Vault 中建立[新金鑰的備份](/powershell/module/az.keyvault/backup-azkeyvaultkey)。

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. 使用[刪除-AzKeyVaultKey Cmdlet](/powershell/module/az.keyvault/remove-azkeyvaultkey)從金鑰保存庫中刪除洩露的金鑰。

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. 要將來使用[還原-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) Cmdlet 還原金鑰庫：

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

有關命令引用，請參閱[Azure CLI 金鑰庫](/cli/azure/keyvault/key)。

1. 建立 [Key Vault 中的新金鑰](/cli/azure/keyvault/key#az-keyvault-key-create)。 請務必將此新金鑰建立在與可能遭破壞的 TDE 保護裝置不同的金鑰保存庫中，因為存取控制佈建於保存庫層級上。

2. 將新金鑰添加到伺服器，並將其更新為伺服器的新 TDE 保護程式。

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. 確保伺服器和任何副本已更新到新的 TDE 保護器。

   > [!NOTE]
   > 新的 TDE 保護裝置可能需要幾分鐘的時間才能傳播至伺服器下的所有資料庫和次要資料庫。

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. 在 Key Vault 中建立新金鑰的備份。

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. 從金鑰保存庫中刪除洩露的金鑰。

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. 將來還原金鑰保存庫。

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>使加密資源變得無法存取

1. 捨棄以可能遭破壞的金鑰加密的資料庫。

   資料庫和記錄檔會自動備份，因此資料庫的時間點還原可以在任何時間點執行 (只要您提供金鑰)。 資料庫必須在作用中的 TDE 保護裝置刪除之前捨棄，否則最多可能會遺失最新交易 10 分鐘內的資料。

2. 在 Key Vault 中備份 TDE 保護裝置的金鑰內容。
3. 從 Key Vault 中移除可能遭破壞的金鑰

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>後續步驟

- 了解如何輪替伺服器的 TDE 保護裝置以符合安全需求：[使用 PowerShell 輪替透明資料加密保護裝置](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- 開始將「攜帶您自己的金鑰支援」用於 TDE：[透過 PowerShell 從 Key Vault 使用您自己的金鑰開啟 TDE](transparent-data-encryption-byok-azure-sql-configure.md)
