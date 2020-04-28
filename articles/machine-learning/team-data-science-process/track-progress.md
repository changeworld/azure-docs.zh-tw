---
title: 追蹤 Team Data 科學流程專案的進度
description: 資料科學群組管理員、小組負責人和專案負責人可以如何追蹤資料科學專案的進度。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75864191"
---
# <a name="track-the-progress-of-data-science-projects"></a>追蹤資料科學專案的進度

資料科學群組管理員、小組負責人和專案負責人可以追蹤其專案的進度。  管理員想要知道哪些工作已完成、誰執行了工作，以及剩餘的工作。   管理期望是成功的重要元素。

## <a name="azure-devops-dashboards"></a>Azure DevOps 儀表板

如果您使用 Azure DevOps，您可以建立儀表板來追蹤與指定 Agile 專案相關聯的活動和工作專案。 如需儀表板的詳細資訊，請參閱[儀表板、報表和小](/azure/devops/report/dashboards/)工具。

如需如何在 Azure DevOps 中建立和自訂儀表板和小工具的指示，請參閱下列快速入門：

- [新增及管理儀表板](/azure/devops/report/dashboards/dashboards)
- [將 widget 新增至儀表板](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>範例儀表板

以下是一個簡單的範例儀表板，可追蹤 Agile 資料科學專案的短期衝刺活動，包括相關聯存放庫的認可數目。 

- [**倒數**] 磚會顯示目前短期衝刺中剩餘的天數。 

- 這兩個程式**代碼磚**會顯示過去七天的兩個專案存放庫中的認可數目。 

- [ **TDSP 客戶專案的工作專案**] 會顯示所有工作專案及其狀態的查詢結果。 

- **累計流量圖**（CFD）會顯示已關閉和作用中工作專案的數目。

- **燃盡圖**會顯示工作仍會在短期衝刺中的剩餘時間內完成。

- 相較于短期衝刺中的總工作量，**已完成工作圖表**會顯示已完成的工作。

![儀表板](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>後續步驟

[執行小組資料科學](walkthroughs.md)程式的逐步解說會列出示範所有進程步驟的逐步解說。 連結的案例說明如何將雲端和內部部署資源管理成智慧型應用程式。 
