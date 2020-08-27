---
title: 使用預測端點以程式設計方式利用分類器測試影像 - 自訂視覺
titleSuffix: Azure Cognitive Services
description: 了解如何使用 API，以程式設計方式利用您的自訂視覺服務分類器來測試影像。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 7f1939536e033d2cf964dd2f4ee562e4ee20061b
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934747"
---
# <a name="use-your-model-with-the-prediction-api"></a>使用您的模型搭配預測 API

定型您的模型之後，您可以藉由將影像提交至預測 API 端點，以程式設計方式測試影像。

> [!NOTE]
> 本文件示範如何使用 C#，送出影像到預測 API。 如需詳細資訊和範例，請參閱 [預測 API 參考](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)。

## <a name="publish-your-trained-iteration"></a>發佈您已定型的反復專案

從[自訂視覺網頁](https://customvision.ai) \(英文\) 選取您的專案，然後選取 [效能]____ 索引標籤。

若要將影像提交至預測 API，您必須先發佈反覆運算以進行預測，您可以選取 [ __發行__ ] 並指定已發行之反復專案的名稱來完成。 這會讓您的模型可供您自訂視覺 Azure 資源的預測 API 存取。

![[效能] 索引標籤會顯示，其中有一個圍繞 [發佈] 按鈕的紅色矩形。](./media/use-prediction-api/unpublished-iteration.png)

成功發佈模型之後，您會看到左側提要欄位中的反復專案旁出現「已發佈」標籤，而且其名稱會出現在反復專案的描述中。

![[效能] 索引標籤會顯示，其中有一個圍繞已發行標籤的紅色矩形，以及已發佈的反復專案名稱。](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>取得 URL 和預測金鑰

一旦發行您的模型之後，您就可以選取 [ __預測 URL__] 來取得所需的資訊。 這會開啟一個對話方塊，其中包含使用預測 API 的資訊，包括 __預測 URL__ 和 __預測金鑰__。

![[效能] 索引標籤會顯示在 [預測 URL] 按鈕周圍的紅色矩形。](./media/use-prediction-api/published-iteration-prediction-url.png)

![[效能] 索引標籤會顯示在預測 URL 值周圍的紅色矩形，以使用影像檔案和預測金鑰值。](./media/use-prediction-api/prediction-api-info.png)


在本指南中，您將使用本機映射，因此， **如果您有** 一個暫存位置的影像檔案，請複製下的 URL。 也複製對應的 __預測__ 索引鍵值。

## <a name="create-the-application"></a>建立應用程式

1. 在 Visual Studio 中，建立新的 c # 主控台應用程式。

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
   * 將 `namespace` 欄位設定為專案的名稱。
   * 將預留位置取代為 `<Your prediction key>` 您稍早取出的索引鍵值。
   * 將預留位置取代為 `<Your prediction URL>` 您稍早取出的 URL。

## <a name="run-the-application"></a>執行應用程式

當您執行應用程式時，系統會提示您在主控台中輸入影像檔案的路徑。 然後，會將影像提交至預測 API，並以 JSON 格式的字串傳回預測結果。 以下是範例回應。

```json
{
    "id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "created":"2019-03-20T16:47:31.322Z",
    "predictions":[
        {"tagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","tagName":"cat", "probability":1.0},
        {"tagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","tagName":"dog", "probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>後續步驟

在本指南中，您已瞭解如何將影像提交至自訂影像分類器/偵測器，並使用 c # SDK 以程式設計方式接收回應。 接下來，瞭解如何使用 c # 完成端對端案例，或開始使用不同的語言 SDK。

* [快速入門：自訂視覺 SDK](quickstarts/image-classification.md)
