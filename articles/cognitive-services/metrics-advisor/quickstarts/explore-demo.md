---
title: 開始探索 Metrics Advisor 示範
titleSuffix: Azure Cognitive Services
description: 使用我們提供的示範，了解 Metrics Advisor 的 Web 介面
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: fbc73e20b8cc2baa1cc5c5a5b2f674fb1b2dde84
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943895"
---
# <a name="quickstart-explore-the-metrics-advisor-demo-with-example-data"></a>快速入門：探索含有範例資料的 Metrics Advisor 示範

> [!Note]
> Metrics Advisor 示範是唯讀的，您無法將資料上線。 若要在您的資料上使用服務，請先[建立 Metrics Advisor 資源](web-portal.md)。

使用本文快速開始探索 Metrics Advisor 中的重要功能。 我們會提供包含範例資料和預設組態的示範網站，讓您熟悉功能完整的入口網站。

按一下[此連結](https://aka.ms/MetricsAdvisor/Demo)移至示範網站。

## <a name="view-the-available-sample-data"></a>檢視可用的範例資料

登入示範網站後，您會看到**資料摘要**頁面。 資料摘要是時間序列資料的邏輯群組，會從您的資料來源進行查詢。 如需有關 Metrics Advisor 中所使用之詞彙和概念的詳細資訊，請參閱[詞彙](../glossary.md)。 

系統會列出數個資料摘要，這是從不同類型的資料來源 (例如 Azure SQL database 或 Azure 資料表) 擷取的。 每個資料摘要都會使用稍微不同的組態設定來連線到相關聯的資料存放區。

:::image type="content" source="../media/sample-datafeeds.png" alt-text="範例資料清單" lightbox="../media/sample-datafeeds.png":::

## <a name="explore-the-data-feed-configurations"></a>探索資料摘要組態

按一下「範例 - 成本/收益 - 城市/類別」資料摘要。 您會看到摘要詳細資料的幾個區段：

* 資料摘要名稱和內嵌狀態。
* 從資料來源查詢的計量清單。 例如「成本」和「收益」。 
* 當資料摘要無法使用時的警示歷程記錄。 
* 更新資料摘要時的記錄。   
* 資料摘要資訊和設定。

:::image type="content" source="../media/data-feed-view.png" alt-text="資料摘要檢視" lightbox="../media/data-feed-view.png":::


## <a name="view-time-series-visualizations-and-configurations"></a>檢視時間序列視覺效果和組態

按一下「範例 - 成本/收益 - 城市/類別」資料摘要中的「成本」計量。 您會看到依維度配置的相關聯時間序列，並根據歷程記錄計量資料來呈現視覺效果。 計量資料周圍的藍色區域代表來自 Metrics Advisor 機器學習模型的預期值範圍。 落在此範圍外的點會標示為紅色，表示偵測到異常狀況。 

:::image type="content" source="../media/series-visualization.png" alt-text="序列視覺效果" lightbox="../media/series-visualization.png":::

您可以微調計量詳細資料頁面左側的**偵測組態**，來設定異常偵測。 您可以使用多種異常偵測方法，並且可以將各種方法結合在一起使用。 您還可以嘗試不同的敏感性、偵測方向和其他組態。 **偵測組態**底部的**進階組態**連結，可讓您建立更複雜且自訂的偵測設定，可用於群組或個別的序列。 

您也可以對偵測演算法提供意見反應，以微調異常偵測。 按一下進入異常狀態，然後使用**新增意見反應**面板設定其異常狀態、季節性和變更點狀態。 未來偵測點時系統會納入此意見反應。  

在**新增意見反應**面板的底部，會顯示**前往事件中樞**的連結，此連結會將您導向事件分析頁面，並分析事件的根本原因。  

:::image type="content" source="../media/incident-link.png" alt-text="事件連結" lightbox="../media/incident-link.png":::

## <a name="explore-anomaly-detection-results-and-perform-root-cause-analysis"></a>探索異常偵測結果並執行根本原因分析

按一下異常中的**前往事件中樞**連結時，您會看到事件分析頁面，其中包含有關事件的診斷深入解析，例如嚴重性、涉及的異常次數，以及開始/結束時間。 **根本原因**區段會藉由分析事件樹狀結構自動顯示建議，並將其納入考量，例如對父系異常的偏差、散佈和比重，這些都可能是事件的根本原因。

**診斷**區段會顯示事件的樹狀結構，以及用來診斷事件的數個索引標籤。

:::image type="content" source="../media/incident-diagnostic.png" alt-text="事件診斷" lightbox="../media/incident-diagnostic.png":::

藉由查明事件的根本原因，您可以採取動作並減輕問題產生的影響，避免出現更糟的情況。 您也可以按一下所提供的其他診斷功能，以探索更多深入解析。 

## <a name="next-steps"></a>後續步驟

- [使用入口網站](web-portal.md)
- [使用 REST API](rest-api.md)
- [將資料摘要上線](../how-tos/onboard-your-data.md)
    - [管理資料摘要](../how-tos/manage-data-feeds.md)
    - [不同資料來源的組態](../data-feeds-from-different-sources.md)
