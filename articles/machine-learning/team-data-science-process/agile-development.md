---
title: 敏捷式資料科學專案開發 - Team Data Science Process
description: 使用團隊資料科學流程，在專案團隊中以系統、版本控制和協作的方式執行資料科學專案。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c097c14406349d973e905fadb806cc159d9b16d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722096"
---
# <a name="agile-development-of-data-science-projects"></a>敏捷式資料科學專案開發

本文件說明開發人員如何藉由使用 [Team Data Science Process](overview.md) (TDSP)，在專案小組中以系統化、版本控制，以及共同作業的方式執行資料科學專案。 TDSP 是由 Microsoft 開發的一個框架，它提供了結構化的活動序列，以高效地執行基於雲的預測分析解決方案。 有關資料科學團隊在 TDSP 上標準化處理的角色和任務的大綱，請參閱[團隊資料科學流程角色和任務](roles-tasks.md)。 

本文章包含如何執行以下作業的指示： 

- 為專案涉及的工作項進行*衝刺 （sprint） 規劃*。
- 將*工作項*添加到衝刺 （sprint） 中。
- 創建並使用*敏捷派生的工作項範本*，該範本特別與 TDSP 生命週期階段保持一致。

以下說明概述了在 Azure DevOps 中使用 Azure 板和 Azure 存儲庫設置 TDSP 團隊環境所需的步驟。 這些說明使用 Azure DevOps，因為這是如何在 Microsoft 中實現 TDSP。 如果組使用不同的代碼託管平臺，則團隊領導任務通常不會更改，但完成任務的方式不同。 例如，將工作項與 Git 分支連結可能與 GitHub 不同，與 Azure 存儲庫不同。

下圖說明瞭資料科學專案的典型衝刺 （sprint） 規劃、編碼和原始程式碼控制工作流：

![Team Data Science Process](./media/agile-development/1-project-execute.png)

##  <a name="work-item-types"></a><a name='Terminology-1'></a>工作項類型

在 TDSP 衝刺 （sprint） 規劃框架中，有四種常用*的工作項*類型：*功能*、*使用者情景*、*任務*和*Bug。* 所有工作項的積壓工作都在專案級別，而不是 Git 存儲庫級別。 

以下是工作項類型的定義：

- **功能**：功能對應于專案參與。 與客戶的不同約定是不同的功能，最好將專案的不同階段視為不同的功能。 如果選擇*\<用戶端名稱>-\<參與名稱>* 等架構來命名您的功能，則可以輕鬆地從名稱本身識別專案的上下文和參與度。
  
- **使用者情景**：使用者情景是端到端完成功能所需的工作項。 使用者情景的示例包括：
  - 取得資料 
  - 探索資料 
  - 生成功能
  - 建置模型
  - 模型運作 
  - 重新定型模型
  
- **任務**：任務是完成特定使用者情景所需的可分配工作項。 例如，使用者情景獲取*資料*中的任務可以是：
  - 獲取 SQL Server 憑據
  - 將資料上載到 SQL 資料倉儲
  
- **Bug**： Bug 是現有代碼或文檔中的問題，必須修復以完成任務。 如果 Bug 是由缺少的工作項引起的，則 Bug 可能會升級為使用者情景或任務。 

