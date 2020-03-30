---
title: 使用媒體服務 v3 .NET - Azure 編碼自訂轉換 |微軟文檔
description: 本主題演示如何使用 Azure 媒體服務 v3 使用 .NET 對自訂轉換進行編碼。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ebe701032e6416b3e007a28db62f5a8235bb1bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80068036"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>如何使用自訂轉換進行編碼 - .NET

使用 Azure 媒體服務進行編碼時，可以根據行業最佳實踐快速啟動推薦的內置預設之一，如[流式處理檔](stream-files-tutorial-with-api.md)教程中所示。 您還可以構建自訂預設，以定位特定方案或設備要求。

## <a name="considerations"></a>考量

創建自訂預設時，需要考慮以下因素：

* AVC 內容上的高度和寬度的所有值必須為 4 的倍數。
* 在 Azure 媒體服務 v3 中，所有編碼位元速率均以每秒位表示。 這與 v2 API 的預設不同，後者使用千位/秒作為單元。 例如，如果 v2 中的位元速率指定為 128 （千位/秒），則 v3 中它將設置為 128000（位/秒）。

## <a name="prerequisites"></a>Prerequisites 

[建立媒體服務帳戶](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>下載範例

使用以下命令將包含完整 .NET Core 範例的 GitHub 存放庫複製到您的機器：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
自訂預設的範例位於 [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) 資料夾。

## <a name="create-a-transform-with-a-custom-preset"></a>使用自訂預設建立轉換 

建立新的[轉換](https://docs.microsoft.com/rest/api/media/transforms)時，您需要指定想要其產生的輸出是什麼。 必要的參數是 [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput) 物件，如下列程式碼所示。 每個 **TransformOutput** 都會包含 **Preset (預設)**。 **預設**描述了用於生成所需**轉換輸出**的視頻和/或音訊處理操作的分步說明。 下列 **TransformOutput** 會建立自訂轉碼器和圖層輸出設定。

建立[轉換](https://docs.microsoft.com/rest/api/media/transforms)時，您應該先使用 **Get** 方法檢查是否已有轉換存在，如下列程式碼所示。 在媒體服務 v3 中，如果實體不存在，**則實體**上的 Get 方法將返回**null（** 對名稱進行不區分大小寫的檢查）。

### <a name="example"></a>範例

下面的示例定義了一組使用此轉換時要生成的輸出。 我們首先為音訊編碼添加 AacAudio 圖層，為視頻編碼添加兩個 H264 視頻圖層。 在視頻圖層中，我們分配標籤，以便在輸出檔案名中使用。 接下來，我們希望輸出還包括縮略圖。 在下面的示例中，我們指定以 PNG 格式生成的圖像，以輸入視頻的 50% 解析度生成，並在輸入視頻長度的三個時間戳記中生成圖像 - [25%、50%、75]。 最後，我們指定輸出檔案的格式 - 一個用於視頻 + 音訊，另一個用於縮略圖。 由於我們擁有多個 H264Layers，因此我們必須使用每個圖層生成唯一名稱的宏。 我們可以使用 或`{Label}``{Bitrate}`宏，示例顯示前者。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>後續步驟

[串流檔案](stream-files-tutorial-with-api.md) 
