---
title: 計量建議程式計量圖表
titleSuffix: Azure Cognitive Services
description: 如何設定您的計量圖形，並將資料中的相關異常視覺化。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: aahi
ms.openlocfilehash: 09ea16f07973757b169f21c7c3f909a24651daa4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936729"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>How to：建立計量圖形來分析相關計量

計量建議程式中的每個計量都會由可從歷程記錄資料中學習的模型個別監視，以預測未來趨勢。 每個度量都有各自適用的模型。 不過，在某些情況下，多個計量可能彼此相關，而異常需要跨多個計量進行分析。 **計量圖表**有助於進行此工作。 

例如，如果您在不同的計量中有不同的遙測串流，計量建議程式會個別監視這些資料流程。 如果某個計量中的異常導致其他的異常狀況，則在處理事件時，尋找這些關聯和資料中的根本原因可能會很有説明。 計量圖表可讓您建立找到異常的視覺化拓撲圖形。 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>選取度量以將第一個節點放到圖形中

按一下巡覽列中的 [ **計量圖表** ] 索引標籤。 建立計量圖表的第一個步驟是將節點放在圖形上。 選取頁面頂端的資料摘要和度量。 節點將會出現在下方面板中。 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="選取計量":::

## <a name="add-a-noderelation-on-existing-node"></a>在現有的節點上新增節點/關聯

接下來，您需要加入另一個節點，並指定與現有節點的關聯 (s) 。 選取現有的節點，並在其上按一下滑鼠右鍵。 內容功能表會出現數個選項。 

按一下 [ **新增關聯**]，您將能夠選擇另一個計量，並指定兩個節點之間的關聯類型。 您也可以套用特定的維度篩選。 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="選取計量":::

重複上述步驟之後，您將會有一個度量圖表，描述所有相關計量之間的關聯性。
**節點色彩的提示**
> [!TIP]
> - 當您選取計量和維度篩選準則時，圖表中具有相同計量和維度篩選準則的所有節點都會以 **<font color=blue>藍色</font>** 標示。
> - 未選取的節點（代表圖形中的度量）將會以 **<font color=green>綠色</font>** 標示。
> - 如果在目前的度量中觀察到異常，節點將會以 **<font color=red>紅色</font>** 標示。

## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>在事件中樞中查看相關計量異常狀態

當建立計量圖形時，只要在圖形中的計量上偵測到異常，您就可以查看相關的異常狀態，並取得事件的高階觀點。 

按一下以進入圖形內的事件，並向下切入到診斷資訊下方的 **交叉計量分析**。

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="選取計量":::

## <a name="next-steps"></a>後續步驟

- [使用意見反應來調整異常偵測](anomaly-feedback.md)
- [診斷事件](diagnose-incident.md)。
- [設定計量和微調偵測組態](configure-metrics.md)
