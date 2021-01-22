---
title: 開始使用 Azure Data Factory 中的整頓資料流程
description: 如何使用整頓資料流程在 Azure Data Factory 中準備資料的教學課程
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: 3d43a532f57feab361c6a3de79269991f46fc55d
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684017"
---
# <a name="prepare-data-with-data-wrangling"></a>使用資料整頓準備資料

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Data factory 中的資料整頓可讓您以原生方式在 ADF 中建立互動式的 Power Query 混合，然後在 ADF 管線內大規模執行。

> [!NOTE]
> ADF 中的 Power Query 活動該處目前已 cmpivot 公開預覽

## <a name="create-a-power-query-activity"></a>建立 Power Query 活動

有兩種方式可在 Azure Data Factory 中建立 Power Query。 其中一種方式是按一下加號圖示，然後 **選取 [** factory 資源] 窗格中的 [資料流程]。

> [!NOTE]
> 先前，資料整頓功能位於資料流程工作流程中。 現在，您將建立您的資料整頓混合 ```New > Power query```

![顯示 [factory 資源] 窗格中 Power Query 的螢幕擷取畫面。](media/data-flow/power-query-wrangling.png)

另一個方法是在管線畫布的 [活動] 窗格中。 開啟 **Power Query** 的 [可折疊]，並將 [ **Power Query** ] 活動拖曳到畫布上。

![醒目顯示資料整頓選項的螢幕擷取畫面。](media/data-flow/power-query-activity.png)

## <a name="author-a-power-query-data-wrangling-activity"></a>撰寫 Power Query 資料整頓活動

為您的 Power Query 混合新增 **源資料集** 。 您可以選擇現有的資料集，或建立一個新的資料集。 您也可以選取接收資料集。 您可以選擇一或多個源資料集，但此時只允許一個接收。 選擇接收資料集是選擇性的，但至少需要一個源資料集。

![爭吵](media/wrangling-data-flow/tutorial4.png)

按一下 [ **建立** ] 以開啟 Power Query 線上混搭編輯器。

![顯示 [建立] 按鈕的螢幕擷取畫面，可開啟 Power Query 線上混搭編輯器。](media/wrangling-data-flow/tutorial5.png)

使用無程式碼的資料準備來撰寫您的整頓 Power Query。 如需可用函式的清單，請參閱 [轉換](wrangling-functions.md)函式。 ADF 會將 M 腳本轉譯成資料流程腳本，讓您可以使用 Azure Data Factory 的資料流程 Spark 環境，大規模地執行您的 Power Query。

![顯示撰寫資料整頓 Power Query 程式的螢幕擷取畫面。](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-power-query-data-wrangling-activity"></a>執行和監視 Power Query 資料整頓活動

若要執行 Power Query 活動的管線 debug 執行，請按一下管線畫布中的 [ **調試** 程式]。 當您發佈管線時， **觸發程式現在** 會執行最後一個已發佈管線的隨選執行。 您可以使用所有現有的 Azure Data Factory 觸發程式來排程 Power Query 管線。

![顯示如何新增 Power Query 資料整頓活動的螢幕擷取畫面。](media/wrangling-data-flow/tutorial3.png)

移至 [ **監視** ] 索引標籤，將觸發的 Power Query 活動執行的輸出視覺化。

![顯示觸發的整頓 Power Query 活動執行輸出的螢幕擷取畫面。](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>下一步

瞭解如何 [建立對應資料流程](tutorial-data-flow.md)。
