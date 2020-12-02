---
title: 定型及部署自訂語音模型-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何定型和部署自訂語音模型。 定型語音轉換文字模型可以改善 Microsoft 基準模型或自訂模型的辨識精確度。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 130cd643856b38471eac6d6869cdc1ed8b0bcd2e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499147"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>定型和部署自訂語音模型

在本文中，您將瞭解如何定型和部署自訂語音模型。 訓練語音轉換文字模型可以改善 Microsoft 基準模型的辨識精確度。 您可以使用人類標示的轉譯和相關文字來定型模型。 這些資料集（以及先前上傳的音訊資料）是用來精簡和定型語音轉換文字模型。

## <a name="use-training-to-resolve-accuracy-problems"></a>使用定型來解決精確度問題

如果您遇到基底模型的辨識問題，您可以使用人為標記的文字記錄和相關資料來定型自訂模型，並協助改善精確度。 您可以使用此資料表來判斷要使用哪一個資料集來解決您的問題：

| 使用案例 | 資料類型 |
| -------- | --------- |
| 改善業界專屬詞彙和文法的辨識準確度，例如醫療術語或 IT 專業術語 | 相關文字 (句子/語句)  |
| 定義包含非標準發音（例如產品名稱或縮略字）的單字或詞彙的語音和顯示形式 | 相關文字 (發音)  |
| 改善說話樣式、重音或特定背景雜訊的辨識精確度 | 音訊 + 人類標記的文字記錄 |

## <a name="train-and-evaluate-a-model"></a>定型及評估模型

定型模型的第一個步驟是上傳定型資料。 請參閱 [準備和測試您的資料](./how-to-custom-speech-test-and-train.md) ，以取得逐步指示，以準備人標記的轉譯和相關文字 (語句和發音) 。 上傳定型資料之後，請依照下列指示開始訓練您的模型：

1. 登入 [自訂語音入口網站](https://speech.microsoft.com/customspeech)。
2. 移至 **語音轉換文字**  >  **自訂語音**  >  **[專案的名稱]**  >  **訓練**。
3. 選取 [ **定型模型**]。
4. 提供訓練的 **名稱** 和 **描述**。
5. 在 [ **案例和基準模型** ] 清單中，選取最適合您的網域的案例。 如果您不確定要選擇哪一種案例，請選取 **[一般**]。 基準模型是定型的起點。 最新的模型通常是最佳選擇。
6. 在 [ **選取定型資料** ] 頁面上，選擇您要用於定型的一或多個音訊 + 人標示的轉譯資料集。
7. 定型完成之後，您可以在新定型的模型上執行精確度測試。 此為選用步驟。
8. 選取 [ **建立** ] 以建立您的自訂模型。

**定型** 資料表會顯示對應至新模型的新專案。 此資料表也會顯示狀態：「 **處理** 中」、「 **成功**」或「 **失敗**」。

請參閱 [如何](how-to-custom-speech-evaluate-data.md) 評估及改善自訂語音模型的精確度。 如果您選擇測試精確度，請務必選取與您在模型中使用的聲場資料集不同，以取得模型效能的實際意義。

## <a name="deploy-a-custom-model"></a>部署自訂模型

在您上傳和檢查資料、評估精確度以及定型自訂模型之後，您可以部署自訂端點，以搭配您的應用程式、工具和產品使用。 

若要建立自訂端點，請登入 [自訂語音入口網站](https://speech.microsoft.com/customspeech)。 在頁面頂端的 **自訂語音** 功能表中選取 [**部署**]。 如果這是您第一次執行，您會發現資料表中沒有列出任何端點。 建立端點之後，您可以使用此頁面來追蹤每個已部署的端點。

接下來，選取 [ **新增端點** ]，並輸入自訂端點的 **名稱** 和 **描述** 。 然後選取您想要與端點建立關聯的自訂模型。  您也可以從這個頁面啟用記錄功能。 記錄可讓您監視端點的流量。 如果記錄已停用，則不會儲存流量。

![顯示新端點頁面的螢幕擷取畫面。](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> 別忘了接受使用條款和定價詳細資料。

接下來，選取 [建立]。 此動作會將您返回 [ **部署** ] 頁面。 資料表現在包含對應至您自訂端點的專案。 端點的狀態會顯示其目前的狀態。 使用您的自訂模型來具現化新端點最多可能需要30分鐘的時間。 當部署的狀態變更為 [ **完成**] 時，端點就可供使用。

部署端點之後，端點名稱會顯示為連結。 選取連結以查看您端點的特定資訊，例如端點金鑰、端點 URL 和範例程式碼。

## <a name="view-logging-data"></a>查看記錄資料

您可以在 [**端點** 詳細資料] 下下載記錄資料  >  ****。
> [!NOTE]
>記錄資料可供 Microsoft 擁有的儲存體使用30天。 之後將會移除此檔案。 如果客戶擁有的儲存體帳戶連結到認知服務訂用帳戶，則不會自動刪除記錄資料。

## <a name="next-steps"></a>後續步驟

* [瞭解如何使用您的自訂模型](how-to-specify-source-language.md)

## <a name="additional-resources"></a>其他資源

- [準備及測試您的資料](./how-to-custom-speech-test-and-train.md)
- [檢查您的資料](how-to-custom-speech-inspect-data.md)
- [評估您的資料](how-to-custom-speech-evaluate-data.md)
