---
title: 在自動調整模式中建立 Azure Cosmos 容器和資料庫。
description: 了解如何在自動調整模式中佈建 Azure Cosmos 資料庫和容器，以及其優點和使用案例。
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 533cd8fa69c01b8a36ff5e314ce61a4b624e62ec
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655819"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-throughput"></a>使用自動調整輸送量來建立 Azure Cosmos 容器和資料庫

Azure Cosmos DB 可讓您在資料庫和容器上設定標準 (手動) 或自動調整佈建輸送量。 本文說明自動調整佈建輸送量的優點和使用案例。 

自動調整佈建輸送量非常適合有變動或無法預測流量模式的任務關鍵性工作負載，而且需要高效能和規模的 SLA。 

使用自動調整時，Azure Cosmos DB 會根據使用量，**自動且立即擴充資料庫或容器的輸送量 (RU/秒)** ，而不會影響工作負載的可用性、延遲、輸送量或效能。 

## <a name="benefits-of-autoscale"></a>自動調整的優點

使用自動調整佈建輸送量設定的 Azure Cosmos 資料庫和容器具有下列優點：

* **簡單：** 自動調整會移除使用自訂指令碼或手動縮放容量來管理 RU/秒的複雜性。 

* **可調整：** 資料庫和容器會視需要自動調整佈建輸送量。 用戶端連線、應用程式或對 Azure Cosmos DB SLA 的影響不會中斷。

* **符合成本效益：** 自動調整會在不使用時相應減少，協助您最佳化 RU/秒使用量和成本使用量。 您只需以每小時為基礎，針對您工作負載所需的資源付費。

* **高可用性：** 使用自動調整的資料庫和容器會使用相同的全域分散式、容錯、高可用性 Azure Cosmos DB 後端，以確保資料持久性和高可用性。

## <a name="use-cases-of-autoscale"></a>自動調整的使用案例

自動調整的使用案例包括：

* **變動或無法預期的工作負載：** 當您的工作負載在使用量上有變動或無法預期的尖峰時，自動調整可協助根據使用量自動相應增加和減少。 範例包括視季節性而有不同流量模式的零售網站；在一天內的不同時間出現尖峰的 IOT 工作負載；每個月或一年會看到幾次或更多尖峰使用量的商務應用程式。 使用自動調整時，您不再需要手動佈建尖峰或平均容量。 

* **新的應用程式：** 如果您正在開發新的應用程式，而不確定您需要的輸送量 (RU/秒)，自動調整可讓您輕鬆地開始使用。 您可以從 400 - 4000 RU/秒的自動調整進入點開始，監視您的使用量，並在一段時間後判斷正確的 RU/秒。

* **不常使用的應用程式：** 如果您的應用程式在一天、一週或一個月內只會使用數次、每次使用幾小時 (例如，低容量應用程式/web/blog 網站)，則自動調整會調整容量以處理尖峰使用量，並在超過時相應減少。 

* **開發和測試工作負載：** 如果您或您的小組在工作時間使用 Azure Cosmos 資料庫和容器，但在夜間或週末不需要使用，自動調整會在不使用時相應減少至最小值，以協助節省成本。 

* **已排程的生產工作負載/查詢：** 如果您有一系列想要在閒置期間內執行的排程要求、作業或查詢，可以輕鬆地使用自動調整來執行這項工作。 當您需要執行工作負載時，輸送量會自動調整為所需的規模，並且隨後相應減少。 

建立這些問題的自訂解決方案不僅需要大量的時間，還會造成應用程式設定或程式碼中的複雜性。 自動調整可讓上述案例現成可用，且不再需要自訂或手動縮放容量。 

## <a name="how-autoscale-provisioned-throughput-works"></a>自動調整佈建輸送量的運作方式

設定具有自動調整的容器和資料庫時，您可以指定所需的最大輸送量 `Tmax`。 Azure Cosmos DB 會調整 `T` 這類 `0.1*Tmax <= T <= Tmax` 的輸送量。 例如，如果您將最大輸送量設定為 20,000 RU/秒，則輸送量會在 2000 到 20,000 RU/秒之間進行調整。 因為縮放比例是自動且即時的，所以您可以隨時可以取用到佈建的最大值 `Tmax` 而不會發生延遲。 

