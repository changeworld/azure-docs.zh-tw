---
title: 除錯渲染
description: 伺服器端除錯呈現效果概述
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: 675f8d988e64ed7b556f154f681ccb53ed1000c6
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394289"
---
# <a name="debug-rendering"></a>除錯渲染

調試呈現 API 提供了一系列全域選項,用於使用不同的調試效果來更改伺服器端呈現。

## <a name="available-debug-rendering-effects"></a>可用的除錯呈現效果

|設定                          | 效果                               |
|---------------------------------|:-------------------------------------|
|框架計數器                    | 將文本疊加渲染到框架的左上角。 文本顯示當前伺服器端幀 ID,該 ID 隨著呈現過程而不斷遞增。 |
|多邊形計數                    | 將文本疊加渲染到框架的左上角。 文字顯示目前呈現的多邊形量,與[伺服器端效能查詢](performance-queries.md)查詢的值相同| 
|線框                        | 如果啟用,則伺服器上載入的所有物件幾何體都將線上框模式下呈現。 只有多邊形的邊緣才會在此模式下進行柵格化。 |

以下代碼支援以下調試效果:

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

![除錯渲染](./media/debug-rendering.png)

> [!NOTE]
> 所有調試呈現效果都是影響整個幀的全域設置。

## <a name="use-cases"></a>使用案例

除錯呈現 API 用於簡單的除錯任務,例如驗證服務連接實際正常執行。 文本呈現選項直接影響向的視頻幀。 啟用它們可驗證是否接收了新幀並正確解碼了視頻。

但是,所提供的效果不會對服務運行狀況進行任何詳細的反省。 建議為此使用伺服器[介面效能查詢](performance-queries.md)。

## <a name="performance-considerations"></a>效能考量

* 啟用文本疊加會產生很少或根本沒有性能開銷。
* 此外,啟用疊加確實會產生非同尋常的性能開銷,儘管它可能因場景而異。 對於複雜場景,此模式可能會導致幀速率下降到 60 Hz 目標以下。

## <a name="next-steps"></a>後續步驟

* [轉譯模式](../../concepts/rendering-modes.md)
* [伺服器端效能查詢](performance-queries.md)
