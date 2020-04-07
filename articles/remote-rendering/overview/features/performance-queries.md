---
title: 伺服器端效能查詢
description: 如何透過 API 呼叫執行伺服器端效能查詢
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 9a28dee2d1e6d1355b729a56e8eeb8447e4ed8c8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682022"
---
# <a name="server-side-performance-queries"></a>伺服器端效能查詢

良好的伺服器上的渲染性能對於穩定的幀速率和良好的用戶體驗至關重要。 仔細監視伺服器上的性能特徵並在必要時進行優化非常重要。 性能數據可以通過專用 API 功能進行查詢。

對渲染性能影響最大的是模型輸入數據。 您可以調整輸入資料,例如[配置模型轉換中](../../how-tos/conversion/configure-model-conversion.md)的描述。

用戶端應用程式性能也可能成為瓶頸。 要深入分析用戶端效能,建議採用[效能追蹤](../../how-tos/performance-tracing.md)。

## <a name="clientserver-timeline"></a>用戶端/伺服器時間線

在詳細介紹各種延遲值之前,有必要查看時間線上的用戶端和伺服器之間的同步點:

![導管時間線](./media/server-client-timeline.png)

下圖顯示了如何:

* *a Pose 估計*由用戶端以恆定的 60 Hz 幀速率(每 16.6 毫秒)開始
* 然後,伺服器根據姿勢開始渲染
* 伺服器傳送回編碼的影像影像
* 用戶端解碼影像,在映像之上執行一些 CPU 和 GPU 工作,然後顯示影像

## <a name="frame-statistics-queries"></a>格格統計資訊查詢

幀統計資訊為最後一幀提供了一些高級資訊,例如延遲。 結構中`FrameStatistics`提供的資料在客戶端進行測量,因此 API 是同步呼叫:

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

檢索`FrameStatistics`的物件包含以下成員:

| member | 說明 |
|:-|:-|
| 延遲接收 | 來自攝像機的延遲在用戶端設備上進行估計,直到此姿勢的伺服器幀完全可供用戶端應用程式使用。 此值包括網路往返、伺服器渲染時間、視頻解碼和抖動補償。 請參閱**上圖中的間隔 1。**|
| 延遲接收到存在 | 從接收的遠端幀的可用性到用戶端應用在 CPU 上調用呈現幀的延遲。 |
| 延遲顯示  | 從在 CPU 上顯示幀到顯示亮起的延遲。 此值包括用戶端 GPU 時間、作業系統執行的任何幀緩衝、硬體重新投影和與設備相關的顯示掃描時間。 請參閱**上圖中的間隔 2。**|
| 時間 自上次存在 | 後續調用 CPU 上的「當前幀」之間的時間。 大於顯示持續時間的值(例如 60 Hz 用戶端設備上的 16.6 ms)表示用戶端應用程式未能及時完成 CPU 工作負載所造成的問題。 請參閱**上圖中的間隔 3。**|
| 視頻幀接收 | 在最後一秒從伺服器接收的幀數。 |
| 視訊幀重新使用計數 | 設備上多次使用的接收幀數。 非零值表示由於網路抖動或伺服器渲染時間過長,必須重複使用和重新投影幀。 |
| 跳過的影片 | 已解碼的最後一秒接收的幀數,但由於較新的幀已到達,因此未顯示。 非零值表示網路抖動導致多個幀延遲,然後一起到達用戶端設備。 |
| 視頻幀已丟棄 | 非常類似於**視頻FrameS跳過**,但被丟棄的原因是,一個幀來得太晚,它甚至不能與任何掛起的姿勢關聯了。 如果發生這種情況,則存在一些嚴重的網路爭用。|
| 視訊框架明三角洲 | 在最後一秒到達的兩個連續幀之間的最短時間量。 與視頻FrameMaxDelta一起,此範圍可指示由網路或視頻編解碼器引起的抖動。 |
| 視訊FrameMaxDelta | 在最後一秒到達的兩個連續幀之間的最大時間量。 與視頻FrameMinDelta一起,此範圍可指示由網路或視頻編解碼器引起的抖動。 |

