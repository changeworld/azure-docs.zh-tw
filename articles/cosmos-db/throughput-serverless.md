---
title: 如何在 Azure Cosmos DB 上的布建輸送量和無伺服器之間進行選擇
description: 瞭解如何為您的工作負載選擇布建的輸送量和無伺服器。
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: 1943aae3a2b01490dca687bcdea99d76da238d51
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187250"
---
# <a name="how-to-choose-between-provisioned-throughput-and-serverless"></a>如何在布建的輸送量和無伺服器之間進行選擇
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 有兩種不同的容量模式：布 [建的輸送量](set-throughput.md) 和 [無伺服器](serverless.md)。 您可以在這兩種模式中執行完全相同的資料庫作業，但您對這些作業計費的方式完全不同。 下列影片說明這些模式之間的核心差異，以及它們如何符合不同類型的工作負載：

> [!VIDEO https://www.youtube.com/embed/CgYQo6uHyt0]

## <a name="detailed-comparison"></a>詳細比較

| 準則 | 佈建的輸送量 | 無伺服器 |
| --- | --- | --- |
| 狀態 | 正式推出 | 預覽狀態 |
| 最適用於 | 需要可預測效能的任務關鍵性工作負載 | 使用輕量和間歇流量的小型到中型非關鍵性工作負載 |
| 運作方式 | 針對每個容器，您可以布建以每秒 [要求單位](request-units.md) 數表示的輸送量量。 每秒，此數量的要求單位適用于您的資料庫作業。 您可以手動更新布建的 [輸送量，或使用自動](provision-throughput-autoscale.md)調整自動調整。 | 您可以對容器執行資料庫作業，而不需要布建任何容量。 |
| 地理分散 | 可用 (不限數量的 Azure 區域)  | 無法使用 (無伺服器帳戶只能在1個 Azure 區域中執行)  |
| 每個容器的儲存體上限 | 無限制 | 50 GB |
| 效能 | SLA 涵蓋99.99% 到99.999% 的可用性<br>SLA 所涵蓋的點讀取和寫入 < 10 毫秒延遲<br>SLA 涵蓋99.99% 的保證輸送量 | SLA 涵蓋99.9% 到99.99% 的可用性<br>適用于點讀取的10毫秒延遲，以及 SLO 所涵蓋的寫入 < 30 毫秒 <<br>SLO 所涵蓋的 95% burstability |
| 計費模式 | 我們會每小時根據佈建的 RU/秒來收取費用，而不論使用的 RU 數目為何。 | 系統會根據您的資料庫作業所耗用的 ru 量，以每小時為單位來計費。 |

> [!IMPORTANT]
> 當無伺服器正式推出時，有些無伺服器限制可能會分階段減緩或移除，而 **您的意見** 反應將有助於我們決定！ 請與我們分享您無伺服器體驗的詳細資訊： [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) 。

## <a name="estimating-your-expected-consumption"></a>估計預期的耗用量

在某些情況下，應該不清楚是否應針對指定的工作負載選擇布建的輸送量或無伺服器。 為了協助進行這項決定，您可以估計整體 **預期的耗用量**，也就是您可以在一個月內取用的 ru 總數 (您可以使用 [此處](plan-manage-costs.md#estimating-serverless-costs) 所示的表格協助來評估這一點) 

**範例 1**：工作負載預期會高載至最多 500 RU/秒，並在一個月內耗用總計20000000個 RU。

- 在布建的輸送量模式中，您會布建 500 RU/秒的容器，每月成本： $0.008 * 5 * 730 = **$29.20**
- 在無伺服器模式中，您需要支付已使用的 ru： $0.25 * 20 = **$5.00**

**範例 2**：工作負載預期會高載至最多 500 RU/秒，並在一個月內耗用總計250000000個 RU。

- 在布建的輸送量模式中，您會布建 500 RU/秒的容器，每月成本： $0.008 * 5 * 730 = **$29.20**
- 在無伺服器模式中，您需要支付已使用的 ru： $0.25 * 250 = **$62.50**

 (這些範例不會計給儲存體成本，這兩種模式之間的差異) 

> [!NOTE]
> 上述範例所示的成本僅供示範之用。 如需最新定價資訊，請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/) 。

## <a name="next-steps"></a>後續步驟

- 深入瞭解如何[在 Azure Cosmos DB 上](set-throughput.md)布建輸送量
- 深入瞭解 [Azure Cosmos DB 無伺服器](serverless.md)
- 熟悉[要求單位](request-units.md)的概念
