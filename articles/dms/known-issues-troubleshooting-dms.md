---
title: 常見問題 - Azure 資料庫移轉服務
description: 瞭解如何解決與使用 Azure 資料庫移轉服務相關的常見已知問題/錯誤。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: c5d2ad481124f5ae048d010cdf632ee661bbd6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649102"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>解決常見的 Azure 資料庫移轉服務問題和錯誤

本文介紹了 Azure 資料庫移轉服務使用者可能遇到的一些常見問題和錯誤。 本文還包括有關如何解決這些問題和錯誤的資訊。

## <a name="migration-activity-in-queued-state"></a>處於排隊狀態的遷移活動

在 Azure 資料庫移轉服務專案中創建新活動時，活動將保持排隊狀態。

| 原因         | 解決方案 |
| ------------- | ------------- |
| 當 Azure 資料庫移轉服務實例已達到同時運行的持續任務的最大容量時，將發生此問題。 任何新活動都排隊，直到容量可用。 | 驗證資料移轉服務實例在專案之間正在運行的活動。 您可以繼續創建新活動，這些活動會自動添加到佇列中以執行。 一旦任何現有正在運行的活動完成，下一個排隊活動就會開始運行，狀態將自動更改為運行狀態。 無需執行任何其他操作即開始遷移排隊活動。<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>為遷移選擇的最大資料庫數

為資料庫移轉專案創建活動以移動到 Azure SQL 資料庫或 Azure SQL 資料庫託管實例時，會發生以下錯誤：

* **錯誤**：遷移設置驗證錯誤"，"錯誤詳細資訊"："已選擇多個"資料庫"的最大"4"物件進行遷移。

| 原因         | 解決方案 |
| ------------- | ------------- |
| 當您為單個遷移活動選擇了多個資料庫時，將顯示此錯誤。 目前，每個遷移活動僅限於四個資料庫。 | 每個遷移活動選擇四個或更少的資料庫。 如果需要並行遷移四個以上資料庫，則預配 Azure 資料庫移轉服務的另一個實例。 目前，每個訂閱最多支援兩個 Azure 資料庫移轉服務實例。<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>MySQL 遷移到 Azure MySQL 時的錯誤，導致恢復失敗

使用 Azure 資料庫移轉服務從 MySQL 遷移到 MySQL Azure 資料庫時，遷移活動將失敗，出現以下錯誤：

* **錯誤**： 資料庫移轉錯誤 - 任務"TaskID"由於 {n} 個連續恢復失敗而掛起。

| 原因         | 解決方案 |
| ------------- | ------------- |
| 當執行遷移的使用者缺少複製管理員角色和/或複製用戶端、複製副本和 SUPER 許可權（版本早于 MySQL 5.6.6 版本）時，可能會出現此錯誤。<br><br><br><br><br><br><br><br><br><br><br><br><br> | 確保使用者帳戶[的先決條件許可權](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites)在 MySQL 實例的 Azure 資料庫上準確配置。 例如，可以遵循以下步驟創建具有所需許可權的名為"遷移使用者"的使用者：<br>1. 創建使用者migrateuser@"%"識別為"機密"; <br>2. 授予db_name.* 上的所有特權，以"遷移使用者"*"%"（由"機密"標識）;重複此步驟以授予更多資料庫的存取權限 <br>3. 在 上授予複製從屬 *。* 到"遷移使用者"@%"，由"機密"標識;<br>4. 在 上授予複製用戶端 *。* 到"遷移使用者"@%"，由"機密"標識;<br>5. 刷新特權; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>嘗試停止 Azure 資料庫移轉服務時出錯

停止 Azure 資料庫移轉服務實例時，您會收到以下錯誤：

