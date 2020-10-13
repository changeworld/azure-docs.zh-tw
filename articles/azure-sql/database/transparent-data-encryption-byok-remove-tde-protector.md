---
title: '移除 TDE 保護裝置 (PowerShell & Azure CLI) '
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: 瞭解如何使用 TDE 搭配攜帶您自己的金鑰 (BYOK) 支援，來回應 Azure SQL Database 或 Azure Synapse Analytics 可能遭盜用的 TDE 保護裝置。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: 77f2312438f3f9db7aa4e0dc7cc0f672644a87c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617395"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>使用 PowerShell 移除透明資料加密 (TDE) 保護裝置
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


本主題說明如何使用 TDE 搭配客戶管理的金鑰，以 Azure Key Vault 攜帶您自己的金鑰 (BYOK) 支援，來回應可能遭盜用的 TDE 保護 Azure SQL Database 或 Azure Synapse Analytics。 若要深入了解 TDE 的 BYOK 支援，請參閱[概觀頁面](transparent-data-encryption-byok-overview.md)。

> [!CAUTION]
> 本文所述的程式應該只在極端案例或測試環境中完成。 請仔細檢查步驟，因為從 Azure Key Vault 中刪除主動使用的 TDE 保護裝置會導致 **資料庫變得無法使用**。

如果金鑰遭到入侵，讓某個服務或使用者有未經授權的金鑰存取權，最好先刪除金鑰。

請記住，在 Key Vault 中刪除 TDE 保護裝置後，在最多10分鐘內，所有加密的資料庫都會開始拒絕所有具有對應錯誤訊息的連線，並將其狀態變更為 [無法存取](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql#inaccessible-tde-protector)。

本操作指南會根據所需的結果，在遭入侵的事件回應之後，提供兩種方法：

- 使 Azure SQL Database/Azure Synapse Analytics 中的資料庫 **無法存取**。
- 若要讓 Azure SQL Database/Azure Azure Synapse Analytics 中的資料庫 (先前的 SQL 資料倉儲) **無法存取**。

## <a name="prerequisites"></a>必要條件

- 您必須具有 Azure 訂用帳戶，並且是該訂用帳戶的系統管理員
- 您必須已安裝且正在執行 Azure PowerShell。
- 本操作指南假設您已經使用 Azure Key Vault 的金鑰作為 Azure SQL Database 的 TDE 保護裝置，或先前為 SQL 資料倉儲)  (的 Azure Synapse。 若要深入了解，請參閱[具有 BYOK 支援的透明資料加密](transparent-data-encryption-byok-overview.md)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 如需 Az 模組安裝指示，請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。 如需特定的 Cmdlet，請參閱[AzureRM。](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) 模組仍受支援，但未來所有的開發都是針對 Az. Sql 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到 Bug 修正。  Az 模組和 AzureRm 模組中命令的引數本質上完全相同。 如需其相容性的詳細資訊，請參閱[新的 Azure PowerShell Az 模組簡介](/powershell/azure/new-azureps-module-az)。

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

如需安裝，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。

* * *

## <a name="check-tde-protector-thumbprints"></a>檢查 TDE 保護裝置指紋

下列步驟概述如何在指定的資料庫 (VLF) ，檢查虛擬記錄檔仍在使用中的 TDE 保護裝置指紋。
資料庫目前 TDE 保護裝置的指紋，而您可以藉由執行下列程式來找到資料庫識別碼：

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint]
 FROM [sys].[dm_database_encryption_keys]
```

下列查詢會傳回使用中的 Vlf 和 TDE 保護裝置個別指紋。 每個不同的指紋都會參考 Azure Key Vault (AKV) 中的不同金鑰：

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

或者，您也可以使用 PowerShell 或 Azure CLI：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell 命令 **>get-azurermsqlserverkeyvaultkey**   會提供查詢中所使用之 TDE 保護裝置的指紋，讓您可以查看要保留的金鑰，以及要在 AKV 中刪除的金鑰。 您只能安全地從 Azure Key Vault 中刪除資料庫不再使用的金鑰。

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell 命令**az sql server key show**   提供查詢中所用 TDE 保護裝置的指紋，讓您可以查看要保留的金鑰，以及要在 AKV 中刪除的金鑰。 您只能安全地從 Azure Key Vault 中刪除資料庫不再使用的金鑰。

* * *

## <a name="keep-encrypted-resources-accessible"></a>讓加密資源保持可存取

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 建立 [Key Vault 中的新金鑰](/powershell/module/az.keyvault/add-azkeyvaultkey)。 請務必將此新金鑰建立在與可能遭破壞的 TDE 保護裝置不同的金鑰保存庫中，因為存取控制佈建於保存庫層級上。

2. 使用 [AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) 和 [AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) Cmdlet 將新的金鑰新增至伺服器，並將其更新為伺服器的新 TDE 保護裝置。

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. 使用 [AzSqlServerTransparentDataEncryptionProtector 指令程式](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) ，確定伺服器和任何複本都已更新為新的 TDE 保護裝置。

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

5. 使用 [AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) Cmdlet 從 Key Vault 刪除遭盜用的金鑰。

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. 若要使用 [AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) Cmdlet 將金鑰還原至未來 Key Vault：

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

如需命令參考，請參閱 [Azure CLI keyvault](/cli/azure/keyvault/key)。

1. 建立 [Key Vault 中的新金鑰](/cli/azure/keyvault/key#az-keyvault-key-create)。 請務必將此新金鑰建立在與可能遭破壞的 TDE 保護裝置不同的金鑰保存庫中，因為存取控制佈建於保存庫層級上。

2. 將新的金鑰新增至伺服器，並將其更新為伺服器的新 TDE 保護裝置。

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. 請確定伺服器和任何複本都已更新為新的 TDE 保護裝置。

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

5. 從 Key Vault 刪除遭盜用的金鑰。

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. 若要在未來將金鑰還原至 Key Vault。

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="make-encrypted-resources-inaccessible"></a>使加密資源無法存取

1. 捨棄以可能遭破壞的金鑰加密的資料庫。

   資料庫和記錄檔會自動備份，因此資料庫的時間點還原可以在任何時間點執行 (只要您提供金鑰)。 資料庫必須在作用中的 TDE 保護裝置刪除之前捨棄，否則最多可能會遺失最新交易 10 分鐘內的資料。

2. 在 Key Vault 中備份 TDE 保護裝置的金鑰內容。
3. 從 Key Vault 中移除可能遭破壞的金鑰

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>後續步驟

- 了解如何輪替伺服器的 TDE 保護裝置以符合安全需求：[使用 PowerShell 輪替透明資料加密保護裝置](transparent-data-encryption-byok-key-rotation.md)
- 開始將「攜帶您自己的金鑰支援」用於 TDE：[透過 PowerShell 從 Key Vault 使用您自己的金鑰開啟 TDE](transparent-data-encryption-byok-configure.md)
