---
title: 開始在 Azure 資料工廠中展開資料流程
description: 關於如何使用爭鳴資料流程在 Azure 資料工廠中準備資料的教程
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 8ae109045381dba77610dedc5bb95e233b213eee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73682271"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>使用爭鳴的資料流程準備資料

## <a name="create-a-wrangling-data-flow"></a>創建爭鳴的資料流程

在 Azure 資料工廠中創建爭鳴的資料流程有兩種方法。 一種方法是按一下加號圖示，並在工廠資源窗格中選擇 **"資料流程**"。

![爭吵](media/wrangling-data-flow/tutorial7.png)

另一種方法位於管道畫布的活動窗格中。 打開 **"移動和變換**手風琴"並將**資料流程**活動拖到畫布上。

在這兩種方法中，在打開的側窗格中，選擇 **"創建新資料流程"** 並選擇 **"旋轉資料流程**"。 按一下 [確定]。

![爭吵](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>編寫一個爭論的資料流程

為爭號資料流程添加**源資料集**。 您可以選擇現有資料集或創建新資料集。 您還可以選擇接收器資料集。 您可以選擇一個或多個源資料集，但此時只允許一個接收器。 選擇接收器資料集是可選的，但至少需要一個源資料集。

> [!NOTE]
> 有限預覽僅支援 ADLS 第 2 代分隔文本。 

![爭吵](media/wrangling-data-flow/tutorial4.png)

按一下"**創建**"以打開電源查詢連線混搭編輯器。

![爭吵](media/wrangling-data-flow/tutorial5.png)

使用無代碼資料準備編寫您爭論的資料流程。 有關可用函數的清單，請參閱[轉換函數](wrangling-data-flow-functions.md)/

![爭吵](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>運行和監視爭鳴的資料流程

要執行爭鳴資料流程的管道調試運行，請按一下管道畫布中的**調試**。 發佈資料流程後，"**觸發器"現在**執行最後一個已發佈的管道的按需運行。 可以與所有現有的 Azure 資料工廠觸發器一起計畫進行爭用資料流程。

![爭吵](media/wrangling-data-flow/tutorial3.png)

轉到 **"監視器"** 選項卡，以視覺化觸發的爭鳴資料流程活動運行的輸出。

![爭吵](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>後續步驟

瞭解如何[創建映射資料流程](tutorial-data-flow.md)。