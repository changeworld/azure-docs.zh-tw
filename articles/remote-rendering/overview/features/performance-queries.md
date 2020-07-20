---
title: 伺服器端效能查詢
description: 如何透過 API 呼叫執行伺服器端效能查詢
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 3207ffca6fd0fbc943f4a2873b8b6c9029d565af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84022789"
---
# <a name="server-side-performance-queries"></a>伺服器端效能查詢

伺服器上的良好轉譯效能對於穩定的畫面播放速率和良好的使用者體驗很重要。 小心地監視伺服器上的效能特性，並在必要時進行最佳化是很重要的。 您可以透過專用 API 函式來查詢效能資料。

轉譯效能的最大影響是模型輸入資料。 如[設定模型轉換](../../how-tos/conversion/configure-model-conversion.md)中所述，您可以調整輸入資料。

用戶端應用程式效能也可能是瓶頸。 若要深入分析用戶端效能，建議採用 [:::no-loc text="performance trace":::](../../how-tos/performance-tracing.md) 。

## <a name="clientserver-timeline"></a>用戶端/伺服器時間軸

在深入了解各種延遲值之前，請先查看時間軸上用戶端與伺服器之間的同步處理點：

![管線時間軸](./media/server-client-timeline.png)

圖解顯示：

* 用戶端如何以固定 60-Hz 畫面播放速率 (每 16.6 毫秒) 開始「姿勢評估」
* 接著，伺服器會根據姿勢開始轉譯
* 伺服器如何傳回編碼視訊的影像
* 用戶端如何將影像解碼、在其上執行一些 CPU 和 GPU 工作，然後顯示影像

## <a name="frame-statistics-queries"></a>畫面統計資料查詢

畫面統計資料會針對最後一個畫面提供一些概觀資訊，例如延遲。 `FrameStatistics` 結構中提供的資料是在用戶端進行測量，因此 API 是同步呼叫：

```cs
void QueryFrameData(AzureSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
```

```cpp
void QueryFrameData(ApiHandle<AzureSession> session)
{
    FrameStatistics frameStatistics;
    if (*session->GetGraphicsBinding()->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        // do something with the result
    }
}
```

抓取的 `FrameStatistics` 物件持有下列成員：

| member | 說明 |
|:-|:-|
| latencyPoseToReceive | 在用戶端裝置上從觀景窗姿勢預估，直到此姿勢的伺服器畫面完全可供用戶端應用程式使用為止的延遲。 此值包括網路來回行程、伺服器轉譯時間、影片解碼和抖動補償。 請參閱**上圖中的間隔 1。**|
| latencyReceiveToPresent | 從接收的遠端框架可用，直到用戶端應用程式對 CPU 呼叫 PresentFrame 的延遲。 |
| latencyPresentToDisplay  | 從在 CPU 上呈現畫面直到顯示器亮起的延遲。 此值包括用戶端 GPU 時間、OS 所執行的任何畫面緩衝、硬體重新投影，以及與裝置相依顯示掃描時間。 請參閱**上圖中的間隔 2。**|
| timeSinceLastPresent | 對 CPU 上的 PresentFrame 進行後續呼叫之間的時間。 大於顯示持續時間的值 (例如 60-Hz 用戶端裝置上為 16.6 毫秒) 表示用戶端應用程式未及時完成其 CPU 工作負載所造成的問題。 請參閱**上圖中的間隔 3。**|
| videoFramesReceived | 最後一秒從伺服器接收的畫面數。 |
| videoFrameReusedCount | 最後一秒接收且在裝置上已使用多次的畫面數。 非零值，表示因網路抖動或伺服器轉譯時間過久而必須重複使用或重新投影的畫面。 |
| videoFramesSkipped | 最後一秒接收且已解碼，但因為較新的畫面已到達而未顯示在顯示器上顯示的畫面數。 非零值，表示網路抖動造成多個畫面延遲，然後以高載方式同時抵達用戶端裝置。 |
| videoFramesDiscarded | 非常類似 **videoFramesSkipped**，但捨棄的原因是畫面來得太晚，甚至無法再與任何暫止的姿勢相互關聯。 如果發生此情況，表示發生一些嚴重的網路爭用。|
| videoFrameMinDelta | 在最後一秒抵達的兩個連續畫面之間的最小時間量。 與 videoFrameMaxDelta 搭配時，此範圍指出由網路或視訊轉碼器造成的抖動。 |
| videoFrameMaxDelta | 在最後一秒抵達的兩個連續畫面之間的最大時間量。 與 videoFrameMinDelta 搭配時，此範圍指出由網路或視訊轉碼器造成的抖動。 |