每小時，將收取系統在一小時內擴充到最高輸送量 `T` 的費用。

自動調整最大輸送量 `Tmax` 的進入點從 4000 RU/秒開始，這會在 400 - 4000 RU/秒之間縮放。 您可以以 1000 RU/秒的增量設定 `Tmax`，並隨時變更該值。  

## <a name="enable-autoscale-on-existing-resources"></a>啟用現有資源的自動調整 ##
使用 [Azure 入口網站](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container)在現有的資料庫或容器上啟用自動調整。 您可以隨時在自動調整和標準 (手動) 佈建輸送量之間切換。 如需詳細資訊，請參閱這份[文件](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work)。

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a> 自動調整的輸送量和儲存體限制

對於 `Tmax` 的任何值，資料庫或容器可以儲存總計 `0.01 * Tmax GB`。 達到此數量的儲存體之後，將會根據新的儲存體值自動增加 RU/秒的上限，且不會影響您的應用程式。 

例如，如果您從 50,000 RU/秒的最大 RU/秒開始 (在 5000 - 50,000 RU/秒之間調整)，您最多可以儲存 500 GB 的資料。 如果您超過 500 GB (例如，儲存體現在是 600 GB)，新的最大 RU/秒將會是 60,000 RU/秒 (在 6000 - 60,000 RU/秒之間調整)。

當您搭配自動調整使用資料庫層級輸送量時，可以讓前 25 個容器共用自動調整 4000 的最大 RU/秒 (在 400 - 4000 RU/秒之間調整)，前提是您未超過 40 GB 的儲存體。 如需詳細資訊，請參閱這份[文件](autoscale-faq.md#can-i-change-the-max-rus-on-the-database-or-container)。

## <a name="comparison--containers-configured-with-manual-vs-autoscale-throughput"></a>比較 – 以手動與自動調整輸送量設定的容器
如需詳細資訊，請參閱此[文件](how-to-choose-offer.md)，了解如何選擇標準 (手動) 和自動調整輸送量。  

|| 具有標準 (手動) 輸送量的容器  | 具有自動調整輸送量的容器 |
|---------|---------|---------|
| **佈建的輸送量 (RU/秒)** | 手動佈建。 | 根據工作負載使用模式自動及立即調整。 |
| **要求/作業的速率限制 (429)**  | 如果耗用量超過佈建的容量，則可能會發生。 | 如果您在已設定的自動調整輸送量範圍內使用 RU/秒，則不會發生這種情況。    |
| **容量規劃** |  您必須進行容量規劃，並佈建所需的確切輸送量。 |    系統會自動負責容量規劃及容量管理。 |
| **定價** | 您需支付每小時手動佈建的 RU/秒 (使用[標準 (手動) RU/秒的每小時費率](https://azure.microsoft.com/pricing/details/cosmos-db/))。 | 您每小時要支付系統在一小時內所相應增加的最高 RU/秒。 <br/><br/> 針對單一寫入區域帳戶，您需支付每小時使用的 RU/秒 (使用 [自動調整 RU/秒的每小時費率](https://azure.microsoft.com/pricing/details/cosmos-db/))。 <br/><br/>針對具有多個寫入區域的帳戶，自動調整不會額外收費。 您需支付每小時使用的輸送量 (使用相同的[多重主機 RU/秒的每小時費率](https://azure.microsoft.com/pricing/details/cosmos-db/))。 |
| **最適合工作負載類型** |  可預測且穩定的工作負載|   無法預期和變動的工作負載  |

## <a name="next-steps"></a>後續步驟

* 檢閱[自動調整常見問題集](autoscale-faq.md)。
* 了解如何[在手動與自動調整輸送量之間進行選擇](how-to-choose-offer.md)。
* 了解如何[在 Azure Cosmos 資料庫或容器上佈建自動調整輸送量](how-to-provision-autoscale-throughput.md)。
* 深入了解 Azure Cosmos DB 中的[資料分割](partition-data.md)。


