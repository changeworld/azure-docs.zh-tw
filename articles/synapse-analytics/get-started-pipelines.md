---
title: 教學課程：開始與管線整合
description: 在此教學課程中，您將了解如何使用 Synapse Studio 來整合管線和活動。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 10/27/2020
ms.openlocfilehash: af01d5b5e424dd5ea229115f7aa3570d0b7cd511
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744925"
---
# <a name="integrate-with-pipelines"></a>與管線整合

在此教學課程中，您將了解如何使用 Synapse Studio 來整合管線和活動。 

## <a name="overview"></a>概觀

您可以在 Azure Synapse 中整合各種不同的工作。

1. 在 Synapse Studio 中，移至 [整合] 中樞。
1. 選取 [+] > [管線]，以建立新的管線。
1. 移至 [開發] 中樞，並選取您先前建立的其中一個筆記本。
1. 將該筆記本拖曳到管線中 ( **注意** ：依照 [文件](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace) 中指定的方式，在筆記本中新增從管線執行時所需的匯入模組步驟)
1. 在管線中，選取 [新增觸發程序] > [新增/編輯]。
1. 在 [選擇觸發程序]，選取 [新增]，然後將 [週期] 設定為 [每 1 小時]。
1. 選取 [確定]。 
1. 選取 [全部發佈]。
1. 若要讓管線立即執行，而不需等待下一個小時，請選取 [新增觸發程序] > [立即觸發]。



## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Power BI 視覺化資料](get-started-visualize-power-bi.md)
                                 
