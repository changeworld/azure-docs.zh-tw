---
title: 在 Azure 自動化中使用原始檔控制整合
description: 此文章說明如何同步處理 Azure 自動化的原始檔控制與其他存放庫。
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: eea4de106fe566b55ae30330d4c9d101f7126bbf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86229613"
---
# <a name="use-source-control-integration"></a>使用原始檔控制整合

 Azure 自動化中的原始檔控制整合支援來自原始檔控制存放庫的單向同步處理。 原始檔控制可讓您利用 GitHub 或 Azure Repos 原始檔控制存放庫中的指令碼，將自動化帳戶中的 Runbook 保持在最新狀態。 此功能可讓您輕鬆地將已在開發環境中測試過的程式碼提升至生產環境自動化帳戶。
 
 原始檔控制整合可讓您輕鬆地與小組共同作業、追蹤變更，以及回復至舊版的 Runbook。 例如，原始檔控制可讓您將原始檔控制中的不同分支與您的開發、測試和生產環境自動化帳戶同步處理。 

## <a name="source-control-types"></a>原始檔控制類型

Azure 自動化支援三種類型的原始檔控制：

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Prerequisites

* 原始檔控制存放庫 (GitHub 或 Azure Repos)
* [執行身分帳戶](manage-runas-account.md)
* 您的自動化帳戶中[最新的 Azure 模組](automation-update-azure-modules.md)，包括 `Az.Accounts` 模組 (`AzureRM.Profile` 的對等 Az 模組)

> [!NOTE]
> 原始檔控制同步處理作業會在使用者的自動化帳戶之下執行，而且會以與其他自動化作業相同的費率計費。

## <a name="configure-source-control"></a>設定原始檔控制

此節說明如何設定自動化帳戶的原始檔控制。 您可以使用 Azure 入口網站或 PowerShell。

### <a name="configure-source-control-in-azure-portal"></a>設定 Azure 入口網站中的原始檔控制

您可以使用此程序來設定使用 Azure 入口網站的原始檔控制。

1. 在您的自動化帳戶內，選取 [原始檔控制]，然後按一下 [新增]。

    ![選取原始檔控制](./media/source-control-integration/select-source-control.png)

2. 選擇 [原始檔控制類型]，然後按一下 [驗證]。 

3. 瀏覽器視窗隨即開啟，並提示您登入。 依照提示完成驗證。

