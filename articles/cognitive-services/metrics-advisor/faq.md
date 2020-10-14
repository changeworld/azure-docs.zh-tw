---
title: 計量顧問常見問題
titleSuffix: Azure Cognitive Services
description: 關於計量顧問服務的常見問題。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: mbullwin
ms.openlocfilehash: 42b23876761afa213b07f07b3a61e125dcf0824b
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046803"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>計量顧問常見問題

### <a name="what-is-the-cost-of-my-instance"></a>我的實例的成本為何？

在預覽期間，使用您的實例目前不會產生任何費用。

### <a name="why-is-the-demo-website-readonly"></a>為什麼示範網站是唯讀的？

[示範網站](https://anomaly-detector.azurewebsites.net/)可公開取得。 此實例會設為唯讀，以防止意外上傳任何資料。

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>為何無法建立資源？ 「定價層」無法使用，且顯示「您已為此訂用帳戶建立了 1 S0」？

:::image type="content" source="media/pricing.png" alt-text="F0 資源已存在時的訊息":::

在公開預覽期間，只允許在單一區域中的訂用帳戶下建立一個計量建議程式實例。

如果您已經有使用相同訂用帳戶在相同區域中建立的實例，您可以嘗試不同的區域或不同的訂用帳戶來建立新的實例。 您也可以刪除現有的實例，以建立一個新的實例。

如果您已刪除現有的實例，但仍看到錯誤，請在資源刪除後等候大約20分鐘，然後再建立新的實例。

## <a name="basic-concepts"></a>基本概念

### <a name="what-is-multi-dimensional-time-series-data"></a>什麼是多維度的時間序列資料？

請參閱詞彙中的 [多維度度量](glossary.md#multi-dimensional-metric)  定義。

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>計量審查程式需要多少資料才能啟動異常偵測？

至少一個資料點可以觸發異常偵測。 不過，這並不會帶來最佳的精確度。 服務會假設先前資料點的視窗使用您在建立資料摘要期間指定為「填滿間距」規則的值。

建議您在想要偵測的時間戳記之前有一些資料。
根據資料的資料細微性，建議的資料量會依以下方式而有所不同。

| 細微度 | 建議用來偵測的資料量 |
| ----------- | ------------------------------------- |
| 少於5分鐘 | 4天的資料 |
| 5分鐘到1天 | 28天的資料 |
| 超過1天，至31天 | 4年的資料 |
| 超過31天 | 48年的資料 |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>為何計量建議程式無法偵測歷程記錄資料中的異常狀況？

計量審查程式是針對偵測即時串流資料而設計的。 服務將會回頭查看並執行異常偵測的歷程記錄資料的最大長度限制。 這表示只有在特定最早時間戳記之後的資料點才會有異常偵測結果。 最早的時間戳記取決於您資料的資料細微性。

根據資料的資料細微性，將會有異常偵測結果的歷程記錄資料長度如下所示。

| 細微度 | 異常偵測之歷程記錄資料的最大長度 |
| ----------- | ------------------------------------- |
| 少於5分鐘 | 上架時間-13 小時 |
| 5分鐘至小於1小時 | 上架時間-4 天  |
| 1小時到1天以內 | 上架時間-14 天  |
| 1 日 | 上架時間-28 天  |
| 大於1天，少於31天 | 上架時間-2 年  |
| 超過31天 | 上架時間-24 年   |

### <a name="more-concepts-and-technical-terms"></a>更多概念和技術術語

另請參閱 [詞彙](glossary.md) 以取得詳細資訊。

###  <a name="how-do-i-write-a-valid-query-for-ingesting-my-data"></a>如何? 撰寫有效的查詢來擷取我的資料嗎？  

若要讓計量建議程式內嵌您的資料，您必須建立查詢，以在單一時間戳傳回資料的維度。 計量顧問會多次執行此查詢，以取得每個時間戳記的資料。 

請注意，在指定的時間戳記中，查詢最多隻能針對每個維度組合傳回一筆記錄。 傳回的所有記錄都必須具有相同的時間戳記。 查詢不應傳回重複的記錄。

例如，假設您針對每日度量建立以下查詢： 
 
`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(DAY, 1, @StartTime)`

請務必針對您的時間序列使用正確的資料細微性。 針對每小時計量，您可以使用： 

`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(hour, 1, @StartTime)`

請注意，這些查詢只會傳回單一時間戳的資料，並包含計量建議程式要內嵌的所有維度組合。 

:::image type="content" source="media/query-result.png" alt-text="F0 資源已存在時的訊息" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>如何? 偵測到尖峰 & dip？

如果您預先定義了硬性閾值，您可以在 [異常偵測](how-tos/configure-metrics.md#anomaly-detection-methods)設定中實際手動設定「硬性閾值」。
如果沒有任何臨界值，您可以使用由 AI 提供技術支援的「智慧偵測」。 請參閱 [微調](how-tos/configure-metrics.md#tune-the-detecting-configuration) 偵測設定以取得詳細資料。

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>如何? 使用一般 (季節性來偵測 inconformity) 模式是否為異常？

「智慧型偵測」能夠學習您的資料模式，包括季節性模式。 然後，它會偵測不符合一般模式的資料點是否為異常。 請參閱 [微調](how-tos/configure-metrics.md#tune-the-detecting-configuration) 偵測設定以取得詳細資料。

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>如何? 將一般線條偵測為異常？

如果您的資料通常很不穩定，而且您想要在它變得太穩定或甚至變成平坦線時收到警示，您可以設定「變更臨界值」，以便在變更太小時偵測這類資料點。
請參閱 [異常偵測](how-tos/configure-metrics.md#anomaly-detection-methods) 設定以取得詳細資料。

## <a name="next-steps"></a>後續步驟
- [Metrics Advisor 概觀](overview.md)
- [試用示範網站](quickstarts/explore-demo.md)
- [使用入口網站](quickstarts/web-portal.md)