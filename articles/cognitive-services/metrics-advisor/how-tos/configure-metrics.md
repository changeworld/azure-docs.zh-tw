---
title: 使用入口網站設定您的計量顧問實例
titleSuffix: Azure Cognitive Services
description: 如何設定您的計量顧問實例，並微調異常偵測結果。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 93fdf2884ca6593cfdb4fb2878ba0dd21246266d
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91446349"
---
# <a name="how-to-configure-metrics-and-fine-tune-detecting-configuration"></a>如何：設定計量並微調偵測設定

您可以使用本文，使用入口網站開始設定您的計量顧問實例。 若要流覽特定資料摘要的計量，請移至 [ **資料** 摘要] 頁面，然後選取其中一個摘要。 這會顯示與其相關聯的計量清單。

:::image type="content" source="../media/metrics/select-metric.png" alt-text="選取計量" lightbox="../media/metrics/select-metric.png":::

按一下其中一個度量名稱以查看其詳細資料。 在此詳細資料檢視中，您可以使用畫面右上角的下拉式清單，切換到相同資料摘要中的另一個度量。

當您第一次查看計量的詳細資料時，您可以讓計量建議程式為您選擇一個時間序列，或指定要針對每個維度包含的值，藉以載入時間序列。 

您也可以選取時間範圍，並變更頁面的版面配置。

> [!NOTE]
> - 開始時間是內含的。
> - 結束時間是獨佔的。 

您可以按一下 [ **事件** ] 索引標籤來查看異常，並尋找 [事件中樞](diagnose-incident.md)的連結。

## <a name="tune-the-detecting-configuration"></a>微調偵測設定

度量可以套用一或多個偵測設定。 根據您的監控需求，您可以編輯或新增每個度量的預設設定。

### <a name="tune-the-configuration-for-all-series-in-current-metric"></a>調整目前度量中所有數列的設定

這項設定會套用到此計量中的所有系列，但具有個別設定的系列除外。 上線資料時，預設會套用計量層級設定，而且會顯示在左側面板上。 使用者可以在 [計量] 頁面上直接編輯計量層級設定。 

還有其他參數（例如 **方向**）和 **有效的異常** ，可用來進一步微調設定。 您也可以合併不同的偵測方法。 

:::image type="content" source="../media/configuration-combination.png" alt-text="選取計量" lightbox="../media/configuration-combination.png":::

### <a name="tune-the-configuration-for-a-specific-series-or-group"></a>調整特定系列或群組的設定

按一下 [計量層級設定] 選項下方的 [ **Advanced configuration** ]，以查看群組層級設定。您可以按一下此視窗中的圖示，以新增個別數列或數列群組的設定 **+** 。 這些參數類似于計量層級設定參數，但您可能需要為群組層級設定指定至少一個維度值，以識別數列群組。 並指定數列層級設定的所有維度值，以識別特定的數列。 

這項設定會套用到數列或特定系列的群組，而非計量層級設定。 設定此群組的條件之後，請加以儲存。

:::image type="content" source="../media/advanced-configuration.png" alt-text="選取計量" lightbox="../media/advanced-configuration.png":::

### <a name="anomaly-detection-methods"></a>異常偵測方法

計量審查程式提供多個異常偵測方法。 您可以使用其中一個，或按一下按鈕來將它們合併使用邏輯運算子 **+** 。 

**智慧型偵測** 

智慧型偵測是由機器學習服務所提供，可從歷程記錄資料中學習模式，並使用它們來進行未來的偵測。 使用這個方法時， **敏感度** 是微調偵測結果最重要的參數。 您可以將它拖曳到較小或較大的值，以影響頁面右側的視覺效果。 選擇適合您資料的資料並加以儲存。 


在智慧偵測模式中，會使用敏感度和界限版本參數來微調異常偵測結果。

敏感度可能會影響每個點的預期值範圍寬度。 增加時，預期的值範圍將會更緊密，而且會回報更多異常：

:::image type="content" source="../media/metrics/smart-detection-high-sensitivity.png" alt-text="選取計量":::

當敏感度關閉時，預期的值範圍將會變寬，而且會回報較少的異常：

:::image type="content" source="../media/metrics/smart-detection-low-sensitivity.png" alt-text="選取計量":::

**變更閾值** 

當計量資料通常會在特定範圍內時，通常會使用變更臨界值。 閾值是根據 **變更百分比**設定。 **變更臨界值**模式能夠偵測情節中的異常狀況：

* 您的資料通常穩定且流暢。 當有波動時，您想要收到通知。
* 您的資料通常很不穩定，而且會有很大的波動。 當您想要在變得太穩定或平坦時收到通知。

使用下列步驟來使用此模式：

1. 當您針對計量或時間序列設定異常偵測設定時，請選取 [ **變更閾值** ] 作為異常偵測方法。
    
    :::image type="content" source="../media/metrics/change-threshold.png" alt-text="選取計量":::

2. 根據您的案例，選取 **超出範圍** 或 **範圍參數中** 的。

    如果您想要偵測波動，請選取 **[超出範圍**]。 例如，在下列設定中，相較于前一個值，任何變更超過10% 的資料點都會被偵測為極端值。
    :::image type="content" source="../media/metrics/out-of-the-range.png" alt-text="選取計量":::

    如果您想要偵測資料中的平條線，請選取 **範圍中的**。 例如，在下列設定中，相較于前一項，在0.01% 內變更的任何資料點都會被偵測為極端值。 因為臨界值很小 (0.01% ) ，所以它會在資料中偵測到一般的行。

    :::image type="content" source="../media/metrics/in-the-range.png" alt-text="選取計量":::

