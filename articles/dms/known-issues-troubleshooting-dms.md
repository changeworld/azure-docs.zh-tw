---
title: 常見問題-Azure 資料庫移轉服務
description: 瞭解如何對使用 Azure 資料庫移轉服務相關聯的常見已知問題/錯誤進行疑難排解。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: f0ec9d2a3794ea910339b4d329bb28f23c5a76b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91297353"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>針對常見的 Azure 資料庫移轉服務問題和錯誤進行疑難排解

本文說明 Azure 資料庫移轉服務使用者可以遇到的一些常見問題和錯誤。 本文也包含如何解決這些問題和錯誤的相關資訊。

> [!NOTE]
> 無偏差通訊
>
> Microsoft 支援多樣化且 inclusionary 的環境。 本文包含單字 _從屬_的參考。 [適用于無偏差通訊的 Microsoft 樣式指南](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md)會將此視為排他性行為單字。 本文中會使用這個字來保持一致性，因為它目前是出現在軟體中的單字。 當軟體更新為移除該字時，將會更新本文以進行調整。
>

## <a name="migration-activity-in-queued-state"></a>處於佇列狀態的遷移活動

當您在 Azure 資料庫移轉服務專案中建立新的活動時，活動會保持佇列狀態。

| 原因         | 解決方案 |
| ------------- | ------------- |
| 當 Azure 資料庫移轉服務實例達到同時執行的進行中工作的最大容量時，就會發生此問題。 任何新活動都會排入佇列，直到容量變成可用為止。 | 驗證資料移轉服務實例是否已跨專案執行活動。 您可以繼續建立新的活動，這些活動會自動新增至佇列以供執行。 一旦有任何現有的執行中活動完成後，下一個已排入佇列的活動就會開始執行，且狀態會自動變更為 [執行中] 狀態。 您不需要採取任何其他動作，就能開始遷移已排入佇列的活動。<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>為遷移選取的資料庫數目上限

為移至 Azure SQL Database 或 Azure SQL 受控執行個體的資料庫移轉專案建立活動時，會發生下列錯誤：

* **錯誤**：遷移設定驗證錯誤 "，" errorDetail "：「已選取超過最大數目 ' 4 ' 個資料庫」物件進行遷移。」

| 原因         | 解決方案 |
| ------------- | ------------- |
| 當您為單一遷移活動選取了四個以上的資料庫時，就會顯示此錯誤。 目前，每個遷移活動的限制為四個資料庫。 | 為每個遷移活動選取四個或更少的資料庫。 如果您需要平行遷移四個以上的資料庫，請布建另一個 Azure 資料庫移轉服務實例。 目前，每個訂用帳戶最多支援兩個 Azure 資料庫移轉服務實例。<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>MySQL 遷移至 Azure MySQL 但發生復原失敗的錯誤

當您使用 Azure 資料庫移轉服務從 MySQL 遷移至適用於 MySQL 的 Azure 資料庫時，遷移活動會失敗，並出現下列錯誤：

* **錯誤**：資料庫移轉錯誤-工作 ' TaskID ' 因 [n] 連續復原失敗而暫止。

| 原因         | 解決方案 |
| ------------- | ------------- |
| 當執行遷移的使用者缺少 ReplicationAdmin 角色及/或複寫用戶端、複寫複本和 SUPER (MySQL 5.6.6) 之前的許可權時，可能會發生此錯誤。<br><br><br><br><br><br><br><br><br><br><br><br><br> | 請確定已在適用於 MySQL 的 Azure 資料庫實例上正確設定使用者帳戶的 [必要許可權](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) 。 例如，您可以遵循下列步驟來建立名為 ' migrateuser 作業 ' 的使用者，並具備必要許可權：<br>1. 建立 ' secret ' 所識別的使用者 migrateuser@ '% '; <br>2. 將 db_name. * 的擁有權限授與 ' secret ' 所識別的 ' migrateuser 作業 ' @ '% ';重複此步驟以授與更多資料庫的存取權 <br>3. 將複寫從屬授與 *。* ' migrateuser 作業 ' @ '% ' （由 ' secret ' 識別）;<br>4. 授與複寫用戶端的 *。* ' migrateuser 作業 ' @ '% ' （由 ' secret ' 識別）;<br>5. 排清許可權; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>嘗試停止 Azure 資料庫移轉服務時發生錯誤

