---
title: 遷移 TDE 憑證受控實例
description: 將使用透明資料加密保護資料庫之資料庫加密金鑰的憑證，移轉到 Azure SQL Database 受控執行個體
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: carlrab, jovanpop
ms.date: 04/25/2019
ms.openlocfilehash: 0f6e379287323d9353acd887cf30d5c9c0065959
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74555391"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-database-managed-instance"></a>將受 TDE 保護之資料庫的憑證移轉到 Azure SQL Database 受控執行個體

使用原生還原選項將受到[透明資料加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)保護的資料庫移轉到 Azure SQL Database 受控執行個體時，來自內部部署或 IaaS SQL Server 的對應憑證必須在資料庫還原之前進行移轉。 本文會逐步引導您進行將憑證手動移轉到 Azure SQL Database 受控執行個體的程序：

> [!div class="checklist"]
> * 將憑證匯出至個人資訊交換 (.pfx) 檔案
> * 將憑證從檔案擷取至 Base 64 字串
> * 使用 PowerShell Cmdlet 進行上傳

如需使用完全受控服務以進行受 TDE 保護資料庫和對應憑證順暢移轉的替代選項，請參閱[如何使用 Azure 資料庫移轉服務，將您的內部部署資料庫移轉到受控執行個體](../dms/tutorial-sql-server-to-managed-instance.md)。

> [!IMPORTANT]
> 移轉的憑證僅適用於還原受 TDE 保護的資料庫。 還原完成後，已遷移的憑證會由不同的保護裝置（服務管理的憑證或金鑰保存庫中的非對稱金鑰）取代，視您在實例上設定的透明資料加密類型而定。

## <a name="prerequisites"></a>Prerequisites

若要完成本文中的步驟，您必須符合下列先決條件︰

- [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) 命令列工具會安裝在內部部署伺服器或其他電腦上，可以存取匯出為檔案的憑證。 Pvk2Pfx 工具屬於[企業 Windows 驅動程式套件](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk)，這是一個獨立式命令列環境。
- 已安裝 [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell) 5.0 版或更新版本。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

請確定您具有下列項目：

- [已安裝並更新](https://docs.microsoft.com/powershell/azure/install-az-ps)Azure PowerShell 模組。
- [Az .sql module](https://www.powershellgallery.com/packages/Az.Sql)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但所有未來的開發都是針對 Az .Sql 模組。 如需這些 Cmdlet，請參閱[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。

在 PowerShell 中執行下列命令，以安裝/更新模組：

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

* * *

## <a name="export-tde-certificate-to-a-personal-information-exchange-pfx-file"></a>將 TDE 憑證匯出至個人資訊交換 (.pfx) 檔案

憑證可以直接從來源 SQL Server 匯出，或者如果是保存在憑證存放區，則從該處匯出。

### <a name="export-certificate-from-the-source-sql-server"></a>從來源 SQL Server 匯出憑證

請使用下列步驟，透過 SQL Server Management Studio 來匯出憑證，並將其轉換成 pfx 格式。 在整個步驟中，系統會對憑證、檔案名稱與路徑使用一般名稱 TDE_Cert** 和 full_path**。 應該以實際名稱加以取代。

1. 請在 SSMS 中，開啟新的查詢視窗，並連線至來源 SQL Server。

1. 使用下列指令碼以列出受 TDE 保護的資料庫，並取得用以保護欲移轉資料庫加密的憑證名稱：

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![TDE 憑證清單](./media/sql-database-managed-instance-migrate-tde-certificate/onprem-certificate-list.png)

1. 請執行下列指令碼以將憑證匯出至一組檔案 (.cer 和 .pvk)，保留公用和私密金鑰資訊：

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![備份 TDE 憑證](./media/sql-database-managed-instance-migrate-tde-certificate/backup-onprem-certificate.png)

1. 使用 PowerShell 主控台，利用 Pvk2Pfx 工具從一組新建立的檔案，將憑證資訊複製到個人資訊交換 (.pfx) 檔案：

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-certificate-from-certificate-store"></a>從憑證存放區匯出憑證

如果憑證保存在 SQL Server 的本機電腦憑證存放區中，可以使用下列步驟來匯出：

1. 開啟 PowerShell 主控台並執行下列命令，以開啟 Microsoft Management Console 的憑證嵌入式管理單元：

   ```cmd
   certlm
   ```

2. 在憑證 MMC 嵌入式管理單元中，展開路徑：[個人 -> 憑證]，以查看憑證的清單

3. 以滑鼠右鍵按一下憑證，然後按一下 [匯出...]

4. 遵循精靈步驟以將憑證和私密金鑰匯出為個人資訊交換格式

## <a name="upload-certificate-to-azure-sql-database-managed-instance-using-azure-powershell-cmdlet"></a>使用 Azure PowerShell Cmdlet 將憑證上傳到 Azure SQL Database 受控執行個體

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 在 PowerShell 中開始準備步驟：

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target Managed Instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. 一旦完成所有準備步驟，請執行下列命令以將 Base 64 編碼憑證上傳至目標受控執行個體：

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

您必須先使用 *.pfx*檔案來[設定 Azure Key Vault](/azure/key-vault/key-vault-manage-with-cli2) 。

1. 在 PowerShell 中開始準備步驟：

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target Managed Instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. 一旦完成所有準備步驟，請執行下列命令以將 Base 64 編碼憑證上傳至目標受控執行個體：

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

憑證現在可用於指定受控執行個體，而且可以成功還原對應受 TDE 保護資料庫的備份。

## <a name="next-steps"></a>後續步驟

在此文章中，您已了解如何將用以保護資料庫加密金鑰 (使用透明資料加密) 的憑證，從內部部署或 IaaS SQL Server 移轉到 Azure SQL Database 受控執行個體。

請參閱[將資料庫備份還原至 Azure SQL Database 受控執行個體](sql-database-managed-instance-get-started-restore.md)，以深入了解如何將資料庫備份還原至 Azure SQL Database 受控執行個體。
