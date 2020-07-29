---
title: 專案負責人在 Team Data Science Process 中的工作
description: Team 資料科學程式小組的專案負責人工作的詳細逐步解說
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4a4d3a1480a852218e698862a509c4af45e49eb8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76714424"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>小組資料科學流程中的專案負責人工作

本文描述*專案負責人*完成的工作，以便在[team 資料科學](overview.md)程式（TDSP）中為其專案小組設定存放庫。 TDSP 是由 Microsoft 開發的架構，可提供結構化的活動序列，以有效率地執行雲端式、預測性分析解決方案。 TDSP 的設計是為了協助改善共同作業和小組學習。 如需在 TDSP 上標準化之資料科學小組的人員角色和相關工作的大綱，請參閱[Team 資料科學程式角色和](roles-tasks.md)工作。

專案負責人會管理 TDSP 中特定資料科學專案上個別資料科學家的每日活動。 下圖顯示專案負責人工作的工作流程：

![專案負責人工作流程](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

本教學課程涵蓋步驟1：建立專案存放庫和步驟2：從您的小組 ProjectTemplate 存放庫植入專案存放庫。 

針對 [步驟3：建立專案的功能工作專案] 和 [步驟4：加入專案] 階段的案例，請參閱[Agile 開發資料科學專案](agile-development.md)。

針對步驟5：建立和自訂儲存體/分析資產和共用（如有必要），請參閱[建立小組資料和分析資源](team-lead-tasks.md#create-team-data-and-analytics-resources)。

針對步驟6：設定專案存放庫的安全性控制，請參閱[新增小組成員和設定許可權](team-lead-tasks.md#add-team-members-and-configure-permissions)。

> [!NOTE] 
> 本文使用 Azure Repos 來設定 TDSP 專案，因為這是在 Microsoft 執行 TDSP 的方式。 如果您的小組使用另一個程式碼裝載平臺，則專案負責人工作會相同，但完成這些工作的方式可能會不同。

## <a name="prerequisites"></a>必要條件

本教學課程假設您的[群組管理員](group-manager-tasks.md)和[小組負責人](team-lead-tasks.md)已設定下列資源和許可權：

- 適用于您資料單位的 Azure DevOps**組織**
- 適用于資料科學小組的 team**專案**
- 小組範本和公用程式**存放庫**
- 您組織帳戶的**許可權**，可讓您建立及編輯專案的儲存機制

若要複製存放庫及修改本機電腦上的內容，或資料科學虛擬機器（DSVM），或設定 Azure 檔案儲存體並將其掛接至 DSVM，您也需要考慮這份檢查清單：

- Azure 訂用帳戶。
- 已在您的電腦上安裝 Git。 如果您使用 DSVM，則會預先安裝 Git。 否則，請參閱[平台和工具附錄](platforms-and-tools.md#appendix)。
- 如果您想要使用 DSVM，則會在 Azure 中建立並設定 Windows 或 Linux DSVM。 如需詳細資訊和指示，請參閱[資料科學虛擬機器檔](/azure/machine-learning/data-science-virtual-machine/)。
- 針對安裝在您電腦上的 Windows DSVM， [Git 認證管理員（GCM）](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) 。 在*README.md*檔案中，向下卷到 [**下載並安裝**] 區段，然後選取**最新的安裝程式**。 從安裝程式頁面下載 *.exe*安裝程式，並加以執行。 
- 針對 Linux DSVM，在您的 DSVM 上設定的 SSH 公開金鑰，並新增到 Azure DevOps 中。 如需詳細資訊和指示，請參閱[平臺和工具附錄](platforms-and-tools.md#appendix)中的**建立 SSH 公用金鑰**一節。 

## <a name="create-a-project-repository-in-your-team-project"></a>在您的 team 專案中建立專案存放庫

若要在小組的**MyTeam**專案中建立專案存放庫：

1. 移至您小組的 [專案**摘要**] 頁面，網址為* \/ / \<server name> / \<organization name> / \<team name> HTTPs：*（例如**HTTPs： \/ /dev.azure.com/DataScienceUnit/MyTeam**），然後從左側導覽中選取 [**存放庫**]。 
   
1. 選取頁面頂端的存放庫名稱，然後從下拉式清單中選取 [**新增存放庫**]。
   
   ![選取新的存放庫](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. 在 [**建立新**的存放庫] 對話方塊中，確認已在 [**類型**] 底下選取 [ **Git** ]。 在 [存放**庫名稱**] 底下輸入*DSProject1* ，然後選取 [**建立**]。
   
   ![建立存放庫](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. 確認您可以在 [專案設定] 頁面上看到新的**DSProject1**存放庫。 
   
   ![專案設定中的專案存放庫](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>將小組範本匯入到您的專案存放庫

若要以小組範本存放庫的內容填入您的專案存放庫：

1. 從小組的專案 [**摘要**] 頁面中，選取左側導覽中的 [**存放庫**]。 
   
1. 選取頁面頂端的存放庫名稱，然後從下拉式清單中選取 [ **DSProject1** ]。
   
1. 在 [ **DSProject1 是空**的] 頁面上，選取 [匯**入**]。 
   
   ![選取 [匯入]](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. 在 [匯**入 git 存放庫**] 對話方塊中，選取 [ **Git** ] 作為 [**來源類型**]，然後在 [**複製 url**] 底下輸入**TeamTemplate**存放庫的 url。 URL 為*HTTPs： \/ / \<server name> / \<organization name> / \<team name> /_git/ \<team template repository name> *。 例如： **HTTPs： \/ /dev.azure.com/DataScienceUnit/MyTeam/_git/TeamTemplate**。 
   
1. 選取 [匯入]。 小組範本存放庫的內容會匯入到您的專案存放庫。 
   
   ![匯入小組範本存放庫](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

如果您需要自訂專案存放庫的內容，以符合專案的特定需求，您可以新增、刪除或修改存放庫檔案和資料夾。 您可以直接在 Azure Repos 中工作，或將存放庫複製到本機電腦或 DSVM、進行變更，然後認可您的更新並推送至共用的專案存放庫。 請遵循[自訂小組存放庫的內容](team-lead-tasks.md#customize-the-contents-of-the-team-repositories)中的指示。

## <a name="next-steps"></a>後續步驟

以下是小組資料科學程式所定義之其他角色和工作的詳細描述連結：

- [資料科學小組的群組管理員工作](group-manager-tasks.md)
- [資料科學小組的小組負責人工作](team-lead-tasks.md)
- [資料科學小組的個別參與者工作](project-ic-tasks.md)
