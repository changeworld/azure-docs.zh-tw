---
title: Azure SQL 資料同步最佳做法
description: 了解設定及執行 Azure SQL 資料同步的最佳做法。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: a45fc5f4e56ff3a5d7f0be167c5d758aa0e47caf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84196361"
---
# <a name="best-practices-for-azure-sql-data-sync"></a>Azure SQL 資料同步最佳做法 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文描述 Azure SQL 資料同步的最佳做法。

如需 SQL 資料同步的概觀，請參閱[使用 Azure SQL 資料同步，跨多個雲端和內部部署資料庫同步處理資料](sql-data-sync-data-sql-server-sql-database.md)。

> [!IMPORTANT]
> Azure SQL 資料同步目前**不**支援 Azure SQL 受控執行個體。

## <a name="security-and-reliability"></a><a name="security-and-reliability"></a> 安全性與可靠性

### <a name="client-agent"></a>用戶端代理程式

-   使用具有網路服務存取權的最低權限使用者帳戶安裝用戶端代理程式。  
-   在不是 SQL Server 電腦的電腦上安裝用戶端代理程式。  
-   請勿向多個代理程式註冊內部部署資料庫。    
    -   即使是要同步處理相異同步群組的不同資料表，也請避免該行為。  
    -   如果您向多個用戶端代理程式註冊內部部署資料庫，當您刪除其中一個同步群組時將會帶來挑戰。

### <a name="database-accounts-with-least-required-privileges"></a>擁有最低必要權限的資料庫帳戶

-   **針對同步安裝**。 建立/更改資料表、更改資料庫、建立程序、選取/更改結構描述、建立使用者定義型別。

-   **進行中的同步**處理。選取/插入/更新/刪除選取要同步處理的資料表，以及同步處理中繼資料和追蹤資料表;服務所建立之預存程式的 Execute 許可權;使用者定義資料表類型的 Execute 許可權。

-   **針對取消佈建**。 在同步作業中針對資料表所做的更改、針對同步處理中繼資料資料表的選取/刪除、針對同步處理追蹤資料表、預存程序和使用者定義型別的控制。

Azure SQL Database 僅支援一組認證。 若要在此條件約束內完成這些工作，請考慮以下選項：

-   變更不同階段的認證 (例如，*credentials1* 適用於設定，*credentials2* 適用於持續)。  
-   變更認證的權限 (也就是在設定同步處理後變更權限)。

## <a name="setup"></a>安裝程式

### <a name="database-considerations-and-constraints"></a><a name="database-considerations-and-constraints"></a> 資料庫考量與限制

#### <a name="database-size"></a>資料庫大小

當您建立新的資料庫時，請設定大小上限，使其一律大於您部署的資料庫。 如果您未將大小上限設為大於已部署的資料庫，同步處理將會失敗。 雖然 SQL 資料同步不提供自動成長，不過，您可以在資料庫建立之後執行 `ALTER DATABASE` 命令來增加它的大小。 請確定您停留在資料庫大小限制內。

> [!IMPORTANT]
> SQL 資料同步會與每個資料庫一起儲存其他中繼資料。 在計算所需的空間時，請務必將這些中繼資料納入考量。 所增加的額外負荷量與資料表寬度 (例如，窄的資料表需要更多額外負荷) 和流量相關。

### <a name="table-considerations-and-constraints"></a><a name="table-considerations-and-constraints"></a> 資料表考量與限制

#### <a name="selecting-tables"></a>選取資料表

您不需要將所有資料表加入同步群組中的資料庫內。 加入同步群組的資料表會影響效率和成本。 請根據商務需求將資料表和與其相依的資料表加入同步群組即可。

#### <a name="primary-keys"></a>主索引鍵

同步群組中的每個資料表都必須有主索引鍵。 SQL 資料同步無法同步處理沒有主鍵的資料表。

在生產環境中使用 SQL 資料同步之前，請先測試初始和持續進行的同步處理效能。

#### <a name="empty-tables-provide-the-best-performance"></a>空資料表提供最佳效能

空資料表在初始化階段提供最佳效能。 如果目標資料表是空的，則資料同步會使用大量插入來載入資料。 否則，資料同步會逐列比較並插入以檢查是否有衝突。 但是，如果不考慮效能，您可以在已經包含資料的資料表之間設定同步。

### <a name="provisioning-destination-databases"></a><a name="provisioning-destination-databases"></a> 佈建目的地資料庫

SQL 資料同步提供基本資料庫自動佈建。

本節將討論 SQL 資料同步的佈建限制。

#### <a name="autoprovisioning-limitations"></a>自動佈建限制

「SQL 資料同步」的自動佈建限制如下：

