---
title: 透明資料加密
description: 概述 Azure Synapse Analytics 中 SQL Database 和 Synapse SQL 的透明資料加密。 本文件說明其優點和設定選項，其中包括服務管理的透明資料加密和「攜帶您自己的金鑰」。
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 04/10/2020
ms.openlocfilehash: 87abdb37ff7773b0205a2ce3ee21689a10c459d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113542"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>SQL Database 和 Azure Synapse 的透明資料加密

[透明資料加密（TDE）](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)可協助保護 Azure Synapse Analytics 中的 Azure SQL Database、azure sql 受控執行個體和 Synapse SQL，藉由加密待用資料來對抗惡意離線活動的威脅。 它會對資料庫、相關聯的備份和待用的交易記錄檔執行即時加密和解密，而不需變更應用程式。 根據預設，TDE 會針對所有新部署的 Azure SQL 資料庫啟用，而且需要針對 Azure SQL Database、Azure SQL 受控執行個體或 Azure Synapse 的舊版資料庫手動啟用。

TDE 會在分頁層次上執行資料的即時 I/O 加密和解密。 每個頁面在讀取到記憶體時都會進行解密，然後再重新加密並寫入至磁碟。 TDE 會使用稱為資料庫加密金鑰（DEK）的對稱金鑰來加密整個資料庫的儲存體。 在資料庫啟動時，加密的 DEK 會解密，然後用於解密和重新加密 SQL Server 資料庫引擎程式中的資料庫檔案。 DEK 會受到 TDE 保護裝置的保護。 TDE 保護裝置可以是服務管理的憑證（服務管理的透明資料加密）或儲存在[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)中的非對稱金鑰（客戶管理的透明資料加密）。 

針對 Azure SQL Database 和 Azure Synapse，TDE 保護裝置會設定于邏輯 SQL server 層級，並由與該伺服器相關聯的所有資料庫繼承。 針對 Azure SQL 受控執行個體（預覽中的 BYOK 功能），TDE 保護裝置會在實例層級設定，並由該實例上所有已加密的資料庫繼承。 除非另有說明，「伺服器」** 字詞在本文件中指的是伺服器和執行個體兩者。

> [!IMPORTANT]
> 根據預設，所有新建立的 Azure SQL 資料庫都會使用服務管理的透明資料加密進行加密。 在2017年5月之前建立的現有 SQL 資料庫，以及透過還原、異地複寫和資料庫複本建立的 SQL 資料庫，預設都不會加密。 根據預設，在2019年2月之前建立的現有受控執行個體資料庫不會加密。 透過 restore 建立的受控執行個體資料庫會從來源繼承加密狀態。

> [!NOTE]
> TDE 無法用來加密 SQL Database 中的邏輯**主要**資料庫。  **主要**資料庫包含在使用者資料庫上執行 TDE 作業所需的物件。


## <a name="service-managed-transparent-data-encryption"></a>服務管理的透明資料加密

