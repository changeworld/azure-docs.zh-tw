---
title: 伺服器端效能查詢
description: 如何透過 API 呼叫執行伺服器端效能查詢
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 9a28dee2d1e6d1355b729a56e8eeb8447e4ed8c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80682022"
---
# <a name="server-side-performance-queries"></a>伺服器端效能查詢

伺服器上的良好轉譯效能對於穩定的畫面播放速率和良好的使用者體驗很重要。 請務必小心監視伺服器上的效能特性，並在必要時進行優化。 您可以透過專用的 API 函數來查詢效能資料。

呈現效能的大部分影響力是模型輸入資料。 您可以調整輸入資料，如設定[模型轉換](../../how-tos/conversion/configure-model-conversion.md)中所述。

用戶端應用程式效能也可能是瓶頸。 若要深入分析用戶端效能，建議採取[效能追蹤](../../how-tos/performance-tracing.md)。

## <a name="clientserver-timeline"></a>用戶端/伺服器時間軸

在深入瞭解各種延遲值之前，請先查看時間軸上用戶端與伺服器之間的同步處理點：

![管線時間軸](./media/server-client-timeline.png)

下圖顯示如何：

* 用戶端會以常數 60-Hz 畫面播放速率（每16.6 毫秒）啟動*姿勢估計*
* 接著，伺服器會根據姿勢開始呈現
* 伺服器傳回編碼的影片影像
* 用戶端會將映射解碼、在其上執行一些 CPU 和 GPU 工作，然後顯示映射

## <a name="frame-statistics-queries"></a>框架統計資料查詢

畫面格統計資料會針對最後一個畫面格提供一些高階資訊，例如延遲。 `FrameStatistics`結構中提供的資料是在用戶端測量，因此 API 是同步呼叫：

