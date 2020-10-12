---
title: 伺服器大小
description: 描述可配置的相異伺服器大小
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.custom: devx-track-csharp
ms.openlocfilehash: b37aabb39e19fa5ec53d2b006a7cbc1793adad72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90988037"
---
# <a name="server-sizes"></a>伺服器大小

Azure 遠端轉譯可在兩個伺服器設定中使用： `Standard` 和 `Premium` 。

## <a name="polygon-limits"></a>多邊形限制

具有 `Standard` 伺服器大小的遠端轉譯的場景大小上限為20000000個多邊形。 大小的遠端轉譯 `Premium` 並不會強制執行最大的限制，但如果您的內容超過服務的轉譯功能，效能可能會降低。

當「標準」伺服器大小上的轉譯器達到這項限制時，它會將轉譯切換至棋盤背景：

![螢幕擷取畫面顯示使用 [工具] 功能表的黑色和白色方塊方格。](media/checkerboard.png)

## <a name="specify-the-server-size"></a>指定伺服器大小

需要在轉譯會話初始化階段指定伺服器設定的類型。 它無法在執行中的會話內變更。 下列程式碼範例會顯示必須指定伺服器大小的位置：

```cs
async void CreateRenderingSession(AzureFrontend frontend)
{
    RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
    sessionCreationParams.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend)
{
    RenderingSessionCreationParams sessionCreationParams;
    sessionCreationParams.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    if (auto createSessionAsync = frontend->CreateNewRenderingSessionAsync(sessionCreationParams))
    {
        // ...
    }
}
```

針對 [範例 PowerShell 腳本](../samples/powershell-example-scripts.md)，必須在檔案中指定所需的伺服器大小 `arrconfig.json` ：

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>轉譯器如何評估多邊形的數目

針對限制測試考慮的多邊形數目是實際傳遞給轉譯器的多邊形數目。 這個幾何通常是所有具現化模型的總和，但也有例外狀況。 **不包含**下列幾何：
* 已載入的模型實例，完全位於視圖的正下方。
* 使用 [階層式狀態覆寫元件](../overview/features/override-hierarchical-state.md)切換為不可見的模型或模型元件。

因此，您可以撰寫一個應用程式，該應用程式的目標大小會將 `standard` 多個模型的多邊形計數設定為接近每個單一模型的限制。 當應用程式一次只顯示單一模型時，就不會觸發棋盤。

### <a name="how-to-determine-the-number-of-polygons"></a>如何判斷多邊形的數目

有兩種方式可判斷模型或場景的多邊形數目，而這些多邊形的組成設定大小的預算限制 `standard` ：
* 在模型轉換端，取出[轉換輸出 json](../how-tos/conversion/get-information.md)檔案，並檢查 `numFaces` [ *inputStatistics*區段](../how-tos/conversion/get-information.md#the-inputstatistics-section)中的專案
* 如果您的應用程式正在處理動態內容，則在執行時間期間可動態查詢轉譯的多邊形數目。 使用 [效能評定查詢](../overview/features/performance-queries.md#performance-assessment-queries) 並檢查 `polygonsRendered` 結構中的成員 `FrameStatistics` 。 當轉譯器 `polygonsRendered` `bad` 達到多邊形限制時，欄位將會設定為。 棋盤背景一律會淡入，並有一些延遲，以確保使用者可以在這個非同步查詢之後採取動作。 例如，使用者動作可以隱藏或刪除模型實例。

## <a name="pricing"></a>定價

如需每種設定類型定價的詳細明細，請參閱 [遠端轉譯定價](https://azure.microsoft.com/pricing/details/remote-rendering) 頁面。

## <a name="next-steps"></a>後續步驟
* [PowerShell 指令碼範例](../samples/powershell-example-scripts.md)
* [模型轉換](../how-tos/conversion/model-conversion.md)

