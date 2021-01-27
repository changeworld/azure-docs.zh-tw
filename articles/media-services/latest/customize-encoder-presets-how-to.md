---
title: 使用媒體服務 v3 .NET 編碼自訂轉換-Azure
description: 本主題說明如何使用 Azure 媒體服務 v3，利用 .NET 來編碼自訂轉換。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 035afd43b49c05f51149ad568a0eb16991dbaf85
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897168"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>如何使用自訂轉換進行編碼-.NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

使用 Azure 媒體服務進行編碼時，您可以使用其中一個建議的內建預設，根據「 [串流](stream-files-tutorial-with-api.md) 檔案」教學課程中所示範的業界最佳作法來快速開始。 您也可以建立自訂預設值，以特定案例或裝置需求為目標。

## <a name="considerations"></a>考量

建立自訂預設時，適用下列考慮事項：

* AVC 內容的所有高度和寬度值都必須是4的倍數。
* 在 Azure 媒體服務 v3 中，所有編碼位元速率都是每秒位數。 這與使用 v2 Api 的預設值不同，後者使用千位/秒作為單位。 例如，如果 v2 中的位元速率指定為 128 (千位/秒) ，則在 v3 中，它會設定為 128000 (位/秒) 。

## <a name="prerequisites"></a>必要條件 

[建立媒體服務帳戶](./create-account-howto.md)

## <a name="download-the-sample"></a>下載範例

使用以下命令將包含完整 .NET Core 範例的 GitHub 存放庫複製到您的機器：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
自訂預設的範例位於 [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) 資料夾。

## <a name="create-a-transform-with-a-custom-preset"></a>使用自訂預設建立轉換 

建立新的[轉換](/rest/api/media/transforms)時，您需要指定想要其產生的輸出是什麼。 必要的參數是 [TransformOutput](/rest/api/media/transforms/createorupdate#transformoutput) 物件，如下列程式碼所示。 每個 **TransformOutput** 都會包含 **Preset (預設)** 。 預設 **會描述影片** 和/或音訊處理作業的逐步指示，這些作業是用來產生所需的 **TransformOutput**。 下列 **TransformOutput** 會建立自訂轉碼器和圖層輸出設定。

建立 [轉換](/rest/api/media/transforms)時，您應該先使用 **Get** 方法檢查是否已有轉換存在，如下列程式碼所示。 在媒體服務 v3 中，如果實體不存在，則 **取得** 實體的方法會傳回 **null** (名稱) 不區分大小寫檢查。

### <a name="example"></a>範例

下列範例會定義一組我們想要在使用此轉換時產生的輸出。 我們會先新增音訊編碼的 >aacaudio 層，以及影片編碼的兩個 >h264video 圖層。 在影片圖層中，我們會指派標籤，讓它們可用於輸出檔名稱。 接下來，我們希望輸出也包含縮圖。 在下列範例中，我們會以 PNG 格式來指定影像，並以50% 的輸入影片解析度產生，以及在輸入影片長度的三個時間戳記-{25%、50%、75%} 產生。 最後，我們會指定輸出檔的格式-一個適用于影片 + 音訊，另一個用於縮圖。 由於有多個 H264Layers，因此我們必須使用每一層產生唯一名稱的宏。 我們可以使用 `{Label}` 或 `{Bitrate}` 宏，此範例會顯示前者。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>後續步驟

[串流檔案](stream-files-tutorial-with-api.md) 
