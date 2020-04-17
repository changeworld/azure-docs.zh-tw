---
title: 使用 Azure 金鑰保存式庫啟用 SQL TDE
description: 了解如何設定 Azure SQL Database 和資料倉儲，以透過 PowerShell 或 CLI 開始使用透明資料加密 (TDE) 進行待用資料加密。
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
ms.openlocfilehash: 5b1c985eeec9af25ec576f4e2375c417dc376f95
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452752"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell 與 CLI:使用 Azure 金鑰保管庫的客戶管理金鑰啟用透明資料加密

本文將逐步解說如何將 Azure Key Vault 中的金鑰用於 SQL Database 或資料倉儲上的透明資料加密 (TDE)。 若要深入了解以 Azure Key Vault 整合進行 TDE - 攜帶您自己的金鑰 (BYOK) 的支援，請參閱 [Azure SQL 透明資料加密：「攜帶您自己的金鑰」支援](transparent-data-encryption-byok-azure-sql.md)。 

## <a name="prerequisites-for-powershell"></a>PowerShell 的必要條件

- 您必須具有 Azure 訂用帳戶，並且是該訂用帳戶的系統管理員。
- [建議執行的選擇性作業] 備妥硬體安全性模組 (HSM) 或本機金鑰存放區，用來建立 TDE 保護裝置金鑰內容的本機複本。
- 您必須安裝並運行 Azure PowerShell。
- 建立要用於 TDE 的 Azure Key Vault 和金鑰。
  - [使用硬體安全性模組 (HSM) 與 Key Vault 的指示](../key-vault/keys/hsm-protected-keys.md)
    - 金鑰保存庫必須具有下列屬性才能用於 TDE：
  - [軟刪除](../key-vault/general/overview-soft-delete.md)與清除保護
- 金鑰必須具有下列屬性才能用於 TDE：
   - 沒有到期日
   - 未停用
   - 能夠執行*取得*、*包裝金鑰*、*解除包裝金鑰*作業

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

