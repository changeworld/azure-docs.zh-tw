---
title: 成像模式
description: 描述不同的伺服器端的模式
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 7f2b1031659864ae338bb0aa320c048ea23c21f3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681697"
---
# <a name="rendering-modes"></a>成像模式

遠端渲染提供了兩種主要操作模式:**基於切片的合成**模式和**深度合成**模式。 這些模式確定工作負載如何分佈在伺服器上的多個 GPU 中。 必須在連接時指定該模式,並且在運行時無法更改。

這兩種模式都有優勢,但也具有固有的功能限制,因此選擇最合適的模式是特定於用例的。

## <a name="modes"></a>模式

現在更詳細地討論了這兩種模式。

### <a name="tilebasedcomposition-mode"></a>"基於'切片的合成"模式

在**基於磁貼的合成**模式下,每個涉及的 GPU 都會在螢幕上渲染特定的子矩形(切片)。 主 GPU 在以影片幀形式發送到用戶端之前,從切片中組成最終圖像。 因此,所有 GPU 都需要具有相同的資源進行渲染,因此載入的資產需要適合單個 GPU 的記憶體。

此模式下的渲染品質略好於**深度基合成**模式,因為 MSAA 可以針對每個 GPU 處理完整的幾何集。 以下螢幕截圖顯示,防鋸齒同樣適用於兩個邊:

![基於切片的合成中的 MSAA](./media/service-render-mode-quality.png)

此外,在此模式下,每個部件都可以切換到透明材質,或通過[分層狀態覆蓋元件](../overview/features/override-hierarchical-state.md)切換到**透明**模式

### <a name="depthbasedcomposition-mode"></a>"深度組合"模式

在**深度基於的合成**模式下,每個涉及的 GPU 以全屏解析度呈現,但僅呈現一個網件的子集。 主 GPU 上的最終圖像合成可注意根據零件的深度資訊正確合併部件。 當然,記憶體負載分佈在 GPU 中,因此允許渲染不適合單個 GPU 記憶體的模型。

每個 GPU 都使用 MSAA 來抗鋸齒本地內容。 但是,不同 GPU 的邊之間可能存在固有的混疊。 通過後處理最終圖像來緩解此效果,但 MSAA 品質仍比**基於磁貼的合成**模式差。

MSAA 工件圖如下![: 基於深度的 MSAA](./media/service-render-mode-balanced.png)

抗鋸齒在雕塑和窗簾之間正常工作,因為這兩個部分都在同一個 GPU 上渲染。 另一方面,窗簾和牆之間的邊緣顯示一些別名,因為這兩個部分由不同的 GPU 組成。

這個模式的最大限制是幾何零件不能動態切換到透明材質,**而透明**模式也不能適用於[分層狀態覆蓋元件](../overview/features/override-hierarchical-state.md)。 但是,其他狀態覆蓋功能(輪廓、顏色色調、...)確實可以正常工作。 此外,在轉換時標記為透明的材料在此模式下正常工作。

### <a name="performance"></a>效能

這兩種模式的性能特性確實因用例而異,很難推理或提供一般性建議。 如果您不受上述限制(記憶體或透明度/查看)的限制,建議嘗試這兩種模式,並使用各種攝像機位置監控性能。

## <a name="setting-the-render-mode"></a>設定成像模式

遠端渲染 VM 上使用的渲染`AzureSession.ConnectToRuntime`模式`ConnectToRuntimeParams`在 期間通過 指定。

```cs
async void ExampleConnect(AzureSession session)
{
    ConnectToRuntimeParams parameters = new ConnectToRuntimeParams();

    // Connect with one rendering mode
    parameters.mode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();

    session.DisconnectFromRuntime();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.mode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();
}
```

## <a name="next-steps"></a>後續步驟

* [工作階段](../concepts/sessions.md)
* [階層狀態覆蓋元件](../overview/features/override-hierarchical-state.md)
