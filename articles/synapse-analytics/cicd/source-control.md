---
title: Synapse Studio 中的原始檔控制
description: 瞭解如何在 Azure Synapse Studio 中設定原始檔控制
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 111b2b5b660133c20afdb3c56e1c3e3ed607398c
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296988"
---
# <a name="source-control-in-azure-synapse-studio"></a>Azure Synapse Studio 中的原始檔控制

根據預設，Azure Synapse Studio 會直接針對 Synapse 服務進行撰寫。 不過，此體驗具有下列限制：

- Synapse studio 不包含用來儲存變更的暫存儲存體。 儲存和共用變更的唯一方式是透過 **發佈** ，而且所有變更都會直接發佈至 Synapse 服務。

- Synapse Studio 未針對協同作業和版本控制優化。

為了提供原始檔控制功能，Synapse Studio 可讓您將工作區與 Git 存放庫、Azure DevOps 或 GitHub 產生關聯。 本文將概述如何在已啟用 git 存放庫的 Synapse 工作區中設定和運作。 此外，我們也強調了一些最佳作法和疑難排解指南。

> [!NOTE]
> Azure Government 雲端無法使用 Azure Synapse Studio git 整合。

## <a name="configure-git-repository-in-synapse-studio"></a>在 Synapse Studio 中設定 Git 存放庫 

啟動 Synapse Studio 之後，您可以在工作區中設定 git 存放庫。 Synapse Studio 工作區一次只能與一個 git 存放庫相關聯。 

### <a name="configuration-method-1-global-bar"></a>設定方法1： global bar

在 Synapse Studio 全域列中，選取 [ **Synapse Live** ] 下拉式功能表，然後選取 [設定程式 **代碼存放庫**]。

![從撰寫中設定程式碼存放庫設定](media/configure-repo-1.png)

### <a name="configuration-method-2-manage-hub"></a>設定方法2：管理中樞

移至 Synapse Studio 的管理中樞。 在 [**原始檔控制**] 區段中，選取 [ **Git** 設定]。 如果您沒有任何已連線的儲存機制，請按一下 [ **設定**]。

![從管理中樞設定程式碼存放庫設定](media/configure-repo-2.png)

> [!NOTE]
> 授與工作區參與者、擁有者或更高層級角色的使用者，可以在 Azure Synapse studio 中設定、編輯設定和中斷連接 git 儲存機制 

您可以在工作區中連接 Azure DevOps 或 GitHub git 儲存機制。

## <a name="connect-with-azure-devops-git"></a>使用 Azure DevOps Git 連接 

您可以將 Synapse 工作區與 Azure DevOps 儲存機制建立關聯，以進行原始檔控制、共同作業、版本設定等。 如果您沒有 Azure DevOps 存放庫，請遵循下列 [指示](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) 來建立您的存放庫資源。

### <a name="azure-devops-git-repository-settings"></a>Azure DevOps Git 存放庫設定

連線至您的 git 存放庫時，請先選取您的存放庫類型作為 Azure DevOps git，然後從下拉式清單中選取一個 Azure AD 租使用者，然後按一下 [ **繼續**]。

![設定程式碼存放庫設定](media/connect-with-azuredevops-repo-selected.png)

[設定] 窗格會顯示下列 Azure DevOps git 設定：