-   只選取會在目的地資料表中建立的資料行。 所有不屬於同步群組的資料行，都不會佈建在目的地資料表中。
-   只會針對所選資料行建立索引。 如果來源資料表索引具有不是同步群組一部分的資料行，那些索引便不會佈建在目的地資料表中。  
-   不會佈建 XML 型別資料行的索引。  
-   不會佈建 CHECK 條件約束。  
-   不會佈建來源資料表上的現有觸發程序。  
-   不會在目的地資料庫上建立檢視和預存程序。
-   目的地資料表中不會針對外部索引鍵限制式重新建立 ON UPDATE CASCADE 和 ON DELETE CASCADE 動作。
-   如果您有有效位數大於28的十進位或數值資料行，SQL 資料同步可能會在同步處理期間遇到轉換溢位問題。我們建議您將 decimal 或 numeric 資料行的有效位數限制為28或更少。

#### <a name="recommendations"></a>建議

-   唯有當您試用服務時，才能使用 SQL 資料同步自動佈建功能。  
-   針對生產環境，佈建資料庫結構描述。

### <a name="where-to-locate-the-hub-database"></a><a name="locate-hub"></a> 要在哪裡尋找中樞資料庫

#### <a name="enterprise-to-cloud-scenario"></a>企業至雲端案例

若要將延遲降至最低，請讓中樞資料庫靠近最集中的同步群組資料庫流量。

#### <a name="cloud-to-cloud-scenario"></a>雲端至雲端案例

-   當同步群組中的所有資料庫都在一個資料中心時，中樞應該位於相同的資料中心。 此設定可減少延遲以及資料中心之間的資料傳輸成本。
-   同步群組中的資料庫在多個資料中心時，中樞應該位於與大多數的資料庫和資料庫流量相同的資料中心。

#### <a name="mixed-scenarios"></a>混合案例

請將前述指導方針套用在複雜的同步群組設定，例如混合企業到雲端和雲端到雲端等案例的設定。

## <a name="sync"></a>Sync

### <a name="avoid-slow-and-costly-initial-sync"></a><a name="avoid-a-slow-and-costly-initial-synchronization"></a> 避免慢速和高成本的首次同步處理

在本節中，我們會討論同步群組的首次同步處理。 了解如何預防首次同步處理花費過長的時間和不必要的成本。

#### <a name="how-initial-sync-works"></a>首次同步處理的運作方式

當您建立同步群組時，請從只有一個資料庫的資料開始。 如果您在多個資料庫中均有資料，則 SQL 資料同步會將每個資料列視為需要解決的衝突。 解決衝突會導致首次同步處理進展緩慢。 如果您有多個容納資料的資料庫，首次同步處理可能需要耗費幾天到幾個月的時間，視資料庫大小而定。

如果資料庫位在不同的資料中心內，每個資料列都必須在不同資料中心之間移動。 這會導致首次同步處理的成本增加。

#### <a name="recommendation"></a>建議

可以的話，請從某一個同步群組資料庫的資料開始。

### <a name="design-to-avoid-sync-loops"></a><a name="design-to-avoid-synchronization-loops"></a> 避免同步迴圈的設計

當同步群組中有循環參考時，就會形成同步迴圈。 在這種情況下，資料庫中的每項變更都會循環且無止盡地複寫到同步群組中的所有資料庫。   

請務必避免同步迴圈，因為其會導致效能降低，且可能會大幅增加成本。

### <a name="changes-that-fail-to-propagate"></a><a name="handling-changes-that-fail-to-propagate"></a> 無法傳播的變更

#### <a name="reasons-that-changes-fail-to-propagate"></a>未能傳播變更的原因

無法傳播變更的原因可能是以下其中之一：

-   結構描述/資料類型不相容。
-   在不可為 Null 的資料行中插入 Null。
-   違反外部索引鍵條件約束。

#### <a name="what-happens-when-changes-fail-to-propagate"></a>未能傳播變更時會發生什麼事？

-   同步群組顯示處於**警告**狀態。
-   詳細資料列示於入口網站 UI 記錄檢視器。
-   如果問題未解決達 45 天，資料庫會變成過期。

> [!NOTE]
> 這些變更無法傳播。 此時，唯一的復原方法是重新建立同步群組。

#### <a name="recommendation"></a>建議

透過入口網站和記錄介面，定期監視同步群組和資料庫健康狀態。


## <a name="maintenance"></a>維護

### <a name="avoid-out-of-date-databases-and-sync-groups"></a><a name="avoid-out-of-date-databases-and-sync-groups"></a> 避免資料庫和同步群組過期

同步群組或同步群組中的資料庫可能會過期。 當同步群組的狀態為**過期**時，其會停止運作。 當資料庫的狀態為**過期**時，可能會遺失資料。 建議您最好避免這種情況發生，而不是嘗試從中復原。

#### <a name="avoid-out-of-date-databases"></a>避免資料庫過期

資料庫已離線 45 天以上時，狀態會設定為**過期**。 若要避免資料庫變成**過期**狀態，請確認沒有任何資料庫離線 45 天以上。

#### <a name="avoid-out-of-date-sync-groups"></a>避免同步群組過期

