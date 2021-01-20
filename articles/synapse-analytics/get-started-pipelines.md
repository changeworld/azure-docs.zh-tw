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
ms.date: 12/31/2020
ms.openlocfilehash: 2ea7c3c440fcf95e4512464333efe8461788bceb
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98219397"
---
# <a name="integrate-with-pipelines"></a>與管線整合

在此教學課程中，您將了解如何使用 Synapse Studio 來整合管線和活動。 

## <a name="overview"></a>概觀

您可以在 Azure Synapse 中整合各種不同的工作。

1. 在 Synapse Studio 中，移至 [整合] 中樞。
1. 選取 [+] > [管線]，以建立新的管線。 按一下 [新增管線] 物件以開啟 [管線設計工具]。
1. 在 [ **活動**] 下，展開 [ **Synapse** ] 資料夾，然後將 **筆記本** 物件拖曳至設計工具中。
1. 選取 [筆記本] 活動屬性的 [ **設定** ] 索引標籤。 使用下拉式清單，從您目前的 Synapse 工作區選取任何筆記本。 
1. 在管線中，選取 [新增觸發程序] > [新增/編輯]。
1. 在 [選擇觸發程序]，選取 [新增]，然後將 [週期] 設定為 [每 1 小時]。
1. 選取 [確定]。 
1. 選取 [全部發佈]。 


## <a name="monitor-pipeline"></a>監視管線

1. 發佈管線之後，若要立即執行管線，而不等候下一個小時，請選取 [立即 **加入觸發** 程式  >  **觸發** 程式]。
1. 在 Synapse Studio 中，移至 [ **監視** ] 中樞，然後選取 [ **管線** 執行] 以監視管線執行進度。



## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Power BI 視覺化資料](get-started-visualize-power-bi.md)
