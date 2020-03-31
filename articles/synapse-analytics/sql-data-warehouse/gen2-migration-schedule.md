---
title: 將 SQL 池遷移到第 2 代
description: 有關將現有 SQL 池遷移到 Gen2 的說明以及按區域進行的遷移計畫。
services: synapse-analytics
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 4714d5908fffb6f5c1440c3ec512fb8173da4b57
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346771"
---
# <a name="upgrade-your-sql-pool-to-gen2"></a>將 SQL 池升級到第 2 代

Microsoft 正在説明降低運行 SQL 池的入門級成本。  能夠處理要求苛刻查詢的較低計算層現在可用於 SQL 池。 閱讀完整公告[降低第 2 代的計算層支援](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/)。 新產品可在下表中注明的區域提供。 對於受支援的區域，可以通過：

- **自動升級過程：** 一旦服務在區域中可用，自動升級就不會啟動。  當在特定區域開始自動升級時，將在您選擇的維護計畫期間進行單個資料倉儲升級。
- [**自我升級到第 2 代：**](#self-upgrade-to-gen2)您可以通過對 Gen2 進行自我升級來控制何時升級。 如果區域尚不受支援，則可以從還原點直接還原到受支援區域中的 Gen2 實例。

## <a name="automated-schedule-and-region-availability-table"></a>自動化排程和國家可用性表格

下表依區域彙整較低 Gen2 計算層的可用時間和自動升級的開始時間。 日後有可能會變更。 回頭查看您的區域何時可用。

\* 表示目前無法使用該區域的特定排程。

| **地區** | **可用的較低 Gen2** | **自動升級開始** |
|:--- |:--- |:--- |
| 加拿大東部 |2020年6月1日 |2020年7月1日 |
| 中國東部 |\* |\* |
| 中國北部 |\* |\* |
| 德國中部 |\* |\* |
| 德國中西部 |可用 |2020年5月1日 |
| 印度西部 |可用 |2020年5月1日  |

## <a name="automatic-upgrade-process"></a>自動升級程序

根據上面的可用性圖表，我們將為您的 Gen1 實例安排自動升級。 為了避免 SQL 池可用性出現任何意外中斷，將在維護計畫期間安排自動升級。 在自動升級到 Gen2 的區域，將禁用創建新的 Gen1 實例的能力。 完成自動升級後，將棄用第 1 代。 如需排程的詳細資訊，請參閱[檢視維護排程](maintenance-scheduling.md#view-a-maintenance-schedule)

當我們重新開機 SQL 池時，升級過程將涉及連線性短暫下降（大約 5 分鐘）。  重新開機 SQL 池後，它將完全可用。 但是，在升級過程中繼續在後臺升級資料檔案時，性能可能會下降。 效能降低的總時間會因您的資料檔案大小而有所不同。

在重新啟動之後，您也可以使用較大的 SLO 和資源類別，在所有主要資料行存放區資料表上執行 [Alter Index rebuild](sql-data-warehouse-tables-index.md)，藉此加速資料檔案升級程序。

> [!NOTE]
> Alter Index rebuild 是一種離線作業，資料表在重建完成後才可使用。

## <a name="self-upgrade-to-gen2"></a>自我升級至 Gen2

您可以通過在現有的 Gen1 SQL 池上執行這些步驟來選擇自我升級。 如果選擇自升級，則必須在您所在區域開始自動升級過程之前完成。 這樣做可確保避免自動升級導致衝突的任何風險。

進行自我升級時，有兩個選項。  您可以就地升級當前 SQL 池，也可以將 Gen1 SQL 池還原到 Gen2 實例中。

- [就地升級](upgrade-to-latest-generation.md)- 此選項會將現有第 1 代 SQL 池升級到第 2 代。 當我們重新開機 SQL 池時，升級過程將涉及連線性短暫下降（大約 5 分鐘）。  重新開機 SQL 池後，它將完全可用。 如果在升級過程中遇到問題，請打開[支援請求](sql-data-warehouse-get-started-create-support-ticket.md)，並將"Gen2 升級"作為可能的原因。
- [從還原點升級](sql-data-warehouse-restore-points.md)- 在當前 Gen1 SQL 池上創建使用者定義的還原點，然後直接還原到 Gen2 實例。 現有的第 1 代 SQL 池將保持不變。 還原完成後，您的 Gen2 SQL 池將完全可用。  在已還原的 Gen2 執行個體上執行所有測試和驗證程序之後，才能刪除原始的 Gen1 執行個體。

   - 步驟 1：從 Azure 門戶[創建使用者定義的還原點](sql-data-warehouse-restore-active-paused-dw.md)。
   - 步驟 2：從使用者定義的還原點還原時，將"性能級別"設置為首選的 Gen2 層。

當升級程序繼續在背景中升級資料檔案時，您可能會遇到效能降低的期間。 效能降低的總時間會因您的資料檔案大小而有所不同。

為了加速背景資料移轉程序，您可以用較大的 SLO 和資源類別，在您會查詢的所有主要資料行存放區資料表上執行 [Alter Index rebuild](sql-data-warehouse-tables-index.md)，立即強制執行資料移動。

> [!NOTE]
> Alter Index rebuild 是一種離線作業，資料表在重建完成後才可使用。

如果 SQL 池遇到任何問題，請創建[支援請求](sql-data-warehouse-get-started-create-support-ticket.md)，並將"Gen2 升級"作為可能的原因。

如需詳細資訊，請參閱[升級至 Gen2](upgrade-to-latest-generation.md)。

## <a name="migration-frequently-asked-questions"></a>移轉常見問題集

**問：第 2 代的成本是否與第 1 代相同？**

- 答： 會。

**問：升級將如何影響我的自動化腳本？**

- 答：任何引用服務等級目標的自動化腳本都應更改為對應于第 2 代等效項。  請參閱[這裡](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal)的詳細資料。

**問：自升級通常需要多長時間？**

- 答：您可以就地升級或從還原點升級。  
   - 升級到位將導致 SQL 池暫時暫停和恢復。  SQL 池連線時，後臺進程將繼續。  
   - 如果您要透過還原點升級，則需花費較長的時間，因為升級會經歷完整的還原程序。

**問：自動升級需要多長時間？**

- 答：升級的實際停機時間只是暫停和恢復服務所需的時間，即 5 到 10 分鐘。 在短暫的停機之後，背景程序會執行儲存體移轉。 後臺進程的時間長度取決於 SQL 池的大小。

**問：何時進行自動升級？**

- 答：在維護計畫期間。 運用您選擇的維護排程會盡可能不中斷您的業務。

**問：如果我的背景升級過程似乎卡住了，我該怎麼辦？**

 - 答：啟動列存儲表的重新索引。 請注意，在此作業期間，重新編製資料表的索引將會離線進行。

**問：如果 Gen2 沒有第 1 代上的服務等級目標怎麼辦？**
- 答：如果您在第 1 代上運行 DW600 或 DW1200，建議分別使用 DW500c 或 DW1000c，因為第 2 代提供的記憶體、資源和性能高於第 1 代。

**問：我可以禁用異地備份嗎？**
- 答：否。 地理備份是一項企業功能，用於在區域無法接通保留 SQL 池可用性。 如果您有進一步考量，請開啟[支援要求](sql-data-warehouse-get-started-create-support-ticket.md)。

**問：第 1 代和 Gen2 之間的 T-SQL 語法是否有差異？**

- 答：從 Gen1 到 Gen2 的 T-SQL 語言語法沒有變化。

**問：第 2 代是否支援維護視窗？**

- 答： 會。

**問：升級區域後，我能否創建新的 Gen1 實例？**

- 答：否。 在區域升級之後，新 Gen1 執行個體的建立將會停用。

## <a name="next-steps"></a>後續步驟

- [升級步驟](upgrade-to-latest-generation.md)
- [維護時段](maintenance-scheduling.md)
- [資源健康情況監視器](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [在開始移轉之前檢閱](upgrade-to-latest-generation.md#before-you-begin)
- [就地升級和從還原點升級](upgrade-to-latest-generation.md)
- [建立使用者定義的還原點](sql-data-warehouse-restore-points.md)
- [了解如何還原至 Gen2](sql-data-warehouse-restore-active-paused-dw.md)
- [開始 SQL 資料倉儲支援要求](https://go.microsoft.com/fwlink/?linkid=857950)
