---
title: 使用媒體服務 v3 .NET 來編碼自訂轉換-Azure |Microsoft Docs
description: 本主題說明如何使用 Azure 媒體服務 v3 來編碼使用 .NET 的自訂轉換。
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
ms.openlocfilehash: 2bcd5f0e6229c4130dddb48c1a20de1c711c6fcf
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86519876"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>如何使用自訂轉換進行編碼-.NET

當使用 Azure 媒體服務進行編碼時，您可以根據如[串流](stream-files-tutorial-with-api.md)檔案教學課程中所示範的業界最佳作法，快速開始使用其中一個建議的內建預設值。 您也可以建立自訂預設值，以特定案例或裝置需求為目標。

## <a name="considerations"></a>考量

建立自訂預設時，適用下列考慮事項：

* AVC 內容的所有高度和寬度值都必須是4的倍數。
* 在 Azure 媒體服務 v3 中，所有編碼位元速率都是以每秒位數為單位。 這不同于預設值和我們的 v2 Api，其使用千位/秒作為單位。 例如，如果 v2 中的位元速率指定為128（千位/秒），則在 v3 中，它會設定為128000（位/秒）。

## <a name="prerequisites"></a>必要條件 

[建立媒體服務帳戶](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>下載範例

使用以下命令將包含完整 .NET Core 範例的 GitHub 存放庫複製到您的機器：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
自訂預設的範例位於 [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) 資料夾。

## <a name="create-a-transform-with-a-custom-preset"></a>使用自訂預設建立轉換 

建立新的[轉換](https://docs.microsoft.com/rest/api/media/transforms)時，您需要指定想要其產生的輸出是什麼。 必要的參數是 [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput) 物件，如下列程式碼所示。 每個 **TransformOutput** 都會包含 **Preset (預設)** 。 預設**會描述影片**和/或音訊處理作業的逐步指示，用來產生所需的**TransformOutput**。 下列 **TransformOutput** 會建立自訂轉碼器和圖層輸出設定。

建立[轉換](https://docs.microsoft.com/rest/api/media/transforms)時，您應該先使用 **Get** 方法檢查是否已有轉換存在，如下列程式碼所示。 在媒體服務 v3 中，如果實體不存在，實體的**Get**方法會傳回**null** （不區分大小寫的名稱檢查）。

### <a name="example"></a>範例

下列範例會定義當使用此轉換時，我們想要產生的一組輸出。 我們會先新增音訊編碼的 Aacaudio 屬性圖層，以及用於影片編碼的兩個 H264Video 圖層。 在影片圖層中，我們會指派標籤，以便在輸出檔案名中使用。 接下來，我們希望輸出也包含縮圖。 在下列範例中，我們會以 PNG 格式來指定影像，在輸入影片的解析度50% 產生，並以三個時間戳記（{25%，50%，75%}）輸入影片的長度。 最後，我們會指定輸出檔案的格式：一個用於 video + 音訊，另一個用於縮圖。 因為我們有多個 H264Layers，所以我們必須使用每個圖層產生唯一名稱的宏。 我們可以使用 `{Label}` 或 `{Bitrate}` 宏，此範例會顯示前者。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>後續步驟

[串流檔案](stream-files-tutorial-with-api.md) 
