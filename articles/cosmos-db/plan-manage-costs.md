---
title: 計畫和管理 Azure 宇宙資料庫的成本
description: 瞭解如何在 Azure 門戶中使用成本分析來規劃和管理 Azure Cosmos DB 的成本。
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: cf259dfa0b00393cf0fee259b199527569ba1792
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152579"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>計畫和管理 Azure 宇宙資料庫的成本

本文介紹如何規劃和管理 Azure Cosmos DB 的成本。 首先，在添加任何資源之前，使用 Azure Cosmos DB 容量計算機説明規劃成本。 接下來，在添加 Azure 資源時，可以查看估計成本。 開始使用 Azure Cosmos DB 資源後，請使用成本管理功能來設置預算並監視成本。 您還可以查看預測的成本並確定支出趨勢，以確定您可能要採取行動的領域。

請注意，Azure Cosmos DB 的成本只是 Azure 帳單中每月成本的一部分。 如果使用其他 Azure 服務，則對 Azure 訂閱中使用的所有 Azure 服務和資源（包括協力廠商服務）進行計費。 本文介紹如何規劃和管理 Azure Cosmos DB 的成本。 熟悉 Azure Cosmos DB 的管理成本後，可以應用類似的方法來管理訂閱中使用的所有 Azure 服務的成本。

## <a name="prerequisites"></a>Prerequisites

成本分析支援不同的 Azure 帳戶類型。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](../cost-management-billing/costs/understand-cost-mgt-data.md)。 若要檢視成本資料，您至少需要 Azure 帳戶的讀取存取。 如需 Azure 成本管理資料的存取權指派相關資訊，請參閱[指派資料的存取權](../cost-management-billing/costs/assign-access-acm-data.md)。

## <a name="review-estimated-costs-with-capacity-calculator"></a>使用容量計算機查看估計成本

在 Azure Cosmos 帳戶中創建資源之前，請使用[Azure 科斯資料庫容量計算機](https://cosmos.azure.com/capacitycalculator/)估計成本。 容量計算機用於估算所需的輸送量和工作負載成本。 為工作負荷配置 Azure Cosmos 資料庫和容器具有適當數量的預配輸送量或[請求單位 （RU/s）](request-units.md)對於優化成本和性能至關重要。 您必須輸入詳細資訊，如 API 類型、區域數、專案大小、每秒讀/寫請求、存儲的總數據以獲取成本估算。 要瞭解有關容量計算機的更多資訊，請參閱[估計](estimate-ru-with-capacity-planner.md)文章。

以下螢幕截圖使用容量計算機顯示輸送量和成本估算：

![Azure 宇宙資料庫容量計算機中的成本估計](./media/plan-manage-costs/capacity-calculator-cost-estimate.png)

## <a name="review-estimated-costs-from-the-azure-portal"></a>從 Azure 門戶查看估計成本

從 Azure 門戶創建 Azure Cosmos DB 資源時，可以看到估計成本。 使用以下步驟查看成本估算：

1. 登錄到 Azure 門戶並導航到 Azure Cosmos 帳戶。
1. 轉到**資料資源管理器**。
1. 創建新容器（如圖形容器）。
1. 輸入工作負載所需的輸送量，例如 400 RU/s。 輸入輸送量值後，您可以看到定價估算，如以下螢幕截圖所示：

   ![Azure 門戶中的成本估計](./media/plan-manage-costs/cost-estimate-portal.png)

如果 Azure 訂閱具有支出限制，Azure 會阻止您花費超過信用額度。 創建和使用 Azure 資源時，將使用配額。 達到信用額度時，您部署的資源將禁用該計費週期的其餘部分。 您無法更改信用額度，但可以將其刪除。 有關支出限制的詳細資訊，請參閱[Azure 支出限制](../billing/billing-spending-limit.md)。

## <a name="use-budgets-and-cost-alerts"></a>使用預算和成本警示

您可以建立[預算](../cost-management/tutorial-acm-create-budgets.md)來管理成本，以及建立警示，以在出現異常消費和超支風險時自動通知利害關係人。 警示是以支出為基礎 (相較於預算和成本閾值)。 為 Azure 訂閱和資源組創建預算和警報，因此它們作為總體成本監視策略的一部分非常有用。 但是，它們可能具有管理單個 Azure 服務成本（如 Azure Cosmos DB 成本）的有限功能，因為它們旨在跟蹤更高級別的成本。

## <a name="monitor-costs"></a>監控成本

當您使用與 Azure Cosmos DB 一起使用資源時，會產生成本。 資源使用單位成本因時間間隔（秒、分鐘、小時和天）或請求單位使用方式而異。 Azure Cosmos DB 的使用一開始，就會產生成本，您可以在 Azure 門戶中[的成本分析](../cost-management/quick-acm-cost-analysis.md)窗格中看到它們。

使用成本分析時，可以在不同時間間隔的圖形和表中查看 Azure Cosmos DB 成本。 一些示例按天、當前、上月和年份進行。 您還可以根據預算和預測成本查看成本。 隨時間轉換到較長的視圖可以説明您確定支出趨勢，並瞭解支出可能發生在哪裡。 如果您已創建預算，您還可以輕鬆查看超出預算的超出位置。要查看成本分析中的 Azure 宇宙資料庫成本：

1. 登錄到[Azure 門戶](https://portal.azure.com)。

1. 打開**成本管理 + 計費**視窗，從功能表中選擇**成本管理**，然後選擇**成本分析**。 然後，可以從 **"範圍"** 下拉清單中更改特定訂閱的範圍。

1. 預設情況下，所有服務的成本都顯示在第一個環圈圖中。 選擇圖表中標記為"Azure 宇宙資料庫"的區域。

1. 要縮小單個服務（如 Azure Cosmos DB）的成本，請選擇 **"添加篩選器**"，然後選擇 **"服務名稱**"。 然後，從清單中選擇**Azure 宇宙 DB。** 下面是一個示例，顯示僅 Azure 宇宙 DB 的成本：
 
   ![使用成本分析窗格監控成本](./media/plan-manage-costs/cost-analysis-pane.png)

在前面的示例中，您可以看到 Azure Cosmos DB 在 2 月當月的目前成本。圖表還包含按位置和資源組顯示的 Azure Cosmos DB 成本。

## <a name="next-steps"></a>後續步驟

請參閱以下文章，瞭解有關定價在 Azure Cosmos DB 中的工作原理：

* [Azure Cosmos DB 的計價模式](how-pricing-works.md)
* [在 Azure Cosmos DB 中最佳化已佈建的輸送量成本](optimize-cost-throughput.md)
* [在 Azure Cosmos DB 中最佳化查詢成本](optimize-cost-queries.md)
* [在 Azure Cosmos DB 中最佳化儲存體成本](optimize-cost-storage.md)