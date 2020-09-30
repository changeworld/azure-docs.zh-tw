---
title: 教學課程：開始使用管線進行協調
description: 在此教學課程中，您將了解如何使用 Synapse Studio 來協調管線和活動。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 72eea7c46dd005cd16ae5b8f0022c1174dd28f27
ms.sourcegitcommit: 0194a29a960e3615f96a2d9d8a7e681cf3e8f9ab
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89667485"
---
# <a name="orchestrate-with-pipelines"></a>使用管線進行協調

在此教學課程中，您將了解如何使用 Synapse Studio 來協調管線和活動。 

## <a name="overview"></a>概觀

您可以在 Azure Synapse 中協調各種不同的工作。

1. 在 Synapse Studio 中，移至 [協調] 中樞。
1. 選取 [+] > [管線]，以建立新的管線。
1. 移至 [開發] 中樞，並選取您先前建立的其中一個筆記本。
1. 將該筆記本拖曳到管線中。
1. 在管線中，選取 [新增觸發程序] > [新增/編輯]。
1. 在 [選擇觸發程序] 中，選取 [新增]，然後在 [週期] 中，將觸發程序設定為每隔 1 小時執行一次。
1. 選取 [確定]。
1. 選取 [全部發佈]。 管線會每小時執行一次。
1. 若要讓管線立即執行，而不等到下一個小時，請選取 [新增觸發程序] > [新增/編輯]。



## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Power BI 視覺化資料](get-started-visualize-power-bi.md)
                                 
