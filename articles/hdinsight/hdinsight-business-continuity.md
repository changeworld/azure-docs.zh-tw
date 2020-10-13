---
title: Azure HDInsight 商務持續性
description: 本文概述 Azure HDInsight 商務持續性規劃的最佳作法、單一區域可用性和優化選項。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: hadoop 高可用性
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: beb3c54a0ab7f6f063232a1ad49744d99746c589
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893640"
---
# <a name="azure-hdinsight-business-continuity"></a>Azure HDInsight 商務持續性

Azure HDInsight 叢集依賴許多 Azure 服務，例如儲存體、資料庫、Active Directory、Active Directory Domain Services、網路和 Key Vault。 設計完善、高度可用且容錯的分析應用程式，應該要有足夠的空間，才能在其中一或多項服務中承受區域或本機中斷。 本文概述商務持續性計畫的最佳作法、單一區域可用性和優化選項。

## <a name="general-best-practices"></a>一般最佳作法

本節討論在商務持續性計畫期間，您要考慮的一些最佳作法。

* 如果發生嚴重損壞和原因，請判斷您需要的最基本商務功能。 例如，評估您是否需要資料轉換層的容錯移轉功能 (以黃色) *和* 資料服務層 (顯示在藍色) 中，或者，如果您只需要資料服務層的容錯移轉。

   :::image type="content" source="media/hdinsight-business-continuity/data-layers.png" alt-text="資料轉換和資料服務層級":::

* 根據工作負載、開發生命週期和部門來分割您的叢集。 擁有更多叢集可減少單一大型失敗的機率，進而影響多個不同的商務處理常式。

* 將您的次要區域設為唯讀。 具有讀取和寫入功能的容錯移轉區域可能會導致複雜的架構。

* 暫時性叢集在發生嚴重損壞時更容易管理。 以可迴圈的方式設計您的工作負載，而不會在叢集中維護狀態。

* 如果發生嚴重損壞，且需要在新區域中重新開機，則通常工作負載會保持未完成。 設計您的工作負載，使其本質上具有等冪性。

* 在叢集部署期間使用自動化，並確保將叢集設定設定為最高的腳本，以確保在發生嚴重損壞時，能快速且完全自動化的部署。

* 使用 HDInsight 上的 Azure 監視工具來偵測叢集中的異常行為，並設定對應的警示通知。 您可以部署預先設定的 HDInsight 叢集特定的管理解決方案，以收集特定叢集類型的重要效能度量。 如需詳細資訊，請參閱 [適用于 HDInsight 的 Azure 監視器](./hdinsight-hadoop-oms-log-analytics-tutorial.md)。  

* 訂閱 Azure 健康狀態警示，以通知有關訂用帳戶、服務或區域的服務問題、預定的維護、健康情況和資訊安全諮詢。 包含問題原因和 resolute ETA 的健康情況通知，可協助您更妥善地執行容錯移轉和容錯回復。 如需詳細資訊，請參閱 [Azure 服務健康狀態檔](/azure/service-health/)。

## <a name="single-region-availability"></a>單一區域可用性

基本的 HDInsight 系統具有下列元件。 所有元件都有自己的單一區域容錯機制。

* 計算 (虛擬機器) ： Azure HDInsight 叢集
* 中繼存放區 (s) ： Azure SQL Database
* 儲存體： Azure Data Lake Gen2 或 Blob 儲存體
* 驗證： Azure Active Directory、Azure Active Directory Domain Services、企業安全性套件
* 功能變數名稱解析： Azure DNS

您可以使用其他選擇性服務，例如 Azure Key Vault 和 Azure Data Factory。

:::image type="content" source="media/hdinsight-business-continuity/hdinsight-components.png" alt-text="資料轉換和資料服務層級":::

### <a name="azure-hdinsight-cluster-compute"></a>Azure HDInsight 叢集 (計算) 

HDInsight 提供99.9% 的可用性 SLA。 為了在單一部署中提供高可用性，HDInsight 預設會伴隨許多處於高可用性模式的服務。 HDInsight 中的容錯機制是由 Microsoft 和 Apache OSS 生態系統高可用性服務所提供。

下列服務是設計成高度可用的服務：

#### <a name="infrastructure"></a>基礎結構

* 主動和待命前端節點
* 多個閘道節點
* 三個 Zookeeper 仲裁節點
* 由容錯網域和更新網域散發的背景工作節點

#### <a name="service"></a>服務

* Apache Ambari 伺服器
* 適用于 YARN 的應用程式時間軸
* Hadoop MapReduce 的作業歷程記錄伺服器
* Apache Livy
* HDFS
* YARN Resource Manager
* HBase Master

