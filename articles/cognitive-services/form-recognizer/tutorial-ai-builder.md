---
title: 教學課程：使用 AI Builder 建立表單處理應用程式 - 表單辨識器
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，您將使用 AI Builder 來建立和定型表單處理應用程式。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: 3ba08f11fb52fc0ffe3acda5e81e21b7e7629628
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95757415"
---
# <a name="tutorial-create-a-form-processing-app-with-ai-builder"></a>教學課程：使用 AI Builder 建立表單處理應用程式

[AI Builder](/ai-builder/overview) 是一項 Power Platform 功能，可讓您將流程自動化並預測結果，以提升業務效能。 您可以使用 AI Builder 表單處理來建立 AI 模型，以識別並擷來自表單文件的機碼值組和資料表資料。

> [!NOTE]
> 此專案也可作為 [Microsoft Learn 模組](/learn/modules/get-started-with-form-processing/)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立表單處理 AI 模型
> * 定型模型
> * 發佈您的模型以在 Azure Power Apps 或 Power Automate 中使用

## <a name="prerequisites"></a>必要條件

* 一組至少有五種相同類型的表單，可用於訓練/測試資料。 請參閱[為建置訓練資料集](./build-training-data-set.md) (機器翻譯)，以獲得產生訓練資料集的提示和選項。 在本快速入門中，您可以使用 [範例資料集](https://go.microsoft.com/fwlink/?linkid=2128080)中 **Train** 資料夾底下的檔案。
* Power Apps 或 Power Automate 授權 - 請參閱[授權指南](https://go.microsoft.com/fwlink/?linkid=2085130)。 授權必須包含 [Common Data Service](https://powerplatform.microsoft.com/common-data-service/)。
* AI Builder [附加元件或試用](https://go.microsoft.com/fwlink/?LinkId=2113956&clcid=0x409)。


## <a name="create-a-form-processing-project"></a>建立表單處理專案

1. 移至 [Power Apps](https://make.powerapps.com/) 或 [Power Automate](https://flow.microsoft.com/signin)，然後使用您的組織帳戶登入。
1. 在左側窗格中選取 [AI Builder] > [建置]。
1. 選取 [表單處理] 卡。
1. 輸入模型名稱。
1. 選取 [建立]。

## <a name="upload-and-analyze-documents"></a>上傳和分析文件

您需要在 [新增文件] 頁面中提供範例檔，針對要從中擷取資訊的表單類型將模型定型。 上傳文件後，AI Builder 會進行分析，以檢查文件是否足以將模型定型。

> [!NOTE]
> AI Builder 目前不支援下列類型的表單處理輸入資料：
>
> - 複雜資料表 (巢狀資料表、合併的標題或資料格等等)
> - 核取方塊或選項按鈕
> - 超過 50 頁的 PDF 文件
> - 可填寫的 PDF
>
> 如需輸入文件需求的詳細資訊，請參閱[輸入需求](./overview.md#input-requirements)。

### <a name="upload-your-documents"></a>上傳您的文件

1. 選取 [新增文件]，最少選取五個檔案，然後選取 [上傳]。
1. 完成上傳後，選取 [關閉]。
1. 接著選取 [分析]。

> [!NOTE] 
> 上傳這些文件後，仍然可以移除部分文件或上傳其他文件。

> [!div class="mx-imgBorder"]
> ![螢幕擷取畫面：醒目提示 [新增文件] 按鈕。](./media/tutorial-ai-builder/add-documents-page.png)

### <a name="analyze-your-documents"></a>分析您的文件

在分析步驟中，AI Builder 會檢查您上傳的文件，並偵測欄位和表格。 完成此作業所需的時間取決於所提供的文件數目，但是在大部分情況下，應該只需要幾分鐘的時間。

當分析完成時，請選取縮圖以開啟欄位選取項目體驗。

> [!IMPORTANT]
> 如果分析失敗，很可能是因為 AI Builder 無法偵測到您文件中的結構化文字。 確認您更新的文件遵循[輸入需求](./overview.md#input-requirements)。

## <a name="select-your-form-fields"></a>選取表單欄位

在欄位選取項目頁面上，您可以選擇要使用的欄位：

1. 若要選取欄位，請按一下表示在文件中偵測到之欄位的矩形，或按一下並拖曳來選取多個欄位。 您也可以從右側窗格中選取欄位。
1. 如果要將欄位重新命名以符合您的需求，或將已擷取的標籤正規化，請按一下所選欄位的名稱。

    按一下已偵測欄位時，會出現下列資訊：

    - **欄位名稱**：偵測到之欄位的標籤名稱。
    - **欄位值**：偵測到之欄位的值。

> [!div class="mx-imgBorder"]
> ![標記頁面](./media/tutorial-ai-builder/select-fields-page.png)

### <a name="label-undetected-fields"></a>標記未偵測到的欄位

如果模型未自動偵測到您要標記的欄位，您可以在其值周圍繪製一個矩形，然後在出現的對話方塊中輸入標籤名稱。

## <a name="train-your-model"></a>定型模型

1. 選取 [下一個]，以檢查您選取的表單欄位。 如果一切看起來都沒問題，請選取 [定型] 以將您的模型定型。

    > [!div class="mx-imgBorder"]
    > ![定型頁面](./media/tutorial-ai-builder/summary-train-page.png)
1. 訓練完成時，請選取 [訓練完成] 畫面中的 [移至詳細資料頁面]。
## <a name="quick-test-your-model"></a>快速測試您的模型

詳細資料頁面可讓您在發佈或使用模型之前先進行測試：

1. 在詳細資料頁面上選取 [快速測試]。
2. 您可以拖放文件，或選取 [從我的裝置上傳] 來上傳您的測試檔案。 快速測試應該只需要幾秒鐘的時間，就會顯示結果。
3. 您可以選取 [重新開始] 以執行另一個測試；如果已完成，請選取 [關閉]。

### <a name="troubleshooting-tips"></a>疑難排解秘訣

如果在某些欄位取得了不正確的結果或較低的信賴分數，請嘗試下列秘訣：

- 在每個欄位中使用具有不同值的表單進行重新定型。
- 使用一組較大的訓練文件重新定型。 您標記的文件越多，AI Builder 就更能學習如何更清楚地辨識欄位。
- 您可以只選取特定頁面進行定型，藉此將 PDF 檔案最佳化。 使用 [列印] > [列印至 PDF] 選項來選取文件中的特定頁面。

## <a name="publish-your-model"></a>發佈您的模型

如果您對模型感到滿意，請選取 [發佈] 加以發佈。 當發佈完成時，您的模型會進階為 **已發佈** 並可供使用。

> [!div class="mx-imgBorder"]
> ![發佈模型頁面](./media/tutorial-ai-builder/model-page.png)

發佈表單處理模型後，您可以在 [Power Apps 畫布應用程式](/ai-builder/form-processor-component-in-powerapps)或 [Power Automate](/ai-builder/form-processing-model-in-flow) 中使用。

## <a name="next-steps"></a>後續步驟

請遵循 AI Builder 文件，以了解如何使用表單處理模型。

* [在 Power Apps 中使用表單處理器元件](/ai-builder/form-processor-component-in-powerapps)
* [在 Power Automate 中使用表單處理模型](/ai-builder/form-processing-model-in-flow)