當同步群組內的任何變更無法傳播到其餘同步群組長達 45 天以上時，狀態會設定為**過期**。 若要避免同步群組變為**過期**狀態，請定期檢查同步群組的歷程記錄。 請確定所有衝突都已解決，且變更已成功傳播到整個同步群組資料庫。

同步群組可能會無法套用變更，原因包括以下其中之一：

-   資料表之間的結構描述不相容。
-   資料表之間的資料不相容。
-   在不允許 Null 值的資料行中插入具有 Null 值的資料列。
-   使用違反外部索引鍵條件約束的值更新資料列。

若要避免同步群組過期：

-   更新結構描述，以允許失敗資料列中包含的值。
-   更新外部索引鍵值，以包含失敗資料列內含的值。
-   更新失敗資料列中的資料值，以和目標資料庫中的結構描述或外部索引鍵相容。

### <a name="avoid-deprovisioning-issues"></a><a name="avoid-deprovisioning-issues"></a>避免取消佈建問題

在某些情況下，向用戶端代理程式取消註冊資料庫可能會導致同步處理失敗。

#### <a name="scenario"></a>狀況

1. 同步群組 A 是使用 SQL Database 實例和與本機代理程式1相關聯的 SQL Server 資料庫所建立。
2. 相同的內部部署資料庫已向本機代理程式 2 註冊 (此代理程式並未與任何同步群組建立關聯)。
3. 從本機代理程式 2 取消註冊內部部署資料庫，會移除內部部署資料庫之同步群組 A 的追蹤和中繼資料表。
4. 同步群組 A 作業失敗，並發生下列錯誤：「目前的作業無法完成，因為資料庫未佈建以進行同步處理，或是您沒有同步設定資料表的權限。」

#### <a name="solution"></a>解決方案

若要避免這種情況發生，請勿向一個以上的代理程式註冊資料庫。

若要從這種情況中復原，請：

1. 從資料庫所屬的每個同步群組移除資料庫。  
2. 將資料庫新增回剛才移除資料庫的每個同步群組。  
3. 部署每個受影響的同步群組 (此動作會佈建資料庫)。  

### <a name="modifying-a-sync-group"></a><a name="modifying-your-sync-group"></a> 修改同步群組

請勿嘗試從同步群組移除資料庫，然後編輯同步群組而不先部署其中一項變更。

相反地，請先從同步群組移除資料庫。 然後部署變更，並等候取消佈建完成。 取消佈建完成時，您可以編輯同步群組及部署變更。

如果您嘗試移除資料庫，然後編輯同步群組而不先部署其中一項變更，則其中一項作業會失敗。 入口網站介面也可能會呈現不一致的狀態。 發生這種情況時，您可以重新整理頁面以還原正確狀態。

### <a name="avoid-schema-refresh-timeout"></a>避免架構重新整理超時

如果您有要同步處理的複雜架構，如果同步中繼資料資料庫的 SKU 較低（例如：基本），您可能會在架構重新整理期間遇到「作業超時」。 

#### <a name="solution"></a>解決方案

若要減少此問題，請將您的同步中繼資料資料庫相應增加為具有較高的 SKU，例如 S3。 

## <a name="next-steps"></a>後續步驟
如需有關 SQL 資料同步的詳細資訊，請參閱：

-   概觀 - [使用 Azure SQL 資料同步，跨多個雲端和內部部署資料庫同步資料](sql-data-sync-data-sql-server-sql-database.md)
-   設定 SQL 資料同步
    - 在入口網站中-[教學課程：設定 SQL 資料同步在 Azure SQL Database 和 SQL Server 之間同步處理資料](sql-data-sync-sql-server-configure.md)
    - 透過 PowerShell
        -  [使用 PowerShell 在 Azure SQL Database 中的多個資料庫之間進行同步處理](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [使用 PowerShell 在 SQL Database 中的資料庫和 SQL Server 實例中的資料庫之間進行同步處理](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Data Sync Agent - [適用於 Azure SQL Data Sync 的 Data Sync Agent](sql-data-sync-agent-overview.md)
-   監視 - [使用 Azure 監視器記錄監視 SQL 資料同步](sql-data-sync-monitor-sync.md)
-   疑難排解 - [為 Azure SQL 資料同步的問題進行疑難排解](sql-data-sync-troubleshoot.md)
-   更新同步結構描述
    -   使用 Transact-SQL - [在 Azure SQL 資料同步中自動執行結構描述變更複寫](sql-data-sync-update-sync-schema.md)
    -   使用 PowerShell - [使用 PowerShell 更新現有同步群組中的同步結構描述](scripts/update-sync-schema-in-sync-group.md)

如需有關 SQL Database 的詳細資訊，請參閱：

-   [SQL Database 概觀](sql-database-paas-overview.md)
-   [資料庫生命週期管理](https://msdn.microsoft.com/library/jj907294.aspx)