在 Azure 中，TDE 的預設設定是由內建的伺服器憑證來保護 DEK。 內建伺服器憑證對每部伺服器而言是唯一的，而且使用的加密演算法是 AES 256。 如果資料庫是在異地複寫關聯性中，則主要和異地次要資料庫都會受到主資料庫的父伺服器金鑰的保護。 如果兩個資料庫連線到相同的伺服器，則也會共用同一個內建憑證。  Microsoft 會遵循內部安全性原則來自動旋轉這些憑證，而根金鑰是由 Microsoft 內部祕密存放區保護。  客戶可以在[Microsoft 信任中心](https://servicetrust.microsoft.com/)提供的獨立協力廠商審查報告中，確認 SQL Database 與內部安全性原則相容。

Microsoft 也會視異地複寫和還原的需要順暢地移動和管理金鑰。


## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>由客戶管理的透明資料加密：攜帶您自己的金鑰

客戶管理的 TDE 也稱為 TDE 的攜帶您自己的金鑰（BYOK）支援。 在此案例中，加密 DEK 的 TDE 保護裝置是客戶管理的非對稱金鑰，它會儲存在客戶擁有和受控 Azure Key Vault （Azure 的雲端式外部金鑰管理系統）中，而且永遠不會離開金鑰保存庫。 TDE 保護裝置可[由金鑰保存庫產生，或](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)從內部部署硬體安全模組（HSM）裝置傳輸至金鑰保存庫。 您必須對 SQL Database 授與客戶擁有之金鑰保存庫的權限，才能對 DEK 進行解密和加密。 如果撤銷了邏輯 SQL server 對金鑰保存庫的許可權，將無法存取資料庫，而且所有資料都會加密

透過與 Azure Key Vault 整合的 TDE，使用者可以使用 Azure Key Vault 功能來控制金鑰管理工作，包括金鑰輪替、金鑰保存庫權限、金鑰備份，以及啟用所有 TDE 保護裝置的稽核/報告功能。 Key Vault 提供集中式的金鑰管理、利用嚴密監控的 Hsm，並能夠區分金鑰與資料管理之間的責任，以協助符合安全性原則的合規性。
若要深入瞭解 Azure SQL Database 和 Azure Synapse 的 BYOK，請參閱[使用 Azure Key Vault 整合的透明資料加密](transparent-data-encryption-byok-azure-sql.md)。

若要開始使用 TDE 與 Azure Key Vault 整合，請參閱操作指南[使用您自己的金鑰開啟透明資料加密，從 Key Vault](transparent-data-encryption-byok-azure-sql-configure.md)。

## <a name="move-a-transparent-data-encryption-protected-database"></a>移動以透明資料加密保護的資料庫

在 Azure 中執行作業時，您無須解密資料庫。 目標會自動繼承來源資料庫或主要資料庫的 TDE 設定。 其中包含的作業涉及：

- 異地還原
- 自助式時間點還原
- 還原已刪除的資料庫
- 使用中的地理複寫
- 建立資料庫複本
- 將備份檔案還原至 Azure SQL 受控執行個體

> [!IMPORTANT]
> 不允許在 Azure SQL 受控執行個體中手動為服務管理的 TDE 所加密的資料庫建立「僅限複製」備份，因為無法存取用於加密的憑證。 請使用時間點還原功能將此類型的資料庫移至另一個受控執行個體。

當您匯出受 TDE 保護的資料庫時，匯出的資料庫內容不會加密。 這些匯出的內容會儲存在未加密的 BACPAC 檔案中。 請務必適當地保護 BACPAC 檔案，並在匯入新的資料庫完成後啟用 TDE。

例如，如果從內部部署 SQL Server 執行個體匯出 BACPAC 檔案，新資料庫的匯入內容並不會自動加密。 同樣地，如果將 BACPAC 檔案匯出至內部部署 SQL Server 執行個體，新的資料庫也不會自動加密。

但當您匯出至 SQL 資料庫或從中匯出時，則屬例外。 TDE 會在新的資料庫中啟用，但 BACPAC 檔案本身仍不會加密。


## <a name="manage-transparent-data-encryption"></a>管理透明資料加密
# <a name="portal"></a>[入口網站](#tab/azure-portal)
管理 Azure 入口網站中的 TDE。

若要透過 Azure 入口網站設定 TDE，您必須以 Azure 擁有者、參與者或 SQL 安全性管理員的身分連線。

您可以開啟和關閉資料庫層級的 TDE。 若要在資料庫上啟用 TDE，請移至[Azure 入口網站](https://portal.azure.com)，然後使用您的 Azure 系統管理員或參與者帳戶登入。 在您的使用者資料庫下方，找到 TDE 設定。 依預設會使用服務管理的透明資料加密。 系統會針對包含資料庫的伺服器自動產生 TDE 憑證。 針對 Azure SQL 受控執行個體使用 T-sql 在資料庫上開啟和關閉 TDE。

![服務管理的透明資料加密](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)  

您在伺服器層級上設定 TDE 主要金鑰（稱為 TDE 保護裝置）。 若要搭配使用 TDE 與 BYOK 支援，並使用 Key Vault 的金鑰保護您的資料庫，請開啟伺服器底下的 TDE 設定。

![具有「攜帶您自己的金鑰」支援的透明資料加密](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
使用 PowerShell 管理 TDE。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但所有未來的開發都是針對 Az .Sql 模組。 如需這些 Cmdlet，請參閱[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。

若要透過 PowerShell 設定 TDE，您必須以 Azure 擁有者、參與者或 SQL 安全性管理員的身分連線。

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>適用于 Azure SQL Database 和 Azure Synapse 的 Cmdlet

針對 Azure SQL Database 和 Azure Synapse，請使用下列 Cmdlet：

| Cmdlet | 描述 |
| --- | --- |
| [設定-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |啟用或停用資料庫的透明資料加密|
| [AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |取得資料庫的透明資料加密狀態 |
| [AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |查看資料庫的加密進度 |
| [新增-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |將 Key Vault 金鑰新增至 SQL Server 執行個體 |
| [AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |取得 Azure SQL Database 伺服器的 Key Vault 金鑰  |
| [設定-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |為 SQL Server 執行個體設定透明資料加密保護裝置 |
| [AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |取得透明資料加密保護裝置 |
| [移除-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |從 SQL Server 執行個體中移除 Key Vault 金鑰 |
|  | |

> [!IMPORTANT]
> 針對 Azure SQL 受控執行個體，請使用 T-sql [ALTER database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database)命令來開啟和關閉資料庫層級的 TDE，並檢查[範例 PowerShell 腳本](transparent-data-encryption-byok-azure-sql-configure.md)來管理實例層級上的 TDE。

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)
使用 Transact-sql 來管理 TDE。

使用在主要資料庫中作為系統管理員或 **dbmanager** 角色成員的登入，連線至資料庫。

| Command | 描述 |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF 可加密或解密資料庫 |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |傳回資料庫及其相關資料庫加密金鑰的加密狀態相關資訊 |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |傳回每個 Azure Synapse 節點和其相關資料庫加密金鑰的加密狀態相關資訊 |
|  | |

您無法使用 Transact-sql 將 TDE 保護裝置切換為 Key Vault 的金鑰。 請使用 PowerShell 或 Azure 入口網站。

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)
使用 REST API 來管理 TDE。

若要透過 REST API 設定 TDE，您必須以 Azure 擁有者、參與者或 SQL 安全性管理員的身分連線。
針對 Azure SQL Database 和 Azure Synapse 使用下列命令集：

| Command | 描述 |
| --- | --- |
|[建立或更新伺服器](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|將 Azure Active Directory 身分識別新增至 SQL Server 執行個體 (用以授與 Key Vault 的存取權)|
|[建立或更新伺服器金鑰](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|將 Key Vault 金鑰新增至 SQL Server 執行個體|
|[刪除伺服器金鑰](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|從 SQL Server 執行個體中移除 Key Vault 金鑰|
|[取得伺服器金鑰](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|從 SQL Server 執行個體取得特定的 Key Vault 金鑰|
|[依伺服器列出伺服器金鑰](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|取得 SQL Server 執行個體的 Key Vault 金鑰 |
|[建立或更新加密保護裝置](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|設定 SQL Server 實例的 TDE 保護裝置|
|[取得加密保護裝置](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|取得 SQL Server 實例的 TDE 保護裝置|
|[依伺服器列出加密保護裝置](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|取得 SQL Server 實例的 TDE 保護裝置 |
|[建立或更新透明資料加密組態](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|啟用或停用資料庫的 TDE|
|[取得透明資料加密組態](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|取得資料庫的 TDE 設定|
|[列出透明資料加密組態結果](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|取得資料庫的加密結果|

## <a name="see-also"></a>另請參閱
- 在 Azure 虛擬機器上執行的 SQL Server 也可以使用 Key Vault 中的非對稱金鑰。 其設定步驟與在 SQL Database 和 SQL 受控執行個體中使用非對稱金鑰不同。 如需詳細資訊，請參閱[使用 Azure Key Vault 進行可延伸金鑰管理 (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)。
- 如需 TDE 的一般描述，請參閱[透明資料加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)。
- 若要深入瞭解 Azure SQL Database 的 BYOK 支援、Azure SQL 受控執行個體和 Azure Synapse 的 TDE，請參閱[使用攜帶您自己的金鑰支援的透明資料加密](transparent-data-encryption-byok-azure-sql.md)。
- 若要開始使用具有攜帶您自己的金鑰支援的 TDE，請參閱操作指南[從 Key Vault 使用您自己的金鑰開啟透明資料加密](transparent-data-encryption-byok-azure-sql-configure.md)。
- 如需 Key Vault 的詳細資訊，請參閱[保護金鑰保存庫的存取權](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)。
