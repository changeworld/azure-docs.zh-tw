---
title: 監視最佳作法-適用於 MySQL 的 Azure 資料庫
description: 本文說明監視適用於 MySQL 的 Azure 資料庫的最佳做法。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 59301e26f4d42056322ba5a7cdaff1555c531096
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96354955"
---
# <a name="best-practices-for-monitoring-azure-database-for-mysql--single-server"></a>監視適用於 MySQL 的 Azure 資料庫的最佳做法-單一伺服器

瞭解可用來監視資料庫作業的最佳做法，並確保當資料大小增加時，效能不會受到危害。 當我們將新功能新增至平臺時，我們會繼續調整本節中詳述的最佳作法。

## <a name="layout-of-the-current-monitoring-toolkit"></a>目前監視工具組的版面配置

適用於 MySQL 的 Azure 資料庫提供的工具和方法可讓您輕鬆地監視、新增或移除資源 (例如 CPU、記憶體或 i/o) 、疑難排解潛在問題，以及協助改善資料庫的效能。 您可以定期 [監視效能計量](concepts-monitoring.md#metrics) ，以查看各種時間範圍的平均值、最大值和最小值。

您可以 [設定](howto-alert-on-metric.md#create-an-alert-rule-on-a-metric-from-the-azure-portal) 計量臨界值的警示，如此一來，如果伺服器已達到這些限制並採取適當的動作，系統就會通知您。  

監視資料庫伺服器，以確保指派給資料庫的資源可以處理應用程式工作負載。 如果資料庫達到資源限制，請考慮下列事項：
    * 識別和優化最耗用資源的查詢。 
    * 藉由升級服務層級來新增更多資源。

### <a name="cpu-utilization"></a>CPU 使用率
監視 CPU 使用量，以及資料庫是否耗盡 CPU 資源。 如果 CPU 使用量為90% 或以上，您應該增加虛擬核心數目或調整為下一個定價層，以擴大計算規模。  當您向上/向下調整 CPU 時，請確定輸送量或平行存取如預期般運作。 

### <a name="memory"></a>Memory 
資料庫伺服器可用的記憶體數量與 [虛擬核心數目](concepts-pricing-tiers.md)成正比。 請確定記憶體足夠用於工作負載。 負載測試您的應用程式，以確認記憶體足以進行讀取和寫入作業。 如果資料庫記憶體耗用量經常成長超過所定義的臨界值，這表示您應該藉由增加虛擬核心或更高的效能層級來升級實例。 使用 [查詢存放區](concepts-query-store.md)、 [查詢效能建議](concepts-performance-recommendations.md) ，以找出持續時間最長的查詢（最長時間執行）。 探索優化的機會。 

### <a name="storage"></a>儲存體 
針對 MySQL 伺服器布建的 [儲存體數量](howto-create-manage-server-portal.md#scale-compute-and-storage) 會決定您伺服器的 IOPs。 服務所使用的儲存體包括資料庫檔案、交易記錄、伺服器記錄和備份快照集。 確定耗用的磁碟空間不會持續超過總布建磁碟空間的85%。 如果是這種情況，您需要從資料庫伺服器刪除或封存資料，以釋出一些空間。 

### <a name="network-traffic"></a>網路流量 

**網路接收輸送量、網路傳輸輸送量** –往返于 MySQL 實例的網路流量速率（以每秒 mb 數為單位）。 您必須評估伺服器的輸送量需求，並在輸送量低於預期時，持續監視流量。 

### <a name="database-connections"></a>資料庫連接 
[**資料庫連接**] –連線到適用於 MySQL 的 Azure 資料庫的用戶端會話數目應該與所 [選 SKU 大小的連接限制](concepts-server-parameters.md#max_connections)一致。 


## <a name="next-steps"></a>後續步驟

- [適用於 MySQL 的 Azure 資料庫效能的最佳作法](concept-performance-best-practices.md)
- [使用適用於 MySQL 的 Azure 資料庫的伺服器作業最佳做法](concept-operation-excellence-best-practices.md)
