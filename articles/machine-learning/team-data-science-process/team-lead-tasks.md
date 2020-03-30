---
title: 小組負責人在 Team Data Science Process Team 中的工作
description: 團隊資料科學處理團隊中團隊領導任務的詳細演練
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d099d7c233c3f4b5e65bfdb7d4b875a0e4098499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864276"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>團隊資料科學流程團隊領導的任務

本文介紹了*團隊領導*為其資料科學團隊完成的任務。 團隊領導的目標是建立一個協作團隊環境，在[團隊資料科學流程](overview.md)（TDSP） 上實現標準化。 TDSP 旨在説明改善協作和團隊學習。 

TDSP 是一種敏捷的反覆運算資料科學方法，可高效提供預測分析解決方案和智慧應用程式。 該過程從 Microsoft 和業界提煉最佳實踐和結構。  目標是成功實施資料科學計畫，並充分實現其分析計畫的好處。 有關在 TDSP 上標準化的資料科學團隊的人員角色和相關任務的大綱，請參閱[團隊資料科學流程角色和任務](roles-tasks.md)。

團隊領導管理一個團隊，由企業資料科學部門中的多個資料科學家組成。 根據資料科學單位的大小和結構，[組經理](group-manager-tasks.md)和團隊領導可能是同一個人，或者他們可以將其任務委託給代理項。 但是，工作本身不會變更。 

下圖顯示了團隊領導為設置團隊環境而完成的任務的工作流：