如需 Az 模組安裝指示，請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。 有關特定的 cmdlet,請參閱[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。

有關金鑰保管庫的詳細說明,請參閱[密鑰保管庫的 PowerShell 說明](../key-vault/secrets/quick-create-powershell.md)以及如何[使用金鑰保管庫軟刪除電源殼](../key-vault/general/soft-delete-powershell.md)。

> [!IMPORTANT]
> PowerShell Azure 資源管理員 (RM) 模組仍受 Azure SQL 資料庫支援,但所有後續開發都針對 Az.Sql 模組。 AzureRM 模組將繼續接收錯誤修復,至少直到2020年12月。  Az 模組和 AzureRm 模組中命令的參數基本相同。 有關其相容性的更多資訊,請參閱[介紹新的 Azure PowerShell Az 模組](/powershell/azure/new-azureps-module-az)。

## <a name="assign-an-azure-ad-identity-to-your-server"></a>將 Azure AD 身分識別指派給您的伺服器

如果您有現有的伺服器，請使用下列程式碼將 Azure AD 身分識別新增至您的伺服器：

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

如果要建立伺服器,請使用帶有標記 -標識[的 New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) cmdlet 在伺服器建立期間添加 Azure AD 識別:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>為您的伺服器授與 Key Vault 權限

使用[Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet 在使用密鑰用於 TDE 之前,可以授予伺服器對金鑰保管庫的訪問許可權。

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>將 Key Vault 金鑰新增至伺服器，並設定 TDE 保護裝置

- 使用[Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) cmdlet 從金鑰保管庫檢索金鑰 ID
- 使用[Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) cmdlet 將密鑰從金鑰保管庫添加到伺服器。
- 使用[Set-AzSqlServer 透明資料加密保護器](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)cmdlet 將密鑰設置為所有伺服器資源的 TDE 保護器。
- 使用[Get-AzSqlServer 透明資料加密保護器](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector)cmdlet 確認 TDE 保護器已按預期配置。

> [!NOTE]
> 金鑰保存庫名稱和金鑰名稱的合併長度不可超過 94 個字元。

> [!TIP]
> Key Vault 中的範例 KeyId： https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>開啟 TDE

使用[集-AzSql 資料庫透明資料加密](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption)cmdlet 打開 TDE。

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

現在，資料庫或資料倉儲已使用 Key Vault 中的加密金鑰啟用 TDE。

## <a name="check-the-encryption-state-and-encryption-activity"></a>檢查加密狀態和加密活動

使用[Get-AzSql 資料庫透明資料加密](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption)取得加密狀態和[Get-AzSql 資料庫透明資料加密活動](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity)來檢查資料庫或數據倉庫的加密進度。

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要安裝所需的命令列介面版本 2.0 或更高版本並連接到 Azure 訂閱,請參閱[安裝和設定 Azure 跨平臺命令列介面 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。

關於金鑰保存的函式庫的詳細細節,請參考[CLI 2.0 管理金鑰保存,](../key-vault/general/manage-with-cli2.md)[以及如何使用 CLI 軟刪除金鑰保存庫](../key-vault/general/soft-delete-cli.md)。

## <a name="assign-an-azure-ad-identity-to-your-server"></a>將 Azure AD 身分識別指派給您的伺服器

```azurecli
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> 防止"principalID" 建立伺服器，它是用於在下一步中指派金鑰保存庫使用權限的物件識別碼

## <a name="grant-key-vault-permissions-to-your-server"></a>為您的伺服器授與 Key Vault 權限

```azurecli
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> 請保留新金鑰的金鑰 URI 或 keyID 以在下一個步驟中使用，例如：https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>將 Key Vault 金鑰新增至伺服器，並設定 TDE 保護裝置

```azurecli
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> 金鑰保存庫名稱和金鑰名稱的合併長度不可超過 94 個字元。

## <a name="turn-on-tde"></a>開啟 TDE

```azurecli
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

現在，資料庫或資料倉儲已使用 Azure Key Vault 中客戶管理的加密金鑰啟用 TDE。

## <a name="check-the-encryption-state-and-encryption-activity"></a>檢查加密狀態和加密活動

```azurecli
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>PowerShell Cmdlet

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- 使用[集-AzSql 資料庫透明資料加密](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption)cmdlet 關閉 TDE。

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled"
   ```

- 使用[Get-AzSqlServerKeyVaultKey cmdlet](/powershell/module/az.sql/get-azsqlserverkeyvaultkey)傳回添加到伺服器的關鍵保管庫密鑰清單。

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- 使用[「刪除-AzSqlServerKeyVaultKeyKey」](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey)從伺服器中刪除金鑰保管庫密鑰。

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

- 有關一般資料庫設定,請參閱[az sql](/cli/azure/sql)。

- 有關保存庫金鑰設定,請參閱[az sql 伺服器金鑰](/cli/azure/sql/server/key)。

- 有關 TDE 設定,請參閱[az sql server tde 鍵](/cli/azure/sql/server/tde-key)與 az sql db [tde](/cli/azure/sql/db/tde)。

* * *

## <a name="troubleshooting"></a>疑難排解

如果發生問題，請進行下列檢查：

- 如果找不到金鑰保管庫,請確保您處於正確的訂閱中。

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- 如果新的金鑰無法新增至伺服器，或新的金鑰無法更新為 TDE 保護裝置，請進行下列檢查：
   - 金鑰不應有到期日
   - 金鑰必須已啟用*取得*、*包裝金鑰*和*解除包裝金鑰*作業。

## <a name="next-steps"></a>後續步驟

- 瞭解如何旋轉伺服器的 TDE 保護程式以符合安全要求:[使用 PowerShell 旋轉透明資料加密保護程式](transparent-data-encryption-byok-azure-sql-key-rotation.md)。
- 如果存在安全風險,請瞭解如何刪除可能受損的 TDE 保護器:[刪除可能洩露的金鑰](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)。
