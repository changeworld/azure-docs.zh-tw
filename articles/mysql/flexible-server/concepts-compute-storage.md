---
title: 計算和儲存選項-適用於 MySQL 的 Azure 資料庫彈性的伺服器
description: 本文說明適用於 MySQL 的 Azure 資料庫彈性伺服器中的計算和儲存體選項。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 267b362c94b04b3be634f7e61c2b6d67604d7854
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954676"
---
# <a name="compute-and-storage-options-in-azure-database-for-mysql---flexible-server-preview"></a>適用於 MySQL 的 Azure 資料庫彈性的伺服器 (預覽) 中的計算和儲存體選項

> [!IMPORTANT] 
> 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器目前處於公開預覽狀態。

您可以使用下列三種不同的計算層級之一來建立適用於 MySQL 的 Azure 資料庫彈性的伺服器：高載、一般用途和記憶體優化。 計算層會由使用 B 系列、D 系列和 E 系列的基礎 VM SKU 來區分。 選擇的計算層和大小可決定伺服器上可用的記憶體和虛擬核心。 所有計算層都使用相同的儲存體技術。 所有資源都會佈建在 MySQL 伺服器層級。 一個伺服器可以有一個或多個資料庫。

| 資源/層 | **高載** | **一般用途** | **記憶體優化** |
|:---|:----------|:--------------------|:---------------------|
| VM 系列| B 系列 | Ddsv4 系列 | Edsv4-series|
| 虛擬核心 | 1, 2 | 2、4、8、16、32、48、64 | 2、4、8、16、32、48、64 |
| 每個虛擬核心的記憶體 | 變數 | 4 GiB | 8 GiB * |
| 儲存體大小 | 5 GiB 至 16 TiB | 5 GiB 至 16 TiB | 5 GiB 至 16 TiB |
| 資料庫備份的保留期限 | 1至35天 | 1至35天 | 1至35天 |

\* 除了 E64ds_v4 (記憶體優化) SKU （具有 504 GB 的記憶體）

若要選擇計算層，請使用下表作為起點。

| 計算層 | 目標工作負載 |
|:-------------|:-----------------|
| 高載 | 最適合不需要持續完整 CPU 的工作負載。 |
| 一般用途 | 需要平衡的計算和記憶體以及可擴充 I/O 輸送量的大多數商務工作負載。 範例包括用於裝載 Web 和行動應用程式的伺服器，以及其他企業應用程式。|
| 記憶體最佳化 | 需要記憶體內效能來提供更快速交易處理和更高並行性的高效能資料庫工作負載。 範例包括用於處理即時資料的伺服器，以及高效能交易式或分析應用程式。|

