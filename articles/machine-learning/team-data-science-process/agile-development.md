---
title: 敏捷式資料科學專案開發 - Team Data Science Process
description: 使用 Team 資料科學程式，在專案小組中以系統化、版本控制和共同作業的方式執行資料科學專案。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76722096"
---
# <a name="agile-development-of-data-science-projects"></a>敏捷式資料科學專案開發

本文件說明開發人員如何藉由使用 [Team Data Science Process](overview.md) (TDSP)，在專案小組中以系統化、版本控制，以及共同作業的方式執行資料科學專案。 TDSP 是由 Microsoft 開發的架構，可提供結構化的活動序列，以有效率地執行雲端式、預測性分析解決方案。 如需在 TDSP 上標準化之資料科學小組所處理的角色和工作的大綱，請參閱[Team 資料科學程式角色和](roles-tasks.md)工作。 

本文章包含如何執行以下作業的指示： 

- 針對與專案相關的工作專案進行短期*衝刺計畫*。
- 將*工作專案*加入至短期衝刺。
- 建立和使用*agile 衍生的工作專案範本*，特別與 TDSP 生命週期階段一致。

下列指示概述在 Azure DevOps 中使用 Azure Boards 和 Azure Repos 設定 TDSP 小組環境所需的步驟。 這些指示會使用 Azure DevOps，因為這是在 Microsoft 執行 TDSP 的方式。 如果您的群組使用不同的程式碼裝載平臺，小組負責人工作通常不會變更，但是完成工作的方式不同。 例如，連結工作專案與 Git 分支可能不會與 Azure Repos 的 GitHub 相同。

下圖說明資料科學專案的典型短期衝刺計畫、編碼和原始檔控制工作流程：

![Team Data Science Process](./media/agile-development/1-project-execute.png)

##  <a name="work-item-types"></a><a name='Terminology-1'></a>工作專案類型

在 TDSP 短期衝刺計畫架構中，有四種常用*的工作專案*類型：*功能*、*使用者故事* *、工作*和*bug*。 所有工作專案的待處理專案都是在專案層級，而不是 Git 存放庫層級。 

以下是工作專案類型的定義：

- **功能**：對應至專案參與的功能。 與用戶端的不同合作是不同的功能，因此最好將專案的不同階段視為不同的功能。 如果您選擇架構，例如* \<ClientName>-\<EngagementName>* 來為您的功能命名，您可以輕鬆地辨識專案的內容，並從名稱本身進行參與。
  
- **使用者案例**：使用者故事是完成功能端對端所需的工作專案。 使用者故事的範例包括：
  - 取得資料 
  - 探索資料 
  - 產生功能
  - 建置模型
  - 模型運作 
  - 重新定型模型
  
- 工作 **：工作**是可指派的工作專案，必須完成才能完成特定的使用者案例。 例如，使用者案例中的工作會*取得資料*：
  - 取得 SQL Server 認證
  - 將資料上傳至 SQL 資料倉儲
  
- **Bug**： bug 是現有程式碼中的問題，或是必須修正才能完成工作的檔。 如果 Bug 是由遺漏的工作專案所造成，則可以呈報為使用者案例或工作。 