當您停止 Azure 資料庫移轉服務實例時，會收到下列錯誤：

* **錯誤**：服務無法停止。 錯誤：'error':{'code':'InvalidRequest','message':'目前正在執行一或多個活動。 若要停止服務，請等候活動完成，或手動停止這些活動，然後再試一次。 '}}

| 原因         | 解決方案 |
| ------------- | ------------- |
| 當您嘗試停止的服務實例包含仍在執行中或存在於遷移專案中的活動時，就會顯示此錯誤。 <br><br><br><br><br><br> | 確定您嘗試停止的 Azure 資料庫移轉服務實例中沒有任何正在執行的活動。 您也可以先刪除活動或專案，再嘗試停止服務。 下列步驟說明如何藉由刪除所有執行中的工作來移除專案，以清除遷移服務實例：<br>1. Install-Module-Name AzureRM. Microsoft.datamigration <br>2. Login-AzureRmAccount <br>3. Select-AzureRmSubscription-SubscriptionName " \<subName> " <br> 4. Remove-AzureRmDataMigrationProject-Name \<projectName> -ResourceGroupName \<rgName> -ServiceName \<serviceName> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>嘗試啟動 Azure 資料庫移轉服務時發生錯誤

當您啟動 Azure 資料庫移轉服務實例時，收到下列錯誤：

* **錯誤**：服務無法啟動。 錯誤： {' errorDetail '：「服務無法啟動，請洽詢 Microsoft 支援服務」}

| 原因         | 解決方案 |
| ------------- | ------------- |
| 當先前的實例在內部失敗時，就會顯示此錯誤。 此錯誤很少發生，而且工程小組知道它。 <br> | 刪除您無法啟動的服務實例，然後布建新的實例來取代它。 |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>將 SQL 遷移至 Azure SQL DB 受控實例時，還原資料庫時發生錯誤

當您執行從 SQL Server 線上遷移至 Azure SQL 受控執行個體時，轉換會失敗，並出現下列錯誤：

* **錯誤**：作業識別碼 ' operationId ' 的還原作業失敗。 程式碼 ' AuthorizationFailed '，訊息 ' 具有物件識別碼 ' objectId ' 的用戶端 ' clientId ' 沒有授權，無法在範圍 '/subscriptions/subscriptionId ' 中執行 ' Microsoft .Sql/位置/managedDatabaseRestoreAzureAsyncOperation/read ' 動作。

| 原因         | 解決方案    |
| ------------- | ------------- |
| 此錯誤表示用於從 SQL Server 到 SQL 的線上遷移的應用程式主體受控執行個體沒有訂用帳戶的「參與」許可權。 目前有受控執行個體的特定 API 呼叫需要訂用帳戶的許可權，才能進行還原作業。 <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | 使用 `Get-AzureADServicePrincipal` `-ObjectId` 錯誤訊息中提供的 PowerShell Cmdlet，以列出所使用之應用程式識別碼的顯示名稱。<br><br> 驗證此應用程式的許可權，並確定它具有訂用帳戶層級的「 [參與者」角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) 。 <br><br> Azure 資料庫移轉服務工程小組正在努力限制訂用帳戶上目前「參與」角色所需的存取權。 如果您的商務需求不允許使用「參與」角色，請聯絡 Azure 支援以取得其他協助。 |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>刪除與 Azure 資料庫移轉服務相關聯的 NIC 時發生錯誤

當您嘗試刪除與 Azure 資料庫移轉服務相關聯的網路介面卡時，刪除嘗試將會失敗，並出現下列錯誤：

* **錯誤**：由於 DMS 服務使用 NIC，無法刪除與 Azure 資料庫移轉服務相關聯的 NIC

| 原因         | 解決方案    |
| ------------- | ------------- |
| 發生此問題的原因是 Azure 資料庫移轉服務實例可能仍存在並耗用 NIC。 <br><br><br><br><br><br><br><br> | 若要刪除此 NIC，請刪除自動刪除服務所使用之 NIC 的 DMS 服務實例。<br><br> **重要**事項：請確定所刪除的 Azure 資料庫移轉服務實例沒有任何執行中的活動。<br><br> 刪除所有與 Azure 資料庫移轉服務實例相關聯的專案和活動之後，您就可以刪除該服務實例。 服務實例所使用的 NIC 會在服務刪除過程中自動清除。 |