| 設定 | 描述 | 值 |
|:--- |:--- |:--- |
| **存放庫類型** | Azure Repos 程式碼存放庫的類型。<br/> | Azure DevOps Git 或 GitHub |
| **Azure Active Directory** | 您的 Azure AD 租用戶名稱。 | `<your tenant name>` |
| **Azure DevOps 帳戶** | 您的 Azure Repos 組織名稱。 您可以在 `https://{organization name}.visualstudio.com` 找到您的 Azure Repos 組織名稱。 您可以[登入您的 Azure Repos 組織](https://www.visualstudio.com/team-services/git/)，以存取 Visual Studio 設定檔和查看您的存放庫與專案。 | `<your organization name>` |
| **ProjectName** | 您的 Azure Repos 專案名稱。 您可以在 `https://{organization name}.visualstudio.com/{project name}` 找到您的 Azure Repos 專案名稱。 | `<your Azure Repos project name>` |
| **RepositoryName** | 您的 Azure Repos 程式碼存放庫名稱。 Azure Repos 專案包含 Git 存放庫，可隨著您的專案成長管理原始程式碼。 您可以建立新的存放庫，或使用專案中既有的存放庫。 | `<your Azure Repos code repository name>` |
| **共同作業分支** | 用於進行發佈的 Azure Repos 共同作業分支。 根據預設，其為 `master`。 如果您想要從其他分支發行資源，請變更此設定。 您可以選取現有的分支或建立新的 | `<your collaboration branch name>` |
| **根資料夾** | 在您 Azure Repos 共同作業分支中的根資料夾。 | `<your root folder name>` |
| **將現有資源匯入存放庫** | 指定是否要將現有的資源從 Synapse Studio 匯入 Azure Repos 的 Git 存放庫。 核取 [將您的工作區資源匯入到相關聯的 Git 存放庫]) 中，將工作區資源匯入 (的方塊 此動作會個別匯出每個資源。 如果未核取此方塊，則不會匯入現有的資源。 | 已核取 (預設)  |
| **將資源匯入此分支** | 選取資源 (sql 腳本、筆記本、spark 作業定義、資料集、資料流程等 ) 匯入的分支。 

您也可以使用存放庫連結，快速指向您想要連接的 git 存放庫。 

### <a name="use-a-different-azure-active-directory-tenant"></a>使用不同的 Azure Active Directory 租用戶

Azure Repos Git 存放庫可以位於不同的 Azure Active Directory 租用戶中。 若要指定不同的 Azure AD 租用戶，您必須擁有所用 Azure 訂用帳戶的系統管理員權限。 如需詳細資訊，請參閱 [變更訂用帳戶管理員](https://docs.microsoft.com/azure/cost-management-billing/manage/add-change-subscription-administrator#assign-a-subscription-administrator)

> [!IMPORTANT]
> 若要連接到其他 Azure Active Directory，登入的使用者必須是該 Active Directory 的一部分。 

### <a name="use-your-personal-microsoft-account"></a>使用您的個人 Microsoft 帳戶

若要使用個人 Microsoft 帳戶進行 Git 整合，您可以將個人的 Azure Repos 連結至貴組織的 Active Directory。

1. 以來賓身分將您的個人 Microsoft 帳戶新增到貴組織的 Active Directory。 如需詳細資訊，請參閱[在 Azure 入口網站中新增 Azure Active Directory B2B 共同作業使用者](https://docs.microsoft.com/azure/active-directory/external-identities/add-users-administrator)。

2. 使用您的個人 Microsoft 帳戶登入 Azure 入口網站。 然後切換到貴組織的 Active Directory。

3. 移至 [Azure DevOps] 區段，您現在會在其中看到您的個人存放庫。 選取存放庫並與 Active Directory 連線。

在這些設定步驟之後，當您在 Synapse Studio 中設定 Git 整合時，即可使用您的個人存放庫。

如需將 Azure Repos 連接到您組織 Active Directory 的詳細資訊，請參閱 [將您的組織連線至 Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)。

## <a name="connect-with-github"></a>使用 GitHub 連接 

 您可以將工作區與 GitHub 存放庫產生關聯，以進行原始檔控制、共同作業、版本控制。 如果您沒有 GitHub 帳戶或存放庫，請遵循[這些指示](https://github.com/join)來建立您的資源。

GitHub 與 Synapse Studio 整合支援公用 GitHub (，也就是 [https://github.com](https://github.com)) 和 GitHub Enterprise。 您可以使用公用和私人 GitHub 存放庫，只要您擁有 GitHub 中存放庫的讀取和寫入權限即可。

### <a name="github-settings"></a>GitHub 設定

連接到您的 git 存放庫時，請先選取您的存放庫類型作為 GitHub，然後提供您的 GitHub 帳戶或 GitHub Enterprise 伺服器 URL （如果您使用 GitHub Enterprise Server），然後按一下 [ **繼續**]。

![GitHub 存放庫設定](media/connect-with-github-repo-1.png)

此設定窗格會顯示下列 GitHub 存放庫設定：

| **設定** | **說明**  | **ReplTest1**  |
|:--- |:--- |:--- |
| **存放庫類型** | Azure Repos 程式碼存放庫的類型。 | GitHub |
| **使用 GitHub Enterprise** | 選取 GitHub Enterprise 的核取方塊 | 未選取 (預設值) |
| **GitHub Enterprise URL** | GitHub Enterprise 根 URL (若為本機 GitHub Enterprise 伺服器，則必須是 HTTPS)。 例如： `https://github.mydomain.com` 。 只有在已選取 [使用 GitHub Enterprise] 時才必要 | `<your GitHub enterprise url>` |                                                           
| **GitHub 帳戶** | 您的 GitHub 帳戶名稱。 您可以從 https:\//github.com/{帳戶名稱}/{存放庫名稱} 找到此名稱。 瀏覽到此頁面時，系統會提示您輸入 GitHub 帳戶的 GitHub OAuth 認證。 | `<your GitHub account name>` |
| **存放庫名稱**  | 您的 GitHub 程式碼存放庫名稱。 GitHub 帳戶包含 Git 存放庫，可用來管理原始程式碼。 您可以建立新的存放庫，或使用帳戶中既有的存放庫。 | `<your repository name>` |
| **共同作業分支** | 用於發行的 GitHub 共同作業分支。 根據預設，其為主要分支。 如果您想要從其他分支發行資源，請變更此設定。 | `<your collaboration branch>` |
| **根資料夾** | 在您 GitHub 共同作業分支中的根資料夾。 |`<your root folder name>` |
| **將現有資源匯入存放庫** | 指定是否要將現有的資源從 Synapse Studio 匯入至 Git 存放庫。 核取 [將您的工作區資源匯入到相關聯的 Git 存放庫]) 中，將工作區資源匯入 (的方塊 此動作會個別匯出每個資源。 如果未核取此方塊，則不會匯入現有的資源。 | 已選取 (預設值) |
| **將資源匯入此分支** | 選取要匯入資源 (sql 腳本、筆記本、spark 作業定義、資料集、資料流程等等 ) 的分支。 

### <a name="github-organizations"></a>GitHub 組織

若要連接到 GitHub 組織，組織必須將許可權授與 Synapse Studio。 具有組織系統管理員許可權的使用者必須執行下列步驟。

#### <a name="connecting-to-github-for-the-first-time"></a>第一次連接到 GitHub

如果您是第一次從 Synapse Studio 連線到 GitHub，請遵循下列步驟來連接到 GitHub 組織。

1. 在 [Git 設定] 窗格的 [ *GitHub 帳戶* ] 欄位中，輸入組織名稱。 系統會提示您登入 GitHub。 

1. 使用您的使用者認證登入。

1. 系統會要求您將 Synapse 授權為名為 *Azure Synapse* 的應用程式。 在此畫面上，您會看到可授與 Synapse 存取組織許可權的選項。 如果您沒有看到授與許可權的選項，請要求系統管理員透過 GitHub 手動授與許可權。

遵循這些步驟之後，您的工作區將能夠連線至組織內的公用和私人存放庫。 如果您無法連線，請嘗試清除瀏覽器快取，然後再試一次。

## <a name="version-control"></a>版本控制

版本控制系統 (也稱為 _原始檔控制_) 可讓開發人員在程式碼上共同作業並追蹤變更。原始檔控制是多開發人員專案的基本工具。

### <a name="creating-feature-branches"></a>建立功能分支

與 Synapse Studio 相關聯的每個 Git 存放庫都有共同作業分支。  (`main` 或 `master` 是預設的共同作業分支) 。 使用者也可以按一下 [分支] 下拉式清單中的 [+ 新增分支] 來建立功能分支。 [新增分支] 窗格出現之後，請輸入功能分支的名稱。

![建立新的分支](media/create-new-branch.png)

當您準備好要將功能分支的變更合併到共同作業分支時，請按一下 [分支] 下拉式清單，然後選取 [建立提取要求]。 此動作會帶您前往 Git 提供者，您可以在其中引發提取要求、執行程式碼審核，以及將變更合併到您的共同作業分支。 您只允許從您的共同作業分支發佈至 Synapse 服務。 

![建立新的提取要求](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>設定發佈設定

根據預設，Synapse Studio 會產生工作區範本，並將其儲存至名為的分支 `workspace_publish` 。 若要設定自訂發佈分支，請將 `publish_config.json` 檔案新增至共同作業分支中的根資料夾。 發佈時，Synapse Studio 會讀取此檔案、尋找該欄位 `publishBranch` ，並將工作區範本檔案儲存至指定的位置。 如果分支不存在，Synapse Studio 會自動加以建立。 以下是此檔案的樣貌範例：

```json
{
    "publishBranch": "workspace_publish"
}
```

Azure Synapse Studio 一次只能有一個發佈分支。 當您指定新的發行分支時，不會刪除先前的發行分支。 如果您想要移除先前的發佈分支，請手動將其刪除。

### <a name="publish-code-changes"></a>發佈程式碼變更

將變更合併到共同作業分支之後，請按一下 [ **發佈** ]，將共同作業分支中的程式碼變更手動發佈至 Synapse 服務。

![發佈變更](media/gitmode-publish.png)

側邊窗格隨即開啟，以供您確認發佈分支和暫止的變更是否正確。 確認變更之後，請按一下 [確定] 來確認發佈。

![確認正確的發佈分支](media/publish-change.png)

> [!IMPORTANT]
> 共同作業分支不代表在服務中部署的內容。 共同作業分支中的變更 *必須* 以手動方式發佈服務。

## <a name="switch-to-a-different-git-repository"></a>切換至不同的 Git 存放庫

若要切換至不同的 Git 存放庫，請移至 [ **原始檔控制**] 下 [管理中樞] 中的 [Git 設定] 頁面。 選取 **[中斷連線]**。 

![Git 圖示](media/remove-repository.png)

輸入您的工作區名稱，然後按一下 **[中斷連線]** ，移除與您工作區相關聯的 Git 存放庫。

移除與目前存放庫的關聯之後，您可以將 Git 設定設定為使用不同的存放庫，然後將現有的資源匯入至新的存放庫。

> [!IMPORTANT]
> 從工作區移除 Git 設定，並不會從存放庫中刪除任何資源。 Synapse 工作區會包含所有已發佈的資源。 您可以繼續對服務直接編輯工作區。

## <a name="best-practices-for-git-integration"></a>Git 整合的最佳做法

-   **權限**。 當您的 git 存放庫已連線到您的工作區之後，任何可以存取您的 git 存放庫（具有您工作區中任何角色）的人，都能夠以 git 模式更新成品，例如 sql 腳本、筆記本、spark 作業定義、資料集、資料流程和管線。 一般來說，您不希望每個小組成員都具有更新工作區的許可權。 只授與 Synapse 工作區成品作者的 git 存放庫許可權。 
-   共同 **作業。** 建議您不要允許直接簽入至共同作業分支。 此限制可協助避免錯誤 (bug)，因為每個簽入都會經歷[建立功能分支](source-control.md#creating-feature-branches)所述的提取要求檢閱程序。
-   **Synapse 即時模式**。 在 git 模式下發布之後，所有變更都會反映在 Synapse live 模式中。 在 Synapse live 模式中，發佈已停用。 而且，如果您已授與正確的許可權，您可以在即時模式中執行成品。 
-   **編輯 Studio 中的構件**。 Synapse studio 是唯一可讓您啟用工作區原始檔控制，以及自動將變更同步至 git 的位置。 任何透過 SDK、PowerShell 進行的變更都不會同步至 git。 建議您在啟用 git 時，一律在 Studio 中編輯成品。

## <a name="troubleshooting-git-integration"></a>針對 git 整合進行疑難排解

### <a name="access-to-git-mode"></a>存取 git 模式 

如果您已授與與您的工作區連結的 GitHub git 儲存機制許可權，但無法存取 Git 模式： 

1. 清除您的快取並重新整理頁面。 

1. 登入您的 GitHub 帳戶。

### <a name="stale-publish-branch"></a>過時的發佈分支

如果發佈分支與共同作業分支不同步，而且包含過時的資源（儘管最近發行），請嘗試執行下列步驟：

1. 移除目前的 Git 存放庫

1. 使用相同的設定重新設定 Git，但請確定已核取 [將 **現有資源匯入至儲存** 機制]，然後選擇相同的分支。  

1. 建立提取要求以將變更合併到共同作業分支 

## <a name="unsupported-features"></a>不支援的功能

- Synapse Studio 不允許對認可或選擇性的資源發佈進行揀選選擇。 
- Synapse Studio 不支援自訂認可訊息。
- 根據設計，Studio 中的刪除動作將直接認可至 git

## <a name="next-steps"></a>下一步

* 若要執行持續整合和部署，請參閱 [ (CI/CD) 的持續整合與傳遞 ](continuous-integration-deployment.md)。
