---
title: Team 資料科學程序群組管理員工作
description: 按照小組經理在資料科學團隊專案上完成的任務的詳細演練進行。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ec7f4242e5046e90fdf0eb8c6c0579f402e4f55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721348"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Team 資料科學程序群組管理員工作

本文介紹了*組管理器*為數據科學組織完成的任務。 組經理管理企業中的整個資料科學單元。 資料科學部門可能有多個團隊，每個團隊都致力於不同業務垂直領域的許多資料科學專案。 團隊經理的目標是建立一個協作組環境，對[團隊資料科學流程](overview.md)（TDSP） 進行標準化。 有關資料科學團隊在 TDSP 上標準化處理的所有人員角色和相關任務的大綱，請參閱[團隊資料科學流程角色和任務](roles-tasks.md)。

下圖顯示了六個主要組管理器設置任務。 組經理可以將任務委派給代理代理，但與角色關聯的任務不會更改。

![組經理任務](./media/group-manager-tasks/tdsp-group-manager.png)

1. 為組設置**Azure DevOps 組織**。
2. 在 Azure DevOps 組織中創建預設**的組公共專案**。
3. 在 Azure 存儲庫中創建**組專案範本**存儲庫。
4. 在 Azure 存儲庫中創建**組實用程式**存儲庫。
5. 將 Microsoft TDSP 團隊的**ProjectTemplate**和**實用程式**存儲庫的內容導入組公共存儲庫。
6. 為團隊成員設置**成員身份**和**許可權**以訪問該組。

以下教程詳細介紹了這些步驟。 

> [!NOTE] 
> 本文使用 Azure DevOps 來設置 TDSP 組環境，因為這是如何在 Microsoft 中實現 TDSP 的方式。 如果組使用其他代碼託管或開發平臺，則組管理器的任務相同，但完成這些任務的方式可能有所不同。

## <a name="create-an-organization-and-project-in-azure-devops"></a>在 Azure DevOps 中創建組織和專案