3. 設定將計為異常的變更百分比，並將使用先前捕獲的資料點進行比較。 這項比較一律會介於目前的資料點和前 N 個資料點之間。
    
    只有當您使用**超出範圍**模式時，**方向**才有效：
    
    * 當 (目前的資料點時，會將偵測**設定**為只偵測異常狀況)  (比較資料點) > **+** 臨界值百分比。
    * **向下** 設定偵測只在 (目前的資料點時偵測異常) - (比較資料點) < **-** 臨界值百分比。
 
**硬性閾值**

 [硬性閾值] 是異常偵測的基本方法。 您可以設定上限和/或下限來判斷預期的值範圍。 任何指向界限的點都將被視為異常。 


## <a name="preset-events"></a>預設事件

有時候，預期的事件和出現 (例如假日) 可能會產生異常的資料。 使用預設事件，您可以在指定的時間內將旗標新增至異常偵測輸出。 您應在上線資料摘要之後設定這項功能。 每個度量只能有一個預設事件設定。

> [!Note]
> 預設事件設定會在異常偵測期間考慮假日，而且可能會變更您的結果。 它會在您儲存設定之後套用至資料點內嵌。 

在每個 [計量詳細資料] 頁面上，按一下 [計量] 下拉式清單旁的 [設定預設 **事件** ] 按鈕。
 
:::image type="content" source="../media/metrics/preset-event-button.png" alt-text="選取計量":::

在出現的視窗中，根據您的使用量來設定選項。 請確定已選取 [ **啟用假日事件** ] 以使用設定。 

**假日事件**區段可協助您隱藏在假日期間偵測到的不必要異常。 您可以套用的 **策略** 選項有兩個選項：

* **隱藏假日**：在假日期間隱藏異常偵測結果中的所有異常和警示。
* **假日為週末**：計算假日前幾個對應週末的平均預期值，並以這些值的異常狀態為基礎。

還有其他幾個您可以設定的值：

|選項  |描述  |
|---------|---------|
|**選擇一個維度作為國家/地區**     | 選擇包含國家/地區資訊的維度。 例如，國家（地區）代碼。         |
|**國家/地區代碼對應**     | 標準 [國家/地區代碼](https://wikipedia.org/wiki/ISO_3166-1_alpha-2)與所選維度的國家/地區資料之間的對應。        |
|**假日選項**    | 是否要將所有假日納入考慮，只有 PTO (付費) 假日或僅限非 PTO 假日。         |
|**要擴展的天數**    |  假日之前和之後的受影響天數。        |


在某些案例中， **迴圈事件** 區段可以用來協助減少不必要的警示，方法是使用資料中的迴圈模式。 例如： 

- 具有多個模式或迴圈的計量，例如每週和每月模式。 
- 沒有明確模式的計量，但資料是一年中的可比較年份 (YoY) 、每月 (MoM) 、一周的每週 (WoW) ，或一天一天 (DoD) 。
 
並非所有的選項都可針對每個細微性進行選取。 每個細微性的可用選項如下：

| 細微度 | YoY | 媽媽 | 哇 | DoD |
|:-|:-|:-|:-|:-|
| 每年 | X | X | X | X |
| 每月 | X | X | X | X |
| 每週 | ✔ | X | X | X |
| 每日 | ✔ | ✔ | ✔ | X |
| 每小時 | ✔ | ✔ | ✔ | ✔ |
| 微小 | X | X | X | X |
| 其次 | X | X | X | X |
| 自訂 | ✔ | ✔ | ✔ | ✔ |

X-無法使用。  
✔-可用。
  
\* 使用自訂資料細微性（以秒為單位）時，只有在度量超過一小時且不到一天時才可使用。

迴圈事件會用來減少因迴圈模式的異常情況，但如果有多個資料點未遵循此模式，則會報告異常。 如果即使一個資料點未遵循模式，則會使用**Strict 模式**來啟用異常報告。 

:::image type="content" source="../media/metrics/preset-events.png" alt-text="選取計量":::

## <a name="view-recent-incidents"></a>查看最近的事件

計量審查程式會在內嵌時偵測所有時間序列資料的異常狀況。 不過，並非所有異常都需要擴大，因為它們可能不會有很大的影響。 系統會對異常執行匯總，將相關的專案群組到事件中。 您可以從 [計量詳細資料] 頁面的 [ **事件** ] 索引標籤中查看這些事件。 

按一下事件可移至 [ **事件分析** ] 頁面，您可以在其中查看更多詳細資料。 按一下 [ **管理新事件中樞中的事件**]，以尋找 [ [事件中樞](diagnose-incident.md) ] 頁面，您可以在其中找到特定度量下的所有事件。 

## <a name="subscribe-anomalies-for-notification"></a>訂閱通知的異常

如果您想要在每次偵測到異常時收到通知，您可以使用攔截來訂閱度量的警示。 如需詳細資訊，請參閱 [使用攔截設定警示和取得通知](alerts.md) 。


## <a name="next-steps"></a>後續步驟 
- [使用掛勾來設定警示並取得通知](alerts.md)
- [使用意見反應來調整異常偵測](anomaly-feedback.md)
- [診斷事件](diagnose-incident.md)。

