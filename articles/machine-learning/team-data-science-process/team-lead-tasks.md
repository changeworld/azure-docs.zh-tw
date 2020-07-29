---
title: 小組負責人在 Team Data Science Process Team 中的工作
description: 小組負責人在 Team 資料科學程式小組工作的詳細逐步解說
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75864276"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>Team 資料科學程式小組的小組負責人工作

本文說明*小組負責人*為其資料科學小組完成的工作。 小組負責人的目標是要建立共同作業的小組環境，以便在[Team 資料科學](overview.md)程式（TDSP）上標準化。 TDSP 的設計是為了協助改善共同作業和小組學習。 

TDSP 是一種敏捷式反復資料科學方法，可有效率地提供預測性分析解決方案和智慧型應用程式。 此程式會抽取 Microsoft 和產業的最佳作法和結構。  目標是成功實行資料科學計畫，並完全實現其分析程式的優點。 如需在 TDSP 上標準化之資料科學小組的人員角色和相關工作的大綱，請參閱[Team 資料科學程式角色和](roles-tasks.md)工作。

小組負責人負責管理由企業資料科學單位中的數個數據科學家組成的小組。 視資料科學單位的大小和結構而定，[群組管理員](group-manager-tasks.md)和小組負責人可能是同一人，也可以將他們的工作委派給代理。 但是，工作本身不會變更。 

下圖顯示小組組長完成設定小組環境之工作的工作流程：

