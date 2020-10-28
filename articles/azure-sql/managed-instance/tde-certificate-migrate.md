---
title: 遷移 TDE 憑證-受控實例
description: 遷移憑證，以透明資料加密至 Azure SQL 受控執行個體保護資料庫的資料庫加密金鑰
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein, jovanpop
ms.date: 07/21/2020
ms.openlocfilehash: 80ff16156348db9c3a209757b48b7d54615d9104
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790690"
---
# <a name="migrate-a-certificate-of-a-tde-protected-database-to-azure-sql-managed-instance"></a>將受 TDE 保護之資料庫的憑證遷移至 Azure SQL 受控執行個體
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

當您使用原生還原選項將受 [透明資料加密 (TDE) ](/sql/relational-databases/security/encryption/transparent-data-encryption) 的資料庫移轉至 Azure SQL 受控執行個體時，來自 SQL Server 實例的對應憑證必須在資料庫還原之前進行遷移。 本文將逐步引導您完成將憑證手動遷移至 Azure SQL 受控執行個體的程式：

> [!div class="checklist"]
>
> * 將憑證匯出至個人資訊交換 ( .pfx) 檔案
> * 從檔案將憑證解壓縮至 base-64 字串
> * 使用 PowerShell Cmdlet 來上傳

如需使用完全受控的服務來順暢地遷移 TDE 保護的資料庫和對應的憑證的替代選項，請參閱 [如何使用 Azure 資料庫移轉服務，將內部部署資料庫移轉至 AZURE SQL 受控執行個體](../../dms/tutorial-sql-server-to-managed-instance.md)。

> [!IMPORTANT]
> 已遷移的憑證僅用於還原受 TDE 保護的資料庫。 在還原完成後，會根據您在實例上設定的 TDE 類型，將已遷移的憑證取代為不同的保護裝置，也就是服務管理的憑證或金鑰保存庫中的非對稱金鑰。

## <a name="prerequisites"></a>必要條件

若要完成本文中的步驟，您必須符合下列先決條件︰

* [Pvk2Pfx](/windows-hardware/drivers/devtest/pvk2pfx) 命令列工具會安裝在內部部署伺服器或其他電腦上，可以存取匯出為檔案的憑證。 Pvk2Pfx 工具是 [企業 Windows 驅動程式套件](/windows-hardware/drivers/download-the-wdk)的一部分，是獨立的命令列環境。
* 已安裝 [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell) 5.0 版或更新版本。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

請確定您具有下列項目：

* [已安裝並更新](/powershell/azure/install-az-ps)Azure PowerShell 模組。
* [Az. Sql 模組](https://www.powershellgallery.com/packages/Az.Sql)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Azure SQL 受控執行個體仍支援 PowerShell Azure Resource Manager 模組，但未來所有的開發都是針對 Az. Sql 模組。 如需這些 Cmdlet，請參閱 [AzureRM.Sql](/powershell/module/AzureRM.Sql/) \(英文\)。 Az 模組和 AzureRM 模組中命令的引數本質上相同。

在 PowerShell 中執行下列命令，以安裝/更新模組：

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

* * *

## <a name="export-the-tde-certificate-to-a-pfx-file"></a>將 TDE 憑證匯出到 .pfx 檔案

您可以直接從來源 SQL Server 實例或從憑證存放區匯出憑證（如果有的話）。

### <a name="export-the-certificate-from-the-source-sql-server-instance"></a>從來源 SQL Server 實例匯出憑證

使用下列步驟來匯出具有 SQL Server Management Studio 的憑證，並將它轉換成 .pfx 格式。 *TDE_Cert* 和 *full_path* 的一般名稱會用於憑證和檔案名，以及透過步驟的路徑。 應該以實際名稱加以取代。

1. 在 SSMS 中，開啟新的查詢視窗，並連接到來源 SQL Server 實例。

1. 使用下列腳本來列出受 TDE 保護的資料庫，並取得保護要遷移之資料庫加密的憑證名稱：

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

   ![TDE 憑證的清單](./media/tde-certificate-migrate/onprem-certificate-list.png)

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

   ![備份 TDE 憑證](./media/tde-certificate-migrate/backup-onprem-certificate.png)

1. 使用 PowerShell 主控台，將憑證資訊從一組新建立的檔案複製到 .pfx 檔案，使用 Pvk2Pfx 工具：

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-the-certificate-from-a-certificate-store"></a>從憑證存放區匯出憑證

如果憑證保留在 SQL Server 的本機電腦憑證存放區中，則可以使用下列步驟來匯出該憑證：

1. 開啟 PowerShell 主控台並執行下列命令，以開啟 Microsoft Management Console 的 [憑證] 嵌入式管理單元：

   ```cmd
   certlm
   ```

2. 在 [憑證] MMC 嵌入式管理單元中，展開 [個人 > 憑證的路徑] 以查看憑證清單。

3. 以滑鼠右鍵按一下憑證，然後按一下 [ **匯出** ]。

4. 遵循嚮導，將憑證和私密金鑰匯出至 .pfx 格式。

## <a name="upload-the-certificate-to-azure-sql-managed-instance-using-an-azure-powershell-cmdlet"></a>使用 Azure PowerShell Cmdlet 將憑證上傳至 Azure SQL 受控執行個體

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 在 PowerShell 中開始準備步驟：

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. 完成所有準備步驟之後，請執行下列命令，以將64編碼的憑證上傳至目標受控實例：

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -AsByteStream
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

您必須先使用 *.pfx* 檔案來 [設定 Azure key vault](../../key-vault/general/manage-with-cli2.md) 。

1. 在 PowerShell 中開始準備步驟：

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. 完成所有準備步驟之後，請執行下列命令，以將64編碼的憑證上傳至目標受控實例：

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

憑證現在可供指定的受控實例使用，而且對應的受 TDE 保護資料庫的備份可以成功還原。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何使用透明資料加密從內部部署或 IaaS SQL Server 實例，將保護資料庫加密金鑰的憑證遷移至 Azure SQL 受控執行個體。

請參閱將 [資料庫備份還原至 AZURE sql 受控執行個體](restore-sample-database-quickstart.md) ，以瞭解如何將資料庫備份還原至 azure sql 受控執行個體。