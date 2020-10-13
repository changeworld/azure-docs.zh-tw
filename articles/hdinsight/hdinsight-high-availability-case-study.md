---
title: Azure HDInsight 高可用性解決方案架構案例研究
description: 本文是虛構的案例研究，可能 Azure HDInsight 高可用性解決方案架構。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: hadoop 高可用性
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 4b98b03c2d7eb4a0403b4595c1376656ed42511b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91855033"
---
# <a name="azure-hdinsight-highly-available-solution-architecture-case-study"></a>Azure HDInsight 高可用性解決方案架構案例研究

Azure HDInsight 的複寫機制可以整合到高可用性的解決方案架構中。 在本文中，Contoso Retail 的虛構案例研究用來說明可能的高可用性嚴重損壞修復方法、成本考慮，以及其對應的設計。

高可用性嚴重損壞修復建議可以有許多排列和組合。 這些解決方案會在 deliberating 每個選項的優缺點之後抵達。 本文只討論一個可能的解決方案。

## <a name="customer-architecture"></a>客戶架構

下圖描述 Contoso 零售主要架構。 此架構包含串流工作負載、批次工作負載、服務層、耗用量層、儲存層和版本控制。

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-architecture.png" alt-text="Contoso 零售架構":::

### <a name="streaming-workload"></a>串流工作負載

裝置和感應器會產生資料給 HDInsight Kafka，其構成訊息架構。 HDInsight Spark 取用者會從 Kafka 主題中讀取。 Spark 會轉換傳入的訊息，並將其寫入服務層上的 HDInsight HBase 叢集。

### <a name="batch-workload"></a>Batch 工作負載

執行 Hive 和 MapReduce 的 HDInsight Hadoop 叢集會從內部部署的交易系統內嵌資料。 Hive 和 MapReduce 轉換的原始資料會儲存在 Azure Data Lake Storage Gen2 之 data lake 的邏輯分割區上的 Hive 資料表中。 儲存在 Hive 資料表中的資料也可以提供給 Spark SQL，這會在將策劃資料儲存在 HBase 以提供服務之前進行批次轉換。

### <a name="serving-layer"></a>服務層

具有 Apache Phoenix 的 HDInsight HBase 叢集用來提供資料給 web 應用程式和視覺效果儀表板。 HDInsight LLAP 叢集可用來滿足內部報告需求。

### <a name="consumption-layer"></a>耗用量層

Azure API Apps 和 API 管理層會傳回公開的網頁。 Power BI 會履行內部報告需求。

### <a name="storage-layer"></a>儲存層

以邏輯方式分割 Azure Data Lake Storage Gen2 可作為企業 Data Lake。 HDInsight 中繼存放區由 Azure SQL DB 支援。

### <a name="version-control-system"></a>版本控制系統

整合至 Azure Pipelines 並裝載于 Azure 外部的版本控制系統。

## <a name="customer-business-continuity-requirements"></a>客戶的商務持續性需求

如果發生嚴重損壞，請務必判斷您所需的最基本商務功能。

### <a name="contoso-retails-business-continuity-requirements"></a>Contoso 零售的商務持續性需求

* 我們必須針對區域失敗或區域服務健康情況問題進行保護。
* 我的客戶絕對不能看到404錯誤。 必須一律提供公用內容。  (RTO = 0)   
* 在年度大部分的情況下，我們可以顯示已過時5小時的公開內容。  (RPO = 5 小時) 
* 在假日季節，我們公開的內容必須永遠保持在最新狀態。  (RPO = 0) 
* 我的內部報告需求不會被視為對商務持續性的關鍵。
* 將商務持續性成本優化。

## <a name="proposed-solution"></a>建議的解決方案

下圖顯示 Contoso 零售的高可用性嚴重損壞修復架構。

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-solution.png" alt-text="Contoso 零售架構":::

**Kafka** 使用 [主動–被動](hdinsight-business-continuity-architecture.md#apache-kafka) 複寫，將 Kafka 主題從主要區域鏡像至次要區域。 Kafka 複寫的替代方案可能是在這兩個區域中產生 Kafka。

**Hive 和 Spark** 在正常情況下會使用作用中 [的主要-隨選次要](hdinsight-business-continuity-architecture.md#apache-spark) 複寫模型。 Hive 複寫進程會定期執行，並隨附于 Hive Azure SQL 中繼存放區和 Hive 儲存體帳戶複寫。 Spark 儲存體帳戶會使用 ADF DistCP 定期進行複寫。 這些叢集的暫時性本質有助於將成本優化。 系統會每隔4小時排程複寫一次，以達到五小時需求內的 RPO。

**HBase** 複寫會在正常時間使用「 [領導者](hdinsight-business-continuity-architecture.md#apache-hbase) 」的「建立者」模型，以確保不論區域為何，資料一律會提供服務，而 RPO 為零。

如果主要區域發生區域失敗，則會從次要區域提供網頁和後端內容一段時間的過期時間。 如果 Azure 服務健康狀態儀表板未在五小時的時間範圍內表示復原 ETA，Contoso Retail 將會在次要區域中建立 Hive 和 Spark 轉換層，然後將所有上游資料來源指向次要區域。 將次要區域設為可寫入，會導致涉及複寫回主要的容錯回復程式。

在尖峰購物季節期間，整個次要管線一律為使用中且正在執行。 Kafka 產生器會同時產生兩個區域，而 HBase 複寫會從 Leader-Follower 變更為 Leader-Leader，以確保公開內容一律保持最新狀態。

不需要針對內部報表設計任何容錯移轉解決方案，因為它對商務持續性並不重要。

## <a name="next-steps"></a>後續步驟

若要深入瞭解本文中討論的專案，請參閱：

* [Azure HDInsight 商務持續性](./hdinsight-business-continuity.md)
* [Azure HDInsight 商務持續性架構](./hdinsight-business-continuity-architecture.md)
* [Azure HDInsight 上的 Apache Hive 和 HiveQL 是什麼？](./hadoop/hdinsight-use-hive.md)