* **錯誤**： 服務無法停止。 錯誤：'error':{'code':'InvalidRequest','message':'目前正在執行一或多個活動。 要停止服務，請等待活動完成或手動停止這些活動，然後重試。

| 原因         | 解決方案 |
| ------------- | ------------- |
| 此錯誤顯示您嘗試停止的服務實例包括遷移專案中仍在運行或存在的活動。 <br><br><br><br><br><br> | 確保嘗試停止的 Azure 資料庫移轉服務實例中沒有正在運行的活動。 您也可以在嘗試停止服務之前刪除活動或專案。 以下步驟演示如何刪除專案以通過刪除所有正在運行的任務來清理遷移服務實例：<br>1. 安裝模組 - 名稱 AzureRM.資料移轉 <br>2. 登錄-AzureRm 帳戶 <br>3. 選擇 AzureRm 訂閱 -\<訂閱名稱 "子名稱>" <br> 4. 刪除 AzureRmData 遷移專案\<- 名稱專案名稱> \<- 資源組名稱\<rgname> - 服務名稱服務名稱> -刪除運行任務 |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>嘗試啟動 Azure 資料庫移轉服務時出錯

啟動 Azure 資料庫移轉服務實例時，您會收到以下錯誤：

* **錯誤**：服務無法啟動。 錯誤： {"錯誤詳細資訊"："服務無法啟動，請聯繫 Microsoft 支援人員"

| 原因         | 解決方案 |
| ------------- | ------------- |
| 當上一個實例在內部失敗時，將顯示此錯誤。 此錯誤很少發生，並且工程團隊知道這一點。 <br> | 刪除無法啟動的服務實例，然後預配新的實例以替換它。 |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>將 SQL 遷移到 Azure SQL DB 託管實例時還原資料庫時出錯

當您執行從 SQL Server 到 Azure SQL 資料庫託管實例的線上遷移時，複製失敗，出現以下錯誤：

* **錯誤**：操作 ID"操作 ID"的還原操作失敗。 代碼"授權失敗"，消息"用戶端"用戶端 ID"與物件識別碼 "objectid"沒有授權執行操作"Microsoft.Sql/位置/託管資料庫還原Azure同步操作/讀取"範圍 "/訂閱/訂閱 Id"。

| 原因         | 解決方案    |
| ------------- | ------------- |
| 此錯誤指示用於從 SQL Server 連線遷移到 Azure SQL 資料庫託管實例的應用程式主體對訂閱沒有貢獻許可權。 當前具有託管實例的某些 API 呼叫需要還原操作的訂閱許可權。 <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | 使用`Get-AzureADServicePrincipal`錯誤訊息中的 PowerShell Cmdlet`-ObjectId`列出正在使用的應用程式 ID 的顯示名稱。<br><br> 驗證對此應用程式的許可權，並確保它在訂閱級別具有[參與者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)。 <br><br> Azure 資料庫移轉服務工程團隊正在努力限制訂閱上當前貢獻角色所需的存取權限。 如果業務需求不允許使用貢獻角色，請與 Azure 支援聯繫以獲取其他説明。 |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>刪除與 Azure 資料庫移轉服務關聯的 NIC 時出錯

當您嘗試刪除與 Azure 資料庫移轉服務關聯的網路介面卡時，刪除嘗試將失敗，但出現此錯誤：

* **錯誤**： 由於使用 NIC 的 DMS 服務，無法刪除與 Azure 資料庫移轉服務關聯的 NIC

| 原因         | 解決方案    |
| ------------- | ------------- |
| 當 Azure 資料庫移轉服務實例可能仍然存在並消耗 NIC 時，將發生此問題。 <br><br><br><br><br><br><br><br> | 要刪除此 NIC，請刪除自動刪除服務使用的 NIC 的 DMS 服務實例。<br><br> **重要提示**：確保正在刪除的 Azure 資料庫移轉服務實例沒有正在運行的活動。<br><br> 刪除與 Azure 資料庫移轉服務實例關聯的所有專案和活動後，可以刪除服務實例。 服務實例使用的 NIC 作為服務刪除的一部分會自動清除。 |

## <a name="connection-error-when-using-expressroute"></a>使用 ExpressRoute 時的連線錯誤

當您嘗試在 Azure 資料庫移轉服務專案精靈中連線到來源時，如果來源使用 ExpressRoute 進行連線，則連線會在延長逾時之後發生失敗。

| 原因         | 解決方案    |
| ------------- | ------------- |
| 使用[ExpressRoute](https://azure.microsoft.com/services/expressroute/)時，Azure 資料庫移轉服務[需要](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online)預配與服務關聯的虛擬網路子網上的三個服務終結點：<br> -- 服務匯流排終結點<br> -- 存儲終結點<br> -- 目標資料庫終結點（例如 SQL 終結點、Cosmos DB 終結點）<br><br><br><br><br> | [啟用](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online)源和 Azure 資料庫移轉服務之間的 ExpressRoute 連接所需的服務終結點。 <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>將 MySQL 資料庫遷移到 Azure 資料庫以進行 MySQL 時鎖定等待逾時錯誤

當您通過 Azure 資料庫移轉服務將 MySQL 資料庫遷移到 MySQL 實例的 Azure 資料庫時，遷移將失敗，並出現以下鎖定等待逾時錯誤：

* **錯誤**： 資料庫移轉錯誤 - 載入檔失敗 - 無法開機檔案"n" RetCode 的載入進程：SQL_ERROR SqlState： HY000 本機錯誤： 1205 消息： [MySQL][ODBC 驅動程式][mysqld] 超過鎖定等待時間;嘗試重新開機事務

| 原因         | 解決方案    |
| ------------- | ------------- |
| 當遷移由於遷移期間的鎖等待超時而失敗時，將發生此錯誤。 | 請考慮增加伺服器參數 **"innodb_lock_wait_timeout"** 的值。 允許值最高為 1073741824。 |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>使用動態埠或命名實例時連接到源 SQL Server 時出錯

當您嘗試將 Azure 資料庫移轉服務連接到在命名實例或動態埠上運行的 SQL Server 源時，連接將失敗，出現此錯誤：

* **錯誤**： -1 - SQL 連接失敗。 和 SQL Server 建立連線時，發生與網路相關或執行個體特定的錯誤。 找不到或無法存取伺服器。 檢查執行個體名稱是否正確以及 SQL Server 執行個體是否設定為允許遠端連接。 (提供者：SQL 網路介面，錯誤：26 - 搜尋指定的伺服器/執行個體時發生錯誤)

| 原因         | 解決方案    |
| ------------- | ------------- |
| 當 Azure 資料庫移轉服務嘗試連接到具有動態埠的源 SQL Server 實例時，即表示此問題，或者正在使用命名實例。 SQL Server 瀏覽器服務偵聽 UDP 埠 1434 以訪問到命名實例的傳入連接或使用動態埠時。 每次 SQL Server 服務重新開機時，動態埠可能會更改。 您可以通過 SQL Server 組態管理員中的網路設定檢查分配給實例的動態埠。<br><br><br> |驗證 Azure 資料庫移轉服務是否可以通過動態分配的 TCP 埠連接到 UDP 埠 1434 上的源 SQL Server 瀏覽器服務，以及 SQL Server 實例（如適用）。 |

## <a name="additional-known-issues"></a>其他已知問題

* [連線遷移到 Azure SQL 資料庫的已知問題/遷移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [MySQL 的 Azure 資料庫連線遷移的已知問題/遷移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [連線遷移到 Azure 資料庫的已知問題/遷移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>後續步驟

* 查看文章[Azure 資料庫移轉服務 PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)。
* 查看文章["如何使用 Azure 門戶在 Azure 資料庫中為 MySQL 佈建服務器參數](https://docs.microsoft.com/azure/mysql/howto-server-parameters)"。
* 查看文章[概述使用 Azure 資料庫移轉服務的先決條件](https://docs.microsoft.com/azure/dms/pre-reqs)。
* 請參閱[有關使用 Azure 資料庫移轉服務的常見問題解答](https://docs.microsoft.com/azure/dms/faq)。
