---
title: 透明資料加密
description: Azure 突觸分析中 SQL 資料庫和突觸 SQL 的透明數據加密概述。 本文件說明其優點和設定選項，其中包括服務管理的透明資料加密和「攜帶您自己的金鑰」。
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
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113542"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>SQL 資料庫和 Azure 突觸的透明資料加密

[透明資料加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)透過加密靜態資料,幫助保護 Azure 同步分析中的 Azure SQL 資料庫、Azure SQL 託管實例和 Synapse SQL,防止惡意離線活動的威脅。 它會對資料庫、相關聯的備份和待用的交易記錄檔執行即時加密和解密，而不需變更應用程式。 默認情況下,為所有新部署的 Azure SQL 資料庫啟用 TDE,並且需要手動為 Azure SQL 資料庫、Azure SQL 託管實例或 Azure 突觸的舊資料庫啟用 TDE。

TDE 會在分頁層次上執行資料的即時 I/O 加密和解密。 每個頁面在讀取到記憶體時都會進行解密，然後再重新加密並寫入至磁碟。 TDE 使用稱為資料庫加密金鑰 (DEK) 的對稱密鑰對整個資料庫的儲存進行加密。 在資料庫啟動時,將解密加密的 DEK,然後用於 SQL Server 資料庫引擎進程中資料庫文件的解密和重新加密。 DEK 受 TDE 保護器的保護。 TDE 保護器是服務託管憑證(服務管理透明資料加密)或存儲在[Azure 金鑰保管庫](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)中的非對稱金鑰(客戶管理的透明數據加密)。 

對於 Azure SQL 資料庫和 Azure 突觸,TDE 保護程式在邏輯 SQL 伺服器級別設置,並由與該伺服器關聯的所有資料庫繼承。 對於 Azure SQL 託管實例(預覽中的 BYOK 功能),TDE 保護器在實例級別設置,並由該實例上的所有加密資料庫繼承。 除非另有說明，「伺服器」** 字詞在本文件中指的是伺服器和執行個體兩者。

> [!IMPORTANT]
> 默認情況下,使用服務管理的透明數據加密對所有新創建的 Azure SQL 資料庫進行加密。 在 2017 年 5 月之前創建的現有 SQL 資料庫以及通過還原、異地複製和資料庫副本創建的 SQL 資料庫在默認情況下不加密。 默認情況下,在 2019 年 2 月之前創建的現有託管實例資料庫不加密。 通過還原創建的託管實例資料庫繼承源的加密狀態。

> [!NOTE]
> TDE 無法用來加密 SQL Database 中的邏輯**主要**資料庫。  **主要**資料庫包含在使用者資料庫上執行 TDE 作業所需的物件。


## <a name="service-managed-transparent-data-encryption"></a>服務管理的透明資料加密