若要深入瞭解，請參閱 [Azure HDInsight 所支援之高可用性服務](./hdinsight-high-availability-components.md) 的相關檔。

它不一定會採取重大事件來影響商務功能。 在單一區域中一或多個下列服務中的服務事件，也可能導致預期的商務功能遺失。

### <a name="hdinsight-metastore"></a>HDInsight 中繼存放區

HDInsight 會使用 [Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/) 作為中繼存放區，提供99.99% 的 SLA。 有三個數據複本保存在具有同步複寫的資料中心內。 如果遺失複本，則會順暢地提供替代複本。 預設支援[主動式異地](../azure-sql/database/active-geo-replication-overview.md)複寫，最多可有四個資料中心。 如果有容錯移轉（手動或資料中心），階層中的第一個複本將會自動變成可讀寫功能。 如需詳細資訊，請參閱 [Azure SQL Database 商務持續性](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md)。

### <a name="hdinsight-storage"></a>HDInsight 儲存體

HDInsight 建議 Azure Data Lake Storage Gen2 作為基礎儲存層。 [Azure 儲存體](https://azure.microsoft.com/support/legal/sla/storage/v1_5/)，包括 Azure Data Lake Storage Gen2，則提供99.9% 的 SLA。 HDInsight 會使用 LRS 服務，其中有三個數據複本保存在資料中心內，且複寫是同步的。 當複本遺失時，會順暢地提供複本。

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/) 提供99.9% 的 SLA。 Active Directory 是具有多個層級的內部冗余和自動復原能力的全域服務。 如需詳細資訊，請參閱 [Microsoft 如何持續提升 Azure Active Directory 的可靠性](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/)。

### <a name="azure-active-directory-domain-services-ad-ds"></a>Azure Active Directory Domain Services (AD DS) 

[Azure Active Directory Domain Services](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) 提供99.9% 的 SLA。 Azure AD DS 是裝載在全域散發資料中心內的高可用性服務。 複本集是 Azure AD DS 中的預覽功能，可在 Azure 區域離線時啟用地理災難復原。 如需詳細資訊，請參閱 [Azure Active Directory Domain Services 的複本集合概念和功能](../active-directory-domain-services/concepts-replica-sets.md) 以深入瞭解。  

### <a name="azure-dns"></a>Azure DNS

[Azure DNS](https://azure.microsoft.com/support/legal/sla/dns/v1_1/) 提供100% 的 SLA。 HDInsight 會使用 Azure DNS 在不同位置進行功能變數名稱解析。

## <a name="multi-region-cost-and-complexity-optimizations"></a>多區域成本和複雜性優化

使用跨區域高可用性嚴重損壞修復來改善商務持續性，需要較高複雜性和更高成本的架構設計。 下表詳細說明可能提高擁有權總成本的一些技術領域。

### <a name="cost-optimizations"></a>成本優化

|區域|成本擴大的原因|優化策略|
|----|------------------------|-----------------------|
|資料儲存體|複製次要區域中的主要資料/資料表|只複寫策劃資料|
|資料輸出|輸出跨區域資料傳輸有價格。 檢查頻寬定價指導方針|只複寫策劃資料以縮減區域輸出使用量|
|叢集計算|次要區域中的其他 HDInsight 叢集|在主要失敗之後，使用自動化腳本來部署次要計算。 使用自動調整，將次要叢集大小保持在最小值。 使用較便宜的 VM Sku。 在 VM Sku 可折扣的區域中建立次要資料庫。|
|驗證 |次要區域中的多使用者案例將會產生額外的 Azure AD DS|避免次要區域中有多使用者的進行。|

### <a name="complexity-optimizations"></a>複雜性優化

|區域|複雜性提升的原因|優化策略|
|----|------------------------|-----------------------|
|讀取寫入模式 |需要讀取和寫入主要和次要都啟用 |將次要資料庫設計為唯讀|
|RTO & 零 RPO |要求零資料遺失 (RPO = 0) 和零停機 (RTO = 0)  |以減少需要容錯移轉的元件數目的方式設計 RPO 和 RTO。|
|商務功能 |在次要資料庫中需要主要的完整商務功能 |評估您是否可以使用次要資料庫中最小的重要商務功能子集來執行。|
|連線能力 |要求來自主要的所有上游和下游系統都必須連接到次要|將次要連接限制為最小的重要子集。|

## <a name="next-steps"></a>後續步驟

若要深入瞭解本文中討論的專案，請參閱：

* [Azure HDInsight 商務持續性架構](./hdinsight-business-continuity-architecture.md)
* [Azure HDInsight 高可用性解決方案架構案例研究](./hdinsight-high-availability-case-study.md)
* [Azure HDInsight 上的 Apache Hive 和 HiveQL 是什麼？](./hadoop/hdinsight-use-hive.md)