所有延遲值的總和通常會比 60 Hz 的可用畫面時間大得多。 這是正常的，因為多個畫面會平行進行，而新的畫面要求會以所需的畫面播放速率開始，如圖解中所顯示。 不過，如果延遲變得太大，其會影響[延遲階段重新投影](../../overview/features/late-stage-reprojection.md)的品質，而且可能會危害整體體驗。

`videoFramesReceived`、`videoFrameReusedCount` 和 `videoFramesDiscarded` 可以用來測量網路和伺服器效能。 如果 `videoFramesReceived` 低，且 `videoFrameReusedCount` 高，這可能表示網路壅塞或伺服器效能不佳。 高 `videoFramesDiscarded` 值也表示網路壅塞。

最後，`timeSinceLastPresent`、`videoFrameMinDelta` 和 `videoFrameMaxDelta` 可讓您了解傳入的視訊畫面和本機呈現呼叫的變異數。 高差異數表示不穩定的畫面播放速率。

上述任何值都不會清楚指出單純的網路延遲 (圖解中的紅色箭號)，因為需要從來回行程值 `latencyPoseToReceive` 減去伺服器忙碌轉譯的確切時間。 整體延遲的伺服器端部分是用戶端無法取得的資訊。 不過，下一段將說明如何透過伺服器的額外輸入取得此值的近似值，並透過 `networkLatency` 值公開。

## <a name="performance-assessment-queries"></a>效能評定查詢

「效能評定查詢」提供有關伺服器上 CPU 和 GPU 工作負載的更深入資訊。 因為資料是從伺服器要求，所以查詢效能快照集會遵循一般非同步模式：

```cs
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

```cpp
void QueryPerformanceAssessment(ApiHandle<AzureSession> session)
{
    ApiHandle<PerformanceAssessmentAsync> assessmentQuery = *session->Actions()->QueryServerPerformanceAssessmentAsync();
    assessmentQuery->Completed([] (ApiHandle<PerformanceAssessmentAsync> res)
    {
        // do something with the result:
        PerformanceAssessment result = *res->Result();
        // ...

    });
}
```

與 `FrameStatistics` 物件相反，`PerformanceAssessment` 物件包含伺服器端資訊：

| member | 說明 |
|:-|:-|
| timeCPU | 每個畫面的平均伺服器 CPU 時間 (毫秒) |
| timeGPU | 每個畫面的平均伺服器 GPU 時間 (毫秒) |
| utilizationCPU | 伺服器 CPU 使用率總計 (百分比) |
| utilizationGPU | 伺服器 GPU 使用率總計 (百分比) |
| memoryCPU | 伺服器主記憶體使用率總計 (百分比) |
| memoryGPU | 固定視訊記憶體使用率占伺服器 GPU 的百分比 |
| networkLatency | z大約的平均來回行程網路延遲 (毫秒)。 在上面的圖解中，這會對應到紅色箭號的總和。 值的計算方式是將實際伺服器轉譯時間從 `FrameStatistics` 的 `latencyPoseToReceive` 值減去。 雖然此近似值不精確，但其提供網路延遲 (從用戶端上計算的延遲值分離出) 的一些指示。 |
| polygonsRendered | 在一個畫面格中轉譯的三角形數目。 此數目也包括在轉譯期間稍後挑選的三角形。 這表示，這個數目在不同觀景窗位置上不會差異很大，但效能可能會有大幅差異，視三角形的挑選率而定。|

為了協助您評估這些值，每個部分都附帶品質分類，例如**絕佳**、**良好**、**中等**或**不良**。
此評量計量可提供伺服器健康情況的粗略指示，但不應視為絕對。 例如，假設您看到 GPU 時間分數為「中等」。 將其視為中等是因為其接近整體畫面時間預算的限制。 不過，在您的案例中，其可能是良好的值，因為您正在轉譯複雜模型。

## <a name="statistics-debug-output"></a>統計資料偵錯輸出

類別 `ARRServiceStats` 是一個 C# 類別，其會包裝畫面統計資料和效能評定查詢，並提供便利的功能來將統計資料傳回為彙總值或預先建立字串。 下列程式碼是在用戶端應用程式中顯示伺服器端統計資料的最簡單方式。

```cs
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

`GetStatsString` API 會將所有值的字串格式化，但也可以從 `ARRServiceStats` 執行個體以程式設計方式查詢每個單一值。

也有成員的變體，其會隨時間彙總值。 請參閱尾碼為 `*Avg`、`*Max` 或 `*Total` 的成員。 成員 `FramesUsedForAverage` 指出此彙總所使用的畫面數目。

## <a name="next-steps"></a>後續步驟

* [建立效能追蹤](../../how-tos/performance-tracing.md)
* [設定模型轉換](../../how-tos/conversion/configure-model-conversion.md)
