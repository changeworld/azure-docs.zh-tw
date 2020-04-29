---
title: 轉譯模式
description: 描述不同的伺服器端呈現模式
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 7f2b1031659864ae338bb0aa320c048ea23c21f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681697"
---
# <a name="rendering-modes"></a>轉譯模式

「遠端呈現」提供兩種主要的作業模式： **TileBasedComposition**模式和**DepthBasedComposition**模式。 這些模式會決定如何在伺服器上的多個 Gpu 間散發工作負載。 模式必須在連接時指定，而且無法在執行時間變更。

這兩種模式都有優點，但也具有固有的功能限制，因此，挑選最適合的模式是使用案例特定。

## <a name="modes"></a>模式

這兩種模式現在會更詳細地討論。

### <a name="tilebasedcomposition-mode"></a>' TileBasedComposition ' 模式

在**TileBasedComposition**模式中，每個相關的 GPU 都會呈現螢幕上的特定 subrectangles （磚）。 主要 GPU 會先將磚中的最後一個影像當做影片框架傳送給用戶端。 因此，所有 Gpu 都必須有相同的資源集合，才能呈現，因此載入的資產必須符合單一 GPU 的記憶體。

此模式中的轉譯品質稍微優於**DepthBasedComposition**模式，因為 MSAA 可以在每個 GPU 的一組完整幾何上作用。 下列螢幕擷取畫面顯示這兩個邊緣的消除鋸齒功能會同樣地運作：

![TileBasedComposition 中的 MSAA](./media/service-render-mode-quality.png)

此外，在此模式中，每個元件都可以切換到透明的資料，或透過[HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)切換至**查看**模式

### <a name="depthbasedcomposition-mode"></a>' DepthBasedComposition ' 模式

在**DepthBasedComposition**模式中，每個相關的 GPU 都會以全螢幕解析度轉譯，但只會呈現網格的子集。 主要 GPU 上的最後一個影像組合會負責根據其深度資訊適當地合併元件。 自然地，記憶體承載會分散在 Gpu 上，因此可讓您將無法放入單一 GPU 記憶體中的模型轉譯。

每個單一 GPU 都會使用 MSAA 來消除本機內容。 不過，不同 Gpu 的邊緣之間可能會有固有的別名。 這種效果是藉由後處理最後一個影像來減輕的，但是 MSAA 品質仍然比**TileBasedComposition**模式更糟。

下圖說明 MSAA 成品： ![DepthBasedComposition 中的 msaa](./media/service-render-mode-balanced.png)

Sculpture 與揭開之間的消除鋸齒功能正常運作，因為這兩個元件都是在相同的 GPU 上轉譯。 另一方面，揭開和牆之間的邊緣會顯示一些別名，因為這兩個部分是由不同的 Gpu 所組成。

此模式的最大限制是，geometry 元件無法動態切換到透明材質，也無法進行[HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)的**查看**模式。 不過，其他狀態覆寫功能（外框、色彩淡色 ...）的確可行。 此外，在轉換時間標示為透明的材質，在此模式下也能正常運作。

### <a name="performance"></a>效能

這兩種模式的效能特性會因使用案例而有所不同，因此很難或提供一般建議。 如果您不受上述限制（記憶體或透明度/查看）的限制，建議您嘗試兩種模式，並使用各種相機位置來監視效能。

## <a name="setting-the-render-mode"></a>設定轉譯模式

遠端呈現 VM 上所使用的轉譯模式是透過在`AzureSession.ConnectToRuntime`期間指定`ConnectToRuntimeParams`。

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
* [階層式狀態覆寫元件](../overview/features/override-hierarchical-state.md)
