---
title: 專案聲學烘焙解析度
titlesuffix: Azure Cognitive Services
description: 此概念概述描述了烘焙聲學時粗解析度和精細解析度之間的區別。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d8eb3b2cbaf7b4e842d8338eefde756f6d381111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854356"
---
# <a name="project-acoustics-bake-resolution"></a>專案聲學烘焙解析度
此概念概述描述了烘焙聲學時粗解析度和精細解析度之間的區別。 在烘焙工作流的"探測"步驟中選擇此設置。

## <a name="coarse-vs-fine-resolution"></a><a name="Coarse-vs-Fine-Resolution"></a>粗糙與精細解析度

[Coarse] \(粗略\) 和 [Fine] \(精細\) 解析度設定之間的唯一差異在於模擬的執行頻率。 [Fine] \(精細\) 所使用的頻率為 [Coarse] \(粗略\) 的兩倍。 這對聲學模擬具有許多影響：

* [Coarse] \(粗略\) 的波長為 [Fine] \(精細\) 的兩倍，因此體素是兩倍大。
* 由於聲場模擬時間與體素大小直接相關，因此 [Coarse] \(粗略\) 模擬比 [Fine] \(精細\) 模擬快 16 倍。
* 無法類比小於體素大小的門戶（例如，門或窗）。 粗糙設置可能會導致某些較小的門戶不被類比;因此，如果設置粗糙，則可能導致某些較小的門戶被類比。因此，它們不會在運行時傳遞聲音。 您可以檢視體素來查看是否會發生這種情況。
* 模擬頻率越低，在角落和邊緣周圍產生的繞射就越少。
* 聲源不能位於"填充"體素（即包含幾何體的體素）內。 這會導致沒有聲音。 放置聲源比使用精細設置時不在較大的粗體內更難放置聲音源。
* 較大的體素侵入門戶的部分也較大，如下所示。 第一張圖是使用 [Coarse] \(粗略\) 解析度來建立的，第二張圖是相同的門口，但使用 [Fine] \(精細\) 解析度。 如紅色標記所示，使用 [Fine] \(精細\) 設定時，侵入門口的程度較小。 藍線是幾何所定義的門口，紅線則是體素大小所定義的有效聲場門戶。 此入侵狀況在指定的情況下會如何表現，完全取決於體素與門戶幾何的對齊方式，而這又取決於場景中物件的大小和位置。

![Unreal 中填滿門口的粗略體素螢幕擷取畫面](media/unreal-coarse-bake.png)

![Unreal 中門口精細體素的螢幕擷取畫面](media/unreal-fine-bake.png)

## <a name="next-steps"></a>後續步驟

使用我們的[虛幻](unreal-baking.md)或[Unity](unity-baking.md)外掛程式自行試用粗糙和精細的解析度設置。
