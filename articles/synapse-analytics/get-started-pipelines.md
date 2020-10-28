---
title: 教學課程：開始使用管線進行協調
description: 在此教學課程中，您將了解如何使用 Synapse Studio 來協調管線和活動。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 42d2ac6cf6592f8e22b0a66aee84c3436d466572
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329878"
---
# <a name="orchestrate-with-pipelines"></a>使用管線進行協調

在此教學課程中，您將了解如何使用 Synapse Studio 來協調管線和活動。 

## <a name="overview"></a>概觀

您可以在 Azure Synapse 中協調各種不同的工作。

1. 在 Synapse Studio 中，移至 [整合] 中樞。
1. 選取 [+] > [管線]，以建立新的管線。
1. 移至 [開發] 中樞，並選取您先前建立的其中一個筆記本。
1. 將該筆記本拖曳到管線中 ( **注意** ：依照[文件](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace)中指定的方式，在筆記本中新增從管線執行時所需的匯入模組步驟
1. 在管線中，選取 [新增觸發程序] > [新增/編輯]。
1. 在 [選擇觸發程序]，選取 [新增]，然後將 [週期] 設定為 [每 1 小時]。
1. 選取 [確定]。 
1. 選取 [全部發佈]。
1. 若要讓管線立即執行，而不需等待下一個小時，請選取 [新增觸發程序] > [立即觸發]。



## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Power BI 視覺化資料](get-started-visualize-power-bi.md)
                                 
