---
title: 將您專用的 SQL 集區 (先前的 SQL DW) 遷移至 Gen2
description: 將現有的專用 SQL 集區 (先前的 SQL DW) 遷移至 Gen2 和依區域的遷移排程的指示。
services: synapse-analytics
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.subservice: sql-dw
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 512775369bd7787c6228c6d452be0e236ddf5cc2
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96456343"
---
# <a name="upgrade-your-dedicated-sql-pool-formerly-sql-dw-to-gen2"></a>將您專用的 SQL 集區 (先前的 SQL DW) 升級至 Gen2

Microsoft 可協助降低 (先前為 SQL DW) 執行專用 SQL 集區的專案層級成本。  可處理要求查詢的較低計算層，現在可用於 (先前為 SQL DW) 的專用 SQL 集區。 閱讀完整公告的 [Gen2 較低的計算層支援](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/)。 新的供應專案可在下表所述的區域中使用。 針對支援的區域，現有的 Gen1 專用 SQL 集區 (先前的 SQL DW) 可透過下列任一項升級為 Gen2：

- **自動升級程式：** 當服務在區域中可供使用時，不會立即啟動自動升級。  當自動升級在特定區域開始時，會在您選取的維護排程期間進行個別的資料倉儲升級。
- [**自我升級至 Gen2：**](#self-upgrade-to-gen2) 您可以藉由執行自我升級至 Gen2 來控制升級的時機。 如果您的區域尚未受到支援，您可以從還原點直接還原至支援區域中的 Gen2 實例。

## <a name="automated-schedule-and-region-availability-table"></a>自動化排程和國家可用性表格

下表依區域彙整較低 Gen2 計算層的可用時間和自動升級的開始時間。 日後有可能會變更。 回頭查看您的區域何時可用。

\* 表示目前無法使用該區域的特定排程。

| **區域** | **可用的較低 Gen2** | **自動升級開始** |
|:--- |:--- |:--- |
| 加拿大東部 |2020 年 6 月 1 日 |2020年7月1日 |
| 中國東部 |\* |\* |
| 中國北部 |\* |\* |
| 德國中部 |\* |\* |
| 德國中西部 |可用 |5月1日，2020 |
| 印度西部 |可用 |5月1日，2020  |

## <a name="automatic-upgrade-process"></a>自動升級程序

根據上述的可用性圖表，我們將為您的 Gen1 實例排程自動升級。 為了避免任何非預期的專用 SQL 集區可用性中斷 (先前為 SQL DW) ，系統會在維護排程期間排程自動升級。 在自動升級為 Gen2 的區域中，將會停用建立新 Gen1 實例的功能。 自動升級完成後，Gen1 將會被取代。 如需排程的詳細資訊，請參閱[檢視維護排程](maintenance-scheduling.md#view-a-maintenance-schedule)

當我們重新開機 (先前為 SQL DW) 的專用 SQL 集區時，升級程式會涉及短暫的連線 (大約5分鐘的) 。  一旦您專用的 SQL 集區 (先前的 SQL DW) 重新開機後，就會完全可供使用。 不過，當升級程式繼續在背景中升級資料檔案時，您可能會遇到效能降低的情況。 效能降低的總時間會因您的資料檔案大小而有所不同。

在重新啟動之後，您也可以使用較大的 SLO 和資源類別，在所有主要資料行存放區資料表上執行 [Alter Index rebuild](sql-data-warehouse-tables-index.md)，藉此加速資料檔案升級程序。

> [!NOTE]
> Alter Index rebuild 是一種離線作業，資料表在重建完成後才可使用。

## <a name="self-upgrade-to-gen2"></a>自我升級至 Gen2

您可以選擇自行升級，方法是在現有的 Gen1 專用 SQL 集區上執行下列步驟， (先前的 SQL DW) 。 如果您選擇自行升級，您必須先完成，才能在您的區域開始自動升級程式。 這麼做可確保您避免自動升級造成衝突的風險。

進行自我升級時，有兩個選項。  您可以就地升級目前的專用 SQL 集區 (先前的 SQL DW) ，也可以將先前 SQL DW)  (的 Gen1 專用 SQL 集區還原至 Gen2 實例。

- [就地升級](upgrade-to-latest-generation.md) -這個選項會將您現有的 GEN1 專用 sql 集區 (先前的 sql DW) 升級至 Gen2。 當我們重新開機 (先前為 SQL DW) 的專用 SQL 集區時，升級程式會涉及短暫的連線 (大約5分鐘的) 。  一旦重新開機，它就會完全可供使用。 如果您在升級期間遇到問題，請開啟 [支援要求](sql-data-warehouse-get-started-create-support-ticket.md) ，並參考「Gen2 升級」作為可能的原因。
- [從還原點升級](sql-data-warehouse-restore-points.md) -在目前的 GEN1 專用 SQL 集區上建立使用者定義的還原點 (先前為 SQL DW) ，然後直接還原至 Gen2 實例。 現有的 Gen1 專用 SQL 集區 (先前的 SQL DW) 將會保持在原處。 還原完成後，您的 Gen2 專用 SQL 集區 (先前的 SQL DW) 將可完整提供使用。  在已還原的 Gen2 執行個體上執行所有測試和驗證程序之後，才能刪除原始的 Gen1 執行個體。

  - 步驟1：從 Azure 入口網站 [建立使用者定義的還原點](sql-data-warehouse-restore-active-paused-dw.md)。
  - 步驟2：從使用者定義的還原點還原時，請將「效能層級」設定為您偏好的 Gen2 層。

當升級程序繼續在背景中升級資料檔案時，您可能會遇到效能降低的期間。 效能降低的總時間會因您的資料檔案大小而有所不同。

為了加速背景資料移轉程序，您可以用較大的 SLO 和資源類別，在您會查詢的所有主要資料行存放區資料表上執行 [Alter Index rebuild](sql-data-warehouse-tables-index.md)，立即強制執行資料移動。

> [!NOTE]
> Alter Index rebuild 是一種離線作業，資料表在重建完成後才可使用。

如果您的專用 SQL 集區遇到任何問題 (先前為 SQL DW) ，請建立 [支援要求](sql-data-warehouse-get-started-create-support-ticket.md) ，並參考「Gen2 升級」作為可能的原因。

如需詳細資訊，請參閱[升級至 Gen2](upgrade-to-latest-generation.md)。

## <a name="migration-frequently-asked-questions"></a>移轉常見問題集

**問： Gen2 的成本是否與 Gen1 相同？**

- A：是。

**問：升級會如何影響我的自動化腳本？**

- 答：任何參考服務等級目標的 automation 腳本都應該變更為對應至 Gen2 對等專案。  請參閱[這裡](upgrade-to-latest-generation.md#upgrade-in-a-supported-region-using-the-azure-portal)的詳細資料。

**問：自我升級通常需要多久的時間？**

- 答：您可以就地升級或從還原點升級。

  - 就地升級會導致您專用的 SQL 集區 (先前的 SQL DW) ，以暫時暫停和繼續。  當專用的 SQL 集區 (先前的 SQL DW) 上線時，背景進程會繼續進行。  
  - 如果您要透過還原點升級，則需花費較長的時間，因為升級會經歷完整的還原程序。

**問：自動升級需要多久時間？**

- 答：升級的實際停機時間只是暫停和繼續服務所需的時間（介於5到10分鐘之間）。 在短暫的停機之後，背景程序會執行儲存體移轉。 背景進程的時間長度取決於您先前的 SQL DW)  (專用 SQL 集區的大小。

**問：此自動升級何時會進行？**

- 答：在維護排程期間。 運用您選擇的維護排程會盡可能不中斷您的業務。

**問：如果我的背景升級程式似乎停滯，該怎麼辦？**

- 答：開始重新編制資料行存放區資料表的索引。 請注意，在此作業期間，重新編製資料表的索引將會離線進行。

**問：如果 Gen2 在 Gen1 上沒有服務等級目標，該怎麼辦？**

- 答：如果您在 Gen1 上執行 DW600 或 DW1200，建議您分別使用 DW500c 或 DW1000c，因為 Gen2 提供比 Gen1 更多的記憶體、資源和更高的效能。

**問：我可以停用異地備份嗎？**

- 答：否。 異地備份是企業功能，可在區域變成無法使用時，將您專用的 SQL 集區保留 (先前的 SQL DW) 可用性。 如果您有進一步考量，請開啟[支援要求](sql-data-warehouse-get-started-create-support-ticket.md)。

**問： Gen1 與 Gen2 之間的 T-sql 語法有何差異？**

- 答：從 Gen1 到 Gen2 的 T-sql 語言語法沒有任何變更。

**問： Gen2 是否支援維護時段？**

- A：是。

**問：我是否可以在我的區域升級之後建立新的 Gen1 實例？**

- 答：否。 在區域升級之後，新 Gen1 執行個體的建立將會停用。

## <a name="next-steps"></a>後續步驟

- [升級步驟](upgrade-to-latest-generation.md)
- [維護期間](maintenance-scheduling.md)
- [資源健康情況監視器](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
- [在開始移轉之前檢閱](upgrade-to-latest-generation.md#before-you-begin)
- [就地升級和從還原點升級](upgrade-to-latest-generation.md)
- [建立使用者定義的還原點](sql-data-warehouse-restore-points.md)
- [了解如何還原至 Gen2](sql-data-warehouse-restore-active-paused-dw.md)
- [開啟 Azure Synapse Analytics 支援要求](https://go.microsoft.com/fwlink/?linkid=857950)
