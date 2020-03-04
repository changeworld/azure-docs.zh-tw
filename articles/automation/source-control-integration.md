---
title: Azure 自動化中的原始檔控制整合
description: 本文說明在 Azure 自動化中與 GitHub 的原始檔控制整合。
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: eef67ca8111983adb4d9994894ba215240daee6f
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78253739"
---
# <a name="source-control-integration-in-azure-automation"></a>Azure 自動化中的原始檔控制整合

 Azure 自動化中的原始檔控制整合支援來自原始檔控制存放庫的單向同步處理。 原始檔控制可讓您使用 GitHub 或 Azure Repos 原始檔控制存放庫中的腳本，讓自動化帳戶中的 runbook 保持在最新狀態。 這項功能可讓您輕鬆地將已在開發環境中測試的程式碼升級為生產自動化帳戶。
 
 您可以使用原始檔控制整合，輕鬆地與您的小組共同作業、追蹤變更，以及復原至舊版的 runbook。 例如，原始檔控制可讓您將原始檔控制中的不同分支與您的開發、測試和生產環境自動化帳戶同步處理。 

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需混合式 Runbook 背景工作角色上的 Az module 安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="source-control-types"></a>原始檔控制類型

Azure 自動化支援三種類型的原始檔控制：

* GitHub
* Azure Repos （Git）
* Azure Repos （TFVC）

## <a name="prerequisites"></a>必要條件

* 原始檔控制存放庫（GitHub 或 Azure Repos）
* [執行身分帳戶](manage-runas-account.md)
* 自動化帳戶中的[最新 Azure 模組](automation-update-azure-modules.md)，包括**az Account**模組（AzureRM 的 az 模組對應項）

> [!NOTE]
> 原始檔控制同步處理工作會在使用者的自動化帳戶下執行，並以與其他自動化作業相同的費率計費。

## <a name="configuring-source-control"></a>正在設定原始檔控制

本節說明如何設定自動化帳戶的原始檔控制。 您可以使用 Azure 入口網站或 PowerShell。

### <a name="configure-source-control----azure-portal"></a>設定原始檔控制--Azure 入口網站

您可以使用此程式來設定使用 Azure 入口網站的原始檔控制。

1. 在您的自動化帳戶中，選取 [**原始檔控制**]，然後按一下 [ **+ 新增**]。

    ![選取原始檔控制](./media/source-control-integration/select-source-control.png)

2. 選擇 [**原始檔控制類型**]，然後按一下 [**驗證**]。 

3. 瀏覽器視窗隨即開啟，並提示您登入。 遵循提示以完成驗證。

