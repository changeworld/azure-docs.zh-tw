---
title: Team 資料科學程序群組管理員工作
description: 遵循這個詳細的逐步解說，瞭解群組管理員在資料科學小組專案上完成的工作。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "76721348"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Team 資料科學程序群組管理員工作

本文說明 *群組管理員* 針對資料科學組織完成的工作。 群組管理員會管理企業中的整個資料科學單位。 資料科學單位可能有數個小組，每個小組都在不同的商務縱向中處理許多資料科學專案。 群組管理員的目標是要建立共同作業群組環境，以標準化 [Team Data 科學流程](overview.md) (TDSP) 。 如需資料科學小組所處理的所有人員角色和相關工作的大綱，請參閱 [Team Data 科學流程角色和](roles-tasks.md)工作。

下圖顯示六個主要群組管理員設定工作。 群組管理員可能會將其工作委派給代理，但與角色相關聯的工作則不會變更。

![群組管理員工作](./media/group-manager-tasks/tdsp-group-manager.png)

1. 設定群組的 **Azure DevOps 組織** 。
2. 在 Azure DevOps 組織中建立預設的 **GroupCommon 專案** 。
3. 在 Azure Repos 中建立 **GroupProjectTemplate** 存放庫。
4. 在 Azure Repos 中建立 **GroupUtilities** 存放庫。
5. 將 Microsoft TDSP 小組的 **ProjectTemplate** 和 **公用程式** 存放庫的內容匯入到群組通用存放庫。
6. 設定小組成員存取群組的 **成員資格** 和 **許可權** 。

下列教學課程將逐步解說這些步驟。 

> [!NOTE] 
> 本文使用 Azure DevOps 設定 TDSP 群組環境，因為這是如何在 Microsoft 中執行 TDSP。 如果您的群組使用其他程式碼裝載或開發平臺，群組管理員的工作也相同，但是完成這些工作的方式可能會不同。

## <a name="create-an-organization-and-project-in-azure-devops"></a>在 Azure DevOps 中建立組織和專案

