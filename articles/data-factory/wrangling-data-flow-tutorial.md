---
title: 開始使用 Azure Data Factory 中的整頓資料流程
description: 如何使用整頓資料流程在 Azure Data Factory 中準備資料的教學課程
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: e9a4f7266522fc17c33e2086af6cb7367bbed39e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650675"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>使用整頓資料流程來準備資料

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

> [!NOTE]
> 整頓資料流程目前已 cmpivot 公開預覽

## <a name="create-a-wrangling-data-flow"></a>建立整頓資料流程

有兩種方式可以在 Azure Data Factory 中建立整頓資料流程。 其中一種方式是按一下加號圖示，然後 **選取 [** factory 資源] 窗格中的 [資料流程]。

![在 [factory 資源] 窗格中顯示資料流程的螢幕擷取畫面。](media/wrangling-data-flow/tutorial7.png)

另一個方法是在管線畫布的 [活動] 窗格中。 開啟 [ **移動和轉換** 可折疊]，然後將 [ **資料流程** ] 活動拖曳到畫布上。

在這兩種方法中，于開啟的側邊窗格中選取 [ **建立新的資料流程** ]，然後選擇 [ **整頓資料流程**]。 按一下 [確定]。

![醒目顯示 [整頓資料流程] 選項的螢幕擷取畫面。](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>撰寫整頓資料流程

為您的整頓資料流程新增 **源資料集** 。 您可以選擇現有的資料集，或建立一個新的資料集。 您也可以選取接收資料集。 您可以選擇一或多個源資料集，但此時只允許一個接收。 選擇接收資料集是選擇性的，但至少需要一個源資料集。

> [!NOTE]
> 只有 ADLS Gen 2 分隔文字支援有限預覽。 

![爭吵](media/wrangling-data-flow/tutorial4.png)

按一下 [ **建立** ] 以開啟 Power Query 線上混搭編輯器。

![顯示 [建立] 按鈕的螢幕擷取畫面，可開啟 Power Query 線上混搭編輯器。](media/wrangling-data-flow/tutorial5.png)

使用無程式碼的資料準備來撰寫您的整頓資料流程。 如需可用函式的清單，請參閱 [轉換](wrangling-data-flow-functions.md)函式。

![顯示撰寫整頓資料流程流程的螢幕擷取畫面。](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>執行和監視整頓資料流程

若要執行整頓資料流程的管線偵錯工具執行，請按一下管線畫布中的 [ **debug** ]。 一旦您發佈資料流程之後， **觸發程式現在** 會執行上一個已發佈管線的隨選執行。 您可以使用所有現有的 Azure Data Factory 觸發程式來排程整頓資料流程。

![顯示如何新增整頓資料流程的螢幕擷取畫面。](media/wrangling-data-flow/tutorial3.png)

移至 [ **監視** ] 索引標籤，將觸發的整頓資料流程活動執行的輸出視覺化。

![顯示觸發的整頓資料流程活動執行輸出的螢幕擷取畫面。](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>後續步驟

瞭解如何 [建立對應資料流程](tutorial-data-flow.md)。