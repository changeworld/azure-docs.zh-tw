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
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 11864cb26b76d414aa2efe2643797a2f66fa30e4
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047602"
---
# <a name="adjust-anomaly-detection-using-feedback"></a>使用意見反應來調整異常偵測

如果您不滿意計量監視器所提供的某些異常偵測結果，可以手動新增意見反應，以影響套用至資料的模型。 

使用頁面右上角的按鈕來啟動意見反應注釋模式。

:::image type="content" source="../media/feedback/annotation-mode.png" alt-text="意見反應批註模式。":::

啟用意見反應批註模式之後，您可以提供一或多個連續點的意見反應。

## <a name="give-feedback-for-one-point"></a>針對某個點提供意見反應 

啟用意見反應注釋模式之後，請按一下某個點以開啟 [ **新增意見** 反應] 面板。 您可以設定想要套用的意見反應類型。 未來偵測點時系統會納入此意見反應。  

* 如果您認為計量監視器未正確標記點，請選取 [ **異常** ]。 您可以指定某個點是否應該或不應該是異常。 
* 如果您認為點指出趨勢變更的開頭，請選取 [ **ChangePoint** ]。
* 選取 [ **週期** ] 以表示季節性。 計量監視可以自動偵測季節性的間隔，您也可以手動指定此值。 

請考慮同時在 **批註** 文字方塊中留下批註，然後按一下 [ **儲存** ] 以儲存您的意見反應。

:::image type="content" source="../media/feedback/feedback-menu.png" alt-text="意見反應批註模式。":::

## <a name="give-feedback-for-multiple-continuous-points"></a>提供多個連續點的意見反應

您可以按一下 [向下]，然後將滑鼠拖曳至您想要加上批註的點，藉此一次提供多個連續點的意見反應。 您會看到與上述相同的意見反應功能表。 當您按一下 [ **儲存**] 之後，就會對所有選擇的點套用相同的意見反應。

:::image type="content" source="../media/feedback/continuous-points.png" alt-text="意見反應批註模式。":::

## <a name="how-to-view-my-feedback"></a>如何查看我的意見反應

若要查看點的異常偵測是否已變更，請將滑鼠停留在該點上。 工具提示會顯示 **因意見反應而受到影響：** 如果偵測已變更，則為 true。 如果顯示 [ **False**]，則會在該點上進行意見計算，但異常偵測結果不會變更。

:::image type="content" source="../media/feedback/affected-point.png" alt-text="意見反應批註模式。":::

## <a name="when-should-i-annotate-an-anomaly-as-normal"></a>我何時應該將異常標注為「正常」

有許多原因會導致您認為異常的警示。 如果適用下列其中一種情況，請考慮使用下列的計量建議程式功能：


|狀況  |建議 |
|---------|---------|
|異常是已知的資料來源變更所造成，例如系統變更。     | 如果此案例不需要定期再次出現，請不要標注此異常。        |
|異常是由假日所造成。     | 使用預設 [事件](configure-metrics.md#preset-events) 來標記指定時間的異常偵測。       |
|偵測到異常的一般模式 (例如在週末) ，而且不應該是異常。      |使用意見反應功能或預設事件。        |

## <a name="next-steps"></a>後續步驟
- [診斷事件](diagnose-incident.md)。
- [設定計量和微調偵測組態](configure-metrics.md)
- [使用掛勾來設定警示並取得通知](../how-tos/alerts.md)