所有延遲值的總和通常比 60 Hz 的可用幀時間大得多。 這沒關係,因為多個幀並行飛行,並且新的幀請求以所需的幀速率啟動,如圖所示。 但是,如果延遲變得太大,它會影響[後期重新投影](../../overview/features/late-stage-reprojection.md)的品質,並可能危及整體體驗。

`videoFramesReceived``videoFrameReusedCount`,`videoFramesDiscarded`並可用於測量網路和伺服器性能。 如果`videoFramesReceived`低且`videoFrameReusedCount`高,則表示網路擁塞或伺服器性能不佳。 高`videoFramesDiscarded`值還表示網路擁塞。

最後,`timeSinceLastPresent` `videoFrameMinDelta``videoFrameMaxDelta`, 並給出傳入視頻幀和本地當前調用的方差。 高方差意味著幀速率不穩定。

上述值均未明確指示純網路延遲(圖中的紅色箭頭),因為需要從往返值`latencyPoseToReceive`中減去伺服器正忙於渲染的確切時間。 總體延遲的伺服器端部分是用戶端不可用的資訊。 但是,下一段將解釋如何通過來自伺服器的其他輸入並通過`networkLatency`該值公開來估計此值。

## <a name="performance-assessment-queries"></a>績效評估查詢

*性能評估查詢*提供有關伺服器上的 CPU 和 GPU 工作負載的更深入資訊。 由於從伺服器請求數據,因此查詢性能快照遵循通常的非同步模式:

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

與`FrameStatistics`物件相反,`PerformanceAssessment`此物件包含伺服器端資訊:

| member | 說明 |
|:-|:-|
| 時間CPU | 每幀平均伺服器 CPU 時間(以毫秒為單位) |
| 時間GPU | 每幀平均伺服器 GPU 時間(以毫秒為單位) |
| 利用率CPU | 伺服器 CPU 總利用率(百分比) |
| 利用 | 伺服器 GPU 總利用率(百分比) |
| 記憶體CPU | 伺服器主記憶體總利用率(百分比) |
| 記憶體GPU | 以伺服器 GPU 百分比表示的專用影片記憶體利用率 |
| 網路延遲 | 近似平均往返網路延遲(以毫秒為單位)。 在上面的插圖中,這對應於紅色箭頭的總和。 這個值是從的值中減去實際伺服器呈現時間來計算`latencyPoseToReceive`。`FrameStatistics` 雖然此近似值不準確,但它提供了一些網路延遲的指示,與用戶端上計算的延遲值隔離。 |
| 多邊形成像 | 在一個幀中呈現的三角形數。 此數位還包括稍後在渲染過程中剔除的三角形。 這意味著,此數位在不同的攝像機位置變化並不大,但性能可能會有很大差異,具體取決於三角形剔除率。|

為了説明你評估值,每個部分都帶有質量分類,如 **「偉大**、**良好**、**平庸**」或 **「壞**」。。
此評估指標提供伺服器運行狀況的粗略指示,但不應將其視為絕對指標。 例如,假設您看到 GPU 時間的"平庸" 分數。 它被認為是平庸的,因為它接近整個幀時間預算的極限。 但是,在您的情況下,它可能是一個不錯的值,因為您正在呈現一個複雜的模型。

## <a name="statistics-debug-output"></a>統計除錯輸出

該類`ARRServiceStats`環繞幀統計資訊和性能評估查詢,並提供方便的功能,將統計資訊返回為聚合值或預構建字串。 以下代碼是在用戶端應用程式中顯示伺服器端統計資訊的最簡單方法。

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

上面的代碼用以下文字填充文字標籤:

![ArrServiceStats 字串輸出](./media/arr-service-stats.png)

`GetStatsString` API 設置所有值的字串,但也可以從`ARRServiceStats`實例以程式設計方式查詢每個值。

成員也有變體,這些變體會隨時間聚合值。 請參考後置字`*Avg`的成員`*Max`,`*Total`或 。 該成員`FramesUsedForAverage`指示此聚合已使用多少幀。

## <a name="next-steps"></a>後續步驟

* [建立效能追蹤](../../how-tos/performance-tracing.md)
* [設定模型轉換](../../how-tos/conversion/configure-model-conversion.md)