1. 移至 [visualstudio.microsoft.com](https://visualstudio.microsoft.com)，選取右上方的 [登 **入** ]，然後登入您的 Microsoft 帳戶。 
   
   ![登入您的 Microsoft 帳戶](./media/group-manager-tasks/signinvs.png)
   
   如果您沒有 Microsoft 帳戶，請選取 [ **立即註冊**]，建立 Microsoft 帳戶，然後使用此帳戶登入。 如果您的組織有 Visual Studio 訂用帳戶，請使用該訂用帳戶的認證登入。
   
1. 登入之後，在 [Azure DevOps] 頁面的右上方，選取 [ **建立新的組織**]。
   
   ![建立新的組織](./media/group-manager-tasks/create-organization.png)
   
1. 如果系統提示您同意服務條款、隱私權聲明及管理辦法，請選取 [ **繼續**]。
   
1. 在 [註冊] 對話方塊中，將您的 Azure DevOps 組織命名並接受主機區域指派，或下拉式清單並選取不同的區域。 然後選取 [繼續]。 

1. 在 [ **建立專案以開始使用**] 底下，輸入 *GroupCommon*，然後選取 [ **建立專案**]。 
   
   ![建立專案](./media/group-manager-tasks/create-project.png)

[ **GroupCommon** 專案 **摘要** ] 頁面隨即開啟。 頁面 URL 是*HTTPs： \/ / \<servername> / \<organization-name> /GroupCommon*。

![專案摘要頁面](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>設定群組通用存放庫

Azure Repos 為您的群組裝載下列類型的存放庫：

- **群組通用存放庫**：資料科學單位中的多個小組可針對許多資料科學專案採用的一般用途存放庫。 
- **小組存放庫**：資料科學單位內特定小組的存放庫。 這些存放庫是專門用來滿足小組的需求，並可用於該小組內的多個專案，但不夠一般，無法在資料科學單位內的多個小組使用。
- **專案存放庫**：特定專案的存放庫。 這類存放庫對於小組內的多個專案或資料科學單位中的其他小組而言可能不夠一般。

若要在您的專案中設定群組通用存放庫，您可以： 
- 將預設的 **GroupCommon** 存放庫重新命名為 **GroupProjectTemplate**
- 建立新的 **GroupUtilities** 存放庫

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>將預設專案存放庫重新命名為 GroupProjectTemplate

若要將預設的 **GroupCommon** 專案存放庫重新命名為 **GroupProjectTemplate**：

1. 在 [ **GroupCommon** 專案 **摘要** ] 頁面上，選取 [ **存放庫**]。 此動作會帶您前往 GroupCommon 專案的預設 **GroupCommon** 存放庫，這目前是空的。
   
1. 在頁面頂端，下拉 **GroupCommon** 旁的箭號，然後選取 [ **管理存放庫**]。
   
   ![管理存放庫](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. 在 [**專案設定**] 頁面上，選取 [ **GroupCommon**] 旁邊的 **[...]** ，然後選取 [**重新命名存放庫**]。 
   
   ![選擇。。。然後選取 [重新命名存放庫]](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. 在 [ **重新命名 GroupCommon 存放庫** ] 快顯視窗中，輸入 *GroupProjectTemplate*，然後選取 [ **重新命名**]。 
   
   ![重新命名存放庫](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>建立 GroupUtilities 存放庫

若要建立 **GroupUtilities** 存放庫：

1. 在 [ **GroupCommon** 專案 **摘要** ] 頁面上，選取 [ **存放庫**]。 
   
1. 在頁面頂端，下拉 **GroupProjectTemplate** 旁的箭號，然後選取 [新增存放 **庫**]。
   
   ![選取新的存放庫](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. 在 [ **建立新** 的存放庫] 對話方塊中，選取 [ **Git** ] 作為 [ **類型**]，輸入 *GroupUtilities* 作為存放 **庫名稱**，然後選取 [ **建立**]。
   
   ![建立 GroupUtilities 存放庫](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. 在 [**專案設定**] 頁面上，從左側導覽中的 [**存放庫**] 底下選取 [**存放庫**]，以查看兩個群組存放庫： **GroupProjectTemplate**和**GroupUtilities**。
   
   ![兩個群組存放庫](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>匯入 Microsoft TDSP 小組存放庫

在本教學課程的這個部分中，您會將 Microsoft TDSP 小組所管理的 **ProjectTemplate** 和 **公用程式** 存放庫內容匯入 **GroupProjectTemplate** 和 **GroupUtilities** 存放庫中。 

若要匯入 TDSP 小組存放庫：

1. 從 **GroupCommon** 專案首頁的左側導覽中，選取 [ **存放庫** ]。 預設的 **GroupProjectTemplate** 存放庫隨即開啟。 
   
1. 在 [ **GroupProjectTemplate 是空** 的] 頁面上，選取 [匯 **入**]。 
   
   ![選取匯入](./media/group-manager-tasks/import-repo.png)
   
1. 在 [匯**入 git 存放庫**] 對話方塊中，選取 [ **Git** ] 作為**來源類型**，然後針對 [**複製 URL**] 輸入*HTTPs： \/ /github.com/Azure/Azure-TDSP-ProjectTemplate.git* 。 然後選取 [匯 **入**]。 Microsoft TDSP team ProjectTemplate 存放庫的內容會匯入您的 GroupProjectTemplate 存放庫中。 
   
   ![匯入 Microsoft TDSP 小組存放庫](./media/group-manager-tasks/import-repo-2.png)
   
1. 在 [ **存放庫** ] 頁面頂端，下拉並選取 **GroupUtilities** 存放庫。
   
1. 重複匯入程式，將 Microsoft TDSP team **公用事業** 存放庫（ *HTTPs： \/ /github.com/Azure/Azure-TDSP-Utilities.git*）的內容匯入 **GroupUtilities** 存放庫。 
   
您的兩個群組存放庫現在會包含 Microsoft TDSP 小組相對應存放庫中的所有檔案（ *git* 目錄中的檔案除外）。 

## <a name="customize-the-contents-of-the-group-repositories"></a>自訂群組存放庫的內容

如果您想要自訂群組存放庫的內容以符合群組的特定需求，您可以立即這麼做。 您可以修改檔案、變更目錄結構，或新增您的群組已開發或對群組很有説明的檔案。

### <a name="make-changes-in-azure-repos"></a>在 Azure Repos 中進行變更

自訂存放庫內容：

1. 在 [ **GroupCommon** 專案 **摘要** ] 頁面上，選取 [ **存放庫**]。 
   
1. 在頁面頂端，選取您要自訂的存放庫。

1. 在存放庫目錄結構中，流覽至您要變更的資料夾或檔案。 
   
   - 若要建立新的資料夾或檔案，請選取 [ **新增**] 旁邊的箭號。 
     
     ![建立新檔案](./media/group-manager-tasks/new-file.png)
     
   - 若要上傳檔案，請選取 **[上傳檔案 (s]) **。 
     
     ![上傳檔案](./media/group-manager-tasks/upload-files.png)
     
   - 若要編輯現有的檔案，請流覽至該檔案，然後選取 [ **編輯**]。 
     
     ![編輯檔案](./media/group-manager-tasks/edit-file.png)
     
1. 新增或編輯檔案之後，請選取 [ **認可**]。
   
   ![認可變更](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>使用您的本機電腦或 DSVM 進行變更

如果您想要使用本機電腦或 DSVM 進行變更，並將變更推送到群組存放庫，請確定您有使用 Git 和 Dsvm 的必要條件：

- Azure 訂用帳戶（如果您想要建立 DSVM）。
- 在您的電腦上安裝 Git。 如果您是使用 DSVM，則會預先安裝 Git。 否則，請參閱[平台和工具附錄](platforms-and-tools.md#appendix)。
- 如果您想要使用 DSVM，則會在 Azure 中建立並設定 Windows 或 Linux DSVM。 如需詳細資訊和指示，請參閱 [資料科學虛擬機器檔](/azure/machine-learning/data-science-virtual-machine/)。
- 若為 Windows DSVM，則會在您的電腦上安裝 [Git 認證管理員 (GCM) ](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) 。 在 *README.md* 檔案中，向下卷到 [ **下載並安裝** ] 區段，然後選取 **最新的安裝程式**。 從安裝程式頁面下載 *.exe* 安裝程式，然後執行它。 
- 針對 Linux DSVM，會在您的 DSVM 上設定 SSH 公開金鑰，並將其新增至 Azure DevOps。 如需詳細資訊和指示，請參閱[平臺和工具附錄](platforms-and-tools.md#appendix)中的**建立 SSH 公開金鑰**一節。 

首先 *，將存放庫複製或複製* 到本機電腦。 
   
1. 在 [ **GroupCommon** 專案 **摘要** ] 頁面上，選取 [ **存放庫**]，然後在頁面頂端選取您要複製的儲存機制。
   
1. 在 [存放庫] 頁面上，選取右上方的 [ **複製** ]。
   
1. 在 [**複製存放庫**] 對話方塊中，針對 HTTP 連線選取**HTTPS** ，或針對 Ssh**連線**選取 [ **SSH** ]，並將 [複製 URL] 下的 [複製 URL] 複製到剪貼簿。
   
   ![複製存放庫](./media/group-manager-tasks/clone.png)
   
1. 在您的本機電腦上，建立下列目錄：
   
   - 針對 Windows： **C:\GitRepos\GroupCommon**
   - 針對 Linux，您主目錄上的 **$/GitRepos/GroupCommon** 
   
1. 變更至您所建立的目錄。
   
1. 在 Git Bash 中執行命令 `git clone <clone URL>.`
   
   例如，下列任一命令都會將 **GroupUtilities** 儲存機制複製到本機電腦上的 *GroupCommon* 目錄。 
   
   **HTTPS 連接：**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **SSH 連接：**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

在存放庫的本機複製品中進行任何您想要的變更之後，您可以將變更推送至共用群組的一般存放庫。 

從您的本機 **GroupProjectTemplate** 或 **GroupUtilities** 目錄執行下列 Git Bash 命令。

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> 如果這是您第一次認可 Git 存放庫，您可能需要在執行此命令之前，先設定 global 參數*user.name*和*使用者。* `git commit` 執行下列兩個命令：
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> 如果您要認可數個 Git 存放庫，請使用相同的名稱和電子郵件地址。 建立 Power BI 儀表板來追蹤多個存放庫中的 Git 活動時，使用相同的名稱和電子郵件地址是很方便的。

## <a name="add-group-members-and-configure-permissions"></a>新增群組成員及設定許可權

若要將成員加入至群組：

1. 在 Azure DevOps 中，從 [ **GroupCommon** 專案] 首頁的左側導覽中選取 [ **專案設定** ]。 
   
1. 從 [ **專案設定** ] 左側導覽中，選取 [ **小組**]，然後在 [ **小組** ] 頁面上，選取 [ **GroupCommon] 團隊**。 
   
   ![設定小組](./media/group-manager-tasks/teams.png)
   
1. 在 [ **小組設定檔** ] 頁面上，選取 [ **新增**]。
   
   ![新增至 GroupCommon 小組](./media/group-manager-tasks/add-to-team.png)
   
1. 在 [ **新增使用者和群組** ] 對話方塊中，搜尋並選取要新增至群組的成員，然後選取 [ **儲存變更**]。 
   
   ![新增使用者和群組](./media/group-manager-tasks/add-users.png)
   

若要設定成員的許可權：

1. 從 [ **專案設定** ] 左側導覽中，選取 [ **許可權**]。 
   
1. 在 [ **許可權** ] 頁面上，選取您要新增成員的群組。 
   
1. 在該群組的頁面上，選取 [ **成員**]，然後選取 [ **新增**]。 
   
1. 在 [ **邀請成員** ] 快顯視窗中，搜尋並選取要新增至群組的成員，然後選取 [ **儲存**]。 
   
   ![授與許可權給成員](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>後續步驟

以下是 Team Data 科學流程中其他角色和工作的詳細描述連結：

- [資料科學小組的小組負責人工作](team-lead-tasks.md)
- [資料科學小組的專案負責人工作](project-lead-tasks.md)
- [適用于資料科學小組的專案個別參與者工作](project-ic-tasks.md)
