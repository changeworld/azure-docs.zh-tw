---
title: 開始使用 Azure Data Factory 中的整頓資料流程
description: 如何使用整頓資料流程在 Azure Data Factory 中準備資料的教學課程
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: f5a7f372f286a7b26a4a9916ed9df913b151e967
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628464"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>使用整頓資料流程準備資料

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="create-a-wrangling-data-flow"></a>建立整頓資料流程

有兩種方式可在 Azure Data Factory 中建立整頓資料流程。 其中一種方式是按一下加號圖示，然後**選取 [** factory 資源] 窗格中的 [資料流程]。

![整頓](media/wrangling-data-flow/tutorial7.png)

另一個方法是在管線畫布的 [活動] 窗格中。 開啟 [**移動並轉換**可折疊]，並將 [**資料流程**] 活動拖曳至畫布上。

在這兩種方法中，在開啟的側邊窗格中選取 [**建立新**的資料流程]，然後選擇 [**整頓資料流程**]。 按一下 [確定]。

![整頓](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>撰寫整頓資料流程

新增整頓資料流程的**來源資料集**。 您可以選擇現有的資料集，或建立一個新的。 您也可以選取接收資料集。 您可以選擇一或多個源資料集，但目前只允許一個接收。 選擇接收資料集是選擇性的，但至少必須要有一個源資料集。

> [!NOTE]
> 有限預覽僅支援 ADLS Gen 2 分隔文字。 

![整頓](media/wrangling-data-flow/tutorial4.png)

按一下 [**建立**] 以開啟 [Power Query 線上混合編輯器]。

![整頓](media/wrangling-data-flow/tutorial5.png)

使用無程式碼的資料準備，撰寫您的整頓資料流程。 如需可用函式的清單，請參閱[轉換](wrangling-data-flow-functions.md)函式/

![整頓](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>執行和監視整頓資料流程

若要執行整頓資料流程的管線 debug 執行，請按一下管線畫布中的 [ **debug** ]。 一旦您發行資料流程，觸發程式**現在**會執行上次發佈管線的隨選執行。 您可以使用所有現有的 Azure Data Factory 觸發程式來排程整頓的資料流程。

![整頓](media/wrangling-data-flow/tutorial3.png)

移至 [**監視**] 索引標籤，以視覺化方式觸發整頓資料流程活動執行的輸出。

![整頓](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>後續步驟

瞭解如何[建立對應的](tutorial-data-flow.md)資料流程。