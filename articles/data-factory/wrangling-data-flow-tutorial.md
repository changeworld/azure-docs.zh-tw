---
title: 開始在 Azure 資料工廠中展開資料串流
description: 關於如何使用爭鳴資料流在 Azure 資料工廠中準備數據的教程
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: a180a7a0c85b642ac09d1d027c95809c4638dee1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409000"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>使用爭鳴的資料串流準備資料

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="create-a-wrangling-data-flow"></a>建立爭鳴的資料串流

在 Azure 數據工廠中創建爭鳴的數據流有兩種方法。 一種方法是單擊加號圖示,並在工廠資源窗格中選擇 **「數據流**」。。

![爭吵](media/wrangling-data-flow/tutorial7.png)

另一種方法位於管道畫布的活動窗格中。 打開 **「移動和變換**手風琴」並將**資料流**活動拖到畫布上。

在這兩種方法中,在打開的側窗格中,選擇 **「創建新資料流」** 並選擇 **「旋轉資料流**」 。。 按一下 [確定]。

![爭吵](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>編寫一個爭論的資料串流

為要排號資料流程到**來源資料集**。 您可以選擇現有資料集或創建新資料集。 您還可以選擇接收器數據集。 您可以選擇一個或多個源資料集,但此時只允許一個接收器。 選擇接收器數據集是可選的,但至少需要一個源數據集。

> [!NOTE]
> 有限預覽僅支援 ADLS 第 2 代分隔文本。 

![爭吵](media/wrangling-data-flow/tutorial4.png)

按下「**建立**」 以打開電源查詢連線混搭編輯器。

![爭吵](media/wrangling-data-flow/tutorial5.png)

使用無代碼數據準備編寫您爭論的數據流。 有關可用函數的清單,請參閱[轉換函數](wrangling-data-flow-functions.md)/

![爭吵](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>執行和監視爭鳴的資料串流

要執行爭鳴資料串流的管線除錯執行,請按下管道畫布中的**除錯**。 發佈數據流后,「**觸發器」現在**執行最後一個已發佈的管道的按需運行。 可以與所有現有的 Azure 數據工廠觸發器一起計畫進行爭用數據流。

![爭吵](media/wrangling-data-flow/tutorial3.png)

跳到 **「監視器」** 選項卡,以可視化觸發的爭鳴數據流活動運行的輸出。

![爭吵](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>後續步驟

瞭解如何[建立映射資料串流](tutorial-data-flow.md)。