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
ms.date: 11/05/2020
ms.author: mbullwin
ms.openlocfilehash: 0c4c296cb1454ed89eef102732533589b1c8ca0d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420951"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>計量顧問常見問題

### <a name="what-is-the-cost-of-my-instance"></a>我的實例的成本為何？

在預覽期間，使用您的實例目前不會產生任何費用。

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>為何無法建立資源？ 「定價層」無法使用，且顯示「您已為此訂用帳戶建立了 1 S0」？

:::image type="content" source="media/pricing.png" alt-text="F0 資源已存在時的訊息":::

在公開預覽期間，只能在訂用帳戶下的每個區域中建立一個計量建議程式實例。

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

:::image type="content" source="media/query-result.png" alt-text="具有一個時間戳記的查詢結果" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>如何? 偵測到尖峰 & dip？

如果您預先定義了硬性閾值，您可以在 [異常偵測](how-tos/configure-metrics.md#anomaly-detection-methods)設定中實際手動設定「硬性閾值」。
如果沒有任何臨界值，您可以使用由 AI 提供技術支援的「智慧偵測」。 請參閱 [微調](how-tos/configure-metrics.md#tune-the-detecting-configuration) 偵測設定以取得詳細資料。

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>如何? 使用一般 (季節性來偵測 inconformity) 模式是否為異常？

「智慧型偵測」能夠學習您的資料模式，包括季節性模式。 然後，它會偵測不符合一般模式的資料點是否為異常。 請參閱 [微調](how-tos/configure-metrics.md#tune-the-detecting-configuration) 偵測設定以取得詳細資料。

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>如何? 將一般線條偵測為異常？

如果您的資料通常很不穩定，而且您想要在它變得太穩定或甚至變成平坦線時收到警示，您可以設定「變更臨界值」，以便在變更太小時偵測這類資料點。
請參閱 [異常偵測](how-tos/configure-metrics.md#anomaly-detection-methods) 設定以取得詳細資料。

### <a name="how-to-set-up-email-settings-and-enable-alerting-by-email"></a>如何設定電子郵件設定，並透過電子郵件啟用警示？

1.  具有訂用帳戶管理員或資源群組系統管理員許可權的使用者，必須流覽至在 Azure 入口網站中建立的計量建議程式資源，然後選取 [ **存取控制] (IAM)** ] 索引標籤。 
2.  選取 [ **新增角色指派** ]
3.  選擇 **認知服務計量審查程式系統管理員** 的角色，然後選取您的帳戶，如下圖所示。
4.  按一下 [ **儲存** ] 按鈕，即表示您已成功新增為計量建議程式資源的系統管理員。 請注意，所有上述動作都必須由訂用帳戶管理員或資源群組系統管理員執行。 

:::image type="content" source="media/access-control.png" alt-text="[存取控制] (IAM) ] 功能表頁面上已選取 [新增角色指派]，後面接著一個方塊，其中具有 [認知服務計量顧問系統管理員] 存取角色所顯示的 [指派使用者存取權]，後面接著選取的 UI 的 [儲存] 按鈕，以說明搜尋使用者及新增特定層級的存取權限的步驟。" lightbox="media/access-control.png":::


5.  傳播許可權最多可能需要一分鐘的時間。 然後，選取您的計量建議程式工作區，然後選取左側導覽面板中的 [ **電子郵件] 設定** 選項。 填寫必要的專案，特別是與 SMTP 相關的資訊。 
6.  選取 [ **儲存** ]，您就可以設定電子郵件設定。 您可以針對近乎即時的警示建立新的勾點並訂閱計量異常。 

## <a name="advanced-concepts"></a>進階概念

### <a name="how-does-metric-advisor-build-an-incident-tree-for-multi-dimensional-metrics"></a>計量審查程式如何建立多維度計量的事件樹狀結構？

度量可以依維度分割成多個時間序列。 例如， `Response latency` 會針對小組擁有的所有服務監視度量。 `Service`類別目錄可以用來做為擴充計量的維度，因此我們會 `Response latency` 依 `Service1` 、等來分割 `Service2` 。 每個服務都可以部署在多個資料中心的不同電腦上，因此度量可以進一步依 `Machine` 和分割 `Data center` 。

|服務| 資料中心| 電腦  | 
|----|------|----------------   |
| S1 |  DC1 |   M1 |
| S1 |  DC1 |   M2 |
| S1 |  DC2 |   M3 |
| S1 |  DC2 |   M4 |
| S2 |  DC1 |   M1 |
| S2 |  DC1 |   M2 |
| S2 |  DC2 |   M5 |
| S2 |  DC2 |   M6 |
| ...|      |      |

從總計開始 `Response latency` ，我們可以依、和來向下切入 `Service` 度量 `Data center` `Machine` 。 不過，可能更適合服務擁有者使用路徑 `Service`  ->  `Data center`  ->  `Machine` ，或可能更適合基礎結構工程師使用此路徑 `Data Center`  ->  `Machine`  ->  `Service` 。 這完全取決於使用者的個別商務需求。 

在計量建議程式中，使用者可以指定想要從階層式拓撲的一個節點向下切入或匯總的任何路徑。 更精確地說，階層式拓撲是有向非迴圈的圖形，而不是樹狀結構。 有完整的階層式拓撲，其中包含所有可能的維度組合，如下所示： 

:::image type="content" source="media/dimension-combinations-view.png" alt-text="階層式拓撲圖包含多個互連頂點和邊緣，其中有多個標記為 S、DC 和 M 的維度，其對應數位範圍介於1到6之間" lightbox="media/dimension-combinations-view.png":::

理論上，如果維度 `Service` 有 `Ls` 相異的值、維度 `Data center` 有 `Ldc` 相異的值，而且維度 `Machine` 有相異的值 `Lm` ，則階層式拓撲中可能會有 `(Ls + 1) * (Ldc + 1) * (Lm + 1)` 維度組合。 

但通常並非所有的維度組合都有效，這可以大幅降低複雜度。 目前，如果使用者匯總了度量本身，就不會限制維度的數目。 如果您需要使用計量審查程式所提供的匯總功能，維度數目不應超過6。 不過，我們會將計量的時間序列數目限制為小於10000的維度。

[診斷] 頁面中的 [ **事件樹狀結構** ] 工具只會顯示偵測到異常的節點，而不是整個拓撲。 這是為了協助您專注于目前的問題。 它也可能不會顯示度量內的所有異常，而是會根據投稿顯示最高的異常。 如此一來，我們可以快速找出異常資料的影響、範圍和散佈路徑。 這可大幅減少我們必須專注的異常數量，並協助使用者瞭解並找出其主要問題。 
 
例如，在發生異常狀況時 `Service = S2 | Data Center = DC2 | Machine = M5` ，異常的偏差會影響同時偵測到異常的父節點 `Service= S2` ，但異常情況並不會影響上的整個資料中心 `DC2` 和上的所有服務 `M5` 。 事件樹狀結構的建立方式如下列螢幕擷取畫面所示，最常見的異常是在上捕捉， `Service = S2` 而根本原因可在兩個路徑中進行分析 `Service = S2 | Data Center = DC2 | Machine = M5` 。

 :::image type="content" source="media/root-cause-paths.png" alt-text="5個標示為頂點的頂點，其中有兩個不同的路徑以邊緣與標示 S2 的一般節點連接。最常見的異常是在服務 = S2 上所捕捉，而根本原因可以由兩個路徑來分析，而這兩個路徑會導致服務 = S2 |資料中心 = DC2 |電腦 = M5" lightbox="media/root-cause-paths.png":::

## <a name="next-steps"></a>後續步驟
- [Metrics Advisor 概觀](overview.md)
- [使用入口網站](quickstarts/web-portal.md)