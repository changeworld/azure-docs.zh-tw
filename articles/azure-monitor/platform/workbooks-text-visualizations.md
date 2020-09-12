---
title: Azure 監視器活頁簿文字視覺效果
description: 瞭解所有 Azure 監視器活頁簿文字視覺效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: e8f2d9495484b781b26962c2946b5bada6c38b4c
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89663985"
---
# <a name="text-visualizations"></a>文字視覺效果

活頁簿可讓作者在其活頁簿中包含文字區塊。 文字可以是人類的遙測分析、協助使用者解讀資料的資訊、區段標題等等。

[![Apdex 文字資料表的螢幕擷取畫面](./media/workbooks-text-visualizations/apdex.png)](./media/workbooks-text-visualizations/apdex.png#lightbox)

文字是透過 Markdown 控制項新增的，可提供完整的格式控制。 這些包括不同的標題和字型樣式、超連結、表格等等。

編輯模式：

![編輯模式中的文字步驟螢幕擷取畫面。](./media/workbooks-text-visualizations/text-edit-mode.png)

預覽模式：

![[預覽] 索引標籤上的 [編輯] 模式中的文字步驟螢幕擷取畫面。](./media/workbooks-text-visualizations/text-edit-mode-preview.png)

## <a name="add-a-text-control"></a>新增文字控制項

1. 按一下 [ **編輯** ] 工具列專案，將活頁簿切換至編輯模式。
2. 使用 [ **新增文字** ] 連結可將文字控制項加入至活頁簿。
3. 在編輯器欄位中加入 Markdown。
4. 使用 *文字樣式* 選項，在以 Azure 入口網站的標準資訊/警告/成功/錯誤樣式包裝的純 markdown 和 markdown 之間切換。
5. 使用 [ **預覽** ] 索引標籤來查看您的內容外觀。 編輯時，預覽會顯示捲軸區域內的內容以限制其大小;不過，在執行時間，markdown 內容會展開以填滿任何需要的空間，而不會有捲軸。
6. 選取 [ **完成編輯** ] 按鈕來完成步驟編輯。

> [!TIP]
> 您可以使用此 [Markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) 功能提要來瞭解不同的格式化選項。

## <a name="text-styles"></a>文字樣式

下列文字樣式適用于文字步驟：

| 樣式     | 說明                                                                               |
|-----------|-------------------------------------------------------------------------------------------|
| `plain`   | 未套用任何其他格式。                                                      |
| `info`    | 入口網站的「資訊」樣式，具有  `ℹ` 或類似的圖示和一般藍色背景。      |
| `error`   | 入口網站的「錯誤」樣式，具有 `❌` 或類似的圖示，通常是紅色背景。     |
| `success` | 入口網站的「成功」樣式，具有 `✔` 或類似的圖示，且通常為綠色背景。  |
| `upsell`  | 入口網站的「追加銷售」樣式，具有 `🚀` 或類似的圖示，通常是紫色背景。 |
| `warning` | 入口網站的「警告」樣式，具有 `⚠` 或類似的圖示和一般藍色背景。   |

您也可以選擇文字參數作為樣式的來源，而不是挑選特定樣式。 參數值必須是上述其中一個文字值。 如果沒有值或任何無法辨識的值，則會被視為 `plain` 樣式。

資訊樣式範例：

![資訊樣式外觀的螢幕擷取畫面。](./media/workbooks-text-visualizations/text-preview-info-style.png)

警告樣式範例：

![警告樣式外觀的螢幕擷取畫面。](./media/workbooks-text-visualizations/text-warning-style.png)

## <a name="next-steps"></a>接下來的步驟

* 瞭解如何 [在活頁簿中建立圖表](workbooks-chart-visualizations.md)。
* 瞭解如何 [在活頁簿中建立方格](workbooks-grid-visualizations.md)。