![團隊領導任務工作流](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. 在 Azure DevOps 中組組織中創建**團隊專案**。 
  
1. 將預設團隊存儲庫重命名為**團隊實用程式**。
  
1. 在團隊專案中創建新的**TeamTemplate**存儲庫。 
  
1. 將組**組實用程式**和**組專案範本**存儲庫的內容導入**團隊實用程式**和**TeamTemplate**存儲庫。 
  
1. 通過添加團隊成員並配置其許可權來設置**安全控制**。
  
1. 如果需要，請創建團隊資料和分析資源：
   - 將特定于團隊的實用程式添加到**TeamUtilities**存儲庫中。 
   - 創建**Azure 檔存儲**以存儲對整個團隊有用的資料資產。 
   - 將 Azure 檔存儲裝載到團隊主管**的資料科學虛擬機器**（DSVM） 中，並將資料資產添加到其中。

以下教程詳細介紹了這些步驟。

> [!NOTE] 
> 本文使用 Azure DevOps 和 DSVM 來設置 TDSP 團隊環境，因為這是如何在 Microsoft 中實現 TDSP 的方式。 如果您的團隊使用其他代碼託管或開發平臺，則團隊領導任務相同，但完成這些任務的方式可能有所不同。

## <a name="prerequisites"></a>Prerequisites

本教程假定[您的組管理器](group-manager-tasks.md)已設置以下資源和許可權：

- 資料單元的 Azure DevOps**組織**
- **組專案範本**和**組實用程式**存儲庫，填充 Microsoft TDSP 團隊**的專案範本**和**實用程式**存儲庫的內容
- 組織帳戶上的許可權，以便為團隊創建專案和存儲庫

為了能夠在本地電腦或 DSVM 上克隆存儲庫並修改其內容，或設置 Azure 檔存儲並將其裝載到 DSVM，您需要執行以下操作：

- Azure 訂用帳戶。
- Git 安裝在您的電腦上。 如果您使用的是 DSVM，則預先安裝 Git。 否則，請參閱[平台和工具附錄](platforms-and-tools.md#appendix)。
- 如果要使用 DSVM，則在 Azure 中創建和配置 Windows 或 Linux DSVM。 有關詳細資訊和說明，請參閱[資料科學虛擬機器文檔](/azure/machine-learning/data-science-virtual-machine/)。
- 對於 Windows DSVM，在電腦上安裝了[Git 認證管理員 （GCM）。](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) 在*README.md*檔中，向下滾動到 **"下載和安裝**"部分，然後選擇**最新的安裝程式**。 從安裝程式頁面下載 *.exe*安裝程式並運行它。 
- 對於 Linux DSVM，在 DSVM 上設置並在 Azure DevOps 中添加的 SSH 公開金鑰。 有關詳細資訊和說明，請參閱[平臺和工具附錄](platforms-and-tools.md#appendix)中的 **"創建 SSH 公開金鑰**"部分。 

## <a name="create-a-team-project-and-repositories"></a>建立 Team 專案和存放庫

在本節中，您可以在組的 Azure DevOps 組織中創建以下資源：

- Azure 開發人員中的**MyTeam**專案
- **團隊範本**存儲庫
- **團隊實用程式**存儲庫

本教程中為存儲庫和目錄指定的名稱假定您希望在更大的資料科學組織中為自己的團隊建立單獨的專案。 但是，整個組可以選擇在組經理或組織管理員創建的單個專案下工作。 然後，所有資料科學團隊在此單個專案下創建存儲庫。 此方案可能適用于：
- 一個小型資料科學組，沒有多個資料科學團隊。 
- 擁有多個資料科學團隊的較大資料科學組，他們希望優化團隊間協作，以及團隊級衝刺 （sprint 規劃）等活動。 

如果團隊選擇在單個組專案下擁有特定于團隊的存儲庫，則團隊領導應創建具有*\<"團隊名稱>範本*和*\<團隊名稱>實用程式*等名稱的存儲庫。 例如：*團隊範本*和*團隊實用程式*。 

在任何情況下，團隊領導都需要讓團隊成員知道要設置和克隆的範本和實用程式存儲庫。 專案主管應遵循[資料科學團隊的專案領導任務](project-lead-tasks.md)，以創建專案存儲庫，無論是在單獨的專案下還是單個專案下。 

### <a name="create-the-myteam-project"></a>建立 MyTeam 專案

要為您的團隊創建單獨的專案，請：

1. 在 Web 瀏覽器中，轉到組位於 URL HTTPs 的 Azure DevOps 組織主頁*\//\<：\<伺服器名稱>/組織名稱>*，然後選擇 **"新建專案**"。 
   
   ![選擇新專案](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. 在 **"創建專案**"對話方塊中，在 **"專案名稱**"下輸入團隊名稱（如*MyTeam），* 然後選擇 **"高級**"。 
   
1. 在**版本控制**下，選擇**Git**，並在 **"工作項"流程**下選擇 **"敏捷**"。 然後選擇 **"創建**"。 
   
   ![建立專案](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
將打開團隊**專案摘要**頁面，其中頁面 URL * \/ / \<HTTPs：伺服器名稱\<>/組織名稱\<>/團隊名稱>*。

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>將 MyTeam 預設存儲庫重命名為團隊實用程式

1. 在**MyTeam**專案**摘要**頁面上，在 **"您想從什麼服務開始？"** 下，選擇 **"存儲庫**"。 
   
   ![選擇"重點"](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. 在**MyTeam**存儲庫頁面上，選擇頁面頂部的**MyTeam**存儲庫，然後從下拉清單中選擇 **"管理存儲庫**"。 
   
   ![選擇"管理存儲庫"](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. 在"**專案設置"** 頁上，選擇**MyTeam**存儲庫旁邊的 **...** **Rename repository** 
   
   ![選擇重命名存儲庫](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. 在 **"重命名 MyTeam"存儲庫**快顯視窗中，輸入 *"團隊實用程式*"，然後選擇 **"重命名**"。 

### <a name="create-the-teamtemplate-repository"></a>創建團隊範本存儲庫

1. 在"**專案設置"** 頁上，選擇 **"新建存儲庫"。** 
   
   ![選擇"新建存儲庫"](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   或者，從**MyTeam**專案**摘要**頁面的左側導航中選擇 **"Repos"，** 選擇頁面頂部的存儲庫，然後從下拉清單中選擇 **"新建存儲庫**"。
   
1. 在 **"創建新存儲庫"對話方塊中**，請確保在**類型**下選擇了**Git。** 在**存儲庫名稱**下輸入*團隊範本*，然後選擇 **"創建**"。
   
   ![創建存儲庫](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. 確認您可以在專案設置頁面上看到兩個存儲庫**團隊實用程式**和**TeamTemplate。** 
   
   ![兩個團隊存儲庫](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>導入組公共存儲庫的內容

要使用組管理器設置的組公共存儲庫的內容填充團隊存儲庫，請：

1. 在**MyTeam**專案主頁中，在左側導航中選擇 **"存儲庫**"。 如果收到一條消息，指出找不到**MyTeam**範本，請選擇 **"否則"中的連結，導航到預設的 TeamTemplate 存儲庫。** 
   
   將打開預設**的 TeamTemplate**存儲庫。 
   
1. 在 **"團隊範本"為空**頁上，選擇 **"導入**"。 
   
   ![選擇導入](./media/team-lead-tasks/import-repo.png)
   
1. 在 **"導入 Git 存儲庫"對話方塊中**，選擇**Git**作為**源類型**，並在克隆 URL 下輸入組常見範本存儲庫的**URL。** URL 是*\//\<HTTPs：伺服器名稱>/\<組織名稱>/_git/\<存儲庫名稱>*。 例如： *HTTPs：\//dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*。 
   
1. 選取 [匯入]****。 組範本存儲庫的內容將導入到團隊範本存儲庫中。 
   
   ![導入組通用範本存儲庫](./media/team-lead-tasks/import-repo-2.png)
   
1. 在專案的**存儲庫**頁面頂部，下拉並選擇**TeamUtilities**存儲庫。
   
1. 重複導入過程以將組公共實用程式存儲庫（例如*GroupUtilities）* 的內容導入**到 TeamUtilities**存儲庫中。 
   
您的兩個團隊存儲庫現在都包含來自相應組公共存儲庫的檔。 

### <a name="customize-the-contents-of-the-team-repositories"></a>自訂團隊存儲庫的內容

如果要自訂團隊存儲庫的內容以滿足團隊的特定需求，現在可以執行此操作。 您可以修改檔、更改目錄結構或添加檔和資料夾。

要直接在 Azure DevOps 中修改、上載或創建檔或資料夾，請執行以下操作：

1. 在**MyTeam**專案**摘要**頁上，選擇 **"重點**"。 
   
1. 在頁面頂部，選擇要自訂的存儲庫。

1. 在回購目錄結構中，導航到要更改的資料夾或檔。 
   
   - 要創建新資料夾或檔，請選擇 **"新建"** 旁邊的箭頭。 
     
     ![創建新檔](./media/team-lead-tasks/new-file.png)
     
   - 要上載檔，請選擇 **"上載檔"**。 
     
     ![上傳檔案](./media/team-lead-tasks/upload-files.png)
     
   - 要編輯現有檔，請導航到檔，然後選擇 **"編輯**"。 
     
     ![編輯檔](./media/team-lead-tasks/edit-file.png)
     
1. 添加或編輯檔後，選擇 **"提交**"。
   
   ![認可變更](./media/team-lead-tasks/commit.png)

要使用本地電腦或 DSVM 上的存儲庫，請先將存儲庫複製或*克隆*到本地電腦，然後將更改提交並推送到共用團隊存儲庫， 

要克隆存儲庫：

1. 在**MyTeam**專案**摘要**頁上，選擇 **"存儲庫**"，在頁面頂部選擇要克隆的存儲庫。
   
1. 在回購頁面上，選擇右上角的 **"克隆**"。
   
1. 在 **"克隆存儲庫**"對話方塊中，在**命令列**下 ，選擇**HTTPS**以進行 HTTP 連接或**SSH**連接，並將克隆 URL 複製到剪貼簿。
   
   ![複製克隆 URL](./media/team-lead-tasks/clone.png)
   
1. 在本地電腦上，創建以下目錄：
   
   - 對於視窗 **：C：[GitRepos]我的團隊**
   - 對於**Linux，$home/GitRepos/MyTeam** 
   
1. 更改為您創建的目錄。
   
1. 在 Git Bash 中`git clone <clone URL>`，運行\<命令 ，其中克隆 URL>是從**克隆**對話方塊複製的 URL。
   
   例如，使用以下命令之一將**TeamUtilities**存儲庫克隆到本地電腦上的*MyTeam*目錄。 
   
   **HTTPS 連接：**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH 連接：**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

在存儲庫的本地克隆中進行任何所需的更改後，提交並將更改推送到共用團隊存儲庫。 

從本地**GitRepos_MyTeam_TeamTemplate**或**GitRepos_MyTeam_團隊實用程式**目錄中運行以下 Git Bash 命令。

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> 如果這是您第一次提交到 Git 存儲庫，則可能需要在運行`git commit`命令之前*配置user.name*和*使用者.* 執行下列兩個命令：
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> 如果要提交多個 Git 存儲庫，請對所有存儲庫使用相同的名稱和電子郵件地址。 在構建 Power BI 儀表板以在多個存儲庫中跟蹤 Git 活動時，使用相同的名稱和電子郵件地址很方便。

## <a name="add-team-members-and-configure-permissions"></a>添加團隊成員並配置許可權

要將成員添加到團隊：

1. 在 Azure DevOps 中，從**MyTeam**專案主頁中選擇 **"專案設置**"左導航。 
   
1. 從 **"專案設置**"左側導航中，選擇 **"團隊**"，然後在 **"團隊"** 頁上選擇 **"我的團隊**"。 
   
   ![配置團隊](./media/team-lead-tasks/teams.png)
   
1. 在 **"團隊設定檔"** 頁上，選擇 **"添加**"。
   
   ![添加到 MyTeam 團隊](./media/team-lead-tasks/add-to-team.png)
   
1. 在"**添加使用者和組**"對話方塊中，搜索並選擇要添加到組的成員，然後選擇"**保存更改**"。 
   
   ![添加使用者和組](./media/team-lead-tasks/add-users.png)
   

要配置團隊成員的許可權，請：

1. 從"**專案設置**"左側導航中，選擇 **"許可權**"。 
   
1. 在 **"許可權"** 頁上，選擇要向其添加成員的組。 
   
1. 在該組的頁面上，選擇 **"成員**"，然後選擇"**添加**"。 
   
1. 在 **"邀請成員**"快顯視窗中，搜索並選擇要添加到組的成員，然後選擇"**保存**"。 
   
   ![向成員授予許可權](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>創建團隊資料和分析資源

此步驟是可選的，但與您的整個團隊共用資料和分析資源具有性能和成本優勢。 團隊成員可以在共用資源上執行其專案，節省預算，並更高效地協作。 您可以創建 Azure 檔存儲並將其裝載到 DSVM 上，以便與團隊成員共用。 

有關與團隊共用其他資源的資訊（如 Azure HDInsight Spark 群集），請參閱[平臺和工具](platforms-and-tools.md)。 該主題從資料科學的角度提供有關選擇適合您需求的資源的指導，以及指向產品頁面和其他相關和有用教程的連結。

>[!NOTE]
> 為了避免跨資料中心傳輸資料（可能速度緩慢且成本高昂），請確保 Azure 資源組、存儲帳戶和 DSVM 都託管在同一 Azure 區域中。 

### <a name="create-azure-file-storage"></a>創建 Azure 檔存儲

1. 運行以下腳本，為對整個團隊有用的資料資產創建 Azure 檔存儲。 該腳本會提示您輸入 Azure 訂閱資訊，以便準備好輸入這些資訊。 

   - 在 Windows 電腦上，從 PowerShell 命令提示符運行腳本：
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - 在 Linux 電腦上，從 Linux shell 運行腳本：
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. 在提示時登錄到 Microsoft Azure 帳戶，然後選擇要使用的訂閱。
   
1. 選擇要使用的存儲帳戶，或在所選訂閱下創建新帳戶。 可以為 Azure 檔存儲名稱使用小寫字元、數位和連字號。
   
1. 為了便於安裝和共用存儲，請按 Enter 或輸入*Y*將 Azure 檔存儲資訊保存到目前的目錄中的文字檔中。 您可以將此文字檔簽入**TeamTemplate**存儲庫，最好是在**Docs_DataCtionctions**下，因此團隊中的所有專案都可以訪問它。 在下一節中，您還需要檔資訊將 Azure 檔存儲裝載到 Azure DSVM 中。 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>將 Azure 檔存儲裝載到本地電腦或 DSVM 上

1. 要將 Azure 檔存儲裝載到本地電腦或 DSVM，請使用以下腳本。
   
   - 在 Windows 電腦上，從 PowerShell 命令提示符運行腳本：
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - 在 Linux 電腦上，從 Linux shell 運行腳本：
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. 如果在上一步中保存了 Azure 檔存儲資訊檔，則按 Enter 或輸入*Y*以繼續。 輸入您創建的檔的完整路徑和名稱。 
   
   如果沒有 Azure 檔存儲資訊檔，請輸入*n，* 然後按照說明輸入訂閱、Azure 存儲帳戶和 Azure 檔存儲資訊。
   
1. 輸入本地或 TDSP 磁碟機的名稱以裝載檔共用。 螢幕顯示現有磁碟機名稱的清單。 提供不存在的磁碟機名稱。
   
1. 確認新磁碟機和存儲已成功安裝在您的電腦上。

## <a name="next-steps"></a>後續步驟

以下是團隊資料科學流程定義的其他角色和任務的詳細描述的連結：

- [資料科學小組的群組管理員工作](group-manager-tasks.md)
- [資料科學小組的專案負責人工作](project-lead-tasks.md)
- [資料科學團隊的專案個人貢獻者任務](project-ic-tasks.md)
