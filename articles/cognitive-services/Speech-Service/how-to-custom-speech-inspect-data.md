---
title: 檢查自訂語音語音服務的資料品質
titleSuffix: Azure Cognitive Services
description: 「自訂語音」提供工具，可讓您以視覺化方式檢查模型的辨識品質，方法是比較音訊資料與對應的辨識結果。 您可以播放上傳的音訊，並判斷提供的辨識結果是否正確。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: 7a8fec876556d943d29756a38ffc27ae8095e3c4
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2020
ms.locfileid: "84466233"
---
# <a name="inspect-custom-speech-data"></a>檢查自訂語音資料

> [!NOTE]
> 此頁面會假設您已閱讀[準備自訂語音的測試資料](how-to-custom-speech-test-data.md)，並已上傳資料集以進行檢查。

「自訂語音」提供工具，可讓您以視覺化方式檢查模型的辨識品質，方法是比較音訊資料與對應的辨識結果。 從[自訂語音入口網站](https://speech.microsoft.com/customspeech)，您可以播放上傳的音訊，並判斷提供的辨識結果是否正確。 這項工具可協助您檢查 Microsoft 的基準語音轉換文字模型品質、檢查定型的自訂模型，或比較兩個模型的轉譯。

在本檔中，您將瞭解如何使用您先前上傳的定型資料，以視覺化方式檢查模型的品質。

在此頁面上，您將瞭解如何以視覺化方式檢查 Microsoft 的基準語音轉換文字模型和（或）您已定型的自訂模型品質。 您將使用您上傳到 [**資料**] 索引標籤的資料進行測試。

## <a name="create-a-test"></a>建立測試

請依照下列指示來建立測試：

1. 登入[自訂語音入口網站](https://speech.microsoft.com/customspeech)。
2. 流覽至**語音轉換文字 > 自訂語音 > 測試**。
3. 按一下 [**加入測試**]。
4. 選取 **[檢查品質（僅限音訊資料）**]。 為測試指定名稱、描述，然後選取您的音訊資料集。
5. 選取最多兩個您想要測試的模型。
6. 按一下 [建立]。

成功建立測試之後，您可以看到模型如何可將您指定的音訊資料集，或並排比較兩個模型的結果。

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>並存模型比較

當測試狀態為 [_成功_] 時，按一下 [測試專案名稱] 以查看測試的詳細資料。 此詳細資料頁面會列出資料集內的所有語句，並顯示您要比較的兩個模型的辨識結果。

若要協助檢查並存比較，您可以切換各種錯誤類型，包括插入、刪除和替代。 藉由接聽音訊並比較每個資料行中的辨識結果（顯示人為標記的轉譯和兩個語音轉換文字模型的結果），您可以決定哪一個模型符合您的需求，以及需要改進的位置。

並存模型測試適用于驗證哪個語音辨識模型最適合應用程式。 如需精確度的目標量值（需要轉譯音訊），請遵循[評估精確度](how-to-custom-speech-evaluate-data.md)中的指示。

## <a name="next-steps"></a>後續步驟

- [評估您的資料](how-to-custom-speech-evaluate-data.md)
- [定型模型](how-to-custom-speech-train-model.md)
- [改善模型](how-to-custom-speech-improve-accuracy.md)
- [部署模型](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>其他資源

- [準備自訂語音的測試資料](how-to-custom-speech-test-data.md)
