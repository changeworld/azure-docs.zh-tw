---
title: 偵錯轉譯
description: 伺服器端偵錯轉譯效果概觀
author: jumeder
ms.author: jumeder
ms.date: 06/15/2020
ms.topic: article
ms.openlocfilehash: aa6e6dced3dfd32896489db2ed76704304dbc745
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565438"
---
# <a name="debug-rendering"></a>偵錯轉譯

偵錯轉譯 API 提供範圍廣泛的全域選項，可使用不同的偵錯效果來改變伺服器端轉譯。

## <a name="available-debug-rendering-effects"></a>可用的偵錯轉譯效果

|設定                          | 效果                               |
|---------------------------------|:-------------------------------------|
|框架計數器                    | 將文字重疊轉譯到框架的左上角。 此文字會顯示目前伺服器端的框架識別碼，這會在轉譯繼續時持續遞增。 |
|多邊形計數                    | 將文字重疊轉譯到框架的左上角。 此文字會顯示目前轉譯的多邊形數量，與[伺服器端效能查詢](performance-queries.md)所查詢的值相同| 
|線框                        | 啟用時，在伺服器上載入的所有物件幾何都會以線框模式轉譯。 只有多邊形的邊緣會在此模式中進行光柵化。 |

下列程式碼會啟用這些偵錯的效果：

```cs
void EnableDebugRenderingEffects(AzureSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Actions.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

```cpp
void EnableDebugRenderingEffects(ApiHandle<AzureSession> session, bool highlight)
{
    ApiHandle<DebugRenderingSettings> settings = *session->Actions()->DebugRenderingSettings();

    // Enable frame counter text overlay on the server side rendering
    settings->RenderFrameCount(true);

    // Enable polygon count text overlay on the server side rendering
    settings->RenderPolygonCount(true);

    // Enable wireframe rendering of object geometry on the server
    settings->RenderWireframe(true);
}
```

![偵錯轉譯](./media/debug-rendering.png)

> [!NOTE]
> 所有的偵錯轉譯效果都是影響整個框架的全域設定。

## <a name="use-cases"></a>使用案例

偵錯轉譯 API 適用於簡單的偵錯工作，例如驗證服務連線確實已啟動並正常執行。 文字轉譯選項會直接影響下游視訊框架。 啟用這些會確認是否已接收新的框架，並正確地進行視訊解碼。

不過，所提供的效果並不會對服務健康情況提供任何詳細的自我檢查。 針對這個使用案例，建議[伺服器端效能查詢](performance-queries.md)。

## <a name="performance-considerations"></a>效能考量

* 啟用文字重疊幾乎不會產生任何效能額外負荷。
* 啟用線框模式會產生非一般的效能額外負荷，不過這需視場景而異。 針對複雜場景，此模式可能會導致畫面播放速率降到 60-Hz 目標以下。

## <a name="next-steps"></a>後續步驟

* [轉譯模式](../../concepts/rendering-modes.md)
* [伺服器端效能查詢](performance-queries.md)