在 Azure 中,TDE 的預設設定是 DEK 受內置伺服器證書的保護。 內建伺服器證書對於每個伺服器是唯一的,使用的加密演演演算法是 AES 256。 如果資料庫處於異地複製關係中,則主資料庫和異地資料庫都受主資料庫的父伺服器密鑰的保護。 如果兩個資料庫連線到相同的伺服器，則也會共用同一個內建憑證。  Microsoft 會遵循內部安全性原則來自動旋轉這些憑證，而根金鑰是由 Microsoft 內部祕密存放區保護。  客戶可以在[Microsoft 信任中心](https://servicetrust.microsoft.com/)提供的獨立第三方審核報告中驗證 SQL 資料庫是否符合內部安全原則。

Microsoft 也會視異地複寫和還原的需要順暢地移動和管理金鑰。


## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>由客戶管理的透明資料加密：攜帶您自己的金鑰

客戶管理的 TDE 也稱為自帶密鑰 (BYOK) 對 TDE 的支援。 在這種情況下,加密 DEK 的 TDE 保護器是客戶管理的不對稱密鑰,存儲在客戶擁有和管理的 Azure 金鑰保管庫(Azure 基於雲端的外部金鑰管理系統)中,永遠不會離開金鑰保管庫。 TDE 保護器可以[由金鑰保管庫生成,也可以](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)從本地硬體安全模組 (HSM) 設備傳輸到密鑰保管庫。 您必須對 SQL Database 授與客戶擁有之金鑰保存庫的權限，才能對 DEK 進行解密和加密。 如果取消邏輯 SQL 伺服器對金鑰保管庫的權限,則無法存取資料庫,並且所有資料都已加密

透過與 Azure Key Vault 整合的 TDE，使用者可以使用 Azure Key Vault 功能來控制金鑰管理工作，包括金鑰輪替、金鑰保存庫權限、金鑰備份，以及啟用所有 TDE 保護裝置的稽核/報告功能。 密鑰保管庫提供中央密鑰管理,利用受到嚴格監視的 HSM,並支援密鑰和數據管理之間的職責分離,以幫助滿足安全策略的合規性。
要瞭解有關 Azure SQL 資料庫與 Azure 突觸的 BYOK 的詳細資訊,請參閱[使用 Azure 金鑰保存庫整合的透明資料加密](transparent-data-encryption-byok-azure-sql.md)。

要開始使用具有 Azure 金鑰保管庫整合的 TDE,請參考如何[使用金鑰保存式庫中的金鑰開啟透明資料加密](transparent-data-encryption-byok-azure-sql-configure.md)。

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

匯出受 TDE 保護的資料庫時,資料庫的匯出內容不會加密。 這些匯出的內容會儲存在未加密的 BACPAC 檔案中。 請務必適當地保護 BACPAC 檔,並在導入新資料庫後啟用 TDE。

例如，如果從內部部署 SQL Server 執行個體匯出 BACPAC 檔案，新資料庫的匯入內容並不會自動加密。 同樣地，如果將 BACPAC 檔案匯出至內部部署 SQL Server 執行個體，新的資料庫也不會自動加密。

但當您匯出至 SQL 資料庫或從中匯出時，則屬例外。 新資料庫中啟用了 TDE,但 BACPAC 檔本身仍未加密。


## <a name="manage-transparent-data-encryption"></a>管理透明資料加密
# <a name="portal"></a>[入口網站](#tab/azure-portal)
在 Azure 門戶中管理 TDE。

要透過 Azure 門戶配置 TDE,必須作為 Azure 擁有者、參與者或 SQL 安全管理器進行連接。

在資料庫級別打開和關閉 TDE。 要在資料庫上啟用 TDE,請轉到[Azure 門戶](https://portal.azure.com)並使用 Azure 管理員或參與者帳戶登錄。 在您的使用者資料庫下方，找到 TDE 設定。 依預設會使用服務管理的透明資料加密。 自動為包含資料庫的伺服器生成 TDE 證書。 對於 Azure SQL 託管實例,使用 T-SQL 在資料庫上打開和關閉 TDE。

![服務管理的透明資料加密](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)  

在伺服器級別設置 TDE 主金鑰(稱為 TDE 保護器)。 要將 TDE 與 BYOK 支援一起使用,並使用密鑰保管庫的密鑰保護您的資料庫,請打開伺服器下的 TDE 設置。

![具有「攜帶您自己的金鑰」支援的透明資料加密](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
使用 PowerShell 管理 TDE。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL 資料庫仍然支援 PowerShell Azure 資源管理器模組,但所有後續開發都針對 Az.Sql 模組。 有關這些 cmdlet,請參閱[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的參數基本相同。

若要透過 PowerShell 設定 TDE，您必須以 Azure 擁有者、參與者或 SQL 安全性管理員的身分連線。

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Azure SQL 資料庫與 Azure 突觸的 Cmdlet

對 Azure SQL 資料庫與 Azure 突觸使用以下 cmdlet:

| Cmdlet | 描述 |
| --- | --- |
| [設定-AzSql資料庫透明資料加密](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |啟用或停用資料庫的透明資料加密|
| [取得 AzSql 資料庫透明資料加密](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |取得資料庫的透明資料加密狀態 |
| [取得 AzSql 資料庫透明資料加密活動](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |查看資料庫的加密進度 |
| [新增-AzSqlServer金鑰庫鍵](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |將 Key Vault 金鑰新增至 SQL Server 執行個體 |
| [取得阿茲SqlServer金鑰庫鍵](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |取得 Azure SQL Database 伺服器的 Key Vault 金鑰  |
| [設定-AzSqlServer透明資料加密保護器](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |為 SQL Server 執行個體設定透明資料加密保護裝置 |
| [取得 AzSqlServer 透明資料加密保護器](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |取得透明資料加密保護裝置 |
| [刪除-AzSqlServer金鑰庫鍵](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |從 SQL Server 執行個體中移除 Key Vault 金鑰 |
|  | |

> [!IMPORTANT]
> 對於 Azure SQL 託管實例,使用 T-SQL [ALTER 資料庫](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database)命令在資料庫等級打開和關閉 TDE,並檢查[範例 PowerShell 腳本](transparent-data-encryption-byok-azure-sql-configure.md)以在實例等級管理 TDE。

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)
使用交易 SQL 管理 TDE。

使用在主要資料庫中作為系統管理員或 **dbmanager** 角色成員的登入，連線至資料庫。

| Command | 描述 |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF 可加密或解密資料庫 |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |傳回資料庫及其相關資料庫加密金鑰的加密狀態相關資訊 |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |傳回有關 Azure 突觸節點及其關聯的資料庫加密金鑰的加密狀態的資訊 |
|  | |

無法使用 Transact-SQL 將 TDE 保護器切換到密鑰保管庫中的密鑰。 請使用 PowerShell 或 Azure 入口網站。

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)
使用 REST API 管理 TDE。

要透過 REST API 設定 TDE,必須作為 Azure 擁有者、參與者或 SQL 安全管理器進行連接。
對 Azure SQL 資料庫與 Azure 突觸使用以下指令集:

| Command | 描述 |
| --- | --- |
|[建立或更新伺服器](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|將 Azure Active Directory 身分識別新增至 SQL Server 執行個體 (用以授與 Key Vault 的存取權)|
|[建立或更新伺服器金鑰](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|將 Key Vault 金鑰新增至 SQL Server 執行個體|
|[刪除伺服器金鑰](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|從 SQL Server 執行個體中移除 Key Vault 金鑰|
|[取得伺服器金鑰](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|從 SQL Server 執行個體取得特定的 Key Vault 金鑰|
|[依伺服器列出伺服器金鑰](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|取得 SQL Server 執行個體的 Key Vault 金鑰 |
|[建立或更新加密保護裝置](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|設定 SQL Server 實體的 TDE 保護器|
|[取得加密保護裝置](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|取得 SQL Server 實體的 TDE 保護器|
|[依伺服器列出加密保護裝置](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|取得 SQL Server 實體的 TDE 保護器 |
|[建立或更新透明資料加密組態](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|為資料庫啟用或關閉 TDE|
|[取得透明資料加密組態](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|取得資料庫的 TDE 設定|
|[列出透明資料加密組態結果](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|取得資料庫的加密結果|

## <a name="see-also"></a>另請參閱
- 在 Azure 虛擬機器上執行的 SQL Server 也可以使用 Key Vault 中的非對稱金鑰。 其設定步驟與在 SQL Database 和 SQL 受控執行個體中使用非對稱金鑰不同。 如需詳細資訊，請參閱[使用 Azure Key Vault 進行可延伸金鑰管理 (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)。
- 有關 TDE 的一般說明,請參閱[透明資料加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)。
- 要瞭解有關 TDE 的詳細資訊,請透過 BYOK 支援 Azure SQL 資料庫、Azure SQL 託管實例和 Azure 突觸,請參閱[使用「自帶密鑰」支援進行透明資料加密](transparent-data-encryption-byok-azure-sql.md)。
- 要開始使用帶有自帶金鑰支援的 TDE,請參閱操作指南,[使用金鑰保管庫 中的您自己的金鑰打開透明資料加密](transparent-data-encryption-byok-azure-sql-configure.md)。
- 有關金鑰保管庫的詳細資訊,請參閱[安全存取金鑰保管庫](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)。
