---
title: 追蹤 Team Data 科學流程專案的進度
description: 資料科學群組管理員、小組負責人和專案負責人可如何追蹤資料科學專案的進度。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 78a543fabadcc0d4e1766af1bc5c65aac0dadebe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358920"
---
# <a name="track-the-progress-of-data-science-projects"></a>追蹤資料科學專案的進度

資料科學群組管理員、小組負責人和專案負責人可以追蹤其專案的進度。  管理員想要知道已完成的工作、誰執行了哪些工作，以及剩餘的工作。   管理期望是一個重要的成功元素。

## <a name="azure-devops-dashboards"></a>Azure DevOps 儀表板

如果您是使用 Azure DevOps，您可以建立儀表板來追蹤與給定 Agile 專案相關聯的活動和工作專案。 如需儀表板的詳細資訊，請參閱 [儀表板、報表和小](/azure/devops/report/dashboards/)工具。

如需有關如何在 Azure DevOps 中建立和自訂儀表板和小工具的指示，請參閱下列快速入門：

- [新增及管理儀表板](/azure/devops/report/dashboards/dashboards)
- [將小工具新增至儀表板](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>範例儀表板

以下是簡單的範例儀表板，可追蹤敏捷式資料科學專案的短期衝刺活動，包括對相關存放庫的認可數目。 

- **倒數計時**圖格會顯示目前衝刺（sprint）中剩餘的天數。 

- 這兩個程式 **代碼磚** 會顯示過去七天內兩個專案存放庫中的認可數目。 

- **TDSP 客戶專案的工作專案** 會顯示所有工作專案及其狀態的查詢結果。 

- **累計流程圖** (CFD) 顯示已關閉和作用中工作專案的數目。

- 待執行工作 **圖表** 會顯示在短期衝刺中仍在剩餘時間內完成的工作。

- **已完成工作圖**會顯示已完成的工作與短期衝刺中的總工作量。

![螢幕擷取畫面顯示 Azure DevOps 的儀表板範例。](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>後續步驟

[執行 Team Data 科學流程](walkthroughs.md) 的逐步解說會列出示範所有處理步驟的逐步解說。 連結的案例說明如何將雲端和內部部署資源管理為智慧型應用程式。 
