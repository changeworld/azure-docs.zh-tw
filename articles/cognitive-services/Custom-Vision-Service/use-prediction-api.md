---
title: 使用預測端點以程式設計方式利用分類器測試影像 - 自訂視覺
titleSuffix: Azure Cognitive Services
description: 了解如何使用 API，以程式設計方式利用您的自訂視覺服務分類器來測試影像。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: dcb12da680d70e1f0ce4cd763bee340bb3416c6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169940"
---
# <a name="use-your-model-with-the-prediction-api"></a>將模型與預測 API 一起使用

訓練模型後，可以通過將圖像提交到預測 API 終結點來以程式設計方式測試圖像。

> [!NOTE]
> 本文件示範如何使用 C#，送出影像到預測 API。 有關詳細資訊和示例，請參閱[預測 API 引用](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)。

## <a name="publish-your-trained-iteration"></a>發佈經過培訓的反覆運算

從[自訂視覺網頁](https://customvision.ai) \(英文\) 選取您的專案，然後選取 [效能]____ 索引標籤。

要將圖像提交到預測 API，首先需要發佈反覆運算以供預測，這可以通過選擇 __"發佈"__ 和指定已發佈反覆運算的名稱來完成。 這將使您的模型可供自訂視覺 Azure 資源的預測 API 訪問。

![將顯示性能選項卡，在"發佈"按鈕周圍有一個紅色矩形。](./media/use-prediction-api/unpublished-iteration.png)

成功發佈模型後，左側側邊欄中會出現反覆運算旁邊的"已發佈"標籤，其名稱將顯示在反覆運算說明中。

![將顯示性能選項卡，在"已發佈"標籤周圍有一個紅色矩形，並顯示已發佈反覆運算的名稱。](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>取得 URL 和預測金鑰

發佈模型後，您可以通過選擇__預測 URL__來檢索所需的資訊。 這將打開一個對話方塊，其中包含用於使用預測 API 的資訊，包括__預測 URL__和__預測鍵__。

![性能選項卡顯示，預測 URL 按鈕周圍有一個紅色矩形。](./media/use-prediction-api/published-iteration-prediction-url.png)

![性能選項卡顯示的是一個紅色矩形，圍繞"預測 URL"值，用於使用影像檔和預測鍵值。](./media/use-prediction-api/prediction-api-info.png)


在本指南中，您將使用本地圖像，因此在 **"如果有影像檔"** 下將 URL 複製到臨時位置。 也複製相應的__預測鍵__值。

## <a name="create-the-application"></a>建立應用程式

1. 在視覺化工作室中，創建新的 C# 主控台應用程式。

1. 使用下列程式碼作為 __Program.cs__ 檔案的主體。

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. 變更下列資訊：
   * 將`namespace`欄位設置為專案名稱。
   * 將預留位置`<Your prediction key>`替換為前面檢索的鍵值。
   * 將預留位置`<Your prediction URL>`替換為您之前檢索的 URL。

## <a name="run-the-application"></a>執行應用程式

運行應用程式時，系統會提示您在主控台中輸入映射檔的路徑。 然後，圖像將提交到預測 API，並將預測結果作為 JSON 格式的字串返回。 下面是一個示例回應。

```json
{
    "Id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "Project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "Iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "Created":"2019-03-20T16:47:31.322Z",
    "Predictions":[
        {"TagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","TagName":"cat", "Probability":1.0},
        {"TagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","TagName":"dog", "Probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>後續步驟

在本指南中，您學習了如何將圖像提交到自訂圖像分類器/檢測器，以及如何使用 C# SDK 以程式設計方式接收回應。 接下來，瞭解如何使用 C# 完成端到端方案，或使用其他語言 SDK 開始。

* [快速入門： .NET SDK](csharp-tutorial.md)
* [快速入門：Python SDK](python-tutorial.md)
* [快速入門：JAVA SDK](java-tutorial.md)
* [快速入門：節點 SDK](node-tutorial.md)
* [快速入門：轉到 SDK](go-tutorial.md)