1. 轉到[visualstudio.microsoft.com，](https://visualstudio.microsoft.com)選擇右上角**的登錄**，然後登錄到您的 Microsoft 帳戶。 
   
   ![登入您的 Microsoft 帳戶](./media/group-manager-tasks/signinvs.png)
   
   如果您沒有 Microsoft 帳戶，請選擇 **"立即註冊**"，創建 Microsoft 帳戶，然後使用此帳戶登錄。 如果您的組織具有 Visual Studio 訂閱，請使用該訂閱的憑據登錄。
   
1. 登錄後，在 Azure DevOps 頁上的右上角選擇 **"創建新組織**"。
   
   ![創建新組織](./media/group-manager-tasks/create-organization.png)
   
1. 如果系統提示您同意服務條款、隱私權聲明和行為準則，請選擇"**繼續**"。
   
1. 在註冊對話方塊中，為 Azure DevOps 組織命名並接受主機區域分配，或下拉並選擇其他區域。 然後選擇 **"繼續**"。 

1. 在 **"創建要開始的專案**"下，輸入*組公共*，然後選擇"**創建專案**"。 
   
   ![建立專案](./media/group-manager-tasks/create-project.png)

將打開 **"組公共**專案**摘要"** 頁。 頁面 URL 是*\//\<HTTPs：伺服器名稱\<>/組織名稱>/組公共*。

![專案摘要頁](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>設置組公共存儲庫

Azure 存儲庫為組承載以下類型的存儲庫：

- **組公共存儲庫**：資料科學單元中的多個團隊可以採用的通用存儲庫，用於許多資料科學專案。 
- **團隊**存儲庫：資料科學單元內特定團隊的存儲庫。 這些存儲庫特定于團隊的需求，可用於團隊中的多個專案，但不夠通用，無法跨資料科學單元中的多個團隊使用。
- **專案**存儲庫：特定專案的存儲庫。 對於團隊中的多個專案或資料科學單元中的其他團隊，此類存儲庫可能不夠通用。

要在專案中設置組公共存儲庫，請： 
- 將預設**組公共**存儲庫重命名為**組專案範本**
- 創建新**的組實用程式**存儲庫

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>將預設專案存儲庫重命名為組專案範本

要將預設**的組公共**專案存儲庫重命名為**組專案範本**：

1. 在 **"組公共**專案**摘要"** 頁上，選擇 **"重點**"。 此操作將帶您到組公共專案的預設**組公共**存儲庫，該存儲庫當前為空。
   
1. 在頁面頂部，下拉**組公共**旁邊的箭頭，然後選擇 **"管理存儲庫**"。
   
   ![管理存儲庫](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. 在"**專案設置"** 頁上，選擇 **"組公共** **"旁邊的**... **Rename repository** 
   
   ![選擇。。。然後選擇重命名存儲庫](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. 在 **"重命名組公共存儲庫**"快顯視窗中，輸入*組專案範本*，然後選擇 **"重命名**"。 
   
   ![重命名存儲庫](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>建立 GroupUtilities 存放庫

要創建**組實用程式**存儲庫，請執行以下操作：

1. 在 **"組公共**專案**摘要"** 頁上，選擇 **"重點**"。 
   
1. 在頁面頂部，下拉**組專案範本**旁邊的箭頭，然後選擇 **"新建存儲庫**"。
   
   ![選擇"新建存儲庫"](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. 在 **"創建新存儲庫"對話方塊中**，選擇**Git**作為**類型**，輸入*組實用程式*作為**存儲庫名稱**，然後選擇 **"創建**"。
   
   ![創建組實用程式存儲庫](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. 在"**專案設置"** 頁上，選擇左側導航中的**存儲庫**下的**存儲庫**以查看兩個組存儲庫：**組專案範本**和**組實用程式**。
   
   ![兩個組存儲庫](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>導入微軟 TDSP 團隊存儲庫

在本教程的這一部分中，您將 Microsoft TDSP 團隊管理的**ProjectTemplate**和**實用程式**存儲庫的內容導入到**組專案範本**和**組實用程式**存儲庫中。 

要導入 TDSP 團隊存儲庫，：

1. 從 **"組公共**專案主頁"中，選擇左側導航中的 **"重點**"。 將打開預設**的組專案範本**回購。 
   
1. 在 **"組專案範本"為空**頁上，選擇 **"導入**"。 
   
   ![選擇導入](./media/group-manager-tasks/import-repo.png)
   
1. 在 **"導入 Git 存儲庫"對話方塊中**，選擇**Git**作為**源類型**，然後輸入*HTTPs：/github.com/Azure/Azure-TDSP-ProjectTemplate.git\/* 克隆**URL**。 然後選擇 **"導入**"。 Microsoft TDSP 團隊 ProjectTemplate 存儲庫的內容將導入到組專案範本存儲庫中。 
   
   ![導入微軟TDSP團隊存儲庫](./media/group-manager-tasks/import-repo-2.png)
   
1. 在 **"存儲庫"** 頁的頂部，下拉並選擇**組實用程式**存儲庫。
   
1. 重複導入過程，將 Microsoft TDSP 團隊**實用程式**存儲庫 *（HTTPs：\//github.com/Azure/Azure-TDSP-Utilities.git）* 的內容導入**到組實用程式**存儲庫中。 
   
您的兩個組存儲庫現在都包含來自 Microsoft TDSP 團隊的相應存儲庫的所有檔，除了 *.git*目錄中的檔。 

## <a name="customize-the-contents-of-the-group-repositories"></a>自訂群組存儲庫的內容

如果要自訂群組存儲庫的內容以滿足組的特定需求，現在可以執行此操作。 您可以修改檔、更改目錄結構或添加組已開發或對組有説明的檔。

### <a name="make-changes-in-azure-repos"></a>在 Azure 存儲庫中進行更改

要自訂存儲庫內容：

1. 在 **"組公共**專案**摘要"** 頁上，選擇 **"重點**"。 
   
1. 在頁面頂部，選擇要自訂的存儲庫。

1. 在回購目錄結構中，導航到要更改的資料夾或檔。 
   
   - 要創建新資料夾或檔，請選擇 **"新建"** 旁邊的箭頭。 
     
     ![創建新檔](./media/group-manager-tasks/new-file.png)
     
   - 要上載檔，請選擇 **"上載檔"**。 
     
     ![上傳檔案](./media/group-manager-tasks/upload-files.png)
     
   - 要編輯現有檔，請導航到檔，然後選擇 **"編輯**"。 
     
     ![編輯檔](./media/group-manager-tasks/edit-file.png)
     
1. 添加或編輯檔後，選擇 **"提交**"。
   
   ![認可變更](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>使用本地電腦或 DSVM 進行更改

如果要使用本地電腦或 DSVM 進行更改並將更改向上推送到組存儲庫，請確保具有使用 Git 和 DSVM 的先決條件：

- 要創建 DSVM 的 Azure 訂閱。
- Git 安裝在您的電腦上。 如果您使用的是 DSVM，則預先安裝 Git。 否則，請參閱[平台和工具附錄](platforms-and-tools.md#appendix)。
- 如果要使用 DSVM，則在 Azure 中創建和配置 Windows 或 Linux DSVM。 有關詳細資訊和說明，請參閱[資料科學虛擬機器文檔](/azure/machine-learning/data-science-virtual-machine/)。
- 對於 Windows DSVM，在電腦上安裝了[Git 認證管理員 （GCM）。](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) 在*README.md*檔中，向下滾動到 **"下載和安裝**"部分，然後選擇**最新的安裝程式**。 從安裝程式頁面下載 *.exe*安裝程式並運行它。 
- 對於 Linux DSVM，在 DSVM 上設置並在 Azure DevOps 中添加的 SSH 公開金鑰。 有關詳細資訊和說明，請參閱[平臺和工具附錄](platforms-and-tools.md#appendix)中的 **"創建 SSH 公開金鑰**"部分。 

首先，將存儲庫複製或*克隆*到本地電腦。 
   
1. 在 **"組公共**專案**摘要"** 頁上，選擇 **"重點**"，在頁面頂部選擇要克隆的存儲庫。
   
1. 在回購頁面上，選擇右上角的 **"克隆**"。
   
1. 在 **"克隆存儲庫"** 對話方塊中，為 HTTP 連接選擇**HTTPS，** 選擇**SSH**連接，並將**命令列**下的克隆 URL 複製到剪貼簿。
   
   ![複製存放庫](./media/group-manager-tasks/clone.png)
   
1. 在本地電腦上，創建以下目錄：
   
   - 對於視窗 **：C：[GitRepos]組公共**
   - 對於**Linux，$/GitRepos/組公共**在您的主目錄中 
   
1. 更改為您創建的目錄。
   
1. 在 Git Bash 中，運行命令`git clone <clone URL>.`
   
   例如，以下任一命令將**組實用程式**存儲庫克隆到本地電腦上的*GroupCommon*目錄。 
   
   **HTTPS 連接：**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **SSH 連接：**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

在存儲庫的本地克隆中執行所需的任何更改後，可以將更改推送到共用組公共存儲庫。 

從本地**組專案範本**或**組實用程式**目錄中運行以下 Git Bash 命令。

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

## <a name="add-group-members-and-configure-permissions"></a>添加組成員並配置許可權

要將成員添加到組：

1. 在 Azure DevOps 中，從**組公共**專案主頁中選擇左側導航中的 **"專案"設置**。 
   
1. 從"**專案設置**"左側導航中，選擇 **"團隊**"，然後在 **"團隊"** 頁上選擇 **"組共同團隊**"。 
   
   ![配置團隊](./media/group-manager-tasks/teams.png)
   
1. 在 **"團隊設定檔"** 頁上，選擇 **"添加**"。
   
   ![添加到組公共團隊](./media/group-manager-tasks/add-to-team.png)
   
1. 在"**添加使用者和組**"對話方塊中，搜索並選擇要添加到組的成員，然後選擇"**保存更改**"。 
   
   ![添加使用者和組](./media/group-manager-tasks/add-users.png)
   

要配置成員的許可權，請：

1. 從"**專案設置**"左側導航中，選擇 **"許可權**"。 
   
1. 在 **"許可權"** 頁上，選擇要向其添加成員的組。 
   
1. 在該組的頁面上，選擇 **"成員**"，然後選擇"**添加**"。 
   
1. 在 **"邀請成員**"快顯視窗中，搜索並選擇要添加到組的成員，然後選擇"**保存**"。 
   
   ![向成員授予許可權](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>後續步驟

以下是團隊資料科學流程中其他角色和任務的詳細描述的連結：

- [資料科學小組的小組負責人工作](team-lead-tasks.md)
- [資料科學小組的專案負責人工作](project-lead-tasks.md)
- [資料科學團隊的專案個人貢獻者任務](project-ic-tasks.md)
