---
title: 映射資料串流視覺化監控
description: 如何以視覺化方式監視 Azure Data Factory 資料流程
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: dea0f9a038958ea747147a179020545f2f6922a2
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605350"
---
# <a name="monitor-data-flows"></a>監視資料流程

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在您完成建置和偵錯資料流程之後，您可將資料流程排定為在管線內容中依照排程執行。 您可以使用觸發程從 Azure Data Factory 進行管線排程。 或者，可以從 Azure Data Factory Pipeline Builder 使用 [立即觸發] 選項來執行單程執行，以在管線內容中測試您的資料流程。

當您執行您的管線時，您可以監視管線和管線內含的所有活動，包括「資料流程」活動。 按一下左側 Azure Data Factory UI 面板中的監視器圖示。 您會看到類似以下的畫面。 醒目提示的圖示可讓您向下鑽研到管線中的活動，包括「資料流程」活動。

![資料流程監控](media/data-flow/mon001.png "資料流程監視")

您將看到此級別的統計資訊,包括運行時間和狀態。 活動層級的執行識別碼與管線層級的執行識別碼不同。 前一層級的執行識別碼適用於管線。 按一下眼鏡可讓您深入了解資料流程執行的詳細資料。

![資料流程監控](media/data-flow/mon002.png "資料流程監視")

當您在圖形化節點監視檢視時，您會看到經過簡化的僅供檢視資料流程圖版本。

![資料流程監控](media/data-flow/mon003.png "資料流程監視")

## <a name="view-data-flow-execution-plans"></a>檢視資料流程執行計劃

在 Spark 中執行數據流時,Azure 數據工廠會根據整個資料流確定最佳代碼路徑。 此外，執行路徑可能會發生在不同的相應放大節點和資料分割區上。 因此，監視圖表示您的流程設計，並將轉換的執行路徑納入考量。 當您按一下個別節點時，您會看到「群組 」，其代表在叢集上一起執行的程式碼。 您看見的時間和計數代表這些群組，而不是您設計中的個別步驟。

![資料流程監控](media/data-flow/mon004.png "資料流程監視")

* 當您按一下監視視窗中的空白處時，底端窗格中的統計資料會顯示每個接收的時間和資料列計數，以及導向轉換歷程接收資料的轉換。

* 選擇單個轉換時,您將在右側面板上收到其他反饋,該面板顯示分區統計資訊、列計數、偏斜度(跨分區分佈的數據是否均勻)和峰度(數據有多尖尖)。

* 當您按一下節點檢視中的 [接收] 時，您會看到資料行歷程。 有三種不同方法可讓資料行累積於資料流程以置入接收中。 其中包括：

  * 已計算:使用列進行條件處理或資料串流中的運算式中,但不要將其放在 Sink 中
  * 派生:該列是您在流中生成的新列,即它不存在於源中
  * 映射:列源自源,而您的列正在將其映射到彙欄位
  * 資料串流狀態:執行的目前狀態
  * 叢集啟動時間:取得 JIT Spark 計算環境以執行資料流的時間
  * 轉換數:在流中執行的轉換步驟數
  
![資料流程監控](media/data-flow/monitornew.png "資料流程監控新增")  
  
## <a name="monitor-icons"></a>監視器圖示

此圖示表示已在叢集上快取轉換資料，所以時間和執行路徑已將該點納入考量：

![資料流程監控](media/data-flow/mon004.png "資料流程監視")

您也會在轉換中看到綠色圓圈圖示。 這些圖示代表資料流入的接收數目。