資料科學家可能感覺更舒適使用敏捷範本，用 TDSP 生命週期階段和子階段替換功能、使用者情景和任務。 要創建與 TDSP 生命週期階段特別一致的敏捷派生範本，請參閱[使用敏捷 TDSP 工作範本](#set-up-agile-dsp-6)。

> [!NOTE]
> TDSP 從軟體代碼管理 （SCM） 中借用了功能、使用者情景、任務和 Bug 的概念。 TDSP 概念可能與其傳統的 SCM 定義略有不同。

## <a name="plan-sprints"></a><a name='SprintPlanning-2'></a>計畫衝刺

許多資料科學家參與多個專案，這些專案可能需要數月才能完成，並以不同的速度進行。 短期衝刺計劃對於專案優先順序，以及資源規劃和配置相當有用。 在 Azure 板中，可以輕鬆地為專案創建、管理和跟蹤工作項，並執行衝刺 （sprint） 規劃，以確保專案按預期推進。

有關衝刺 （sprint） 規劃的詳細資訊，請參閱[Scrum 衝刺 。](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint) 

有關 Azure 板中衝刺 （sprint） 規劃的詳細資訊，請參閱[將積壓工作項分配給衝刺 （sprint）](/azure/devops/boards/sprints/assign-work-sprint)。 

## <a name="add-a-feature-to-the-backlog"></a><a name='AddFeature-3'></a>向積壓工作添加功能 

創建專案和專案代碼存儲庫後，可以將功能添加到積壓工作以表示專案的工作。

1. 從專案頁面中，選擇左側導航中的 **"板** > **積壓工作**"。 
   
1. 在 **"積壓工作"** 選項卡上，如果頂部欄中的工作項類型是 **"情景**"，請下拉並選擇 **"要素**"。 然後選擇 **"新建工作項"。**
   
   ![選擇"新建工作項"](./media/agile-development/2-sprint-team-overview.png)
   
1. 輸入功能的標題（通常是專案名稱），然後選擇"**添加到頂部**"。 
   
   ![輸入標題並選擇"添加到頂部"](./media/agile-development/3-sprint-team-add-work.png)
   
1. 從 **"積壓工作"** 清單中，選擇並打開新功能。 填寫說明、分配團隊成員並設置規劃參數。 
   
   還可以通過在 **"開發"** 部分下選擇 **"添加"連結**，將功能連結到專案的 Azure 存儲庫代碼存儲庫。 
   
   編輯要素後，選擇 **"保存&關閉**"。
   
   ![編輯功能並選擇"保存&關閉](./media/agile-development/3a-add-link-repo.png)

## <a name="add-a-user-story-to-the-feature"></a><a name='AddStoryunderfeature-4'></a>向功能添加使用者故事 

在"功能"下，您可以添加使用者情景來描述完成專案所需的主要步驟。 

要向功能添加新的使用者故事，可以：

1. 在 **"積壓工作"** 選項卡上**+**，選擇"功能"左側。 
   
   ![在功能下添加新的使用者故事](./media/agile-development/4-sprint-add-story.png)
   
1. 為使用者故事提供標題，並編輯分配、狀態、說明、注釋、計畫和優先順序等詳細資訊。 
   
   還可以通過在 **"開發**"部分下選擇 **"添加連結**"，將使用者情景連結到專案的 Azure 存儲庫分支。 選擇要將工作項鍊接到的存儲庫和分支，然後選擇 **"確定**"。
   
   ![新增連結](./media/agile-development/5-sprint-edit-story.png)
   
1. 完成編輯使用者故事後，選擇 **"保存&關閉**"。 

## <a name="add-a-task-to-a-user-story"></a><a name='AddTaskunderstory-5'></a>向使用者情景添加任務 

任務是完成每個使用者情景所需的特定詳細步驟。 完成使用者情景的所有任務後，也應完成使用者情景。 

要將任務添加到使用者故事，請選擇"使用者故事**+**"項的旁邊，然後選擇 **"任務**"。 在任務中填寫標題和其他資訊。

![向使用者情景添加任務](./media/agile-development/7-sprint-add-task.png)

創建功能、使用者情景和任務後，可以在**積壓工作**或**板**視圖中查看它們以跟蹤其狀態。

![積壓工作視圖](./media/agile-development/8-sprint-backlog-view.png)

![板視圖](./media/agile-development/8a-sprint-board-view.png)

## <a name="use-an-agile-tdsp-work-template"></a><a name='set-up-agile-dsp-6'></a>使用敏捷的 TDSP 工作範本

資料科學家可能感覺更舒適使用敏捷範本，用 TDSP 生命週期階段和子階段替換功能、使用者情景和任務。 在 Azure 板中，您可以創建一個敏捷派生的範本，該範本使用 TDSP 生命週期階段創建和跟蹤工作項。 以下步驟將演練設置特定于資料科學的敏捷流程範本，並根據該範本創建資料科學工作項。

### <a name="set-up-an-agile-data-science-process-template"></a>設置敏捷資料科學流程範本

1. 從 Azure DevOps 組織主頁中，從左側導航中選擇 **"組織設置**"。 
   
1. 在 **"組織設置**左側導航"中，**在"板"** 下，選擇 **"進程**"。 
   
1. 在 **"所有進程"** 窗格中，選擇**敏捷**旁邊的 **...** **Create inherited process**
   
   ![從敏捷創建繼承的流程](./media/agile-development/10-settings.png) 
   
1. 在**從敏捷對話方塊創建繼承的進程中**，輸入*名稱"敏捷資料科學過程*"，然後選擇 **"創建過程**"。
   
   ![創建敏捷資料科學流程流程](./media/agile-development/11-agileds.png)
   
1. **在所有流程**中，選擇新的**敏捷資料科學流程**。 
   
1. 在 **"工作項類型"** 選項卡上 **，通過選擇**每個專案**User Story**旁邊的 **...** **Feature** **Task** **Disable** 
   
   ![禁用工作項類型](./media/agile-development/12-disable.png)
   
1. 在所有**進程中**，選擇 **"積壓工作級別**"選項卡。在 **"專案組合積壓工作**"下，選擇 Epic 旁邊的 **...** **（禁用的），** 然後選擇 **"編輯/重命名**"。 
   
1. 在 **"編輯積壓工作級別"** 對話方塊中：
   1. 在**名稱**下，用*TDSP 專案*替換**Epic。** 
   1. **在此積壓工作級別的工項類型**下，選擇 **"新建的工作項類型**"，輸入*TDSP 專案*，然後選擇 **"添加**"。 
   1. 在 **"預設工作項類型**"下，下拉並選擇**TDSP 專案**。 
   1. 選取 [儲存]****。
   
   ![設置專案組合積壓工作級別](./media/agile-development/13-rename.png)  
   
1. 按照相同的步驟將**功能**重命名為*TDSP 階段*，並添加以下新的工作項類型：
   
   - *業務理解*
   - *資料擷取*
   - *模型化*
   - *部署*
   
1. 在 **"要求積壓"** 下，將**情景**重命名為*TDSP 子階段*，添加新的工作項類型*TDSP 子階段*，並將預設工作項類型設置為**TDSP 子階段**。
   
1. 在**反覆運算積壓工作**項下，添加新的工作項類型*TDSP 任務*，並將其設置為預設工作項類型。 
   
完成這些步驟後，積壓工作級別應如下所示：
   
 ![TDSP 範本積壓工作級別](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>創建敏捷資料科學流程工作項

您可以使用資料科學流程範本創建 TDSP 專案並跟蹤對應于 TDSP 生命週期階段的工作項。

1. 從 Azure DevOps 組織主頁中選擇 **"新專案**"。 
   
1. 在 **"創建新專案"** 對話方塊中，為專案指定名稱，然後選擇 **"高級**"。 
   
1. 在**工作項流程**下，下拉並選擇**敏捷資料科學流程**，然後選擇"**創建**"。
   
   ![建立 TDSP 專案](./media/agile-development/15-newproject.png)
   
1. 在新創建的專案中，選擇左側導航中的 **"板** > **積壓工作**"。
   
1. 要使 TDSP 專案可見，請選擇 **"配置團隊設置**"圖示。 在 **"設置"** 螢幕中，選擇 **"TDSP 專案**"核取方塊，然後選擇 **"保存並關閉**"。
   
   ![選擇 TDSP 專案核取方塊](./media/agile-development/16-enabledsprojects1.png)
   
1. 要創建特定于資料科學的 TDSP 專案，請在頂部欄中選擇**TDSP 專案**，然後選擇 **"新建工作項**"。 
   
1. 在快顯視窗中，為 TDSP 專案工作項指定名稱，然後選擇"**添加到頂部**"。
   
   ![創建資料科學專案工作項](./media/agile-development/17-dsworkitems0.png)
   
1. 要在 TDSP 專案下添加工作項，請選擇**+** 專案的下一個，然後選擇要創建的工作項的類型。 
   
   ![選擇資料科學工作項類型](./media/agile-development/17-dsworkitems1.png)
   
1. 在新工作項中填寫詳細資訊，然後選擇 **"保存&關閉**"。
   
1. 繼續選擇工作項**+** 旁邊的符號，以添加新的 TDSP 階段、子階段和任務。 
   
下面是資料科學專案工作項應如何在**積壓工作**視圖中顯示的示例：

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>後續步驟

[與 Git 的協作編碼](collaborative-coding-with-git.md)描述了如何使用 Git 作為共用代碼開發框架為數據科學專案進行協作代碼開發，以及如何將這些編碼活動與敏捷流程計畫的工作聯繫起來。

[示例演練](walkthroughs.md)列出了特定方案的演練，包含連結和縮略圖說明。 連結的方案說明了如何將雲和本地工具和服務合併到工作流或管道中以創建智慧應用程式。
  
有關敏捷流程的其他資源：

- [敏捷流程](/azure/devops/boards/work-items/guidance/agile-process)
  
- [敏捷流程工作項類型和工作流](/azure/devops/boards/work-items/guidance/agile-process-workflow)

