---
title: 檢查自訂語音-語音服務的資料品質
titleSuffix: Azure Cognitive Services
description: 自訂語音提供的工具，可讓您藉由比較音訊資料與對應的辨識結果，以視覺化方式檢查模型的辨識品質。 您可以播放上傳的音訊，並判斷提供的辨識結果是否正確。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: d4da9a819d7aa96992259112c75154b1651341ac
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604734"
---
# <a name="inspect-custom-speech-data"></a>檢查自訂語音資料

> [!NOTE]
> 此頁面會假設您已閱讀 [自訂語音的準備測試資料](how-to-custom-speech-test-data.md) ，並已上傳資料集以進行檢查。

自訂語音提供的工具，可讓您藉由比較音訊資料與對應的辨識結果，以視覺化方式檢查模型的辨識品質。 您可以從 [自訂語音入口網站](https://speech.microsoft.com/customspeech)播放上傳的音訊，並判斷提供的辨識結果是否正確。 這項工具可協助您檢查 Microsoft 的基準語音轉換文字模型的品質、檢查定型的自訂模型，或比較兩個模型的轉譯。

在本檔中，您將瞭解如何以視覺化方式檢查 Microsoft 的基準語音轉換文字模型的品質，以及/或您已定型的自訂模型。 您也將瞭解如何使用線上轉譯編輯器來建立和修改加上標籤的音訊資料集。

## <a name="create-a-test"></a>建立測試

請遵循下列指示來建立測試：

1. 登入 [自訂語音入口網站](https://speech.microsoft.com/customspeech)。
2. 流覽至 **語音轉換文字 > 自訂語音 > [專案名稱] > 測試**。
3. 按一下 [ **加入測試**]。
4. 選取 [ **檢查品質 (僅限音訊的資料) **。 提供測試的名稱、描述，然後選取您的音訊資料集。
5. 選取最多兩個您想要測試的模型。
6. 按一下 [建立]。

成功建立測試之後，您可以看到模型如何轉譯您所指定的音訊資料集，或將兩個模型的結果與並排比較。

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>並存模型比較

當測試狀態為 [ _成功_] 時，請按一下測試專案名稱以查看測試的詳細資料。 此詳細資料頁面會列出資料集中的所有語句，並顯示您要比較的兩個模型的辨識結果。

若要協助檢查並存比較，您可以切換各種錯誤類型，包括插入、刪除和替代。 藉由在每個資料行中接聽音訊並比較辨識結果， (顯示人為標記的轉譯，以及兩個語音轉換文字模型的結果) ，您可以決定哪個模型符合您的需求，以及需要改進的地方。

並存模型測試有助於驗證應用程式最適合的語音辨識模型。 如需精確度的目標量值（需要轉譯音訊），請遵循 [評估精確度](how-to-custom-speech-evaluate-data.md)中找到的指示。

## <a name="online-transcription-editor"></a>線上轉譯編輯器

線上轉譯編輯器可讓您輕鬆地使用自訂語音中的音訊轉譯。 編輯器的主要使用案例如下： 

* 您只會有音訊資料，但想要從頭開始建立精確的音訊 + 人為標記的資料集，以便在模型定型中使用。
* 您已經有音訊 + 人標記的資料集，但轉譯中有錯誤或瑕疵。 編輯器可讓您快速修改轉譯，以獲得最佳的定型精確度。

使用轉譯編輯器的唯一需求是將音訊資料上傳 (僅限音訊或音訊 + 轉譯) 。

### <a name="import-datasets-to-editor"></a>將資料集匯入編輯器

若要將資料匯入編輯器，請先流覽至 **自訂語音 > [您的專案] > 編輯器**。

![[編輯器] 索引標籤](media/custom-speech/custom-speech-editor-detail.png)

接下來，使用下列步驟來匯入資料。

1. 按一下 [匯**入資料**]
1. 建立新的資料集 () ，並為其提供描述
1. 選取資料集。 支援多個選項，而且您只能選取音訊資料，以及音訊 + 人類標記的資料。
1. 針對僅限音訊的資料，您可以選擇性地使用預設模型，在匯入編輯器之後自動產生機器轉譯
1. 按一下 [匯**入**]

成功匯入資料之後，您可以按一下資料集並開始編輯。

> [!TIP]
> 您也可以選取資料集，然後按一下 [**匯出至編輯器**]，直接將資料集匯入編輯器中

### <a name="edit-transcription-by-listening-to-audio"></a>藉由接聽音訊來編輯轉譯

資料上傳成功之後，請按一下每個專案名稱以查看資料的詳細資料。 詳細資料頁面會列出資料集中的所有檔案，而且您可以按一下所需的語句。 針對每個語句，您可以播放音訊並檢查文字記錄，並在找到任何插入、刪除或替代錯誤時編輯轉譯。 如需錯誤類型的詳細資訊，請參閱 [資料評估](how-to-custom-speech-evaluate-data.md) 的作法。

![編輯器頁面](media/custom-speech/custom-speech-editor.png)

如果音訊檔很長，則會自動分成較小的片段。 您可以使用 [**上一****步] 和 [下一步]** 在頁面之間移動來逐一編輯這些專案。 進行編輯之後，請按一下 [ **儲存** ] 按鈕。

### <a name="export-datasets-from-the-editor"></a>從編輯器匯出資料集

若要將資料集匯出回 [ **資料** ] 索引標籤，請流覽至 [資料詳細資料] 頁面，然後按一下 [ **匯出** ] 按鈕，將所有檔案匯出為新的資料集。 您也可以依上次編輯時間、音訊持續時間等方式來篩選檔案，以部分選取所需的檔案。 

![匯出資料](media/custom-speech/custom-speech-editor-export.png)

匯出至資料的檔案將會用來做為全新的資料集，而且不會影響任何現有的資料/訓練/測試實體。

## <a name="next-steps"></a>後續步驟

- [評估您的資料](how-to-custom-speech-evaluate-data.md)
- [定型模型](how-to-custom-speech-train-model.md)
- [改善模型](how-to-custom-speech-improve-accuracy.md)
- [部署模型](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>其他資源

- [準備自訂語音的測試資料](how-to-custom-speech-test-data.md)
