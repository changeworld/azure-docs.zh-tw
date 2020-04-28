---
title: 以 Azure Notebooks Preview 的投影片形式呈現 Jupyter 筆記本
description: 瞭解如何在 Jupyter 筆記本中設定投影片模式的儲存格，然後使用增加延伸模組來呈現投影片。
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 05dd3d9c5580e208ecf6f9e6d762476b0b493a6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75647113"
---
# <a name="run-a-notebook-slideshow-in-azure-notebooks-preview"></a>在 Azure Notebooks Preview 中執行筆記本投影片

Azure Notebooks 預先設定有 Jupyter/IPython 投影片擴充功能 (RISE)，可讓您直接以投影片放映 Notebook。 在投影片模式中，資料格通常會使用適合呈現於大型螢幕的字型大小逐一顯示，且您仍可執行程式碼，而無須切換至不同的示範電腦。

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

下圖顯示標準 Notebook 檢視，在其中您可以同時檢視 Markdown 和程式碼資料格：

![標準檢視中的 Notebook](media/slideshow/slideshow-notebook-view.png)

當您開始放映投影片時，第一個資料格會放大並填滿瀏覽器中，其中，左上方的 **X** 可結束投影片，左下方的 **?** 可顯示鍵盤快速鍵，而右下方的箭號可在投影片之間瀏覽：

![投影片模式中的 Notebook](media/slideshow/slideshow-slide-view.png)

準備以投影片放映 Notebook 主要牽涉到兩項活動：

1. 由於 Markdown 資料格會以較大的字型呈現，某些內容可能無法顯示在投影片中。 因此，您應限制任何給定資料格中的文字數量；一個標頭包含四到六行通常最適合。 如果您有較多文字，請將該資訊分散到多個資料格中。

2. 使用投影片資料格工具列，設定投影片中各個資料格的行為。 資料格類型將決定導覽按鈕的行為。

## <a name="the-anatomy-of-a-slideshow"></a>投影片的結構

如果您採用隨機 Notebook，並將其用於投影片放映，您通常會發現所有資料格都混雜在一起，且有許多內容隱藏在瀏覽器視窗底部。 若要進行有效的簡報，您必須使用投影片資料格工具列，為每個資料格指派投影片類型：

1. 在 [檢視]**** 功能表上，選取 [資料格工具列]**** > [投影片]****：

    ![開啟資料格投影片工具列](media/slideshow/slideshow-view-cell-toolbar.png)

1. [投影片類型]**** 下拉式清單會出現在 Notebook 中每個資料格的右上方：

    ![資料格投影片工具列](media/slideshow/slideshow-cell-toolbar.png)

1. 為每個資料格選取五種類型之一：

    ![資料格投影片類型](media/slideshow/slideshow-cell-slide-types.png)

    | 投影片類型 | 行為 |
    | --- | --- |
    | - (未設定) | 資料格會與上一個資料格一起顯示，但這通常不是我們在投影片中所要的效果。 |
    | 投影片 | 資料格是主要的投影片，可使用導覽控制項的左右箭號來瀏覽。 |
    | 子投影片 | 資料格位於主要投影片「下方」，可使用導覽控制項的向下箭號來瀏覽。 向上箭號可回到主要投影片。 子投影片可用於您在簡報的主要流程中可略過的次要資料，但如有需要隨時可使用。 |
    | 片段 | 使用向下導覽箭號時，資料格內容會出現在上一張投影片或子投影片的內容中 (使用向上箭號時，會移除片段)。 您可以對程式碼資料格使用片段，使該程式碼出現在投影片內，或者，您可以使用多個片段，使文字項目符號逐一出現 (請參閱下一節的範例)。 由於片段建置於目前的投影片上，因此額外的片段不會顯示在瀏覽器視窗底部。 |
    | Skip | 資料格不會顯示在投影片中。 |
    | 注意 | 資料格會包含在主講人備忘稿內，而不會顯示在投影片中。 |

1. 一開始最好為每個資料格選擇 [投影片]****。 接著，您可以執行投影片，並進行適當的調整。

### <a name="example-fragment-cells-for-bullet-items"></a>範例：項目符號項目的片段資料格

若要讓投影片上的項目符號逐一出現，請在屬於 [投影片]**** 類型的 Markdown 資料格中放入投影片標頭，然後在屬於 [片段]**** 類型的個別 Markdown 資料格中放入每個項目符號：

![為項目符號項目建立多個 Markdown 資料格的範例](media/slideshow/slideshow-fragments.png)

由於投影片在呈現片段時所使用的垂直間距會比所有項目符號位於相同資料格內時來得大，因此您可能無法使用那麼多項目符號項目。

## <a name="run-the-slideshow"></a>執行投影片

1. 如果您已編輯任何 Markdown 資料格，請務必加以執行以呈現其 HTML，否則這些資料格在投影片中會*呈現為* Markdown。

1. 在您為每個資料格設定 [投影片類型]**** 之後，請選取要作為投影片開頭的資料格，然後選取主要工具列上的 [進入/結束 RISE 投影片]**** 按鈕：

    ![主要工具列上的進入/結束 RISE 投影片按鈕](media/slideshow/slideshow-start.png)

1. 若要在投影片與片段之間瀏覽，請使用導覽控制項中的左右箭號。 控制項中的文字會顯示代表 *slide.sub-slide* 的號碼。

    ![投影片導覽控制項](media/slideshow/slideshow-navigation-control.png)

1. 若要在投影片與子投影片以及片段之間瀏覽，請使用上下箭號 (若已啟用)：

    ![子投影片的投影片導覽控制項](media/slideshow/slideshow-navigation-control-subslide.png)

1. 程式碼資料格上，使用播放按鈕執行程式碼；輸出會出現在投影片上：

    ![執行程式碼資料格的播放按鈕](media/slideshow/slideshow-run-code-cell.png)

    ![程式碼資料格輸出會出現在投影片中](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > 資料格輸出會被視為投影片資料格的一部分。 如果您在 Notebook 或投影片檢視中執行資料格，輸出也會出現在其他檢視中。 若要清除輸出，請使用 [資料**格** > ] [**目前輸出** > ] [**清除**] 命令（針對目前的儲存格）或 [資料**格** > ] [**所有輸出** > **] （所有**資料格）。

1. 完成投影片放映後，請使用 **X** 返回 Notebook 檢視。

## <a name="next-steps"></a>後續步驟

- [如何：設定和管理專案](configure-manage-azure-notebooks-projects.md)
- [如何：從筆記本內安裝套件](install-packages-jupyter-notebook.md)
- [如何：使用資料檔案](work-with-project-data-files.md)
- [如何：存取資料資源](access-data-resources-jupyter-notebooks.md)
