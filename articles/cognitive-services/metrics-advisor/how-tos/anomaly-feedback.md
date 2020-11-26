---
title: 將異常意見反應提供給計量顧問服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何針對您的計量顧問實例找到的異常狀況傳送意見反應，以及調整結果。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbullwin
ms.openlocfilehash: 96a6dfebdd0047d5a6f4ff3e295ab96dd8c7226a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184852"
---
# <a name="provide-anomaly-feedback"></a>提供異常意見反應

使用者意見反應是探索異常偵測系統內瑕疵的最重要方法之一。 我們在此提供一種方式，讓使用者在時間序列上直接標記不正確的偵測結果，並立即套用意見反應。 如此一來，使用者就能教授異常偵測系統如何透過主動互動針對特定的時間序列執行異常偵測。 

> [!NOTE]
> 目前的意見反應只會影響 **智慧型偵測** 的異常偵測結果，但不會影響 **臨界值** 和 **變更臨界值**。

## <a name="how-to-give-time-series-feedback"></a>如何提供時間序列意見反應

您可以從任何系列的度量詳細資料頁面提供意見反應。 只要選取任何點，您就會看到下列意見反應對話方塊。 它會顯示您所選系列的維度。 您可以重新選擇維度值，或甚至移除部分值以取得一批時間序列資料。 選擇時間序列之後，請選取 [ **新增** ] 按鈕來新增意見反應，您可以提供四種意見反應。 若要附加多個意見專案，請在完成批註後選取 [ **儲存** ] 按鈕。

:::image type="content" source="../media/feedback/click-on-any-point.png" alt-text="具有時間序列資料的圖表，在不同的點會有藍色線和紅點。包含下列文字的紅色方塊：選取任何點":::

:::image type="content" source="../media/feedback/select-or-remove.png" alt-text="新增具有兩個維度的 [意見反應] 對話方塊，以及選取或移除維度和新增意見的選項。":::

### <a name="mark-the-anomaly-point-type"></a>標記異常點類型

如下圖所示，[意見反應] 對話方塊會自動填滿您所選點的時間戳記，但您可以編輯此值。 然後，您可以選取是否要將此專案識別為 `Anomaly` 、 `NotAnomaly` 或 `AutoDetect` 。

:::image type="content" source="../media/feedback/anomaly-value.png" alt-text="具有異常、NotAnomaly 和自動偵測選項的下拉式功能表":::

此選項會將您的意見反應套用至相同數列的未來異常偵測處理。 處理的點將不會重新計算。 這表示，如果您將異常標示為 NotAnomaly，我們會在未來隱藏類似的異常，如果您將某個 `NotAnomaly` 點標示為 `Anomaly` ，我們通常會在未來偵測到類似 `Anomaly` 的點。 如果 `AutoDetect` 選擇了，則未來會忽略任何先前在相同時間點的意見反應。

## <a name="provide-feedback-for-multiple-continuous-points"></a>提供多個連續點的意見反應 

如果您想要同時提供多個連續點的異常意見反應，請選取您要加上批註的點群組。 當您提供異常意見反應時，您會看到所選的時間範圍自動填滿。

:::image type="content" source="../media/feedback/continuous-anomaly-feedback.png" alt-text="已選取異常和特定時間範圍的異常意見反應功能表":::

若要查看個別的點是否受到異常意見反應影響，在流覽時間序列時，請選取單一點。 如果回應中的異常偵測結果已變更，則工具提示會顯示 [ **受意見反應： true**]。 如果它顯示 **受意見反應影響： false**，這表示已針對此點執行異常意見反應計算，但不應該變更異常偵測結果。

:::image type="content" source="../media/feedback/affected-by-feedback.png" alt-text="具有文字的工具提示顯示：受意見影響： true，以紅色反白顯示":::

在某些情況下，我們不建議您提供意見反應：

- 異常是由假日所造成。 建議您使用預設事件來解決這種錯誤警示，因為這樣會更精確。
- 異常是已知的資料來源變更所造成。 例如，發生上游系統變更時。 在此情況下，預期會提供異常警示，因為我們的系統不知道造成值變更的原因，以及類似的值變更會再次發生。 因此，我們不建議將這類問題批註為 `NotAnomaly` 。

## <a name="change-points"></a>變更點

有時資料的趨勢變更將會影響異常偵測結果。 當決定某個點是否為異常時，會將歷程記錄資料的最新視窗納入考慮。 當您的時間序列有趨勢變更時，您可以標示確切的變更點，這將可協助我們在未來分析中的異常偵測器。