## <a name="connection-error-when-using-expressroute"></a>使用 ExpressRoute 時的連線錯誤

當您嘗試在 Azure 資料庫移轉服務專案精靈中連線到來源時，如果來源使用 ExpressRoute 進行連線，則連線會在延長逾時之後發生失敗。

| 原因         | 解決方案    |
| ------------- | ------------- |
| 使用 [ExpressRoute](https://azure.microsoft.com/services/expressroute/)時，Azure 資料庫移轉服務 [需要](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) 在與服務相關聯的虛擬網路子網上布建三個服務端點：<br> --服務匯流排端點<br> --儲存體端點<br> --目標資料庫端點 (例如 SQL 端點、Cosmos DB 端點) <br><br><br><br><br> | 針對來源與 Azure 資料庫移轉服務之間的 ExpressRoute 連線能力[啟用](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online)必要的服務端點。 <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>將 MySQL 資料庫遷移至適用于 MySQL 的 Azure DB 時，鎖定等候逾時錯誤

當您透過 Azure 資料庫移轉服務將 MySQL 資料庫遷移至適用於 MySQL 的 Azure 資料庫實例時，遷移會失敗，並出現下列鎖定等候逾時錯誤：

* **錯誤**：資料庫移轉錯誤-無法載入檔案-無法啟動檔案 ' n ' 的載入進程 RetCode： SQL_ERROR SQLSTATE： HY000 NativeError：1205訊息： [MySQL] [ODBC 驅動程式] [Mysqld.exe] 鎖定等候超時;嘗試重新開機交易

| 原因         | 解決方案    |
| ------------- | ------------- |
| 當遷移失敗，因為遷移期間的鎖定等候超時，就會發生此錯誤。 | 請考慮增加伺服器參數 **' innodb_lock_wait_timeout '** 的值。 允許的最高值為1073741824。 |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>使用動態埠或命名實例時，連接到來源 SQL Server 時發生錯誤

當您嘗試將 Azure 資料庫移轉服務連線至在命名的實例或動態埠上執行的 SQL Server 來源時，連線會失敗並出現下列錯誤：

* **錯誤**：-1-SQL 連接失敗。 和 SQL Server 建立連線時，發生與網路相關或執行個體特定的錯誤。 找不到或無法存取伺服器。 檢查執行個體名稱是否正確以及 SQL Server 執行個體是否設定為允許遠端連接。 (提供者：SQL 網路介面，錯誤：26 - 搜尋指定的伺服器/執行個體時發生錯誤)

| 原因         | 解決方案    |
| ------------- | ------------- |
| 當 Azure 資料庫移轉服務嘗試連接的來源 SQL Server 實例具有動態埠或正在使用已命名的實例時，就會發生此問題。 SQL Server Browser 服務會接聽 UDP 埠1434，以連線到已命名的實例或使用動態埠時的連入連線。 當 SQL Server 服務重新開機時，動態埠可能會變更。 您可以透過 SQL Server 組態管理員中的 [網路設定]，檢查指派給實例的動態埠。<br><br><br> |確認 Azure 資料庫移轉服務可以透過動態指派的 TCP 埠，在 UDP 埠1434和 SQL Server 實例上連接到來源 SQL Server Browser 服務。 |

## <a name="additional-known-issues"></a>其他已知問題

* [線上遷移至 Azure SQL Database 的已知問題/遷移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [線上遷移至適用於 MySQL 的 Azure 資料庫的已知問題/遷移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [線上遷移至適用於 PostgreSQL 的 Azure 資料庫的已知問題/遷移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>後續步驟

* 查看 [Azure 資料庫移轉服務 PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)的文章。
* 請參閱文章， [以瞭解如何使用 Azure 入口網站在適用於 MySQL 的 Azure 資料庫中設定伺服器參數](https://docs.microsoft.com/azure/mysql/howto-server-parameters)。
* 請參閱文章， [以瞭解使用 Azure 資料庫移轉服務的必要條件](https://docs.microsoft.com/azure/dms/pre-reqs)。
* 請參閱 [有關使用 Azure 資料庫移轉服務的常見問題](https://docs.microsoft.com/azure/dms/faq)。