4. 在 [**原始檔控制摘要**] 頁面上，使用欄位填入下列定義的原始檔控制屬性。 完成後，請按一下 [儲存]。 

    |屬性  |描述  |
    |---------|---------|
    |原始檔控制名稱     | 原始檔控制的易記名稱。 此名稱必須只包含字母和數位。        |
    |原始檔控制類型     | 原始檔控制機制的類型。 可用選項包括：</br> GitHub</br>Azure Repos （Git）</br> Azure Repos （TFVC）        |
    |儲存機制     | 存放庫或專案的名稱。 系統會抓取前200存放庫。 若要搜尋存放庫，請在欄位中輸入名稱，然後按一下 [**在 GitHub 上搜尋**]。|
    |分支     | 要從中提取來源檔案的分支。 分支目標不適用於 TFVC 原始檔控制類型。          |
    |資料夾路徑     | 包含要同步處理之 runbook 的資料夾，例如/Runbooks。 只有指定資料夾中的 runbook 會進行同步處理。 不支援遞迴。        |
    |自動同步處理<sup>1</sup>     | 在原始檔控制存放庫中進行認可時，會開啟或關閉自動同步處理的設定。        |
    |發佈 Runbook     | 如果 runbook 在從原始檔控制進行同步處理之後自動發行，則設定為 On，否則為 Off。           |
    |描述     | 指定原始檔控制之其他詳細資料的文字。        |

    <sup>1</sup>若要在設定與 Azure Repos 的原始檔控制整合時啟用自動同步處理，您必須是專案系統管理員。

   ![原始檔控制摘要](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> 您的原始檔控制儲存機制登入可能會與 Azure 入口網站的登入不同。 設定原始檔控制時，請確定您是以正確的原始檔控制存放庫帳戶登入。 如果不確定，請在瀏覽器中開啟新的索引標籤，從 visualstudio.com 或 github.com 登出，然後再次嘗試連接到原始檔控制。

### <a name="configure-source-control----powershell"></a>設定原始檔控制--PowerShell

您也可以使用 PowerShell 來設定 Azure 自動化中的原始檔控制。 若要使用 PowerShell Cmdlet 進行這種操作，您需要個人存取權杖（PAT）。 使用[AzAutomationSourceControl](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) Cmdlet 來建立原始檔控制連接。 此 Cmdlet 需要安全的 PAT 字串。 若要瞭解如何建立安全字串，請參閱[convertto-html-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6)。

下列小節說明如何建立適用于 GitHub、Azure Repos （Git）和 Azure Repos （TFVC）之原始檔控制連接的 PowerShell。

#### <a name="create-source-control-connection-for-github"></a>建立適用于 GitHub 的原始檔控制連接

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>建立 Azure Repos 的原始檔控制連接（Git）

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>建立 Azure Repos 的原始檔控制連接（TFVC）

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>個人存取權杖（PAT）許可權

原始檔控制需要 Pat 的一些最低許可權。 下列小節包含 GitHub 和 Azure Repos 所需的最小許可權。

##### <a name="minimum-pat-permissions-for-github"></a>GitHub 的最低 PAT 許可權

下表定義 GitHub 所需的最低 PAT 許可權。 如需在 GitHub 中建立 PAT 的詳細資訊，請參閱[建立命令列的個人存取權杖](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)。

|範圍  |描述  |
|---------|---------|
|**存放庫**     |         |
|repo:status     | 存取認可狀態         |
|repo_deployment      | 存取部署狀態         |
|public_repo     | 存取公用存放庫         |
|**admin:repo_hook**     |         |
|write:repo_hook     | 寫入存放庫勾點         |
|read:repo_hook|讀取存放庫勾點|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Azure Repos 的最小 PAT 許可權

下列清單定義 Azure Repos 所需的最小 PAT 許可權。 如需在 Azure Repos 中建立 PAT 的詳細資訊，請參閱[使用個人存取權杖來驗證存取權](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)。

| 範圍  |  存取類型  |
|---------| ----------|
| 程式碼      | 讀取  |
| 專案與小組 | 讀取 |
| 相同比較 | 讀取     |
| 使用者設定檔 | 讀取     |
| 工作項目 | 讀取    |
| 服務連接 | 讀取、查詢、管理<sup>1</sup>    |

<sup>1</sup>只有在您已啟用自動同步時，才需要服務連線許可權。

## <a name="synchronizing"></a>正在同步處理

執行下列動作，以與原始檔控制進行同步處理。 

1. 從 [**原始檔控制**] 頁面上的資料表中選取來源。 

2. 按一下 [開始同步處理] 開始同步處理程序。 

3. 按一下 [**同步處理工作**] 索引標籤，以查看目前同步處理作業或先前的狀態。 

4. 在 [**原始檔控制**] 下拉式功能表中，選取 [原始檔控制] 機制。

    ![同步處理狀態](./media/source-control-integration/sync-status.png)

5. 按一下作業可讓您檢視作業輸出。 以下範例是原始檔控制同步作業的輸出。

    ```output
    ============================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzureRmEnvironment.

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

     =========================================================================

    ```

6. 選取 [**原始檔控制同步處理作業摘要**] 頁面上的 [**所有記錄**]，即可取得其他記錄。 這些額外的記錄專案可協助您針對使用原始檔控制時可能發生的問題進行疑難排解。

## <a name="disconnecting-source-control"></a>中斷原始檔控制的連線

若要中斷與原始檔控制存放庫的連線：

1. 在您的自動化帳戶中，于 [**帳戶設定**] 下開啟**原始檔控制**。

2. 選取要移除的原始檔控制機制。 

3. 在 [原始檔控制摘要] 頁面上，按一下 [刪除]。

## <a name="handling-encoding-issues"></a>處理編碼問題

如果有多位使用者正在使用不同的編輯器編輯原始檔控制存放庫中的 runbook，則可能會發生編碼問題。 若要深入瞭解這種情況，請參閱[編碼問題的常見原因](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="updating-the-pat"></a>更新 PAT

目前沒有任何方法可以使用 Azure 入口網站來更新原始檔控制中的 PAT。 在您的 PAT 過期或撤銷之後，您可以使用下列其中一種方式，以新的存取權杖來更新原始檔控制：

* 使用[REST API](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update)。
* 使用[AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) Cmdlet。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 runbook 類型以及其優點和限制，請參閱[Azure 自動化 runbook 類型](automation-runbook-types.md)。