資料科學家可能會覺得使用 agile 範本來取代功能、使用者故事和工作，並使用 TDSP 生命週期階段和階段，來更加熟悉。 若要建立與 TDSP 生命週期階段特別一致的 agile 衍生範本，請參閱[使用 AGILE TDSP 工作範本](#set-up-agile-dsp-6)。

> [!NOTE]
> TDSP 會從軟體程式碼管理（SCM）借用功能、使用者故事、工作和 Bug 的概念。 TDSP 概念可能會與傳統 SCM 定義稍有不同。

## <a name="plan-sprints"></a><a name='SprintPlanning-2'></a>規劃短期衝刺

許多資料科學家都會參與多個專案，這可能需要數個月的時間才能完成，並在不同的步調中繼續進行。 短期衝刺計劃對於專案優先順序，以及資源規劃和配置相當有用。 在 Azure Boards 中，您可以輕鬆地建立、管理和追蹤專案的工作專案，並進行短期衝刺計畫，以確保專案如預期般向前移動。

如需短期衝刺計畫的詳細資訊，請參閱[Scrum 短期衝刺](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint)。 

如需 Azure Boards 中短期衝刺計畫的詳細資訊，請參閱[將待處理專案指派給短期衝刺（sprint](/azure/devops/boards/sprints/assign-work-sprint)）。 

## <a name="add-a-feature-to-the-backlog"></a><a name='AddFeature-3'></a>將功能新增至待處理專案 

建立專案和專案程式碼存放庫之後，您可以將功能加入至待處理專案（backlog），以表示專案的工作。

1. 從您的專案頁面中 **，選取** > 左側**導覽中的**[面板待辦專案]。 
   
1. 在 [**待辦**專案] 索引標籤上，如果頂端列的工作專案類型是 [**故事**]、[下拉式] 和 [選取**功能**]。 然後選取 [**新增工作專案]。**
   
   ![選取 [新增工作專案]](./media/agile-development/2-sprint-team-overview.png)
   
1. 輸入功能的 [標題]，通常是您的專案名稱，然後選取 [**新增到頂端**]。 
   
   ![輸入標題，然後選取 [新增至頁首]](./media/agile-development/3-sprint-team-add-work.png)
   
1. 從**待**處理專案清單中，選取並開啟 [新增] 功能。 填寫 [描述]、[指派小組成員] 和 [設定計劃參數]。 
   
   您也可以選取 [**開發**] 區段底下的 [**新增] 連結**，將功能連結至專案的 Azure Repos 程式碼存放庫。 
   
   編輯功能之後，請選取 [**儲存 & 關閉**]。
   
   ![編輯功能，然後選取 [儲存] & 關閉](./media/agile-development/3a-add-link-repo.png)

## <a name="add-a-user-story-to-the-feature"></a><a name='AddStoryunderfeature-4'></a>將使用者故事新增至功能 

在此功能下，您可以新增使用者故事來描述完成專案所需的主要步驟。 

若要將新的使用者故事新增至功能：

1. 在 [**待**處理專案] 索引**+** 標籤上，選取功能左邊的。 
   
   ![在功能下加入新的使用者故事](./media/agile-development/4-sprint-add-story.png)
   
1. 提供使用者案例的標題，並編輯詳細資料，例如指派、狀態、描述、批註、規劃和優先順序。 
   
   您也可以選取 [**開發**] 區段底下的 [**新增] 連結**，將使用者故事連結到專案的 Azure Repos 程式碼存放庫的分支。 選取您想要連結工作專案的存放庫和分支，然後選取 **[確定]**。
   
   ![新增連結](./media/agile-development/5-sprint-edit-story.png)
   
1. 當您完成編輯使用者案例時，請選取 [**儲存 & 關閉**]。 

## <a name="add-a-task-to-a-user-story"></a><a name='AddTaskunderstory-5'></a>將工作新增至使用者案例 

工作是完成每個使用者案例所需的特定詳細步驟。 當使用者故事的所有工作都完成之後，應該也會完成使用者案例。 

若要將工作新增至使用者案例，請選取**+** [使用者故事] 專案旁邊的，然後選取 [工作 **]。** 填寫工作中的標題和其他資訊。

![將工作新增至使用者案例](./media/agile-development/7-sprint-add-task.png)

建立功能、使用者故事和工作之後，您可以在待處理專案（ **backlog** **）或面板**視圖中加以查看，以追蹤其狀態。

![待處理專案視圖](./media/agile-development/8-sprint-backlog-view.png)

![電路板視圖](./media/agile-development/8a-sprint-board-view.png)

## <a name="use-an-agile-tdsp-work-template"></a><a name='set-up-agile-dsp-6'></a>使用 agile TDSP 工作範本

資料科學家可能會覺得使用 agile 範本來取代功能、使用者故事和工作，並使用 TDSP 生命週期階段和階段，來更加熟悉。 在 Azure Boards 中，您可以建立 agile 衍生的範本，以使用 TDSP 生命週期階段來建立和追蹤工作專案。 下列步驟將逐步解說如何設定資料科學特定 agile 流程範本，以及如何根據範本建立資料科學工作專案。

### <a name="set-up-an-agile-data-science-process-template"></a>設定 Agile 資料科學流程範本

1. 從您 Azure DevOps 組織的主頁面，選取左側導覽中的 [**組織設定**]。 
   
1. 在 [**組織設定**] [左側流覽] 的 [面板]**底下，選取**[**處理**]。 
   
1. 在 [**所有進程**] 窗格中，選取 [ **Agile**] 旁的 [ **...** ]，然後選取 [**建立繼承的進程**]。
   
   ![從 Agile 建立繼承的進程](./media/agile-development/10-settings.png) 
   
1. 在 [**從 Agile 建立繼承的進程**] 對話方塊中，輸入名稱*AgileDataScienceProcess*，然後選取 [**建立進程**]。
   
   ![建立 AgileDataScienceProcess 流程](./media/agile-development/11-agileds.png)
   
1. 在 [**所有進程**] 中，選取新的**AgileDataScienceProcess**。 
   
1. 在 [**工作專案類型**] 索引標籤上，選取每個專案旁邊的 [ **...** ]，然後選取 [**停****用]，以停**用**長篇**、**功能**、**使用者案例**和工作。 
   
   ![停用工作專案類型](./media/agile-development/12-disable.png)
   
1. 在 [**所有進程**] 中，選取 [**待**處理專案] 索引標籤。在 **[組合待處理**專案] 底下，選取 [**長篇（已停用）**] 旁的 [ **...** ]，然後選取 [**編輯/重新命名**]。 
   
1. 在 [**編輯待辦專案層級**] 對話方塊中：
   1. 在 [**名稱**] 底下，將 [**長篇**] 取代為 [ *TDSP 專案*]。 
   1. 在**此待處理專案層級的 [工作專案類型**] 底下，選取 [**新增工作專案類型**]，輸入*TDSP 專案*，然後選取 [**新增**]。 
   1. 在 [**預設工作專案類型**] 下，下拉並選取 [ **TDSP 專案**]。 
   1. 選取 [儲存]  。
   
   ![設定待辦專案組合層級](./media/agile-development/13-rename.png)  
   
1. 遵循相同的步驟將**功能**重新命名為*TDSP 階段*，並加入下列新的工作專案類型：
   
   - *瞭解商務*
   - *資料擷取*
   - *模型化*
   - *部署*
   
1. 在 [**需求待**處理專案] 底下，將**故事**重新命名為*TDSP 階段*，加入新的工作專案類型*TDSP Substage*，並將預設工作專案類型設定為**TDSP Substage**。
   
1. 在 [**反覆運算待**處理專案] 下，加入新的工作專案類型*TDSP Task*，並將它設定為預設的工作專案類型。 
   
完成這些步驟之後，待處理專案（backlog）層級看起來應該像這樣：
   
 ![TDSP 範本待處理專案層級](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>建立 Agile 資料科學處理工作專案

您可以使用資料科學流程範本來建立 TDSP 專案，以及追蹤對應至 TDSP 生命週期階段的工作專案。

1. 從您的 Azure DevOps 組織主要頁面上，選取 [**新增專案**]。 
   
1. 在 [**建立新專案**] 對話方塊中，為您的專案提供名稱，然後選取 [ **Advanced**]。 
   
1. 在 [**工作專案流程**] 下，下拉並選取 [ **AgileDataScienceProcess**]，然後選取 [**建立**]。
   
   ![建立 TDSP 專案](./media/agile-development/15-newproject.png)
   
1. 在新建立的專案中，**選取** > 左側**導覽中的**[面板待辦專案]。
   
1. 若要讓 TDSP 專案可見，請選取 [**設定小組設定**] 圖示。 在 [**設定**] 畫面中，選取 [ **TDSP 專案**] 核取方塊，然後選取 [**儲存並關閉**]。
   
   ![[選取 TDSP 專案] 核取方塊](./media/agile-development/16-enabledsprojects1.png)
   
1. 若要建立資料科學特定的 TDSP 專案，請選取頂端列中的 [ **TDSP 專案**]，然後選取 [**新增工作專案**]。 
   
1. 在快顯視窗中，提供 TDSP 專案工作專案的名稱，然後選取 [**加入至頂端**]。
   
   ![建立資料科學專案工作專案](./media/agile-development/17-dsworkitems0.png)
   
1. 若要在 TDSP 專案下新增工作專案，請選取**+** 專案旁邊的，然後選取要建立的工作專案類型。 
   
   ![選取資料科學工作專案類型](./media/agile-development/17-dsworkitems1.png)
   
1. 在新的工作專案中填入詳細資料，然後選取 [**儲存 & 關閉**]。
   
1. 繼續選取 [工作**+** 專案] 旁的符號，以加入新的 TDSP 階段、階段和工作。 
   
以下是「資料科學專案」工作專案應該如何出現在待處理專案（ **backlog** ）視圖中的範例：

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>後續步驟

[使用 git](collaborative-coding-with-git.md)的共同作業程式碼說明如何使用 git 做為共用程式碼開發架構來進行資料科學專案的協同作業程式碼開發，以及如何將這些編碼活動連結至以 agile 流程規劃的工作。

[範例](walkthroughs.md)逐步解說會列出特定案例的逐步解說，其中包含連結和縮圖描述。 連結的案例說明如何將雲端和內部部署工具和服務組合成工作流程或管線，以建立智慧型應用程式。
  
Agile 流程上的其他資源：

- [Agile 流程](/azure/devops/boards/work-items/guidance/agile-process)
  
- [Agile 流程工作專案類型和工作流程](/azure/devops/boards/work-items/guidance/agile-process-workflow)