````c#
void QueryFrameData(AzureSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
````

抓取的`FrameStatistics`物件會保存下列成員：

| member | 說明 |
|:-|:-|
| latencyPoseToReceive | 相機的延遲會導致用戶端裝置上的預估，直到此姿勢的伺服器框架完全可供用戶端應用程式使用為止。 此值包括網路往返、伺服器轉譯時間、影片解碼和抖動補償。 請參閱**上圖中的間隔1。**|
| latencyReceiveToPresent | 在用戶端應用程式對 CPU 呼叫 PresentFrame 之前，從已接收的遠端框架的可用性開始延遲。 |
| latencyPresentToDisplay  | 在 CPU 上呈現畫面格的延遲，直到顯示燈為止。 此值包括用戶端 GPU 時間、作業系統所執行的任何框架緩衝、硬體 reprojection，以及與裝置相關的顯示掃描時間。 請參閱**上圖中的間隔2。**|
| timeSinceLastPresent | 對 CPU 上的 PresentFrame 進行後續呼叫之間的時間。 大於顯示持續時間的值（例如 60-Hz 用戶端裝置上的16.6 毫秒）表示用戶端應用程式未及時完成其 CPU 工作負載所造成的問題。 請參閱**上圖中的間隔3。**|
| videoFramesReceived | 上一秒從伺服器接收的框架數目。 |
| videoFrameReusedCount | 過去一秒在裝置上使用的已接收框架數超過一次。 非零值表示必須重複使用框架，並因網路抖動或伺服器轉譯時間過多而 reprojected。 |
| videoFramesSkipped | 最後一秒已解碼，但因為較新的框架已到達而未顯示在顯示上的已接收框架數目。 非零值表示網路 jittering 造成多個框架延遲，然後在高載中同時抵達用戶端裝置。 |
| videoFramesDiscarded | 非常類似于**videoFramesSkipped**，但被捨棄的原因是畫面上出現了一段延遲，甚至無法再與任何暫止的姿勢相互關聯。 如果發生這種情況，就會發生一些嚴重的網路爭用。|
| videoFrameMinDelta | 在最後一秒抵達的兩個連續框架之間的最小時間量。 與 videoFrameMaxDelta 搭配使用時，此範圍會指出由網路或視頻編碼器所造成的抖動。 |
| videoFrameMaxDelta | 在最後一秒抵達的兩個連續框架之間的最大時間量。 與 videoFrameMinDelta 搭配使用時，此範圍會指出由網路或視頻編碼器所造成的抖動。 |

所有延遲值的總和通常會比 60 Hz 的可用框架時間大得多。 這是正常的，因為多個畫面會平行進行，而新的框架要求會以所需的畫面播放速率開始，如圖所示。 不過，如果延遲變得太大，它會影響[延遲階段 reprojection](../../overview/features/late-stage-reprojection.md)的品質，而且可能會危害整體體驗。

`videoFramesReceived`、 `videoFrameReusedCount`和`videoFramesDiscarded`可以用來測量網路和伺服器效能。 如果`videoFramesReceived`很低且`videoFrameReusedCount`非常高，這可能表示網路擁塞或伺服器效能不佳。 較高`videoFramesDiscarded`的值也表示網路擁塞。

最後，`timeSinceLastPresent` `videoFrameMinDelta`、和`videoFrameMaxDelta`會瞭解傳入的影片畫面和本機的目前電話的變異數。 高差異表示 instable 畫面播放速率。

上述任何值都不會清楚指出單純的網路延遲（圖例中的紅色箭號），因為必須從往返值`latencyPoseToReceive`中減去伺服器忙碌轉譯的確切時間。 整體延遲的伺服器端部分是用戶端無法使用的資訊。 不過，下一段將說明此值如何透過伺服器的其他輸入來逼近，並透過`networkLatency`值公開。

## <a name="performance-assessment-queries"></a>效能評定查詢

*效能評定查詢*可提供有關伺服器上 CPU 和 GPU 工作負載的更深入資訊。 因為資料是從伺服器要求，所以查詢效能快照集會遵循一般非同步模式：

``` cs
PerformanceAssessmentAsync _assessmentQuery = null;

void QueryPerformanceAssessment(AzureSession session)
{
    _assessmentQuery = session.Actions.QueryServerPerformanceAssessmentAsync();
    _assessmentQuery.Completed += (PerformanceAssessmentAsync res) =>
    {
        // do something with the result:
        PerformanceAssessment result = res.Result;
        // ...

        _assessmentQuery = null;
    };
}
```

與`FrameStatistics`物件相反， `PerformanceAssessment`物件包含伺服器端資訊：

| member | 說明 |
|:-|:-|
| timeCPU | 每個畫面格的平均伺服器 CPU 時間（毫秒） |
| timeGPU | 每個畫面的平均伺服器 GPU 時間（毫秒） |
| utilizationCPU | 伺服器 CPU 使用率總計（百分比） |
| utilizationGPU | 伺服器 GPU 總使用率（以百分比為單位） |
| memoryCPU | 伺服器主要記憶體使用率總計（百分比） |
| memoryGPU | 伺服器 GPU 的專用視頻記憶體使用率總計（以百分比表示） |
| networkLatency | 大約的平均往返網路延遲（以毫秒為單位）。 在上圖中，這會對應到紅色箭號的總和。 值的計算方式是將實際伺服器轉譯時間減去的`latencyPoseToReceive`值`FrameStatistics`。 雖然此近似值不精確，但它會提供網路延遲的一些指示，與用戶端上計算的延遲值隔離。 |
| polygonsRendered | 在一個畫面格中呈現的三角形數目。 此數目也包括在轉譯期間稍後挑選的三角形。 這表示，這個數位不會在不同的相機位置上有很大的差異，但效能可能會有大幅差異，視三角形的挑選率而定。|

為了協助您評估這些值，每個部分都有品質分類，例如**絕佳**、**良好**、**中等**或**不良**。
此評量度量可提供伺服器健全狀況的粗略指示，但不應視為絕對。 例如，假設您在 GPU 時間看到 ' 中等 ' 分數。 它會被視為中等，因為它會接近整體框架時間預算的限制。 不過，在您的案例中，它可能是很好的值，因為您正在轉譯複雜的模型。

## <a name="statistics-debug-output"></a>統計資料的調試輸出

類別`ARRServiceStats`會包裝框架統計資料和效能評估查詢，並提供便利的功能來將統計資料當做匯總值或預先建立的字串傳回。 下列程式碼是在用戶端應用程式中顯示伺服器端統計資料的最簡單方式。

``` cs
ARRServiceStats _stats = null;

void OnConnect()
{
    _stats = new ARRServiceStats();
}

void OnDisconnect()
{
    _stats = null;
}

void Update()
{
    if (_stats != null)
    {
        // update once a frame to retrieve new information and build average values
        _stats.Update(Service.CurrentActiveSession);

        // retrieve a string with relevant stats information
        InfoLabel.text = _stats.GetStatsString();
    }
}
```

上述程式碼會將文字標籤填入下列文字：

![ArrServiceStats 字串輸出](./media/arr-service-stats.png)

`GetStatsString` API 會將所有值的字串格式化，但每個單一值也可以透過程式設計方式從`ARRServiceStats`實例進行查詢。

也有成員的變異數，其會在一段時間後匯總值。 請參閱具有尾碼`*Avg`、 `*Max`或`*Total`的成員。 成員`FramesUsedForAverage`表示此匯總所使用的框架數目。

## <a name="next-steps"></a>後續步驟

* [建立效能追蹤](../../how-tos/performance-tracing.md)
* [設定模型轉換](../../how-tos/conversion/configure-model-conversion.md)
