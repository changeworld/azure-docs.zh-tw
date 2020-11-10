---
title: 設定以事件為基礎的視頻錄製的信號閘道-Azure
description: 本文提供如何在媒體圖形中設定信號閘道的指引。
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: afcec7c03f1353f08b58311278f5a533e0c911bc
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410788"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>設定以事件為基礎的影片錄製的信號閘道

在 media graph 中，「 [信號閘道處理器」節點](media-graph-concept.md#signal-gate-processor) 可讓您在閘道由事件觸發時，將媒體從某個節點轉送到另一個節點。 觸發此閘道時，閘道會開啟，並讓媒體流經指定的持續時間。 如果沒有事件觸發閘道，閘道就會關閉，而媒體也會停止流動。 您可以使用信號閘道處理器進行以事件為基礎的影片錄製。

在本文中，您將瞭解如何設定信號閘道處理器。

## <a name="suggested-prereading"></a>建議的 prereading
-   [媒體圖表](media-graph-concept.md)
-   [發生事件時錄製影片](event-based-video-recording-concept.md)


## <a name="problem"></a>問題
使用者可能會想要在事件觸發閘道之前或之後的特定時間開始錄製。 使用者知道其系統內可接受的延遲。 因此，他們想要指定信號閘道處理器的延遲。 他們也會想要指定記錄的最小和最大持續時間，無論收到多少個新事件。
 
### <a name="use-case-scenario"></a>使用案例
假設您想要在每次建築物的前門開啟時錄製影片。 您希望記錄： 

- 在大門開啟之前包含 *X* 秒。 
- 如果未再次開啟門，則為最後至少 *Y* 秒。 
- 如果已重複開啟門，則會在最多 *Z* 秒內最後一次。 
 
您知道您的大門感應器的延遲為 *K* 秒。 若要減少事件在延遲抵達時被忽略的機率，您想要允許至少 *K* 秒的時間到達事件。


## <a name="solution"></a>解決方案

若要解決此問題，請修改您的信號閘道處理器參數。

若要設定信號閘道處理器，請使用下列四個參數：
- 啟用評估視窗
- 啟用信號位移
- 最小啟用視窗
- 啟用視窗上限

觸發信號閘道處理器時，它會在最小啟用時間保持開啟狀態。 啟動事件是從最早事件的時間戳記開始，再加上啟用信號位移。 

如果信號閘道處理器在開啟時再次觸發，計時器會重設，且閘道至少會保持開啟狀態，至少會有最短的啟用時間。 信號閘道處理器永遠保持開啟的時間超過最大啟用時間。 

事件 (事件 1) 發生在另一個事件 (事件 2) （根據媒體時間戳記），如果系統延遲且事件1在事件2之後抵達信號閘道處理器，則可以忽略此事件。 如果事件1到達事件2和啟用評估視窗的抵達之間，則會忽略事件1。 它不會透過信號閘道處理器傳遞。 

每個事件都會設定相互關聯識別碼。 這些識別碼是從初始事件進行設定。 針對下列每個事件，它們都是連續的。

> [!IMPORTANT]
> 媒體時間是以媒體中發生事件時的媒體時間戳記為基礎。 抵達信號閘道的事件順序可能不會反映抵達媒體時間的事件順序。


### <a name="parameters-based-on-the-physical-time-that-events-arrive-at-the-signal-gate"></a>參數，以事件抵達信號閘道的實際時間為基礎

* **minimumActivationTime (記錄) 的最短可能持續時間** ：在觸發接收新事件之後，信號閘道處理器保持開啟的最小秒數，除非 maximumActivationTime 已中斷。
* **maximumActivationTime (記錄) 的最長可能持續時間** ：初始事件的最大秒數，在觸發這些事件後，信號閘道處理器會保持開啟狀態，而不論收到的事件為何。
* **activationSignalOffset** ：啟用信號閘道處理器與開始錄製影片之間的秒數。 通常，這個值是負數，因為它會在觸發事件之前開始錄製。
* **activationEvaluationWindow** ：從初始觸發事件開始，在初始事件之前發生的事件（以媒體時間為單位），在被忽略並視為延遲抵達之前，必須先抵達信號閘道處理器。

> [!NOTE]
> *延遲抵達* 是在啟用評估視窗經過之後，但在媒體時間內的初始事件之前抵達的任何事件。

### <a name="limits-of-parameters"></a>參數的限制

* **activationEvaluationWindow** ：0秒到10秒
* **activationSignalOffset** ：-1 分鐘到1分鐘
* **minimumActivationTime** ：1秒到1小時
* **maximumActivationTime** ：1秒到1小時


在使用案例中，您會設定參數，如下所示：

* **activationEvaluationWindow** ： *K* 秒
* **activationSignalOffset** ： *-X* 秒
* **minimumActivationWindow** ： *Y* 秒
* **maximumActivationWindow** ： *Z* 秒


以下是「 **信號閘道處理器** 節點」區段如何查看下列參數值之媒體圖形拓撲的範例：
* **activationEvaluationWindow** ：1秒
* **activationSignalOffset** ：-5 秒
* **minimumActivationTime** ：20秒
* **maximumActivationTime** ：40秒

> [!IMPORTANT]
> 每個參數值都必須要有[ISO 8601 持續時間格式](https://en.wikipedia.org/wiki/ISO_8601#Durations
)。 例如，PT1S = 1 秒。


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


現在請考慮此信號閘道處理器設定在不同錄製案例中的行為。

### <a name="recording-scenarios"></a>錄製案例

**一個來源 ( *一般啟用* ) 的事件**

接收一個事件的信號閘道處理器會產生記錄，此記錄會在事件抵達閘道之前，啟動5秒鐘 (啟用信號 = 5) 秒。 錄製的其餘部分是20秒 (最小啟用時間 = 20 秒) ，因為在重新觸發閘道的最小啟動時間結束之前沒有其他事件到達。

範例圖：
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="顯示一個來源的一個事件正常啟用的圖表。":::

* 錄製期間 =-offset + minimumActivationTime = [E1 + offset，E1 + minimumActivationTime]


**來自一個來源的兩個事件 ( *retriggered 啟用* )**

接收兩個事件的信號閘道處理器會產生記錄，以在事件抵達閘道之前的5秒 (啟用信號位移 = 5) 秒。 此外，事件2會在事件1之後抵達5秒。 因為事件2在事件1的最小啟動時間結束之前抵達 (20 秒) ，所以閘道 retriggered。 其餘記錄是20秒 (最小啟用時間 = 20 秒) ，因為在從事件2開始的最小啟動時間結束之前沒有其他事件可重新觸發閘道。

範例圖：
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="此圖顯示一個來源的兩個事件的 retriggered 啟用。":::

* 錄製的持續時間 =-事件2的位移 + (抵達-事件1的抵達) + minimumActivationTime


**來自一個來源 ( *最大啟用* ) 的 *N* 個事件**

接收 *N* 個事件的信號閘道處理器會產生記錄，此記錄會在第一個事件抵達閘道之前，開始5秒鐘 (啟用信號位移 = 5 秒) 。 當每個事件在上一次事件的最小啟用時間（20秒）結束之前抵達時，閘道會持續 retriggered。 它會保持開啟狀態，直到第一個事件之後的40秒啟用時間上限為止。 然後，閘道會關閉，不再接受任何新的事件。

範例圖：
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="顯示單一來源的 N 個事件的最大啟用數目的圖表。":::
 
* 錄製期間 =-offset + maximumActivationTime

> [!IMPORTANT]
> 上述圖表假設每個事件都會在實體時間和媒體時間的相同時刻抵達。 也就是說，他們假設沒有遲到的抵達。

## <a name="next-steps"></a>後續步驟

試用以 [事件為基礎的影片記錄教學](event-based-video-recording-tutorial.md)課程。 從編輯 [topology.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)開始著手。 修改 [signalgateProcessor] 節點的參數，然後遵循本教學課程的其餘部分。 檢查影片錄製以分析參數的效果。



