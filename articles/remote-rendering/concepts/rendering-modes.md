---
title: 轉譯模式
description: 描述不同的伺服器端呈現模式
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 0e61ea423cb94dc1192d6b189a95e5a5da3b38ad
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613912"
---
# <a name="rendering-modes"></a>轉譯模式

遠端轉譯提供兩種主要的作業模式： **TileBasedComposition** 模式和 **DepthBasedComposition** 模式。 這些模式會決定如何將工作負載分散到伺服器上的多個 Gpu。 模式必須在連接時指定，且在執行時間不能變更。

這兩種模式都有優點，但也有固有的功能限制，因此挑選最適合的模式是使用案例特定的。

## <a name="modes"></a>模式

現在會更詳細地討論這兩種模式。

### <a name="tilebasedcomposition-mode"></a>' TileBasedComposition ' 模式

在 **TileBasedComposition** 模式中，每個相關的 GPU 都會轉譯畫面上) 的特定 subrectangles (圖格。 主要 GPU 會先從磚撰寫最終影像，再將它當作影片框架傳送給用戶端。 因此，所有 Gpu 都需要有相同的資源集合，才能進行轉譯，因此載入的資產必須符合單一 GPU 的記憶體。

在此模式中的轉譯品質稍微優於 **DepthBasedComposition** 模式，因為 MSAA 可以處理每個 GPU 的一組完整幾何。 下列螢幕擷取畫面顯示兩個邊緣的消除鋸齒同樣可以正常運作：

![TileBasedComposition 中的 MSAA](./media/service-render-mode-quality.png)

此外，在此模式下，每個元件都可以切換到透明材質，或透過[HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)切換到**查看**模式

### <a name="depthbasedcomposition-mode"></a>' DepthBasedComposition ' 模式

在 **DepthBasedComposition** 模式中，每個相關的 GPU 都會以全螢幕解析度轉譯，但只呈現網格的子集。 主要 GPU 上的最後一個影像組合，會負責根據這些零件的深度資訊來適當地合併這些零件。 本質上，記憶體承載會分散在 Gpu 上，因此可讓您呈現不適合單一 GPU 記憶體的模型。

每個單一 GPU 都會使用 MSAA 來消除本機內容的鋸齒。 不過，不同 Gpu 的邊緣之間可能會有固有的別名。 藉由將最終的影像進行動畫處理，可減輕此效果，但 MSAA 品質仍然比 **TileBasedComposition** 模式更糟。

下圖說明 MSAA 構件： ![ DepthBasedComposition 中的 msaa](./media/service-render-mode-balanced.png)

Sculpture 和 curtain 之間的消除鋸齒可正常運作，因為這兩個元件都在相同的 GPU 上轉譯。 另一方面，curtain 和牆壁之間的邊緣會顯示一些別名，因為這兩個部分是由不同的 Gpu 組成。

這種模式的最大限制是，幾何部分無法動態切換為透明材質，也不會對[HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)進行**查看**模式的運作。 但是，其他狀態覆寫功能 (大綱、色彩色調 ... ) 運作。 此外，在轉換時間標示為透明的材質在此模式下也會正常運作。

### <a name="performance"></a>效能

這兩種模式的效能特性會根據使用案例而有所不同，因此很難或提供一般建議。 如果您不受上述限制的限制 (記憶體或透明/查看) ，建議您嘗試兩種模式，並使用各種相機位置來監視效能。

## <a name="setting-the-render-mode"></a>設定轉譯模式

遠端轉譯伺服器上使用的轉譯模式是在中透過進行指定 `AzureSession.ConnectToRuntime` `ConnectToRuntimeParams` 。

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

## <a name="api-documentation"></a>API 文件

* [C # AzureSession. ConnectToRuntime ( # B1 ](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.azuresession.connecttoruntime)
* [C # ConnectToRuntimeParams 結構](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.connecttoruntimeparams)
* [C + + AzureSession：： ConnectToRuntime ( # B1 ](https://docs.microsoft.com/cpp/api/remote-rendering/azuresession#connecttoruntime)
* [C + + ConnectToRuntimeParams 結構](https://docs.microsoft.com/cpp/api/remote-rendering/connecttoruntimeparams)

## <a name="next-steps"></a>接下來的步驟

* [工作階段](../concepts/sessions.md)
* [階層式狀態覆寫元件](../overview/features/override-hierarchical-state.md)
