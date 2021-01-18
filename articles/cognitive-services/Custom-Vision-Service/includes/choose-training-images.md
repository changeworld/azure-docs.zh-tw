---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: d07a5da3b9013700694f6c20102ef2e8c5066087
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98256261"
---
建議在初始定型集的每個標記使用至少 30 個影像。 您也會想要收集一些額外的影像，來測試已定型的模型。

若要有效地定型您的模型，可使用有不同視覺效果的影像。 選取有下列各種變化的影像：
* 攝影機角度
* 光源
* 背景資訊
* 視覺效果樣式
* 單一/群組對象
* {1}size{2}
* type

此外，請確定所有的訓練映像符合下列準則：
* .jpg、.png、.bmp 或 .gif 格式
* 不大於 6MB (預測影像為 4MB)
* 最短邊緣不小於 256 像素；任何超過此上限的影像會由自訂視覺服務自動相應增加

> [!NOTE]
> Trove 是 Microsoft Garage 專案，可讓您收集和購買影像集以供訓練使用。 收集影像之後，您可以下載影像，然後以一般方式將其匯入至自訂視覺專案。 如需深入了解，請造訪 [Trove 頁面](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3)。