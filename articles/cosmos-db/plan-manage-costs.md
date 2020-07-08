---
title: 規劃和管理 Azure Cosmos DB 的成本
description: 瞭解如何使用 Azure 入口網站中的成本分析來規劃和管理 Azure Cosmos DB 的成本。
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: 42421f745759d9aee75b285c3fbc6ea7217ba5c0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85112696"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>規劃和管理 Azure Cosmos DB 的成本

本文說明如何規劃和管理 Azure Cosmos DB 的成本。 首先，您可以使用 Azure Cosmos DB 容量計算機來協助規劃成本，然後再新增任何資源。 接下來，當您新增 Azure 資源時，您可以查看估計成本。 在您開始使用 Azure Cosmos DB 資源之後，請使用成本管理功能來設定預算和監視成本。 您也可以查看預測的成本，並識別支出趨勢，以識別您可能想要採取行動的區域。

瞭解 Azure Cosmos DB 的成本只是 Azure 帳單中每月成本的一部分。 如果您使用其他 Azure 服務，則會向您收取 Azure 訂用帳戶中所使用的所有 Azure 服務和資源的費用，包括協力廠商服務。 本文說明如何規劃和管理 Azure Cosmos DB 的成本。 在您熟悉如何管理 Azure Cosmos DB 的成本之後，您可以套用類似的方法來管理您的訂用帳戶中所使用之所有 Azure 服務的成本。

## <a name="prerequisites"></a>必要條件

成本分析支援不同的 Azure 帳戶類型。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](../cost-management-billing/costs/understand-cost-mgt-data.md)。 若要檢視成本資料，您至少需要 Azure 帳戶的讀取存取。 如需 Azure 成本管理資料的存取權指派相關資訊，請參閱[指派資料的存取權](../cost-management-billing/costs/assign-access-acm-data.md)。

## <a name="review-estimated-costs-with-capacity-calculator"></a>使用容量計算機來審查估計成本

在 Azure Cosmos 帳戶中建立資源之前，請使用[Azure Cosmos DB 容量計算機](https://cosmos.azure.com/capacitycalculator/)來預估成本。 容量計算機可用來估計所需的輸送量和工作負載的成本。 將您的 Azure Cosmos 資料庫和容器設定為適當數量的布建輸送量，或針對您的工作負載設定[要求單位（RU/秒）](request-units.md)，對於將成本和效能優化非常重要。 您必須輸入詳細資料，例如 API 類型、區域數目、專案大小、每秒的讀取/寫入要求、儲存用來取得成本估計值的總數。 若要深入瞭解容量計算機，請參閱[預估](estimate-ru-with-capacity-planner.md)文章。

下列螢幕擷取畫面顯示使用容量計算機的輸送量和成本估計：

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Azure Cosmos DB 容量計算機中的成本預估":::

## <a name="review-estimated-costs-from-the-azure-portal"></a>查看 Azure 入口網站的預估成本

當您從 Azure 入口網站建立 Azure Cosmos DB 資源時，您可以看到估計的成本。 使用下列步驟來審查成本預估：

1. 登入 Azure 入口網站，然後流覽至您的 Azure Cosmos 帳戶。
1. 移至 [**資料總管**]。
1. 建立新的容器，例如圖形容器。
1. 輸入您的工作負載所需的輸送量，例如 400 RU/秒。 輸入輸送量值之後，您可以看到價格預估，如下列螢幕擷取畫面所示：

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Azure 入口網站中的成本預估":::

如果您的 Azure 訂用帳戶有消費限制，Azure 就會讓您無法花費超過點數金額。 當您建立和使用 Azure 資源時，會使用您的點數。 達到點數限制時，您部署的資源會在該計費期間的剩餘時間內停用。 您無法變更您的點數限制，但您可以將其移除。 如需有關消費限制的詳細資訊，請參閱[Azure 消費限制](../billing/billing-spending-limit.md)。

## <a name="use-budgets-and-cost-alerts"></a>使用預算和成本警示

您可以建立[預算](../cost-management/tutorial-acm-create-budgets.md)來管理成本，以及建立警示，以便在出現異常消費和超支風險時自動通知利害關係人。 警示是以支出為基礎 (相較於預算和成本閾值)。 預算和警示是針對 Azure 訂用帳戶和資源群組所建立，因此在整體成本監視策略中相當有用。 不過，它們的功能可能有限，可以管理個別的 Azure 服務成本，例如 Azure Cosmos DB 的成本，因為它們是設計來追蹤較高層級的成本。

## <a name="monitor-costs"></a>監視成本

當您將資源與 Azure Cosmos DB 搭配使用時，會產生成本。 資源使用量單位成本會因時間間隔（秒、分鐘、小時、天）或要求單位使用量而異。 一旦開始使用 Azure Cosmos DB，就會產生成本，而您可以在 Azure 入口網站的 [[成本分析](../cost-management/quick-acm-cost-analysis.md)] 窗格中看到它們。

當您使用成本分析時，可以針對不同的時間間隔，查看圖形和資料表中的 Azure Cosmos DB 成本。 部分範例包括日期、目前、上個月和年。 您也可以根據預算和預測成本來查看成本。 隨著時間切換到較長的視圖，可協助您找出支出趨勢，並查看超支可能發生的位置。 如果您已建立預算，您也可以輕鬆地查看其超出的位置。若要在成本分析中查看 Azure Cosmos DB 成本：

1. 登入[Azure 入口網站](https://portal.azure.com)。

1. 開啟 [**成本管理 + 計費**] 視窗，從功能表中選取 [**成本管理**]，然後選取 [**成本分析**]。 然後，您可以從 [**範圍**] 下拉式清單中變更特定訂用帳戶的範圍。

1. 根據預設，所有服務的成本會顯示在第一個環圈圖中。 選取圖表中標示為「Azure Cosmos DB」的區域。

1. 若要縮小單一服務的成本（例如 Azure Cosmos DB），請選取 [**新增篩選**]，然後選取 [**服務名稱**]。 然後，從清單中選擇 [ **Azure Cosmos DB** ]。 以下範例顯示只 Azure Cosmos DB 的成本：
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="使用成本分析窗格監視成本":::

在上述範例中，您會看到二月月份 Azure Cosmos DB 的目前成本。這些圖表也包含依位置和資源群組 Azure Cosmos DB 的成本。

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以深入瞭解定價在 Azure Cosmos DB 中的運作方式：

* [Azure Cosmos DB 中的計價模式](how-pricing-works.md)
* [在 Azure Cosmos DB 中最佳化已佈建的輸送量成本](optimize-cost-throughput.md)
* [在 Azure Cosmos DB 中最佳化查詢成本](optimize-cost-queries.md)
* [在 Azure Cosmos DB 中最佳化儲存體成本](optimize-cost-storage.md)