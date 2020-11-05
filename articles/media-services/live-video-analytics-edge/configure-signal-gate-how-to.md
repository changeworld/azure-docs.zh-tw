---
title: 設定以事件為基礎的視頻錄製的信號閘道-Azure
description: 本文提供如何在媒體圖形中設定信號閘道的指引。
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: 4204a43915f9c79cae7dfe82b37e741fee89fb4a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93380140"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>設定以事件為基礎的影片錄製的信號閘道

在 media graph 中，當事件觸發閘道時，[ [信號閘道處理器] 節點](media-graph-concept.md#signal-gate-processor) 可讓您將媒體從某個節點轉送到另一個節點。 觸發此閘道時，閘道會開啟並讓媒體流經指定的持續時間。 如果沒有事件觸發閘道，閘道將會關閉，且媒體會停止流動。 信號閘道處理器適用于以事件為基礎的影片錄製。
在本文中，您將瞭解如何設定信號閘道處理器的詳細資料。

## <a name="suggested-pre-reading"></a>建議的閱讀準備事項
-   [媒體圖表](media-graph-concept.md)
-   [發生事件時錄製影片](event-based-video-recording-concept.md)


## <a name="problem"></a>問題
使用者可能會想要在事件觸發閘道之前或之後，開始錄製特定的時間。 使用者知道其系統內可接受的延遲，因此使用者想要指定信號閘道處理器的延遲。 使用者想要指定記錄的持續時間最短和最長的時間，不論收到多少新的事件。
 
### <a name="use-case-scenario"></a>使用案例
假設您想要在每次建築物的前門開啟時錄製影片。 您希望在錄製中包含開啟門之前的 **X** 秒。 如果未再次開啟門，您希望錄製的時間至少為 **Y** 秒。 如果已重複開啟門，您想要在最多 **Z** 秒的時間內最後錄製。 您知道您的大門感應器的延遲為 **K** 秒，而且想要減少事件被忽略的機率 ( 「延遲抵達」 ) ，因此您想要允許至少 **K** 秒的時間到達事件。


## <a name="solution"></a>解決方案

**_修改信號閘道處理器參數_* _

信號閘道處理器是由4個參數設定：
- _ *啟用評估視窗**
- **啟用信號位移**
- **最小啟用視窗**
- **最大啟用視窗** 。 

觸發信號閘道處理器時，它會在最小啟用時間保持開啟狀態。 啟動事件是從最早事件的時間戳記開始，再加上啟用信號位移。 如果再次觸發信號閘道處理器，則在開啟時，計時器會重設，且閘道至少會保持開啟狀態，至少會有最短的啟用時間。 信號閘道處理器永遠不會保持開啟狀態超過最大啟用時間。 事件 **(事件 1)** 發生在另一個事件 **(事件 2)** （根據媒體時間戳記），如果系統延遲且 **事件 1** 在 **事件 2** 之後抵達信號閘道處理器，則會被忽略。 如果事件 **1** 到達 **事件 2** 和 **啟用評估** 期間的抵達之間，系統將會忽略 **事件 1** ，且不會透過信號閘道處理器傳遞事件1。 每個事件都會設定相互關聯識別碼。 這些識別碼是從初始事件設定的，而且是每個事件的順序。

> [!IMPORTANT]
> 媒體時間是以媒體中發生事件的時間戳記為基礎。 到達信號閘道的事件順序可能不會反映抵達媒體時間的事件順序。


### <a name="parameters-based-on-when-events-arrive-in-physical-time-to-the-signal-gate"></a>參數：根據事件何時抵達信號閘道的實體時間 () 

* **minimumActivationTime (記錄的最短可能持續時間)** = 觸發接收新事件之後，信號閘道處理器將保持開啟狀態的最小秒數，除非 **maximumActivationTime** 中斷
* **maximumActivationTime (可能記錄的最長持續時間)** = 觸發以接收新的事件時，發出信號閘道處理器將保持開啟狀態的初始事件最大秒數，無論收到的事件為何
* **activationSignalOffset** = 啟動信號閘道處理器與影片錄製開始之間的秒數，通常此值為負數，以便在觸發事件之前開始錄製
* **activationEvaluationWindow** = 從初始觸發事件開始的秒數，在這種情況下，在初始事件之前發生的事件（以媒體時間為單位）必須先抵達信號閘道處理器，然後才會被忽略並視為「延遲抵達」

> [!NOTE]
> 延遲抵達是啟用評估視窗經過之後抵達的任何事件，但是此事件會在媒體時間的初始事件之前抵達。

### <a name="limits-of-parameters"></a>參數的限制

* **activationEvaluationWindow：0秒到10秒**

* **activationSignalOffset：-1 分鐘到1分鐘**

* **minimumActivationTime：1秒到1小時**

* **maximumActivationTime：1秒到1小時**


根據使用案例，參數將設定如下：

* **activationEvaluationWindow = K 秒**
* **activationSignalOffset =-X 秒**
* **minimumActivationWindow = Y 秒**
* **maximumActivationWindow = Z 秒**


以下是「信號閘道處理器節點」區段在下列參數值的 media graph 拓撲中所需的範例：
* **activationEvaluationWindow = 1 秒**
* **activationSignalOffset =-5 秒**
* **minimumActivationTime = 20 秒**
* **maximumActivationTime = 40 秒**

> [!IMPORTANT]
> 每個參數值都必須要有[ISO 8601 持續時間格式](https://en.wikipedia.org/wiki/ISO_8601#Durations
)。 
**例如： PT1S = 1 秒**


```
"processors":              
[
          {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "iotMessageSource"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "-PT5S",
            "minimumActivationTime": "PT20S",
            "maximumActivationTime": "PT40S"
          }
]
```


讓我們來看看此信號閘道處理器設定在不同錄製案例中的行為。


**1個來源中的1個事件 ( *正常啟用* )**

接收一個事件的信號閘道處理器會產生記錄，此記錄會在事件抵達閘道之前，開始「啟用信號位移」 (5) 秒。 錄製的其餘部分是「最小啟用時間」 (20) 秒長度，因為在重新觸發閘道的最小啟用時間完成之前，不會到達任何其他事件。

範例圖：
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="正常啟用":::

* 錄製期間 =-offset + minimumActivationTime = [E1 + offset，E1 + minimumActivationTime]


**從1來源 ( *Retriggered 啟用* ) 的2個事件**

接收兩個事件的信號閘道處理器會產生記錄，此記錄會在第一個事件抵達閘道之前，開始「啟用信號位移」 (五) 秒。 第二個事件會在第一個事件的五秒後抵達，也就是第一個事件的「最小啟用時間」 (20) 秒之前完成，因此閘道 retriggered 保持開啟狀態。 錄製的其餘部分是「最小啟用時間」 (20) 秒長度，因為第二個事件的最小啟動時間完成後，不會有其他事件到達之後再次重新觸發閘道。

範例圖：
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="Retriggered 啟用":::

* 錄製的持續時間 =-第1個事件的第二個事件抵達的位移 + (抵達) + minimumActivationTime


**來自1個來源 ( *最大啟用* ) 的 N 個事件**

收到 N 個事件的信號閘道處理器會產生記錄，此記錄會在第一個事件抵達閘道之前，啟動「啟用信號位移」 (5) 秒。 當每個事件在完成前一個事件的「最小啟用時間」 (20) 秒之前，閘道會持續 retriggered 並保持開啟，直到第一個事件的「最大啟用時間」 (40) 秒為止，其中閘道會關閉且不再接受任何新的事件。

範例圖：
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="啟用上限":::
 
* 錄製期間 =-offset + maximumActivationTime

> [!IMPORTANT]
> 圖表假設每個事件都會在實體和媒體時間抵達相同的實例。  (沒有延遲抵達) 

## <a name="next-steps"></a>後續步驟

### <a name="try-it-out"></a>立即試用

[以事件為基礎的影片記錄教學課程](event-based-video-recording-tutorial.md)

使用以事件為基礎的影片記錄教學課程、編輯 [topology.js開啟](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)、修改 signalgateProcessor 節點的參數，然後遵循本教學課程的其餘部分。 檢查影片錄製以分析參數的效果。