![小組負責人工作流程](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. 在 Azure DevOps 中，于群組的組織中建立**team 專案**。 
  
1. 將預設的 team 存放庫重新命名為**TeamUtilities**。
  
1. 在 team 專案中建立新的**TeamTemplate**存放庫。 
  
1. 將群組的**GroupUtilities**和**GroupProjectTemplate**存放庫的內容匯入**TeamUtilities**和**TeamTemplate**存放庫。 
  
1. 藉由新增小組成員和設定其許可權來設定**安全性控制**。
  
1. 如有需要，請建立小組資料和分析資源：
   - 將小組特有的公用程式新增至**TeamUtilities**存放庫。 
   - 建立**Azure 檔案儲存體**來儲存適用于整個小組的資料資產。 
   - 將 Azure 檔案儲存體掛接至小組負責人的**資料科學虛擬機器**（DSVM），並在其中新增資料資產。

下列教學課程將逐步解說詳細的步驟。

> [!NOTE] 
> 本文使用 Azure DevOps 和 DSVM 來設定 TDSP 小組環境，因為這是在 Microsoft 執行 TDSP 的方式。 如果您的小組使用其他程式碼裝載或開發平臺，小組負責人工作會相同，但完成這些工作的方式可能會不同。

## <a name="prerequisites"></a>必要條件

本教學課程假設您的[群組管理員](group-manager-tasks.md)已設定下列資源和許可權：

- 適用于您資料單位的 Azure DevOps**組織**
- **GroupProjectTemplate**和**GroupUtilities**存放庫，其中填入了 Microsoft TDSP 團隊的**ProjectTemplate**和**公用程式**存放庫的內容
- 您組織帳戶的許可權，可讓您為小組建立專案和存放庫

若要能夠複製存放庫，並在本機電腦或 DSVM 上修改其內容，或設定 Azure 檔案儲存體並將其掛接至您的 DSVM，您需要下列各項：

- Azure 訂用帳戶。
- 已在您的電腦上安裝 Git。 如果您使用 DSVM，則會預先安裝 Git。 否則，請參閱[平台和工具附錄](platforms-and-tools.md#appendix)。
- 如果您想要使用 DSVM，則會在 Azure 中建立並設定 Windows 或 Linux DSVM。 如需詳細資訊和指示，請參閱[資料科學虛擬機器檔](/azure/machine-learning/data-science-virtual-machine/)。
- 針對安裝在您電腦上的 Windows DSVM， [Git 認證管理員（GCM）](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) 。 在*README.md*檔案中，向下卷到 [**下載並安裝**] 區段，然後選取**最新的安裝程式**。 從安裝程式頁面下載 *.exe*安裝程式，並加以執行。 
- 針對 Linux DSVM，在您的 DSVM 上設定的 SSH 公開金鑰，並新增到 Azure DevOps 中。 如需詳細資訊和指示，請參閱[平臺和工具附錄](platforms-and-tools.md#appendix)中的**建立 SSH 公用金鑰**一節。 

## <a name="create-a-team-project-and-repositories"></a>建立 Team 專案和存放庫

在本節中，您會在群組的 Azure DevOps 組織中建立下列資源：

- 中的**MyTeam**專案 Azure DevOps
- **TeamTemplate**存放庫
- **TeamUtilities**存放庫

本教學課程中為存放庫和目錄指定的名稱會假設您想要在較大的資料科學組織中為自己的小組建立個別的專案。 不過，整個群組可以選擇在由群組管理員或組織系統管理員所建立的單一專案下工作。 然後，所有資料科學小組都會在這個單一專案下建立存放庫。 此案例對而言可能是有效的：
- 不具有多個資料科學小組的小型資料科學群組。 
- 具有多個資料科學小組的較大型資料科學群組，但想要使用群組層級短期衝刺計畫，將小組間共同作業優化。 

如果小組選擇在單一群組專案下擁有其小組專屬的存放庫，小組負責人應該使用* \<TeamName> 範本*和* \<TeamName> 公用程式*之類的名稱來建立存放庫。 例如： *TeamATemplate*和*TeamAUtilities*。 

在任何情況下，小組負責人都必須讓其小組成員知道要設定和複製的範本和公用程式存放庫。 專案負責人應遵循[資料科學小組的專案負責人](project-lead-tasks.md)工作來建立專案存放庫，無論是在個別專案或單一專案下。 

### <a name="create-the-myteam-project"></a>建立 MyTeam 專案

若要為您的小組建立個別的專案：

1. 在您的網頁瀏覽器中，移至您群組的 Azure DevOps 組織首頁，網址為*HTTPs： \/ / \<server name> / \<organization name> *，然後選取 [**新增專案**]。 
   
   ![選取 [新增專案]](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. 在 [**建立專案**] 對話方塊中，于 [**專案名稱**] 下輸入您的小組名稱（例如*MyTeam*），然後選取 [ **Advanced**]。 
   
1. 在 [**版本控制**] 底下，選取 [ **Git**]，然後在 [**工作專案進程**] 下選取 [ **Agile**]。 然後選取 [建立]。 
   
   ![建立專案](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
[Team 專案**摘要**] 頁面隨即開啟，頁面 URL 為*HTTPs： \/ / \<server name> / \<organization name> / \<team name> *。

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>將 MyTeam 預設存放庫重新命名為 TeamUtilities

1. 在 [ **MyTeam**專案**摘要**] 頁面的 [**您要以何種服務開頭？**] 底下，選取 [**存放庫**]。 
   
   ![選取存放庫](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. 在 [ **MyTeam**存放庫] 頁面上，選取頁面頂端的 [ **MyTeam**儲存機制]，然後從下拉式清單中選取 [**管理存放庫**]。 
   
   ![選取 [管理存放庫]](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. 在 [**專案設定**] 頁面上，選取 [ **MyTeam** ] 存放庫旁的 [ **...** ]，然後選取 [**重新命名存放庫**]。 
   
   ![選取 [重新命名存放庫]](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. 在 [**重新命名 MyTeam 存放庫**] 快顯視窗中，輸入*TeamUtilities*，然後選取 [**重新命名**]。 

### <a name="create-the-teamtemplate-repository"></a>建立 TeamTemplate 存放庫

1. 在 [**專案設定**] 頁面上，選取 [**新增存放庫]。** 
   
   ![選取新的存放庫](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   或者，從 [ **MyTeam**專案**摘要**] 頁面的左側導覽中選取 [**存放庫**]，選取頁面頂端的 [存放庫]，然後從下拉式清單中選取 [**新增存放庫**]。
   
1. 在 [**建立新**的存放庫] 對話方塊中，確認已在 [**類型**] 底下選取 [ **Git** ]。 在 [存放**庫名稱**] 底下輸入*TeamTemplate* ，然後選取 [**建立**]。
   
   ![建立存放庫](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. 確認您可以在 [專案設定] 頁面上看到兩個存放庫**TeamUtilities**和**TeamTemplate** 。 
   
   ![兩個小組存放庫](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>匯入群組通用存放庫的內容

若要使用群組管理員所設定的群組通用存放庫內容填入您的小組存放庫：

1. 從您的**MyTeam**專案首頁中，選取左側導覽中的 [**存放庫**]。 如果您收到一則訊息，指出找不到**MyTeam**範本，請選取中的連結 **，否則請流覽至您的預設 TeamTemplate 存放庫。** 
   
   預設的**TeamTemplate**存放庫隨即開啟。 
   
1. 在 [ **TeamTemplate 是空**的] 頁面上，選取 [匯**入**]。 
   
   ![選取 [匯入]](./media/team-lead-tasks/import-repo.png)
   
1. 在 [匯**入 git 存放庫**] 對話方塊中，選取 [ **Git** ] 作為 [**來源類型**]，然後在 [**複製 URL**] 底下輸入群組通用範本存放庫的 URL。 URL 為*HTTPs： \/ / \<server name> / \<organization name> /_git/ \<repository name> *。 例如： *HTTPs： \/ /dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*。 
   
1. 選取 [匯入]。 群組範本存放庫的內容會匯入至您的小組範本存放庫。 
   
   ![匯入群組通用範本存放庫](./media/team-lead-tasks/import-repo-2.png)
   
1. 在專案的 [**存放庫**] 頁面頂端，下拉並選取 [ **TeamUtilities** ] 存放庫。
   
1. 重複匯入程式，將您的群組的內容（例如*GroupUtilities*）匯入您的**TeamUtilities**存放庫。 
   
您兩個 team 存放庫中的每個現在都包含對應的群組一般存放庫中的檔案。 

### <a name="customize-the-contents-of-the-team-repositories"></a>自訂小組存放庫的內容

如果您想要自訂小組存放庫的內容，以符合小組的特定需求，您可以立即這麼做。 您可以修改檔案、變更目錄結構，或新增檔案和資料夾。

若要直接在 Azure DevOps 中修改、上傳或建立檔案或資料夾：

1. 在 [ **MyTeam**專案**摘要**] 頁面上，選取 [**存放庫**]。 
   
1. 在頁面頂端，選取您想要自訂的存放庫。

1. 在存放庫目錄結構中，流覽至您要變更的資料夾或檔案。 
   
   - 若要建立新的資料夾或檔案，請選取 [**新增**] 旁邊的箭號。 
     
     ![建立新檔案](./media/team-lead-tasks/new-file.png)
     
   - 若要上傳檔案，請選取 **[上傳**檔案]。 
     
     ![上傳檔案](./media/team-lead-tasks/upload-files.png)
     
   - 若要編輯現有的檔案，請流覽至該檔案，然後選取 [**編輯**]。 
     
     ![編輯檔案](./media/team-lead-tasks/edit-file.png)
     
1. 新增或編輯檔案之後，請選取 [**認可**]。
   
   ![認可變更](./media/team-lead-tasks/commit.png)

若要在本機電腦或 DSVM 上使用存放庫，您必須先*將存放庫複製或複製*到本機電腦，然後再認可並推送您的變更到共用的小組存放庫， 

若要複製存放庫：

1. 在 [ **MyTeam**專案**摘要**] 頁面上，選取 [**存放庫**]，然後在頁面頂端選取您要複製的存放庫。
   
1. 在 [存放庫] 頁面上，選取右上方的 [**複製**]。
   
1. 在 [**複製存放庫**] 對話方塊的 [**命令列**] 底下 **，針對 [** ssh 連線] 選取 [ **HTTPS** ]，並將複製 URL 複製到剪貼簿。
   
   ![複製複本 URL](./media/team-lead-tasks/clone.png)
   
1. 在您的本機電腦上，建立下列目錄：
   
   - 針對 Windows： **C:\GitRepos\MyTeam**
   - 針對 Linux， **$home/gitrepos/myteam** 
   
1. 變更至您所建立的目錄。
   
1. 在 Git Bash 中，執行命令 `git clone <clone URL>` ，其中 \<clone URL> 是您從 [複製] 對話方塊**Clone**複製的 URL。
   
   例如，使用下列其中一個命令，將**TeamUtilities**存放庫複製到本機電腦上的*MyTeam*目錄。 
   
   **HTTPS 連線：**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH 連線：**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

在存放庫的本機複本中進行任何變更之後，請認可並將變更推送至共用的小組存放庫。 

從您的本機**GitRepos\MyTeam\TeamTemplate**或**GitRepos\MyTeam\TeamUtilities**目錄執行下列 Git Bash 命令。

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> 如果這是您第一次認可至 Git 存放庫，您可能需要在執行命令之前，先設定全域參數*user.name*和*user. email* `git commit` 。 執行下列兩個命令：
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> 如果您要認可數個 Git 存放庫，請針對所有儲存機制使用相同的名稱和電子郵件地址。 建立 Power BI 儀表板以追蹤多個存放庫中的 Git 活動時，使用相同的名稱和電子郵件地址會很方便。

## <a name="add-team-members-and-configure-permissions"></a>新增小組成員及設定許可權

若要將成員加入至小組：

1. 在 Azure DevOps 中，從**MyTeam**專案首頁，從左側導覽中選取 [**專案設定**]。 
   
1. 從 [**專案設定**] [左側流覽] 選取 [**小組**]，然後在 [**小組**] 頁面上選取 [ **MyTeam] 小組**。 
   
   ![設定小組](./media/team-lead-tasks/teams.png)
   
1. 在 [**小組設定檔**] 頁面上，選取 [**新增**]。
   
   ![新增至 MyTeam 小組](./media/team-lead-tasks/add-to-team.png)
   
1. 在 [**新增使用者和群組**] 對話方塊中，搜尋並選取要新增至群組的成員，然後選取 [**儲存變更**]。 
   
   ![新增使用者和群組](./media/team-lead-tasks/add-users.png)
   

若要設定小組成員的許可權：

1. 從 [**專案設定**] 左側導覽中，選取 [**許可權**]。 
   
1. 在 [**許可權**] 頁面上，選取您想要新增成員的群組。 
   
1. 在該群組的頁面上，選取 [**成員**]，然後選取 [**新增**]。 
   
1. 在 [**邀請成員**] 快顯視窗中，搜尋並選取要新增至群組的成員，然後選取 [**儲存**]。 
   
   ![授與許可權給成員](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>建立小組資料與分析資源

此步驟是選擇性的，但與您的整個小組共用資料和分析資源有其效能和成本效益。 小組成員可以在共用資源上執行其專案、節省預算，以及更有效率地共同作業。 您可以建立 Azure 檔案儲存體，並將它裝載在您的 DSVM 上，以與小組成員共用。 

如需與您的小組（例如 Azure HDInsight Spark 叢集）共用其他資源的詳細資訊，請參閱[平臺和工具](platforms-and-tools.md)。 該主題提供從資料科學觀點來看，針對您的需求選取適當資源的指引，以及產品頁面的連結，以及其他相關且實用的教學課程。

>[!NOTE]
> 若要避免跨資料中心傳輸資料，這可能會變慢且成本高昂，請確定您的 Azure 資源群組、儲存體帳戶和 DSVM 全都裝載于相同的 Azure 區域中。 

### <a name="create-azure-file-storage"></a>建立 Azure 檔案儲存體

1. 執行下列腳本，為適用于整個小組的資料資產建立 Azure 檔案儲存體。 此腳本會提示您輸入您的 Azure 訂用帳戶資訊，因此請準備好進入。 

   - 在 Windows 電腦上，從 PowerShell 命令提示字元執行腳本：
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - 在 Linux 機器上，從 Linux shell 執行腳本：
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. 出現提示時，登入您的 Microsoft Azure 帳戶，然後選取您想要使用的訂用帳戶。
   
1. 選取要使用的儲存體帳戶，或在您選取的訂用帳戶底下建立一個新帳戶。 您可以針對 Azure 檔案儲存體名稱使用小寫字元、數位和連字號。
   
1. 若要協助掛接和共用存放裝置，請按 Enter 或輸入*Y* ，將 Azure 檔案儲存體資訊儲存到您目前目錄中的文字檔。 您可以將此文字檔簽入**TeamTemplate**存放庫（最好是在**Docs\DataDictionaries**下），讓您小組中的所有專案都可以存取它。 在下一節中，您也需要檔案資訊，將 Azure 檔案儲存體掛接至您的 Azure DSVM。 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>在您的本機電腦或 DSVM 上掛接 Azure 檔案儲存體

1. 若要將 Azure 檔案儲存體掛接至本機電腦或 DSVM，請使用下列腳本。
   
   - 在 Windows 電腦上，從 PowerShell 命令提示字元執行腳本：
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - 在 Linux 機器上，從 Linux shell 執行腳本：
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. 如果您在上一個步驟中儲存了 Azure 檔案儲存體資訊檔案，請按 Enter 或輸入*Y*繼續。 輸入您所建立之檔案的完整路徑和名稱。 
   
   如果您沒有 Azure 檔案儲存體資訊檔案，請輸入*n*，並遵循指示來輸入您的訂用帳戶、azure 儲存體帳戶和 azure 檔案儲存體資訊。
   
1. 輸入要在其中掛接檔案共用的本機或 TDSP 磁片磁碟機名稱。 畫面會顯示現有磁片磁碟機名稱的清單。 提供尚未存在的磁片磁碟機名稱。
   
1. 確認新的磁片磁碟機和存放裝置已成功掛接在您的電腦上。

## <a name="next-steps"></a>後續步驟

以下是小組資料科學程式所定義之其他角色和工作的詳細描述連結：

- [資料科學小組的群組管理員工作](group-manager-tasks.md)
- [資料科學小組的專案負責人工作](project-lead-tasks.md)
- [資料科學小組的專案個別參與者工作](project-ic-tasks.md)
