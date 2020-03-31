---
title: Azure 自動化中的原始檔控制整合
description: 本文說明在 Azure 自動化中與 GitHub 的原始檔控制整合。
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 166902978d1641458f18aeee6269c8d819e85233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132918"
---
# <a name="source-control-integration-in-azure-automation"></a>Azure 自動化中的原始檔控制整合

 Azure 自動化中的原始程式碼管理集成支援源控制存儲庫中的單方向同步。 原始程式碼管理允許您使用 GitHub 或 Azure 存儲庫原始程式碼管理存儲庫中的腳本使自動化帳戶中的 Runbook 保持最新。 此功能使開發環境中測試過的代碼輕鬆推廣到生產自動化帳戶。
 
 原始程式碼管理集成可讓您輕鬆地與團隊協作、跟蹤更改並回滾到 Runbook 的早期版本。 例如，原始程式碼管理允許您將原始程式碼管理中的不同分支與開發、測試和生產自動化帳戶同步。 

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶，可以使用["如何更新 Azure 自動化 中的 Azure PowerShell"模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="source-control-types"></a>原始程式碼管理類型

Azure 自動化支援三種類型的原始程式碼管理：

* GitHub
* Azure 存儲庫（Git）
* Azure 存儲庫 （TFVC）

## <a name="prerequisites"></a>Prerequisites

* 原始程式碼管理存儲庫（GitHub 或 Azure 存儲庫）
* [以帳戶身份運行](manage-runas-account.md)
* 自動化帳戶中[的最新 Azure 模組](automation-update-azure-modules.md)，包括`Az.Accounts`模組（相當於`AzureRM.Profile`的 Az 模組）

> [!NOTE]
> 原始程式碼管理同步作業在使用者的自動化帳戶下運行，並且以與其他自動化作業相同的費率計費。

## <a name="configuring-source-control"></a>配置原始程式碼管理

本節介紹如何為自動化帳戶配置原始程式碼管理。 可以使用 Azure 門戶或 PowerShell。

### <a name="configure-source-control-in-azure-portal"></a>在 Azure 門戶中配置原始程式碼管理

使用此過程使用 Azure 門戶配置原始程式碼管理。

1. 在自動化帳戶中，選擇 **"源控制"，** 然後按一下"**添加**"。

    ![選取原始檔控制](./media/source-control-integration/select-source-control.png)

2. 選擇**原始程式碼管理類型**，然後按一下 **"身份驗證**"。 

3. 將打開瀏覽器視窗並提示您登錄。 按照提示完成身份驗證。

4. 在"源控制摘要"頁上，使用欄位填寫下面定義的原始程式碼管理屬性。 完成後，請按一下 [儲存]****。 

    |屬性  |描述  |
    |---------|---------|
    |原始檔控制名稱     | 源控制項的易記名稱。 此名稱必須僅包含字母和數位。        |
    |原始檔控制類型     | 原始程式碼管理機制的類型。 可用選項包括：</br> • GitHub</br>• Azure 存儲庫（Git）</br> • Azure 存儲庫 （TFVC）        |
    |Repository     | 存儲庫或專案的名稱。 檢索前 200 個存儲庫。 要搜索存儲庫，請在欄位中鍵入名稱，然後按一下**GitHub 上的搜索**。|
    |分支     | 分支，從中拉出原始檔案。 分支定位不適用於 TFVC 原始程式碼管理類型。          |
    |資料夾路徑     | 包含要同步的 Runbook 的資料夾，例如 **/Runbook**。 僅同步指定資料夾中的 Runbook。 不支援遞迴。        |
    |自動同步<sup>1</sup>     | 設置在原始程式碼管理存儲庫中進行提交時打開或關閉自動同步。        |
    |發佈 Runbook     | 如果從原始程式碼管理自動發佈運行簿後自動發佈，則設置 On，否則將關閉。           |
    |描述     | 指定有關原始程式碼管理的其他詳細資訊的文本。        |

    <sup>1</sup>要在配置與 Azure 存儲庫的原始程式碼管理集成時啟用自動同步，您必須是專案管理員。

   ![原始檔控制摘要](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> 原始程式碼管理存儲庫的登錄名可能與 Azure 門戶的登錄不同。 在配置原始程式碼管理時，請確保使用原始程式碼管理存儲庫的正確帳戶登錄。 如果有疑問，請打開瀏覽器中的新選項卡，從**dev.azure.com、visualstudio.com**或**github.com**登出，**visualstudio.com**然後嘗試重新連接到原始程式碼管理。

### <a name="configure-source-control-in-powershell"></a>在 PowerShell 中配置原始程式碼管理

您還可以使用 PowerShell 在 Azure 自動化中配置原始程式碼管理。 要將 PowerShell Cmdlet 用於此操作，您需要個人訪問權杖 （PAT）。 使用[新阿茲自動化源控制](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
)Cmdlet 創建原始程式碼管理連接。 此 Cmdlet 需要 PAT 的安全字串。 要瞭解如何創建安全字串，請參閱[轉換到安全字串](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6)。

以下小節說明 PowerShell 為 GitHub、Azure 存儲庫 （Git） 和 Azure 存儲庫 （TFVC） 創建了原始程式碼管理連接。 

#### <a name="create-source-control-connection-for-github"></a>為 GitHub 創建原始程式碼管理連接

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>為 Azure 存儲庫 （Git） 創建原始程式碼管理連接

> [!NOTE]
> Azure Repos （Git） 使用訪問**dev.azure.com**的 URL，而不是以早期格式使用**visualstudio.com**。 較舊的 URL`https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>`格式已棄用，但仍受支援。 首選新格式。


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>為 Azure 存儲庫 （TFVC） 創建原始程式碼管理連接

> [!NOTE]
> Azure 存儲庫 （TFVC） 使用訪問**dev.azure.com**的 URL，而不是以較早格式使用**visualstudio.com**。 較舊的 URL`https://<accountname>.visualstudio.com/<projectname>/_versionControl`格式已棄用，但仍受支援。 首選新格式。

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>個人訪問權杖 （PAT） 許可權

原始程式碼管理需要一些對 PAC 的最小許可權。 以下小節包含 GitHub 和 Azure 存儲庫所需的最小許可權。

##### <a name="minimum-pat-permissions-for-github"></a>GitHub 的最低 PAT 許可權

下表定義了 GitHub 所需的最小 PAT 許可權。 有關在 GitHub 中創建 PAT 的詳細資訊，請參閱[為命令列創建個人訪問權杖](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)。

|影響範圍  |描述  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | 存取認可狀態         |
|`repo_deployment`      | 存取部署狀態         |
|`public_repo`     | 存取公用存放庫         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | 寫入存放庫勾點         |
|`read:repo_hook`|讀取存放庫勾點|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Azure 存儲庫的最低 PAT 許可權

下面的清單定義 Azure 存儲庫所需的最小 PAT 許可權。 有關在 Azure 存儲庫中創建 PAT 的詳細資訊，請參閱[使用個人訪問權杖進行身份驗證存取權限](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)。

| 影響範圍  |  存取類型  |
|---------| ----------|
| `Code`      | 讀取  |
| `Project and team` | 讀取 |
| `Identity` | 讀取     |
| `User profile` | 讀取     |
| `Work items` | 讀取    |
| `Service connections` | 讀取、查詢、管理<sup>1</sup>    |

<sup>1</sup> `Service connections`僅當您啟用了自動同步時，才需要該許可權。

## <a name="synchronizing"></a>正在同步處理

按照以下步驟與原始程式碼管理同步。 

1. 從"源"控制項頁上的表中選擇源。 

2. 按一下 [開始同步處理]**** 開始同步處理程序。 

3. 按一下"**同步作業"** 選項卡查看當前同步作業或以前的同步作業的狀態。 

4. 在 **"原始程式碼管理**"下拉式功能表上，選擇原始程式碼管理機制。

    ![同步處理狀態](./media/source-control-integration/sync-status.png)

5. 按一下作業可讓您檢視作業輸出。 以下範例是原始檔控制同步作業的輸出。

    ```output
    ===================================================================

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

    ==================================================================

    ```

6. 通過選擇"源控制同步作業摘要"頁上**的所有日誌**，可以獲取其他日誌記錄。 這些附加日誌條目可以説明您解決使用原始程式碼管理時可能出現的問題。

## <a name="disconnecting-source-control"></a>中斷原始檔控制的連線

要斷開與原始程式碼管理存儲庫的連接：：

1. 自動化**帳戶中的帳戶設置**下的開源**控制**。

2. 選擇要刪除的原始程式碼管理機制。 

3. 在 [原始檔控制摘要] 頁面上，按一下 [刪除]****。

## <a name="handling-encoding-issues"></a>處理編碼問題

如果多人使用不同的編輯器在原始程式碼管理存儲庫中編輯 Runbook，則可能發生編碼問題。 要瞭解有關此情況的更多內容，請參閱[編碼問題的常見原因](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)。

## <a name="updating-the-pat"></a>更新 PAT

目前，不能使用 Azure 門戶在原始程式碼管理中更新 PAT。 當 PAT 過期或吊銷時，可以使用以下方式之一使用新的訪問權杖更新原始程式碼：

* 使用[REST API](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update)。
* 使用[更新-阿茲自動化源控制](/powershell/module/az.automation/update-azautomationsourcecontrol)Cmdlet。

## <a name="next-steps"></a>後續步驟

要瞭解有關 Runbook 類型及其優點和限制的更多資訊，請參閱[Azure 自動化 Runbook 類型](automation-runbook-types.md)。
