---
title: 偵錯轉譯
description: 伺服器端的調試轉譯效果總覽
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: f10c736cad9322752d5d552d29ef0c63635628a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81868166"
---
# <a name="debug-rendering"></a>偵錯轉譯

Debug 轉譯 API 提供範圍廣泛的全域選項，可使用不同的調試效果來改變伺服器端轉譯。

## <a name="available-debug-rendering-effects"></a>可用的調試轉譯效果

|設定                          | 效果                               |
|---------------------------------|:-------------------------------------|
|框架計數器                    | 將文字重迭呈現到框架的左上角。 此文字會顯示目前的伺服器端框架識別碼，這會在轉譯繼續時持續遞增。 |
|多邊形計數                    | 將文字重迭呈現到框架的左上角。 此文字會顯示目前呈現的多邊形數量，與[伺服器端效能查詢](performance-queries.md)所查詢的值相同| 
|線框                        | 啟用時，在伺服器上載入的所有物件幾何都會以線框模式呈現。 只有多邊形的邊緣會在此模式中進行柵格化。 |

下列程式碼會啟用這些偵錯工具的效果：

``` cs
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

![調試轉譯](./media/debug-rendering.png)

> [!NOTE]
> 所有的偵錯工具轉譯效果都是影響整個框架的全域設定。

## <a name="use-cases"></a>使用案例

Debug 轉譯 API 適用于簡單的偵錯工具工作，例如驗證服務連線確實已啟動並正常執行。 文字轉譯選項會直接影響縮小資料流程的影片畫面。 啟用它們會確認是否已接收新的畫面格，並正確地進行影片解碼。

不過，所提供的效果並不會對服務健康狀態提供任何詳細的自我檢查。 建議使用[伺服器端效能查詢](performance-queries.md)做為此使用案例。

## <a name="performance-considerations"></a>效能考量

* 啟用文字重迭幾乎不會產生任何效能負擔。
* 啟用線框模式並不會產生非常簡單的效能負擔，不過它可能會因場景而有所不同。 針對複雜場景，此模式可能會導致畫面播放速率降到 60-Hz 目標以下。

## <a name="next-steps"></a>後續步驟

* [轉譯模式](../../concepts/rendering-modes.md)
* [伺服器端效能查詢](performance-queries.md)
