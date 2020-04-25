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
ms.openlocfilehash: 88aaa3d54ea44a15f7900aba093bf28c70c19695
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82129865"
---
# <a name="use-your-model-with-the-prediction-api"></a>搭配預測 API 使用您的模型

定型模型之後，您可以將影像提交至預測 API 端點，以程式設計方式測試它們。

> [!NOTE]
> 本文件示範如何使用 C#，送出影像到預測 API。 如需詳細資訊和範例，請參閱[預測 API 參考](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)。

## <a name="publish-your-trained-iteration"></a>發行定型的反復專案

從[自訂視覺網頁](https://customvision.ai) \(英文\) 選取您的專案，然後選取 [效能]____ 索引標籤。

若要將影像提交至預測 API，您必須先發佈反復專案以進行預測，這可以藉由選取 [__發行__] 並指定已發行之反復專案的名稱來完成。 這會讓您的模型可供自訂視覺 Azure 資源的預測 API 存取。

![[效能] 索引標籤隨即顯示，並以紅色矩形括住 [發行] 按鈕。](./media/use-prediction-api/unpublished-iteration.png)

成功發行模型之後，您會看到左側提要欄位中的反復專案旁邊出現「已發行」標籤，而且其名稱會出現在反復專案的描述中。

![[效能] 索引標籤隨即顯示，並以紅色矩形括住已發行的標籤和已發佈的反復專案名稱。](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>取得 URL 和預測金鑰

一旦您的模型發佈之後，您就可以選取 [__預測 URL__] 來抓取所需的資訊。 這會開啟一個對話方塊，其中包含使用預測 API 的資訊，包括__預測 URL__和__預測金鑰__。

![[效能] 索引標籤會顯示在 [預測 URL] 按鈕周圍的紅色矩形。](./media/use-prediction-api/published-iteration-prediction-url.png)

![[效能] 索引標籤會顯示在 [預測 URL] 值周圍的紅色矩形，用於使用影像檔和預測索引鍵值。](./media/use-prediction-api/prediction-api-info.png)


在本指南中，您將使用本機影像，因此，**如果您有影像檔**案到暫存位置，請複製下的 URL。 也複製對應的__預測索引鍵值__。

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
   * 將`namespace`欄位設定為專案的名稱。
   * 以您稍`<Your prediction key>`早取得的金鑰值取代預留位置。
   * 將預留位置`<Your prediction URL>`取代為您稍早取得的 URL。

## <a name="run-the-application"></a>執行應用程式

當您執行應用程式時，系統會提示您在主控台中輸入影像檔案的路徑。 然後，影像會提交至預測 API，而預測結果會以 JSON 格式的字串傳回。 以下是範例回應。

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

在本指南中，您已瞭解如何使用 c # SDK，以程式設計方式將影像提交至您的自訂影像分類器/偵測器，並接收回應。 接下來，瞭解如何使用 c # 完成端對端案例，或開始使用不同的語言 SDK。

* [快速入門：自訂視覺 SDK](quickstarts/image-classification.md)
