---
title: VM 大小
description: 描述可配置的相異 VM 大小
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.openlocfilehash: aab914caa2647146639aa366f558c80bebcfde54
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84809935"
---
# <a name="vm-sizes"></a>VM 大小

轉譯服務可以在 Azure 中的兩種不同類型的機器上運作，稱為 `Standard` 和 `Premium` 。

## <a name="polygon-limits"></a>多邊形限制

針對 VM 大小，有**20000000 個多邊形**的硬性限制 `Standard` 。 大小沒有這類限制 `Premium` 。

當標準 VM 上的轉譯器達到這項限制時，它會將轉譯切換為棋盤背景：

![棋盤式](media/checkerboard.png)

## <a name="allocate-the-vm"></a>配置 VM

需要在轉譯會話初始化時間指定所需的 VM 類型。 它不能在執行中的會話內進行變更。 下列程式碼範例顯示必須指定 VM 大小的位置：

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

針對[範例 PowerShell 腳本](../samples/powershell-example-scripts.md)，必須在檔案內指定 VM 大小 `arrconfig.json` ：

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

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>轉譯器如何評估多邊形數目

視為限制測試的多邊形數目是實際傳遞至轉譯器的多邊形數目。 這個幾何通常是所有具現化模型的總和，但也有例外狀況。 **不包含**下列幾何：
* 已載入完全位於視圖正下方的模型實例。
* 使用[階層式狀態覆寫元件](../overview/features/override-hierarchical-state.md)，切換為不可見的模型或模型元件。

因此，您可以撰寫一個應用程式，將目標設為 `standard` 載入多個模型的大小，且其多邊形計數接近每個單一模型的限制。 當應用程式一次只顯示單一模型時，就不會觸發棋盤。

### <a name="how-to-determine-the-number-of-polygons"></a>如何判斷多邊形數目

有兩種方式可判斷構成 VM 大小之預算限制的模型或場景的多邊形數目 `standard` ：
* 在模型轉換端，取出[轉換輸出 json](../how-tos/conversion/get-information.md)檔案，並檢查 `numFaces` [ *inputStatistics*區段](../how-tos/conversion/get-information.md#the-inputstatistics-section)中的專案
* 如果您的應用程式處理動態內容，則可以在執行時間中動態查詢轉譯的多邊形數目。 使用[效能評定查詢](../overview/features/performance-queries.md#performance-assessment-queries)，並檢查 `polygonsRendered` 結構中的成員 `FrameStatistics` 。 棋盤背景一律會淡入，並會有一些延遲，以確保使用者可以在這個非同步查詢之後採取動作。 實例的使用者動作可以隱藏或刪除模型實例。

## <a name="pricing"></a>定價

如需每種 VM 類型的定價詳細資訊，請參閱[遠端轉譯定價](https://azure.microsoft.com/pricing/details/remote-rendering)頁面。

## <a name="next-steps"></a>後續步驟
* [PowerShell 指令碼範例](../samples/powershell-example-scripts.md)
* [模型轉換](../how-tos/conversion/model-conversion.md)

