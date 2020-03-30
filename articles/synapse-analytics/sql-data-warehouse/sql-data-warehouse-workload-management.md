---
title: 工作負載管理
description: 在 Azure 同步分析中實現工作負載管理的指導。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 1809902fb153c5c9c83a9d00b6f817fde975d0fe
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349904"
---
# <a name="what-is-workload-management"></a>什麼是工作負載管理？

運行混合工作負載可能會給繁忙的系統帶來資源挑戰。  解決方案架構師尋求分離經典資料倉儲活動（如載入、轉換和查詢資料）的方法，以確保有足夠的資源來處理 SL。  

物理伺服器隔離可能導致部分基礎設施未充分利用、過度預訂或緩存始終在硬體啟動和停止的情況下進行準備。  成功的工作負載管理方案可有效管理資源，確保高效的資源利用率，並最大限度地提高投資回報 （ROI）。

資料倉儲工作負載是指與資料倉儲相關的所有操作。 這些元件的深度和廣度取決於資料倉儲的成熟度級別。  資料倉儲工作負載包括： 
- 將資料載入到倉庫的整個過程 
- 執行資料倉儲分析和報告
- 管理資料倉儲中的資料 
- 從資料倉儲匯出資料

資料倉儲的效能處理能力取決於[資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md)。
- 要查看為所有性能設定檔分配的資源，請參閱[記憶體和併發限制](memory-concurrency-limits.md)。
- 要調整容量，可以[向上或向下擴展](quickstart-scale-compute-portal.md)。


## <a name="workload-management-concepts"></a>工作負載管理概念
過去，對於 Azure Synaps 中的 SQL 分析，您可以通過[資源類](resource-classes-for-workload-management.md)管理查詢性能。  允許根據角色成員身份將記憶體分配給查詢的資源類。  資源類面臨的主要挑戰是，一旦配置，就不存在控制工作負載的治理或能力。  

例如，向 Smallrc 授予臨時使用者角色成員身份允許該使用者消耗系統上 100% 的記憶體。  使用資源類時，無法保留並確保資源可用於關鍵工作負載。

Azure Synapse 中的 SQL 分析工作負載管理由三個高級概念組成：[工作負載分類](sql-data-warehouse-workload-classification.md)、[工作負載重要性](sql-data-warehouse-workload-importance.md)和[工作負載隔離](sql-data-warehouse-workload-isolation.md)。  這些功能使您能夠更控制系統工作負載如何利用系統資源。

工作負載分類是將請求分配給工作負載組並設置重要性級別的概念。  從歷史上看，此任務是通過使用[sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class)的角色成員身份來完成的。  這現在可以通過[創造任務程式](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)來完成。  分類功能提供了一組更豐富的選項，如標籤、會話和對請求進行分類的時間。

工作負載重要性會影響請求訪問資源的順序。  在繁忙的系統上，具有更高重要性的請求可以首先訪問資源。  重要性還可以確保對鎖的有序訪問。 

工作負載隔離為工作負載組保留資源。  工作負載組中保留的資源僅保留該工作負載組，以確保執行。  工作負載組還允許您定義每個請求分配的資源量，就像資源類一樣。  工作負載組使您能夠保留或限制一組請求可以使用的資源量。  最後，工作負載組是一種將規則（如查詢超時）應用於請求的機制。  


## <a name="next-steps"></a>後續步驟

- 有關工作負載分類的詳細資訊，請參閱[工作負載分類](sql-data-warehouse-workload-classification.md)。  
- 有關工作負載隔離的詳細資訊，請參閱[工作負載隔離](sql-data-warehouse-workload-isolation.md)。  
- 有關工作負載重要性的詳細資訊，請參閱[工作負載重要性](sql-data-warehouse-workload-importance.md)。  
- 有關工作負載管理監視的詳細資訊，請參閱[工作負載監管中心監視](sql-data-warehouse-workload-management-portal-monitor.md)。  