4. 在 [原始檔控制摘要] 頁面上，使用欄位填入下列定義的原始檔控制屬性。 完成後，請按一下 [儲存]。 

    |屬性  |描述  |
    |---------|---------|
    |原始檔控制名稱     | 原始檔控制的易記名稱。 此名稱只能包含字母與數字。        |
    |原始檔控制類型     | 原始檔控制機制的類型。 可用選項包括：</br> * GitHub</br>* Azure Repos (Git)</br> * Azure Repos (TFVC)        |
    |Repository     | 存放庫或專案的名稱。 系統會擷取前 200 個存放庫。 若要搜尋存放庫，請在欄位中輸入名稱，然後按一下 [在 GitHub 上搜尋]。|
    |分支     | 要從中提取原始程式檔的分支。 分支目標不適用於 TFVC 原始檔控制類型。          |
    |資料夾路徑     | 包含要同步處理之 Runbook 的資料夾，例如 **/Runbooks**。 只有指定資料夾中的 Runbook 會進行同步處理。 不支援遞迴。        |
    |自動同步處理<sup>1</sup>     | 在原始檔控制存放庫中進行認可時，可開啟或關閉自動同步處理的設定。        |
    |發佈 Runbook     | 如果 Runbook 在從原始檔控制進行同步處理之後自動發行，則設定為 [開啟]，否則為 [關閉]。           |
    |描述     | 指定原始檔控制之其他詳細資料的文字。        |

    <sup>1</sup> 若要在設定與 Azure Repos 的原始檔控制整合時啟用自動同步處理，您必須是專案系統管理員。

   ![原始檔控制摘要](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> 原始檔控制存放庫的登入可能會與 Azure 入口網站的登入不同。 確定您在設定原始檔控制時，使用了正確的原始檔控制存放庫帳戶登入。 如果有所疑慮，請在您的瀏覽器中開啟新的索引標籤並從 **dev.azure.com**、**visualstudio.com** 或 **github.com** 登出，然後嘗試重新連線至原始檔控制。

### <a name="configure-source-control-in-powershell"></a>在 PowerShell 中設定原始檔控制

您也可以使用 PowerShell 來設定 Azure 自動化中的原始檔控制。 若要使用 PowerShell Cmdlet 進行此作業，您需要個人存取權杖 (PAT)。 使用 [New-AzAutomationSourceControl](/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0) \(英文\) Cmdlet 來建立原始檔控制連線。 此 Cmdlet 需要安全的 PAT 字串。 若要了解如何建立安全字串，請參閱 [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6)。

下列小節說明如何建立適用於 GitHub、Azure Repos (Git) 和 Azure Repos (TFVC) 之原始檔控制連線的 PowerShell。 

#### <a name="create-source-control-connection-for-github"></a>建立適用於 GitHub 的原始檔控制連線

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>建立適用於 Azure Repos (Git) 的原始檔控制連線

> [!NOTE]
> Azure Repos (Git) 會使用可存取 **dev.azure.com** 而不是 **visualstudio.com** (用於舊版格式) 的 URL。 舊版的 URL 格式 `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` 已被取代，但仍受到支援。 建議採用新格式。


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>建立適用於 Azure Repos (TFVC) 的原始檔控制連線

> [!NOTE]
> Azure Repos (TFVC) 會使用可存取 **dev.azure.com** 而不是 **visualstudio.com** (用於舊版格式) 的 URL。 舊版的 URL 格式 `https://<accountname>.visualstudio.com/<projectname>/_versionControl` 已被取代，但仍受到支援。 建議採用新格式。

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>個人存取權杖 (PAT) 權限

原始檔控制需要一些 PAT 的最低權限。 下列小節包含 GitHub 和 Azure Repos 所需的最低權限。

##### <a name="minimum-pat-permissions-for-github"></a>GitHub 的最低 PAT 權限

下表定義 GitHub 所需的最低 PAT 權限。 如需在 GitHub 中建立 PAT 的詳細資訊，請參閱[建立命令列的個人存取權杖](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) \(英文\)。

|影響範圍  |描述  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | 存取認可狀態         |
|`repo_deployment`      | 存取部署狀態         |
|`public_repo`     | 存取公用存放庫         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | 寫入存放庫勾點         |
|`read:repo_hook`|讀取存放庫勾點|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Azure Repos 的最低 PAT 權限

下列清單定義 Azure Repos 所需的最低 PAT 權限。 如需在 Azure Repos 中建立 PAT 的詳細資訊，請參閱[使用個人存取權杖驗證存取](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page) \(英文\)。

| 影響範圍  |  存取類型  |
|---------| ----------|
| `Code`      | 讀取  |
| `Project and team` | 讀取 |
| `Identity` | 讀取     |
| `User profile` | 讀取     |
| `Work items` | 讀取    |
| `Service connections` | 讀取、查詢、管理<sup>1</sup>    |

<sup>1</sup> 只有當您已啟用自動同步處理時，才需要 `Service connections` 權限。

## <a name="synchronize-with-source-control"></a>與原始檔控制同步處理

請遵循這些步驟來與原始檔控制進行同步處理。 

1. 請從 [原始檔控制] 頁面上的表格中選取來源。 

2. 按一下 [開始同步處理] 開始同步處理程序。 

3. 按一下 [同步處理作業] 索引標籤，以檢視目前或先前同步處理作業的狀態。 

4. 在 [原始檔控制] 下拉式功能表中，選取原始檔控制機制。

    ![同步處理狀態](./media/source-control-integration/sync-status.png)

5. 按一下作業可讓您檢視作業輸出。 以下範例是原始檔控制同步作業的輸出。

    ```output
    ===================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzEnvironment.

    Getting AzureRunAsConnection.

    Logging in to Azure...

    Source control information for syncing:

    [Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

    Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

    Connecting to VSTS...

    Source Control Sync Summary:

    2 files synced:
     - ExampleRunbook1.ps1
     - ExampleRunbook2.ps1

    ==================================================================

    ```

6. 選取 [原始檔控制同步處理作業摘要] 頁面上的 [所有記錄]，即可取得其他記錄。 這些額外的記錄項目可協助您針對使用原始檔控制時可能發生的問題進行疑難排解。

## <a name="disconnect-source-control"></a>中斷原始檔控制連線

中斷與原始檔控制存放庫的連線：

1. 在自動化帳戶的 [帳戶設定] 下，開啟 [原始檔控制]。

2. 選取要移除的原始檔控制機制。 

3. 在 [原始檔控制摘要] 頁面上，按一下 [刪除]。

## <a name="handle-encoding-issues"></a>處理編碼問題

如果有多位使用者正在使用不同的編輯器編輯原始檔控制存放庫中的 Runbook，則可能會發生編碼問題。 若要深入了解這種情況，請參閱[編碼問題的常見原因](/powershell/scripting/components/vscode/understanding-file-encoding?view=powershell-7#common-causes-of-encoding-issues)。

## <a name="update-the-pat"></a>更新 PAT

目前，您無法使用 Azure 入口網站來更新原始檔控制中的 PAT。 當您的 PAT 過期或撤銷時，您可以使用這些方式的其中一種，以新的存取權杖來更新原始檔控制：

* 使用 [REST API](/rest/api/automation/sourcecontrol/update) \(部分機器翻譯\)。
* 使用 [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) \(英文\) Cmdlet。

## <a name="next-steps"></a>後續步驟

* 若要整合 Azure 自動化中的原始檔控制，請參閱 [Azure 自動化：Azure 自動化中的原始檔控制整合](https://azure.microsoft.com/blog/azure-automation-source-control-13/) \(英文\)。  
* 若要將 Runbook 原始檔控制與 Visual Studio Online 整合，請參閱 [Azure 自動化：使用 Visual Studio Online 整合 Runbook 原始檔控制](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/) \(英文\)。
