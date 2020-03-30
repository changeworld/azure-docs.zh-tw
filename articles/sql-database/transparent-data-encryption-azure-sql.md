---
title: 透明資料加密
description: Azure Synaps 中 SQL 資料庫和 SQL 分析的透明資料加密概述。 本文件說明其優點和設定選項，其中包括服務管理的透明資料加密和「攜帶您自己的金鑰」。
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
ms.date: 02/06/2020
ms.openlocfilehash: 5bbb537ef6545852423bf5315b7636671c598fdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255635"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>用於 SQL 資料庫和 Azure 突觸的透明資料加密

透明資料加密 （TDE） 通過加密靜態資料，有助於保護 Azure SQL 資料庫、Azure SQL 託管實例和 Azure 突觸，防止惡意離線活動的威脅。 它會對資料庫、相關聯的備份和待用的交易記錄檔執行即時加密和解密，而不需變更應用程式。 根據預設，會為所有新部署的 Azure SQL 資料庫啟用 TDE。 TDE 無法用來加密 SQL Database 中的邏輯**主要**資料庫。  **主要**資料庫包含在使用者資料庫上執行 TDE 作業所需的物件。

需要手動為 Azure SQL 資料庫、Azure SQL 託管實例或 Azure Synape 的舊資料庫啟用 TDE。
通過還原創建的託管實例資料庫繼承源資料庫的加密狀態。

透明資料加密會使用稱為資料庫加密金鑰的對稱金鑰，來將整個資料庫的儲存體加密。 此資料庫加密金鑰受到透明資料加密保護裝置的保護。 此保護裝置可以是服務管理的憑證 (服務管理的透明資料加密) 或儲存在 Azure Key Vault 中的非對稱金鑰 (攜帶您自己的金鑰)。 在伺服器級別為 Azure SQL 資料庫和 Azure 突觸設置透明資料加密保護程式，並為 Azure SQL 託管實例設置實例級別。 除非另有說明，「伺服器」** 字詞在本文件中指的是伺服器和執行個體兩者。

在資料庫啟動時，加密的資料庫加密金鑰會進行解密，然後在 SQL Server Database Engine 程序中用於資料庫檔案的解密和重新加密。 透明資料加密會在頁面層級執行資料的即時 I/O 加密和解密。 每個頁面在讀取到記憶體時都會進行解密，然後再重新加密並寫入至磁碟。 有關透明資料加密的一般說明，請參閱[透明資料加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)。

