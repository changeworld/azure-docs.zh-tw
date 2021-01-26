---
title: Azure Data Factory 中的 Power Query 活動
description: 瞭解如何在 Data Factory 管線中使用資料整頓功能的 Power Query 活動
services: data-factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: c0ad769ceba4fc3fa7f602d70188ea1942ca73aa
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791689"
---
# <a name="power-query-activity-in-data-factory"></a>Data factory 中的 Power query 活動

Power Query 活動可讓您建立並執行 Power Query 的混合，以在 Data Factory 管線中大規模執行資料整頓。 您可以從 [新資源] 功能表選項或將電源活動新增至管線，以建立新的 Power Query 混合。

![顯示 [factory 資源] 窗格中 Power Query 的螢幕擷取畫面。](media/data-flow/power-query-wrangling.png)

先前，Azure Data Factory 中的資料整頓是從 [資料流程] 功能表選項所撰寫。 這已從新的 Power Query 活動變更為撰寫。 您可以直接在 Power Query 的混合編輯器內工作，以執行互動式資料探索，然後儲存您的工作。 完成之後，您可以取得 Power Query 活動，並將它新增至管線。 Azure Data Factory 會使用 Azure Data Factory 的資料流程 Spark 環境，自動相應放大並讓您的資料整頓。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>轉譯成資料流程腳本

為了利用您的 Power Query 活動進行調整，Azure Data Factory 將 ```M``` 腳本轉譯成資料流程腳本，讓您可以使用 Azure Data Factory 的資料流程 Spark 環境，大規模地執行您的 Power Query。 使用無程式碼的資料準備來撰寫您的整頓資料流程。 如需可用函式的清單，請參閱 [轉換](wrangling-functions.md)函式。

## <a name="next-steps"></a>後續步驟

深入瞭解[在 Azure Data Factory 中使用 Power Query 的](wrangling-tutorial.md)資料整頓概念
