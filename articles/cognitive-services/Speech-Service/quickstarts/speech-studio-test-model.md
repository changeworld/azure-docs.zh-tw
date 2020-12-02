---
title: 使用音訊檔案來測試模型 - Speech Studio
titleSuffix: Azure Cognitive Services
description: 在此操作說明中，您會使用 Speech Studio 來測試音訊檔案中的語音辨識。
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: v-demjoh
ms.openlocfilehash: 572b3b3459e1d837130f3c987d45ee45629f37ad
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485071"
---
# <a name="test-a-model-using-an-audio-file-in-speech-studio"></a>在 Speech Studio 中使用音訊檔案來測試模型

在此操作說明中，您會使用 Speech Studio 將語音從音訊檔案轉換成文字。 Speech Studio 可讓您使用相關的文字、具有人為標記之文字記錄的音訊，以及您提供的發音指引，來測試、比較、改進及部署語音辨識模型。

## <a name="prerequisites"></a>必要條件

使用語音入口網站之前，請[遵循這些指示來建立 Azure 帳戶，並訂閱語音服務](../custom-speech-overview.md#set-up-your-azure-account)。 此整合訂用帳戶可讓您存取語音轉換文字、文字轉換語音、語音翻譯與自訂語音入口網站。

## <a name="download-an-audio-file"></a>下載音訊檔案

請遵循下列步驟來下載包含語音的音訊檔案，並將其封裝成 zip 檔案。

1. 以滑鼠右鍵按一下連結，然後選取 [另存連結]，以 **[從此連結下載範例 wav 檔案](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav)** 。 按一下 [儲存] 以下載 `whatstheweatherlike.wav` 檔案。
2. 使用檔案瀏覽器或終端機視窗搭配 zip 工具，建立名為 `whatstheweatherlike.zip` 的 zip 檔案，其中包含您下載的 `whatstheweatherlike.wav` 檔案。 在 Windows 中，您可以開啟 Windows 檔案總管並瀏覽至 `Downloads` 資料夾、以滑鼠右鍵按一下 `whatstheweatherliike.wav`、按一下 [傳送到]、按一下 [壓縮的 (zipped) 資料夾]，然後按 Enter 以接受預設檔案名稱。

## <a name="create-a-project-in-the-custom-speech-portal"></a>在自訂語音入口網站中建立專案

請遵循下列步驟來建立包含一個音訊檔案之 zip 的專案。

1. 開啟 [Visual Studio](https://speech.microsoft.com/)，然後按一下 [新增專案]。 輸入此專案的名稱，然後按一下 [建立]。 您的專案會出現在 [自訂語音] 清單中。
2. 按一下您專案的名稱。 在 [資料] 索引標籤中，按一下 [上傳資料]。
3. 語音資料類型預設為 [僅音訊]，因此請按一下 [下一步]。
4. 將新的語音資料集命名為 `MyZipOfAudio`，然後按一下 [下一步]。
5. 按一下 [瀏覽檔案]、瀏覽至您的 `whatstheweatherlike.zip` 檔案，然後按一下 [開啟]。
6. 按一下 [上傳]  按鈕。 瀏覽器會將您的 zip 檔案上傳至 Speech Studio，而 Speech Studio 會處理內容。

## <a name="test-a-model"></a>測試模型

在 Speech Studio 處理您 zip 檔案的內容之後，您可以在檢查文字記錄以尋找錯誤或疏漏時播放來源音訊。 請遵循下列步驟來檢查瀏覽器中的文字記錄品質。

1. 按一下 [測試] 索引標籤，然後按一下 [新增測試]。
2. 在此測試中，我們要檢查僅限音訊資料的品質，因此按一下 [下一步] 以接受此測試類型。
3. 將此測試命名為 `MyModelTest`，然後按一下 [下一步]。
4. 按一下 `MyZipOfAudio` 左邊的選項按鈕，然後按一下 [下一步]。
5. [模型 1] 下拉式清單預設為最新的辨識模型，因此，請按一下 [建立]。 在處理音訊資料集的內容之後，測試狀態將會變更為 [成功]。
6. 按一下 [MyModelTest]。 語音辨識的結果隨即出現。 按一下圓形中的向右三角形以聆聽音訊，並將您聽到的內容與圓圈中的文字進行比較。

## <a name="download-detailed-results"></a>下載詳細結果

如需更詳細的資訊，您可以下載描述文字記錄的檔案。 此檔案會在您的音訊檔案中包含語音的文字形式，以及包含每個單字之位移、持續時間與文字記錄信賴度詳細資料的 JSON 檔案。 若要查看這些檔案，請遵循下列步驟。

1. 按一下 [下載] 。
2. 在 [下載] 對話方塊中，取消選取 [音訊]，然後按一下 [下載]。
3. 將下載的 zip 檔案解壓縮，並檢查解壓縮的檔案。

## <a name="next-steps"></a>後續步驟

了解如何透過[將自訂模型定型](../how-to-custom-speech-test-and-train.md)，以改善語音辨識的精確度。