在 Azure 虛擬機器上執行的 SQL Server 也可以使用 Key Vault 中的非對稱金鑰。 其設定步驟與在 SQL Database 和 SQL 受控執行個體中使用非對稱金鑰不同。 如需詳細資訊，請參閱[使用 Azure Key Vault 進行可延伸金鑰管理 (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)。

## <a name="service-managed-transparent-data-encryption"></a>服務管理的透明資料加密

在 Azure 中，透明資料加密的預設設定是以內建伺服器憑證保護資料庫加密金鑰。 內置伺服器憑證對於每個伺服器是唯一的，使用的加密演算法是 AES 256。 如果資料庫具有異地複寫關聯性，則主要和異地次要資料庫都會受到主要資料庫的父伺服器金鑰保護。 如果兩個資料庫連線到相同的伺服器，則也會共用同一個內建憑證。  Microsoft 會遵循內部安全性原則來自動旋轉這些憑證，而根金鑰是由 Microsoft 內部祕密存放區保護。  客戶可以在[Microsoft 信任中心](https://servicetrust.microsoft.com/)提供的獨立協力廠商審核報告中驗證 SQL 資料庫是否符合內部安全性原則。

Microsoft 也會視異地複寫和還原的需要順暢地移動和管理金鑰。

> [!IMPORTANT]
> 預設情況下，使用服務託管的透明資料加密對所有新創建的 SQL 資料庫和託管實例資料庫進行加密。 在 2017 年 5 月之前創建的現有 SQL 資料庫以及通過還原、異地複製和資料庫複本建立的 SQL 資料庫在預設情況下不加密。 預設情況下，在 2019 年 2 月之前創建的現有託管實例資料庫不加密。 通過還原創建的託管實例資料庫繼承源的加密狀態。

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>由客戶管理的透明資料加密：攜帶您自己的金鑰

[Azure Key Vault 中使用由客戶管理之金鑰進行的 TDE](transparent-data-encryption-byok-azure-sql.md) 允許使用由客戶管理的非對稱金鑰 (稱為 TDE 保護裝置) 來加密資料庫加密金鑰 (DEK)。  這通常也稱為透明資料加密的攜帶您自己的金鑰 (BYOK) 支援。 在 BYOK 情節中，TDE 保護裝置會儲存於客戶擁有且管理的 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) 中，這是 Azure 的雲端式外部金鑰管理系統。 TDE 保護器可以[由金鑰保存庫生成，也可以](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)從本地 HSM 設備傳輸到金鑰保存庫。 儲存於資料庫開機頁面上的 TDE DEK，是由 TDE 保護裝置進行加密和解密，該保護裝置儲存於 Azure Key Vault 中且永遠都不會離開金鑰保存庫。  您必須對 SQL Database 授與客戶擁有之金鑰保存庫的權限，才能對 DEK 進行解密和加密。 如果撤銷了邏輯 SQL 伺服器對金鑰保存庫的權限，資料庫將無法存取且會將所有資料加密。 針對 Azure SQL Database，會將 TDE 保護裝置設定於邏輯 SQL 伺服器層級，並由所有與該伺服器相關聯的資料庫繼承。 針對 [Azure SQL 受控執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance)，系統會將 TDE 保護裝置設定於執行個體層級，並由該執行個體上的所有「加密」** 資料庫繼承。 除非另有說明，「伺服器」** 字詞在本文件中指的是伺服器和執行個體兩者。

透過與 Azure Key Vault 整合的 TDE，使用者可以使用 Azure Key Vault 功能來控制金鑰管理工作，包括金鑰輪替、金鑰保存庫權限、金鑰備份，以及啟用所有 TDE 保護裝置的稽核/報告功能。 Key Vault 可提供集中金鑰管理、使用嚴密監控的硬體安全性模組 (HSM)，並能區分管理金鑰和資料的責任，以協助符合安全性原則的合規性。
要瞭解有關 Azure 金鑰保存庫集成（為 Azure SQL 資料庫、SQL 託管實例和 Azure 突觸提供您自己的金鑰支援）的透明資料加密的詳細資訊，請參閱[使用 Azure 金鑰保存庫集成的透明資料加密](transparent-data-encryption-byok-azure-sql.md)。

若要開始使用與 Azure Key Vault 整合 (攜帶您自己的金鑰支援) 的透明資料加密，請參閱[透過 PowerShell 從 Key Vault 使用您自己的金鑰開啟透明資料加密](transparent-data-encryption-byok-azure-sql-configure.md)操作指南。

## <a name="move-a-transparent-data-encryption-protected-database"></a>移動以透明資料加密保護的資料庫

在 Azure 中執行作業時，您無須解密資料庫。 在目標上會自動繼承來源資料庫或主要資料庫的透明資料加密設定。 其中包含的作業涉及：

- 異地還原
- 自助式時間點還原
- 還原已刪除的資料庫
- 使用中的地理複寫
- 建立資料庫複本
- 將備份檔案還原至 Azure SQL 受控執行個體

> [!IMPORTANT]
> 不允許在 Azure SQL 受控執行個體中手動為服務管理的 TDE 所加密的資料庫建立「僅限複製」備份，因為無法存取用於加密的憑證。 請使用時間點還原功能將此類型的資料庫移至另一個受控執行個體。

當您匯出以透明資料加密保護的資料庫時，匯出的資料庫內容並不會加密。 這個匯出的內容會儲存在未加密的 BACPAC 檔案中。 請務必適當保護 BACPAC 檔案，並在新的資料庫匯入完成後啟用透明資料加密。

例如，如果從內部部署 SQL Server 執行個體匯出 BACPAC 檔案，新資料庫的匯入內容並不會自動加密。 同樣地，如果將 BACPAC 檔案匯出至內部部署 SQL Server 執行個體，新的資料庫也不會自動加密。

但當您匯出至 SQL 資料庫或從中匯出時，則屬例外。 新的資料庫中會啟用透明資料加密，但 BACPAC 檔案本身仍不會加密。


## <a name="manage-transparent-data-encryption"></a>管理透明資料加密
# <a name="portal"></a>[入口網站](#tab/azure-portal)
在 Azure 門戶中管理透明資料加密。

若要透過 Azure 入口網站設定透明資料加密，您必須以 Azure 擁有者、參與者或 SQL 安全性管理員的身分連線。

您要在資料庫層級上開啟和關閉透明資料加密。 若要在資料庫上啟用透明資料加密，請前往 [Azure 入口網站](https://portal.azure.com)，並使用您的 Azure 系統管理員或參與者帳戶登入。 在您的使用者資料庫下找出透明資料加密設定。 依預設會使用服務管理的透明資料加密。 系統會自動為包含資料庫的伺服器產生透明資料加密憑證。 針對 Azure SQL 受控執行個體，使用 T-SQL 在資料庫上開啟和關閉透明資料加密。

![服務管理的透明資料加密](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)

您可以在伺服器層級上設定透明資料加密主要金鑰，也就是透明資料加密保護裝置。 若要使用具有「攜帶您自己的金鑰」支援的透明資料加密，並以 Key Vault 中的金鑰保護您的資料庫，請開啟伺服器下方的透明資料加密設定。

![具有「攜帶您自己的金鑰」支援的透明資料加密](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)
使用 PowerShell 管理透明資料加密。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL 資料庫仍然支援 PowerShell Azure 資源管理器模組，但所有後續開發都針對 Az.Sql 模組。 有關這些 Cmdlet，請參閱[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的參數基本相同。

若要透過 PowerShell 設定透明資料加密，您必須以 Azure 擁有者、參與者或 SQL 安全性管理員的身分連線。

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>用於 Azure SQL 資料庫和 Azure 突觸的 Cmdlet

對 Azure SQL 資料庫和 Azure 突觸使用以下 Cmdlet：

| Cmdlet | 描述 |
| --- | --- |
| [設置-AzSql資料庫透明資料加密](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |啟用或停用資料庫的透明資料加密|
| [獲取 AzSql 資料庫透明資料加密](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |取得資料庫的透明資料加密狀態 |
| [獲取 AzSql 資料庫透明資料加密活動](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |查看資料庫的加密進度 |
| [添加-AzSqlServer金鑰庫鍵](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |將 Key Vault 金鑰新增至 SQL Server 執行個體 |
| [獲取阿茲SqlServer金鑰庫鍵](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |取得 Azure SQL Database 伺服器的 Key Vault 金鑰  |
| [設置-AzSqlServer透明資料加密保護器](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |為 SQL Server 執行個體設定透明資料加密保護裝置 |
| [獲取 AzSqlServer 透明資料加密保護器](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |取得透明資料加密保護裝置 |
| [刪除-AzSqlServer金鑰庫鍵](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |從 SQL Server 執行個體中移除 Key Vault 金鑰 |
|  | |

> [!IMPORTANT]
> 針對 Azure SQL 受控執行個體，使用 T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) 命令在資料庫層級上開啟和關閉透明資料加密，並查看[範例 PowerShell 指令碼](transparent-data-encryption-byok-azure-sql-configure.md)以在執行個體層級上管理透明資料加密。

# <a name="transact-sql"></a>[交易-SQL](#tab/azure-TransactSQL)
使用 Transact-SQL 管理透明資料加密。

使用在主要資料庫中作為系統管理員或 **dbmanager** 角色成員的登入，連線至資料庫。

| Command | 描述 |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF 可加密或解密資料庫 |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |傳回資料庫及其相關資料庫加密金鑰的加密狀態相關資訊 |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |傳回每個資料倉儲節點及其相關資料庫加密金鑰的加密狀態相關資訊 |
|  | |

您無法使用 Transact-SQL 將透明資料加密保護裝置切換為 Key Vault 中的金鑰。 請使用 PowerShell 或 Azure 入口網站。

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)
使用 REST API 管理透明資料加密。

若要透過 REST API 設定透明資料加密，您必須以 Azure 擁有者、參與者或 SQL 安全性管理員的身分連線。
對 Azure SQL 資料庫和 Azure 突觸使用以下命令集：

| Command | 描述 |
| --- | --- |
|[建立或更新伺服器](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|將 Azure Active Directory 身分識別新增至 SQL Server 執行個體 (用以授與 Key Vault 的存取權)|
|[建立或更新伺服器金鑰](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|將 Key Vault 金鑰新增至 SQL Server 執行個體|
|[刪除伺服器金鑰](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|從 SQL Server 執行個體中移除 Key Vault 金鑰|
|[取得伺服器金鑰](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|從 SQL Server 執行個體取得特定的 Key Vault 金鑰|
|[依伺服器列出伺服器金鑰](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|取得 SQL Server 執行個體的 Key Vault 金鑰 |
|[建立或更新加密保護裝置](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|為 SQL Server 執行個體設定透明資料加密保護裝置|
|[取得加密保護裝置](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|取得 SQL Server 執行個體的透明資料加密保護裝置|
|[依伺服器列出加密保護裝置](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|取得 SQL Server 執行個體的透明資料加密保護裝置 |
|[建立或更新透明資料加密組態](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|啟用或停用資料庫的透明資料加密|
|[取得透明資料加密組態](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|取得資料庫的透明資料加密組態|
|[列出透明資料加密組態結果](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|取得資料庫的加密結果|

## <a name="next-steps"></a>後續步驟

- 有關透明資料加密的一般說明，請參閱[透明資料加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)。
- 要瞭解有關透明資料加密的詳細資訊，請通過自帶金鑰支援 Azure SQL 資料庫、Azure SQL 託管實例和 Azure Synapse 來瞭解[透明資料加密，請參閱使用"自帶金鑰支援"進行透明資料加密](transparent-data-encryption-byok-azure-sql.md)。
- 若要開始使用具有「攜帶您自己的金鑰」支援的透明資料加密，請參閱[透過 PowerShell 從 Key Vault 使用您自己的金鑰開啟透明資料加密](transparent-data-encryption-byok-azure-sql-configure.md)操作指南。
- 如需 Key Vault 的詳細資訊，請參閱 [Key Vault 文件頁面](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)。
