---
title: 針對 SQL 資料同步進行疑難排解
description: 了解如何對使用 Azure SQL 資料同步的常見問題進行疑難排解。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: d6ea604446cb9d56bb699685d24c81992bcac3a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81382900"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>針對 SQL 資料同步的問題進行疑難排解

本文說明如何針對 Azure SQL 資料同步的已知問題進行疑難排解。如果有問題的解決方法，請在這裡提供。

如需 SQL 資料同步的概觀，請參閱[使用 Azure SQL 資料同步，跨多個雲端和內部部署資料庫同步處理資料](sql-database-sync-data.md)。

> [!IMPORTANT]
> Azure SQL 資料同步目前**不**支援 Azure SQL Database 受控執行個體。

## <a name="sync-issues"></a>同步問題

- [與用戶端代理程式相關聯之內部部署資料庫的入口網站 UI 同步處理失敗](#sync-fails)

- [我的同步群組停滯在處理狀態](#sync-stuck)

- [我在資料表中看到錯誤的資料](#sync-baddata)

- [我在同步成功之後看到不一致的主索引鍵資料](#sync-pkdata)

- [我看到效能顯著降低](#sync-perf)

- [我看到此訊息：「無法將 Null 值插入資料行\<資料行>。資料行不允許 null。」這是什麼意思，以及如何修正此問題？](#sync-nulls)

- [資料同步如何處理迴圈參考？也就是在多個同步處理群組中同步處理相同的資料，並持續變更結果？](#sync-circ)

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a><a name="sync-fails"></a> 與用戶端代理程式建立關聯的內部部署資料庫的入口網站 UI 同步失敗

在 SQL 資料同步入口網站 UI 上，與用戶端代理程式相關聯之內部部署資料庫的同步失敗。 在執行代理程式的本機電腦上，您會在事件記錄檔看到 System.IO.IOException 錯誤。 錯誤說明磁碟空間不足。

- **原因**。 磁碟機沒有足夠的空間。

- **解決**方式。 在 %TEMP% 目錄所在的磁碟機上建立更多空間。

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a><a name="sync-stuck"></a> 我的同步群組停頓於處理中狀態

SQL 資料同步中的同步群組已處於處理中狀態一段時間。 它不會回應**停止**命令，而記錄中未顯示新的項目。

下列任何一個狀況都可能導致同步群組停頓於處理中狀態：

- **原因**。 用戶端代理程式已離線

- **解決**方式。 確定用戶端代理程式已上線，然後再試一次。

- **原因**。 用戶端代理程式已解除安裝或遺失。

- **解決**方式。 如果用戶端代理程式已解除安裝或以別的方式遺失：

    1. 如果檔案存在，請從 SQL 資料同步安裝資料夾中移除代理程式的 XML 檔案。
    1. 在內部部署電腦上安裝代理程式 (可在相同或不同電腦上)。 然後提交在入口網站中為顯示為離線之代理程式所產生的代理程式金鑰。

- **原因**。 SQL 資料同步服務已停止。

- **解決**方式。 重新啟動 SQL 資料同步服務。

    1. 在 [**開始**] 功能表中，搜尋 [**服務**]。
    1. 在搜尋結果中，選取 [**服務**]。
    1. 尋找**SQL 資料同步**服務。
    1. 如果服務狀態是 [**已停止**]，請以滑鼠右鍵按一下服務名稱，然後選取 [**啟動**]。

> [!NOTE]
> 如果上述資訊未讓您的同步群組脫離處理中狀態，Microsoft 支援人員可以重設您的同步群組的狀態。 若要讓您同步群組的狀態重設，請在 [Azure SQL Database 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)中建立文章。 在文章中，新增您的訂用帳戶識別碼和需要重設之群組的同步群組識別碼。 Microsoft 支援工程師會回應您的文章，並在已重設狀態時告訴您。

### <a name="i-see-erroneous-data-in-my-tables"></a><a name="sync-baddata"></a> 我在資料表中看到錯誤的資料

如果資料表有相同的名稱，但在同步中併入了來自不同資料庫結構描述，在同步之後，您會在資料表中看到發生錯誤的資料。

- **原因**。 SQL 資料同步佈建程序對於具有相同名稱但位在不同結構描述的資料表會使用相同的追蹤資料表。 因此，來自這兩個資料表的變更會反映在相同的追蹤資料表。 這會導致在同步期間產生錯誤的資料變更。

- **解決**方式。 確定同步牽涉的資料表名稱彼此不同，即使資料裝屬於資料庫中的不同結構描述。

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a><a name="sync-pkdata"></a>我在同步處理成功之後看到不一致的主鍵資料

同步報告為成功，且記錄檔顯示沒有任何失敗或略過的資料列，但您發現主索引鍵資料在同步群組中的資料庫之間不一致。

- **原因**。 此結果是原先的設計。 任何主索引鍵資料行中的變更都會導致主索引鍵已變更的資料列中資料不一致。

- **解決**方式。 若要避免這個問題，請確認主要索引鍵資料行中的資料都未變更。 在發生問題之後，若要修正此問題，請從同步群組中的所有端點刪除具有不一致資料的資料列。 然後，重新插入資料列。

### <a name="i-see-a-significant-degradation-in-performance"></a><a name="sync-perf"></a> 我看到效能顯著降低

您的效能大幅降低，可能會達到甚至無法開啟資料同步 UI 的程度。

- **原因**。 最可能的原因是同步迴圈。 當同步群組 A 的同步觸發同步群組 B 的同步，然後觸發同步群組 A 的同步，即發生同步迴圈。實際情況可能更為複雜，而且它也可能在迴圈中牽涉到兩個以上的同步群組。 問題在於同步有因同步群組彼此重疊造成的循環觸發。

- **解決**方式。 最佳的修正方式就是預防。 確保您的同步群組中沒有循環參考。 由某個同步群組同步的任何資料列不能同步至另一個同步群組。

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a><a name="sync-nulls"></a>我看到此訊息：「無法將 Null 值插入資料行\<資料行>。 資料行不允許 Null。」 這是什麼意思，該如何修正？ 
此錯誤訊息表示發生了下列兩種問題的其中之一：
-  資料表沒有主索引鍵。 若要修正此問題，請將主索引鍵新增至要同步的所有資料表。
-  在 CREATE INDEX 陳述式中可能有 WHERE 子句。 資料同步不會處理這個狀況。 若要修正此問題，請移除 WHERE 子句或手動變更所有資料庫。 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a><a name="sync-circ"></a> 資料同步如何處理循環參考？ 也就是，相同的資料已在多個同步群組中同步，且因此持續變更？
資料同步不會處理迴圈參考。 請務必避免。 

## <a name="client-agent-issues"></a>用戶端代理程式問題

若要針對用戶端代理程式的問題進行疑難排解，請參閱 [Data Sync Agent 問題疑難排解](sql-database-data-sync-agent.md#agent-tshoot)。

## <a name="setup-and-maintenance-issues"></a>設定和維護問題

- [我收到「磁碟空間不足」的訊息](#setup-space)

- [我無法刪除我的同步群組](#setup-delete)

- [我無法取消註冊內部部署 SQL Server 資料庫](#setup-unreg)

- [我沒有足夠的權限可以啟動系統服務](#setup-perms)

- [資料庫具有「過期」狀態](#setup-date)

- [同步群組具有「過期」狀態](#setup-date2)

- [卸載或停止代理程式三分鐘內無法刪除同步群組](#setup-delete2)

- [當我還原遺失或損毀的資料庫時，會發生什麼情況？](#setup-restore)

### <a name="i-get-a-disk-out-of-space-message"></a><a name="setup-space"></a>我收到「磁碟空間不足」的訊息

- **原因**。 如果必須刪除剩餘的檔案，可能會出現「磁碟空間不足」訊息。 這可能是因為防毒軟體，或是嘗試進行刪除作業時檔案開啟。

- **解決**方式。 手動刪除 %temp% 資料夾 (`del \*sync\* /s`) 中的同步檔案。 然後，刪除 %temp% 資料夾中的子目錄。

> [!IMPORTANT]
> 請勿於同步正在進行時刪除任何檔案。

### <a name="i-cant-delete-my-sync-group"></a><a name="setup-delete"></a> 我無法刪除我的同步群組

您嘗試刪除同步群組失敗。 任何下列的情節可能會導致無法刪除同步群組：

- **原因**。 用戶端代理程式已離線。

- **解決**方式。 確保用戶端代理程式已上線，然後再試一次。

- **原因**。 用戶端代理程式已解除安裝或遺失。

- **解決**方式。 如果用戶端代理程式已解除安裝或以別的方式遺失：  
    a. 如果檔案存在，請從 SQL 資料同步安裝資料夾中移除代理程式的 XML 檔案。  
    b. 在內部部署電腦上安裝代理程式 (可在相同或不同電腦上)。 然後提交在入口網站中為顯示為離線之代理程式所產生的代理程式金鑰。

- **原因**。 資料庫已離線。

- **解決**方式。 確保您的 SQL Database 和 SQL Server 資料庫都在線上。

- **原因**。 同步群組正在佈建或同步。

- **解決**方式。 等候佈建或同步程序完成，然後重試刪除同步群組。

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a><a name="setup-unreg"></a> 我無法取消註冊內部部署 SQL Server 資料庫

- **原因**。 最有可能的原因是您正在嘗試取消註冊已刪除的資料庫。

- **解決**方式。 若要取消註冊內部部署 SQL Server 資料庫，請選取資料庫，然後按一下 [強制刪除]****。

  如果這項作業無法從同步群組移除資料庫：

  1. 停止然後再重新啟動用戶端代理程式主機服務：  
    a. 選取 [開始]**** 功能表。  
    b. 在搜尋方塊中輸入 **services.msc**。  
    c. 在搜尋結果窗格的 [程式]**** 區段中按兩下 [服務]****。  
    d. 以滑鼠右鍵按一下 [SQL 資料同步]**** 服務。  
    e. 如果服務正在執行，請將它停止。  
    f. 以滑鼠右鍵按一下服務，然後選取 [啟動]****。  
    g. 檢查資料庫是否仍註冊。 如果它不再為已註冊，則作業完成。 否則，請繼續下一個步驟。
  1. 開啟用戶端代理程式應用程式 (SqlAzureDataSyncAgent)。
  1. 選取 [編輯認證]****，然後輸入資料庫的認證。
  1. 繼續進行取消註冊。

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a><a name="setup-perms"></a>我沒有足夠的許可權可以啟動系統服務

- **原因**。 有兩種情況會發生此錯誤：
  -   使用者名稱及/或密碼不正確。
  -   指定的使用者帳戶沒有足夠的權限，無法以服務方式登入。

- **解決**方式。 授與以服務方式登入的服務認證給使用者帳戶：

  1.  > 前往 [ ** ****控制台** > ] [系統**管理工具** > ] [**本機安全性原則** > ] [**本機原則** > ] [**使用者 Rights Management**]。
  1. 選取 [以服務方式登入]****。
  1. 在 [內容]**** 對話方塊中，新增使用者帳戶。
  1. 選取 [套用]  ，然後選取 [確定]  。
  1. 關閉所有視窗。

### <a name="a-database-has-an-out-of-date-status"></a><a name="setup-date"></a>資料庫具有「過期」狀態

- **原因**。 SQL 資料同步會從服務移除已離線 45 天以上 (從資料庫離線的時開始計算) 的資料庫。 如果資料庫離線 45 天以上，然後再恢復上線，其狀態是**過期**。

- **解決**方式。 您可以藉由確定沒有任何資料庫離線 45 天以上來避免**過期**狀態。

  如果資料庫的狀態是**過期**：

  1. 從同步群組的移除具有**過期**狀態的資料庫。
  1. 將資料庫新增回到同步群組。

  > [!WARNING]
  > 您遺失此資料庫離線時對它的所有變更。

### <a name="a-sync-group-has-an-out-of-date-status"></a><a name="setup-date2"></a> 同步群組具有「過期」的狀態

- **原因**。 如果一或多個變更無法套用於 45 天的整個保留期限，同步群組可能會過期。

- **解決**方式。 若要避免同步群組的**過期**狀態，請定期在歷程記錄檢視器中檢查同步工作的結果。 調查並解決無法套用的任何變更。

  如果同步群組的狀態為**過期**，請刪除同步群組，然後重新建立。

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a><a name="setup-delete2"></a> 解除安裝或停止代理程式三分鐘內無法刪除同步群組

您無法在解除安裝或停止相關聯之 SQL 資料同步用戶端代理程式的三分鐘內，刪除同步群組。

- **解決**方式。

  1. 在相關聯的同步處理代理程式為線上時移除同步群組 (建議選項)。
  1. 如果代理程式為離線但已安裝，請讓它在內部部署電腦上連線。 等候代理程式在 SQL 資料同步入口網站顯示為**線上**狀態。 然後，移除同步群組。
  1. 如果代理程式因為已解除安裝而離線：  
    a.  如果檔案存在，請從 SQL 資料同步安裝資料夾中移除代理程式的 XML 檔案。  
    b.  在內部部署電腦上安裝代理程式 (可在相同或不同電腦上)。 然後提交在入口網站中為顯示為離線之代理程式所產生的代理程式金鑰。  
    c. 嘗試刪除同步群組。

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a><a name="setup-restore"></a>當我還原遺失或損毀的資料庫時，會發生什麼事？

如果您從備份還原遺失或損毀的資料庫，資料庫所屬的同步群組中可能有無法聚合的資料。

## <a name="next-steps"></a>後續步驟
如需有關 SQL 資料同步的詳細資訊，請參閱：

-   概觀 - [使用 Azure SQL 資料同步，跨多個雲端和內部部署資料庫同步資料](sql-database-sync-data.md)
-   設定資料同步
    - 在入口網站中 - [教學課程：設定 SQL 資料同步以同步處理 Azure SQL Database 與內部部署 SQL Server 之間的資料](sql-database-get-started-sql-data-sync.md)
    - 透過 PowerShell
        -  [使用 PowerShell 在多個 Azure SQL 資料庫之間進行同步處理](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [使用 PowerShell 設定「資料同步」在內部部署的 Azure SQL Database 和 SQL Server 之間進行同步處理](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Data Sync Agent - [適用於 Azure SQL Data Sync 的 Data Sync Agent](sql-database-data-sync-agent.md)
-   最佳做法 - [Azure SQL 資料同步最佳做法](sql-database-best-practices-data-sync.md)
-   監視 - [使用 Azure 監視器記錄監視 SQL 資料同步](sql-database-sync-monitor-oms.md)
-   更新同步結構描述
    -   使用 Transact-SQL - [在 Azure SQL 資料同步中自動執行結構描述變更複寫](sql-database-update-sync-schema.md)
    -   使用 PowerShell - [使用 PowerShell 更新現有同步群組中的同步結構描述](scripts/sql-database-sync-update-schema.md)

如需有關 SQL Database 的詳細資訊，請參閱：

-   [SQL Database 總覽](sql-database-technical-overview.md)
-   [資料庫生命週期管理](https://msdn.microsoft.com/library/jj907294.aspx)
