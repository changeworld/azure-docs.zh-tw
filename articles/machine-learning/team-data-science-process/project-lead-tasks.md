---
title: 專案負責人在 Team Data Science Process 中的工作
description: 團隊資料科學流程團隊專案負責人任務的詳細演練
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76714424"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>團隊資料科學流程中的專案領導任務

本文介紹*專案主管*完成的任務，以在[團隊資料科學流程](overview.md)（TDSP） 中為其專案團隊設置存儲庫。 TDSP 是由 Microsoft 開發的一個框架，它提供了結構化的活動序列，以高效地執行基於雲的預測分析解決方案。 TDSP 旨在説明改善協作和團隊學習。 有關在 TDSP 上標準化的資料科學團隊的人員角色和相關任務的大綱，請參閱[團隊資料科學流程角色和任務](roles-tasks.md)。

專案負責人負責管理資料科學家在TDSP中特定資料科學專案的日常活動。 下圖顯示了專案主管任務的工作流：

![專案主管任務工作流](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

本教程介紹步驟 1：創建專案存儲庫，以及步驟 2：從團隊 ProjectTemplate 存儲庫中設置種子專案存儲庫。 

有關步驟 3：為專案創建功能工作項，步驟 4：為專案階段添加情景，請參閱[資料科學專案的敏捷開發](agile-development.md)。

有關步驟 5：創建和自訂存儲/分析資產，並在必要時共用，請參閱[創建團隊資料和分析資源](team-lead-tasks.md#create-team-data-and-analytics-resources)。

有關步驟 6：設置專案存儲庫的安全控制，請參閱[添加團隊成員並配置許可權](team-lead-tasks.md#add-team-members-and-configure-permissions)。

> [!NOTE] 
> 本文使用 Azure 存儲庫來設置 TDSP 專案，因為這是如何在 Microsoft 中實現 TDSP 的方式。 如果您的團隊使用另一個代碼託管平臺，則專案主管任務相同，但完成它們的方式可能不同。

## <a name="prerequisites"></a>Prerequisites

本教程假定[您的團隊經理](group-manager-tasks.md)和[團隊領導](team-lead-tasks.md)已設置以下資源和許可權：

- 資料單元的 Azure DevOps**組織**
- 資料科學團隊的團隊**專案**
- 團隊範本和實用程式**存儲庫**
- 組織**帳戶上的許可權**，用於為專案創建和編輯存儲庫

要克隆存儲庫並修改本地電腦或資料科學虛擬機器 （DSVM） 上的內容，或設置 Azure 檔存儲並將其裝載到 DSVM，還需要考慮以下檢查表：

- Azure 訂用帳戶。
- Git 安裝在您的電腦上。 如果您使用的是 DSVM，則預先安裝 Git。 否則，請參閱[平台和工具附錄](platforms-and-tools.md#appendix)。
- 如果要使用 DSVM，則在 Azure 中創建和配置 Windows 或 Linux DSVM。 有關詳細資訊和說明，請參閱[資料科學虛擬機器文檔](/azure/machine-learning/data-science-virtual-machine/)。
- 對於 Windows DSVM，在電腦上安裝了[Git 認證管理員 （GCM）。](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) 在*README.md*檔中，向下滾動到 **"下載和安裝**"部分，然後選擇**最新的安裝程式**。 從安裝程式頁面下載 *.exe*安裝程式並運行它。 
- 對於 Linux DSVM，在 DSVM 上設置並在 Azure DevOps 中添加的 SSH 公開金鑰。 有關詳細資訊和說明，請參閱[平臺和工具附錄](platforms-and-tools.md#appendix)中的 **"創建 SSH 公開金鑰**"部分。 

## <a name="create-a-project-repository-in-your-team-project"></a>在團隊專案中創建專案存儲庫

要在團隊的**MyTeam**專案中創建專案存儲庫，請執行操作：

1. 轉到團隊的專案**摘要**頁面*\//\<HTTPs：伺服器\<名稱>/組織名稱>/\<團隊名稱>，* 例如**HTTPs：/dev.azure.com/DataScienceUnit/MyTeam\/**，並從左側導航中選擇**Repos。** 
   
1. 選擇頁面頂部的存儲庫名稱，然後從下拉清單中選擇 **"新建存儲庫**"。
   
   ![選擇"新建存儲庫"](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. 在 **"創建新存儲庫"對話方塊中**，請確保在**類型**下選擇了**Git。** 在**存儲庫名稱**下輸入*DSProject1，* 然後選擇 **"創建**"。
   
   ![創建存儲庫](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. 確認您可以在專案設置頁面上看到新的**DSProject1**存儲庫。 
   
   ![專案設置中的專案存儲庫](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>將團隊範本導入專案存儲庫

要使用團隊範本存儲庫的內容填充專案存儲庫，請執行以下操作：

1. 從團隊的專案**摘要**頁面中，選擇左側導航中的 **"重點**"。 
   
1. 選擇頁面頂部的存儲庫名稱，然後從下拉清單中選擇**DSProject1。**
   
1. 在**DSProject1 上為空**頁，選擇 **"導入**"。 
   
   ![選擇導入](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. 在 **"導入 Git 存儲庫"對話方塊中**，選擇**Git**作為**源類型**，並在克隆 URL 下輸入**TeamTemplate**存儲庫的**URL。** URL 是*HTTPs：\//\<伺服器名稱\<>/組織名稱>/\<團隊名稱>/_git/\<團隊範本存儲庫名稱>*。 例如： **HTTPs：\//dev.azure.com/DataScienceUnit/MyTeam/_git/TeamTemplate**。 
   
1. 選取 [匯入]****。 團隊範本存儲庫的內容將導入到專案存儲庫中。 
   
   ![導入團隊範本存儲庫](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

如果需要自訂專案存儲庫的內容以滿足專案的特定需求，可以添加、刪除或修改存儲庫檔和資料夾。 可以直接在 Azure 存儲庫中工作，也可以將存儲庫克隆到本地電腦或 DSVM 中，進行更改，並將更新提交並推送到共用專案存儲庫。 按照[自訂團隊存儲庫內容](team-lead-tasks.md#customize-the-contents-of-the-team-repositories)的說明進行操作。

## <a name="next-steps"></a>後續步驟

以下是團隊資料科學流程定義的其他角色和任務的詳細描述的連結：

- [資料科學小組的群組管理員工作](group-manager-tasks.md)
- [資料科學小組的小組負責人工作](team-lead-tasks.md)
- [資料科學團隊的個人貢獻者任務](project-ic-tasks.md)
