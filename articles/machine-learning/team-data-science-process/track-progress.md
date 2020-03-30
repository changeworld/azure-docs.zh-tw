---
title: 跟蹤團隊資料科學流程專案的進度
description: 資料科學組經理、團隊領導和專案領導如何跟蹤資料科學專案的進度。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8cf1e5a4d97b882e7a8d0c81041bbcde709760d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864191"
---
# <a name="track-the-progress-of-data-science-projects"></a>跟蹤資料科學專案的進度

資料科學組經理、團隊領導和專案主管可以跟蹤其專案的進度。  管理者想知道已經完成了哪些工作，誰做了這項工作，以及仍然從事哪些工作。   管理期望是成功的一個重要因素。

## <a name="azure-devops-dashboards"></a>Azure DevOps 儀表板

如果使用 Azure DevOps，則可以構建儀表板來跟蹤與給定敏捷專案關聯的活動和工作項。 有關儀表板的詳細資訊，請參閱[儀表板、報表和小部件](/azure/devops/report/dashboards/)。

有關如何在 Azure DevOps 中創建和自訂儀表板和微件的說明，請參閱以下快速入門：

- [新增及管理儀表板](/azure/devops/report/dashboards/dashboards)
- [將小部件添加到儀表板](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>範例儀表板

下面是一個簡單的示例儀表板，用於跟蹤敏捷資料科學專案的衝刺 （sprint） 活動，包括提交到關聯存儲庫的數量。 

- **倒計時**磁貼顯示當前衝刺 （sprint） 中保留的天數。 

- 兩個**代碼磁貼**顯示過去七天兩個專案存儲庫中的提交數。 

- **TDSP 客戶專案的工作項**顯示所有工作項的查詢結果及其狀態。 

- **累積流程圖**（CFD） 顯示已關閉和活動工作項的數量。

- **燃盡圖表**顯示在衝刺 （sprint） 中剩餘時間仍要完成的工作。

- **燃燒圖表**顯示已完成的工作與衝刺 （sprint） 中的工時總數進行比較。

![儀表板](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>後續步驟

[執行團隊資料科學流程的演練](walkthroughs.md)列出了演示所有流程步驟的演練。 連結的方案說明了如何將雲和本地資源管理到智慧應用程式中。 