在您建立伺服器之後，計算層、計算大小和儲存體大小都會變更。 計算調整需要重新開機並花60-120 秒，而儲存體調整不需要重新開機。 您也可以單獨調整備份保留期限。 如需詳細資訊，請參閱 [調整資源](#scale-resources) 一節。

## <a name="compute-tiers-size-and-server-types"></a>計算層、大小和伺服器類型

您可以根據層級和大小來選取計算資源。 這會決定虛擬核心和記憶體大小。 虛擬核心代表基礎硬體的邏輯 CPU。

可用伺服器類型的詳細規格如下所示：

| 計算大小         | 虛擬核心 | 記憶體大小 (GiB)  | 
|----------------------|--------|-------------------|
| **高載**        |        |                   | 
| Standard_B1s         | 1      | 1                 |  
| Standard_B1ms        | 1      | 2                 | 
| Standard_B2s         | 2      | 4                 |  
| **一般用途**  |        |                   | 
| Standard_D2ds_v4     | 2      | 8                 |  
| Standard_D4ds_v4     | 4      | 16                | 
| Standard_D8ds_v4     | 8      | 32                | 
| Standard_D16ds_v4    | 16     | 64                | 
| Standard_D32ds_v4    | 32     | 128               |  
| Standard_D48ds_v4    | 48     | 192               |  
| Standard_D64ds_v4    | 64     | 256               | 
| **記憶體優化** |        |                   |
| Standard_E2ds_v4     | 2      | 16                |
| Standard_E4ds_v4     | 4      | 32                |
| Standard_E8ds_v4     | 8      | 64                |
| Standard_E16ds_v4    | 16     | 128               |
| Standard_E32ds_v4    | 32     | 256               |
| Standard_E48ds_v4    | 48     | 384               |
| Standard_E64ds_v4    | 64     | 504               |

若要取得可用計算系列的詳細資料，請參閱適用于 [高載 (B 系列) ](../../virtual-machines/sizes-b-series-burstable.md)的 Azure VM 檔、 [一般用途 (Ddsv4 系列) ](../../virtual-machines/ddv4-ddsv4-series.md)，以及 [記憶體優化 (Edsv4 系列) ](../../virtual-machines/edv4-edsv4-series.md)。

>[!NOTE]
>針對 [高載 (B 系列) ](../../virtual-machines/sizes-b-series-burstable.md) 計算層，當伺服器因為任何原因而重新開機時（例如使用者起始、規劃或未規劃的維護），累積的點數可能會遺失。 原因是當適用於 MySQL 的 Azure 資料庫重新開機它時，它會保留在相同的節點上，將會保留累積的信用額度。 Contrarily，每當適用於 MySQL 的 Azure 資料庫伺服器在新節點上啟動全新時，就會取得初始點數。 如需詳細資訊，請參閱 [高載 (B 系列) 常見問題](https://docs.microsoft.com/azure/virtual-machines/sizes-b-series-burstable#q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart)。

## <a name="storage"></a>儲存體

您布建的儲存體是彈性伺服器可用的儲存體容量數量。 儲存體會用於資料庫檔案、暫存檔案、交易記錄和 MySQL 伺服器記錄。 在所有計算層中，支援的最小儲存體為 5 GiB，最大值為 16 TiB。 儲存體會以 1 GiB 增量進行調整，而且可以在建立伺服器之後相應增加。

>[!NOTE]
> 儲存體只能擴大，而不能縮小。

您可以使用儲存體限制、儲存體百分比和儲存體使用的計量，以 Azure 監視器) 監視 Azure 入口網站 (中的儲存體耗用量。 請參閱 [監視文章](./concepts-monitoring.md) ，以瞭解計量。 

### <a name="reaching-the-storage-limit"></a>到達儲存體限制

當伺服器上使用的儲存體接近布建的限制時，伺服器會進入唯讀模式，以保護伺服器上遺失的任何寫入。 如果可用儲存體小於所布建儲存體大小的5%，則會將小於等於 100 GiB 布建儲存體的伺服器標示為唯讀。 GiB 布建的儲存體超過100的伺服器，會在可用儲存空間小於 5 GiB 時標示為唯讀。

例如，如果您已布建 110 GiB 的儲存體，且實際使用率超過 105 GiB，則伺服器會標示為唯讀。 或者，如果您已布建 5 GiB 的儲存體，則當可用儲存空間小於 256 MB 時，伺服器會標示為唯讀。

當服務嘗試讓伺服器變為唯讀時，會封鎖所有新的寫入交易要求，而現有的使用中交易會繼續執行。 當伺服器設為唯讀時，所有後續的寫入作業和交易認可都會失敗。 讀取查詢將會繼續運作，不會中斷。 

若要讓伺服器退出唯讀模式，您應該增加伺服器上的已布建儲存體。 這可使用 Azure 入口網站或 Azure CLI 來完成。 一旦增加之後，伺服器將會準備好再次接受寫入交易。

我們建議您設定警示，讓系統可在伺服器儲存容量接近閾值時發出通知，以避免進入唯讀狀態。 請參閱 [監視文章](./concepts-monitoring.md) ，以瞭解可用的計量。 

我們建議您 <!--turn on storage auto-grow or to--> 設定警示，以在您的伺服器存放裝置接近閾值時通知您，如此您就可以避免進入唯讀狀態。 如需詳細資訊，請參閱警示檔中有關 [如何設定警示](how-to-alert-on-metric.md)的檔。

### <a name="storage-auto-grow"></a>儲存體自動成長

適用於 MySQL 的 Azure 資料庫彈性的伺服器尚未提供儲存體自動成長。

## <a name="iops"></a>IOPS
所有計算大小的最小有效 IOPS 為100，而最大有效 IOPS 取決於下列兩個屬性： 
- 計算：最大有效 IOPS 可能受限於所選取計算大小的最大可用 IOPS。
- 儲存體：在所有計算層中，IOPS 會以3:1 的比率調整布建的儲存體大小。

您可以藉由增加布建的儲存體，或移至較大的計算大小來調整可用的有效 IOPS (如果您的 IOPS 受限於計算) 。 在預覽中，支援的有效 IOPS 上限為 20000 IOPS。

若要深入瞭解每個計算大小的最大有效 IOPS，請使用計算和儲存體的組合，如下所示： 

| 計算大小         | 最大有效 IOPS  | 
|----------------------|---------------------|
| **高載**        |                     |
| Standard_B1s         | 320                 |
| Standard_B1ms        | 640                 |
| Standard_B2s         | 1280                | 
| **一般用途**  |                     |
| Standard_D2ds_v4     | 3200                |
| Standard_D4ds_v4     | 6400                |
| Standard_D8ds_v4     | 12800               |
| Standard_D16ds_v4    | 20000               |
| Standard_D32ds_v4    | 20000               |
| Standard_D48ds_v4    | 20000               | 
| Standard_D64ds_v4    | 20000               | 
| **記憶體最佳化** |                     | 
| Standard_E2ds_v4     | 3200                | 
| Standard_E4ds_v4     | 6400                | 
| Standard_ E8ds_v4    | 12800               | 
| Standard_ E16ds_v4   | 20000               | 
| Standard_E32ds_v4    | 20000               | 
| Standard_E48ds_v4    | 20000               | 
| Standard_E64ds_v4    | 20000               |  

有效 IOPS 的最大值取決於每個計算大小的最大可用 IOPS。 請參閱下面的公式，並參考資料行最大未快取的磁片輸送量： [B 系列](../../virtual-machines/sizes-b-series-burstable.md)、 [Ddsv4 系列](../../virtual-machines/ddv4-ddsv4-series.md)和 [EDSV4 系列](../../virtual-machines/edv4-edsv4-series.md)檔中的 *IOPS/MBps* 。

**最大有效 IOPS** = 最小 (「最大未快取的 *磁片輸送量： IOPS/MBps* 」的計算大小、GiB 中布建的儲存體 * 3) 

您可以使用 [IO 百分比](./concepts-monitoring.md) 計量，以 Azure 監視器) 監視 Azure 入口網站 (中的 i/o 耗用量。 如果您需要更多 IOPS，您將需要瞭解您是否受限於計算大小或布建的儲存體。 相應調整您伺服器的計算或儲存體的布建。

## <a name="backup"></a>備份

服務會自動採用伺服器的備份。 您可以從1到35天的範圍選取保留週期。 深入瞭解 [備份和還原概念文章](concepts-backup-restore.md)中的備份。

## <a name="scale-resources"></a>調整資源

建立伺服器之後，您可以單獨變更計算層、計算大小 (虛擬核心和記憶體) 、儲存空間量，以及備份保留期限。 計算大小可以向上或向下調整。 備份保留期限可以從1到35天擴大或縮小。 儲存體大小只能增加。 您可以透過入口網站或 Azure CLI 來調整資源。

> [!NOTE]
> 儲存體大小只能增加。 增加之後，您就無法返回較小的儲存體大小。

當您變更計算層級或計算大小時，伺服器會重新開機，讓新的伺服器類型生效。 在系統切換到新伺服器的期間，您無法建立任何新的連線，且所有未認可的交易皆會復原。 此視窗會有所不同，但在大部分情況下，會在60-120 秒之間。 

調整儲存體和變更備份保留期限為線上作業，不需要重新開機伺服器。

## <a name="pricing"></a>定價

如需最新的定價資訊，請參閱服務的[定價頁面](https://azure.microsoft.com/pricing/details/MySQL/)。 若要查看您想要的設定成本， [Azure 入口網站](https://portal.azure.com/#create/Microsoft.MySQLServer/flexibleServers) 根據您選取的選項，在 [ **計算 + 儲存體** ] 索引標籤上顯示每月成本。 如果您沒有 Azure 訂用帳戶，則可以使用 Azure 價格計算機來取得估計的價格。 在 [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/) 網站上，選取 **[新增專案**]，展開 [ **資料庫** ] 類別，選擇 [ **適用於 MySQL 的 Azure 資料庫**] 和 [ **彈性伺服器** ] 作為部署類型，以自訂選項。

如果您想要將伺服器成本優化，可以考慮下列秘訣：

- 如果計算使用量過低，請縮小計算層級或計算大小 (虛擬核心) 。
- 如果您的工作負載不需要從一般用途和記憶體優化層持續完整的計算容量，請考慮切換到高載計算層級。
- 停止未使用的伺服器。
- 如果不需要較長的備份保留期，請縮短備份保留期限。

## <a name="next-steps"></a>下一步

- 深入了解如何[在入口網站中建立 MySQL 伺服器](quickstart-create-server-portal.md)。
- 瞭解 [服務限制](concepts-limitations.md)。