如下圖所示，您可以選取 `ChangePoint` 作為意見類型，然後從下拉式清單中選取 `ChangePoint` 、 `NotChangePoint` 或 `AutoDetect` 。

:::image type="content" source="../media/feedback/changepoint.png" alt-text="變更包含 ChangePoint、NotChangePoint 和自動偵測之選項的點功能表":::

> [!NOTE]
> 如果您的資料持續變更，您只需要將某個點標示為 `ChangePoint` ，因此，如果您標示了 `timerange` ，則會自動填滿最後時間點的時間戳記和時間。 在此情況下，您的注釋將只會影響12點之後的異常偵測結果。

## <a name="seasonality"></a>季節性

針對季節性資料，當我們執行異常偵測時，其中一個步驟是估計時間序列 (季節性) 期間，並將其套用至異常偵測階段。 有時候，很難識別精確的期間，而且期間也可能會變更。 未正確定義的期間可能會對您的異常偵測結果產生副作用。 您可以從工具提示中找到目前的期間，其名稱為 `Min Period` 。

:::image type="content" source="../media/feedback/min-period.png" alt-text="工具提示會與單字句號重迭，而第七個則以紅色反白顯示。":::

您可以提供期間的意見反應，以修正這類異常偵測錯誤。 如圖所示，您可以設定期間值。 單位 `interval` 表示一個資料細微性。 此處的零間隔表示資料為非季節性。 您也可以選取 `AutoDetect` 是否要取消先前的意見反應，並自動讓管線偵測到期間。 
 
> [!NOTE]
> 當設定期間，您不需要指派時間戳記或 timerange 時，期間會在您提供意見反應的時候，影響整個時間序列的未來異常偵測。


:::image type="content" source="../media/feedback/period-feedback.png" alt-text="自動偵測期間設定為28的功能表，並將 [間隔] 設定為 [零]，表示非季節性。":::

## <a name="provide-comment-feedback"></a>提供批註意見反應

您也可以加入批註，並將內容提供給您的資料。 若要新增批註，請選取時間範圍，並新增批註的文字。

:::image type="content" source="../media/feedback/feedback-comment.png" alt-text="具有設定時間範圍和方塊的功能，以新增以文字為基礎的批註":::

## <a name="time-series-batch-feedback"></a>時間序列批次意見反應

如先前所述，意見反應模式可讓您重新選擇或移除維度值，以取得維度篩選所定義的時間序列批次。 您也可以按一下左側面板中的 [+] 按鈕，然後選取 [維度] 和 [維度值]，來開啟此強制回應。

:::image type="content" source="../media/feedback/feedback-time-series.png" alt-text="具有藍色加號符號的功能表，以紅色醒目提示文字意見反應旁的紅色":::

:::image type="content" source="../media/feedback/feedback-dimension.png" alt-text="新增具有兩個 Dim1 和 Dim2 所指出之維度的意見反應功能表":::

## <a name="how-to-view-feedback-history"></a>如何查看意見反應歷程記錄

有兩種方式可以查看意見反應歷程記錄。 您可以從左面板中選取 [意見反應歷程記錄] 按鈕，將會看到意見反應清單強制回應。 它會列出您在單一數列或維度篩選之前提供的所有意見反應。

:::image type="content" source="../media/feedback/feedback-history-options.png" alt-text="意見清單功能表 '":::

另一個查看意見反應歷程記錄的方式是從一個數列。 您會在每個數列的右上角看到幾個按鈕。 選取 [顯示意見反應] 按鈕，這條線將從顯示異常點切換為顯示意見專案。 綠色旗標表示變更點，而藍色點則是其他意見反應點。 您也可以選取它們，並取得意見反應清單強制回應，其中會列出針對此點提供的意見反應詳細資料。

:::image type="content" source="../media/feedback/feedback-history-graph.png" alt-text="意見反應歷程記錄圖表":::

:::image type="content" source="../media/feedback/feedback-list.png" alt-text="有兩個維度的意見清單功能表":::

> [!NOTE]
> 可以存取度量的任何人都可以提供意見反應，因此您可能會看到其他 datafeed 擁有者所提供的意見反應。 如果您編輯的是其他人的相同時間點，則您的意見反應將會覆寫先前的意見反應專案。       

## <a name="next-steps"></a>後續步驟
- [診斷事件](diagnose-incident.md)。
- [設定計量和微調偵測組態](configure-metrics.md)
- [使用掛勾來設定警示並取得通知](../how-tos/alerts.md)