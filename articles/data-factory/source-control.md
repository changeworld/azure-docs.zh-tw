---
title: 原始檔控制
description: 了解如何在 Azure Data Factory 中設定原始檔控制
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: dcstwh
ms.author: weetok
manager: anandsub
ms.reviewer: ''
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/02/2020
ms.openlocfilehash: 27f14834e45dd32bdf3a26d22d38ab250aa1bcbe
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127731"
---
# <a name="source-control-in-azure-data-factory"></a>Azure Data Factory 中的原始檔控制
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

根據預設，Azure Data Factory 使用者介面體驗 (UX) 會直接針對資料處理站服務來撰寫。 這項體驗有下列限制：

- Data Factory 服務未包含存放庫，此存放庫會用於儲存變更的 JSON 實體。 若要儲存變更，唯一的方式是透過 [全部發佈] 按鈕，將所有變更直接發佈給資料處理站服務。
- Data Factory 服務並未針對共同作業和版本控制進行最佳化。
- 不包含部署 Data Factory 本身所需的 Azure Resource Manager 範本。

為了提供更好的撰寫體驗，Azure Data Factory 可讓您使用 Azure Repos 或 GitHub 來設定 Git 存放庫。 Git 是一種版本控制系統，可讓您更輕鬆地追蹤變更和共同作業。 本文將概述如何在 git 存放庫中設定及使用，以及反白顯示最佳作法和疑難排解指南。

> [!NOTE]
> Azure Data Factory 的 git 整合僅適用于 Azure Government 雲端中的 GitHub Enterprise。

若要深入瞭解 Azure Data Factory 如何與 Git 整合，請參閱下列15分鐘的教學課程影片：

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4GNKv]

## <a name="advantages-of-git-integration"></a>Git 整合的優點

下面列出 Git 整合能為撰寫體驗提供的一些優點：

-   **原始檔控制：** 當您的資料處理站工作負載變得更重要時，您可以將您的處理站與 Git 整合，以利用數個原始程式碼控制優點，例如：
    -   追蹤/稽核變更的能力。
    -   還原造成錯誤 (Bug) 之變更的能力。
-   **部分儲存：** 針對資料處理站服務來撰寫時，您無法將變更儲存為草稿，而且所有發佈都必須通過資料處理站驗證。 無論您的管線是否未完成，或是您不想要在電腦損毀時遺失變更，git 整合可讓資料處理站資源的累加式變更，不論它們位於何種狀態。 設定 Git 存放庫可讓您儲存變更，從而讓您只在將變更測試到滿意時才發佈。
-   **共同作業與控制：** 若您有多個參與相同處理站的小組成員，您可以透過程式碼檢閱程序讓您的小組成員彼此共同作業。 您也可以設定處理站，而不讓每個參與者具有相同的權限。 某些小組成員只能透過 Git 進行變更，而且小組中只有特定人員可以將變更發佈到處理站。
-   **更好的 CI/CD：** 如果您要使用 [持續傳遞程序](continuous-integration-deployment.md)來部署到多個環境，Git 整合可以讓某些動作變得更容易。 其中一些動作包括：
    -   將發行管線設定為會在「開發」處理站有所變更時就自動觸發。
    -   自訂處理站中的屬性，這些屬性會以 Resource Manager 範本中參數的形式來提供。 只保留必要屬性集做為參數並將其他項目以硬式編碼方式撰寫非常實用。
-   **更好的效能：** 具有 Git 整合的一般處理站，其載入速度會比針對資料處理站服務進行撰寫的處理站快 10 倍。 會有此效能提升是因為資源會透過 Git 來下載。

> [!NOTE]
> 設定了 Git 存放庫時，Azure Data Factory UX 會停用直接使用 Data Factory 服務來撰寫。 透過 PowerShell 或 SDK 所做的變更會直接發佈至 Data Factory 服務，且不會在 Git 中輸入。

## <a name="connect-to-a-git-repository"></a>連接到 Git 存放庫

有四種不同的方式可將 Git 存放庫連線至您的 data factory，Azure Repos 和 GitHub。 連線到 Git 存放庫之後，您可以在 [**原始檔控制**] 區段中的 [ **Git** 設定] 下，于 [管理中樞](author-management-hub.md)內查看及管理您的設定。

### <a name="configuration-method-1-home-page"></a>設定方法1：首頁

在 Azure Data Factory 首頁] 中，選取 [ **設定程式碼存放庫**]。

![從首頁設定程式碼存放庫](media/author-visually/configure-repo.png)

### <a name="configuration-method-2-authoring-canvas"></a>設定方法2：撰寫畫布

在 Azure Data Factory UX 的撰寫畫布中選取 [Data Factory] 下拉式功能表，然後選取 [設定程式碼存放庫]。

![從撰寫中設定程式碼存放庫設定](media/author-visually/configure-repo-2.png)

### <a name="configuration-method-3-management-hub"></a>設定方法3：管理中樞

移至 ADF UX 中的管理中樞。 在 [**原始檔控制**] 區段中，選取 [ **Git** 設定]。 如果您沒有任何已連線的儲存機制，請按一下 [ **設定程式碼存放庫**]。

![從管理中樞設定程式碼存放庫設定](media/author-visually/configure-repo-3.png)

### <a name="configuration-method-4-during-factory-creation"></a>設定方法4：建立工廠期間

在 Azure 入口網站中建立新的 data factory 時，您可以在 [ **git** 設定] 索引標籤中設定 git 存放庫資訊。

> [!NOTE]
> 在 Azure 入口網站中設定 git 時，必須以手動方式輸入 [專案名稱] 和 [存放庫名稱] 等設定，而不是下拉式清單的一部分。

![從 Azure 入口網站設定程式碼存放庫設定](media/author-visually/configure-repo-4.png)

## <a name="author-with-azure-repos-git-integration"></a>使用 Azure Repos Git 整合來進行撰寫

使用 Azure Repos Git 整合來進行視覺化撰寫時，可針對您資料處理站管線上的工作支援原始檔控制和共同作業。 您可以將資料處理站與 Azure Repos Git 組織存放庫建立關聯，以進行原始檔控制、共同作業及版本設定等。 一個 Azure Repos Git 組織可以有多個存放庫，但一個 Azure Repos Git 存放庫只能與一個資料處理站建立關聯。 如果您沒有 Azure Repos 組織或存放庫，請依照[這些指示](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)來建立您的資源。

> [!NOTE]
> 您可以將指令碼和資料檔案儲存在 Azure Repos Git 存放庫中。 不過，您必須手動將檔案上載至 Azure 儲存體。 Data factory 管線不會自動將 Azure Repos Git 存放庫中儲存的腳本或資料檔案上傳至 Azure 儲存體。

### <a name="azure-repos-settings"></a>Azure Repos 設定

![設定程式碼存放庫設定](media/author-visually/repo-settings.png)

此設定窗格會顯示下列 Azure Repos 程式碼存放庫設定：

| 設定 | 描述 | 值 |
|:--- |:--- |:--- |
| **存放庫類型** | Azure Repos 程式碼存放庫的類型。<br/> | Azure DevOps Git 或 GitHub |
| **Azure Active Directory** | 您的 Azure AD 租用戶名稱。 | `<your tenant name>` |
| **Azure Repos 組織** | 您的 Azure Repos 組織名稱。 您可以在 `https://{organization name}.visualstudio.com` 找到您的 Azure Repos 組織名稱。 您可以[登入您的 Azure Repos 組織](https://www.visualstudio.com/team-services/git/)，以存取 Visual Studio 設定檔和查看您的存放庫與專案。 | `<your organization name>` |
| **ProjectName** | 您的 Azure Repos 專案名稱。 您可以在 `https://{organization name}.visualstudio.com/{project name}` 找到您的 Azure Repos 專案名稱。 | `<your Azure Repos project name>` |
| **RepositoryName** | 您的 Azure Repos 程式碼存放庫名稱。 Azure Repos 專案包含 Git 存放庫，可隨著您的專案成長管理原始程式碼。 您可以建立新的存放庫，或使用專案中既有的存放庫。 | `<your Azure Repos code repository name>` |
| **共同作業分支** | 用於進行發佈的 Azure Repos 共同作業分支。 依預設，它是 `main` 。 如果您想要從其他分支發行資源，請變更此設定。 | `<your collaboration branch name>` |
| **根資料夾** | 在您 Azure Repos 共同作業分支中的根資料夾。 | `<your root folder name>` |
| **將現有的 Data Factory 資源匯入存放庫** | 指定是否要從 UX **撰寫畫布** 將現有的資料處理站資源匯入到 Azure Repos Git 存放庫。 選取此方塊可將您的資料處理站資源以 JSON 格式匯入到相關聯的 Git 存放庫。 此動作會將每個資源個別匯出 (亦即，已連結的服務和資料集會匯出至個別的 JSON)。 若未選取此方塊，則不會匯入現有資源。 | 已選取 (預設值) |
| **要匯入資源的分支** | 指定要匯入資料處理站資源 (管線、資料集、連結服務等等) 的分支。 您可以將資源匯入下列其中一個分支：a. 共同作業 b. 新建 c. 使用現有的 |  |

> [!NOTE]
> 如果您使用 Microsoft Edge，但未在 [Azure DevOps 帳戶] 下拉式清單中看到任何值，請將 https://*.visualstudio.com 新增至信任的網站清單。

### <a name="use-a-different-azure-active-directory-tenant"></a>使用不同的 Azure Active Directory 租用戶

Azure Repos Git 存放庫可以位於不同的 Azure Active Directory 租用戶中。 若要指定不同的 Azure AD 租用戶，您必須擁有所用 Azure 訂用帳戶的系統管理員權限。 如需詳細資訊，請參閱 [變更訂用帳戶管理員](../cost-management-billing/manage/add-change-subscription-administrator.md#to-assign-a-user-as-an-administrator)

> [!IMPORTANT]
> 若要連接到其他 Azure Active Directory，登入的使用者必須是該 Active Directory 的一部分。 

### <a name="use-your-personal-microsoft-account"></a>使用您的個人 Microsoft 帳戶

若要使用個人 Microsoft 帳戶進行 Git 整合，您可以將個人的 Azure Repos 連結至貴組織的 Active Directory。

1. 以來賓身分將您的個人 Microsoft 帳戶新增到貴組織的 Active Directory。 如需詳細資訊，請參閱[在 Azure 入口網站中新增 Azure Active Directory B2B 共同作業使用者](../active-directory/external-identities/add-users-administrator.md)。

2. 使用您的個人 Microsoft 帳戶登入 Azure 入口網站。 然後切換到貴組織的 Active Directory。

3. 移至 [Azure DevOps] 區段，您現在會在其中看到您的個人存放庫。 選取存放庫並與 Active Directory 連線。

在這些設定步驟之後，當您在 Data Factory UI 中設定 Git 整合時，可以使用您的個人存放庫。

如需將 Azure Repos 連線至貴組織 Active Directory 的詳細資訊，請參閱[將您的 Azure DevOps 組織連線至 Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)。

## <a name="author-with-github-integration"></a>使用 GitHub 整合進行撰寫

使用 GitHub 整合所進行的視覺化撰寫，會對資料處理站管線上的工作支援原始檔控制和共同作業功能。 您可以將資料處理站與 GitHub 帳戶存放庫建立關聯，以進行原始檔控制、共同作業及版本設定。 一個 GitHub 帳戶可以有多個存放庫，但一個 GitHub 存放庫只能與一個資料處理站建立關聯。 如果您沒有 GitHub 帳戶或存放庫，請遵循[這些指示](https://github.com/join)來建立您的資源。

GitHub 與 Data Factory 的整合支援公用 GitHub (即 [https://github.com](https://github.com)) 與 GitHub Enterprise。 只要您具備 GitHub 中存放庫的讀取和寫入權限，就能搭配 Data Factory 使用公用和私人 GitHub 存放庫。

若要設定 GitHub 存放庫，您必須擁有所用 Azure 訂用帳戶的系統管理員權限。

### <a name="github-settings"></a>GitHub 設定

![GitHub 存放庫設定](media/author-visually/github-integration-image2.png)

此設定窗格會顯示下列 GitHub 存放庫設定：

| **設定** | **說明**  | **ReplTest1**  |
|:--- |:--- |:--- |
| **存放庫類型** | Azure Repos 程式碼存放庫的類型。 | GitHub |
| **使用 GitHub Enterprise** | 選取 GitHub Enterprise 的核取方塊 | 未選取 (預設值) |
| **GitHub Enterprise URL** | GitHub Enterprise 根 URL (若為本機 GitHub Enterprise 伺服器，則必須是 HTTPS)。 例如： `https://github.mydomain.com` 。 只有在已選取 [使用 GitHub Enterprise] 時才必要 | `<your GitHub enterprise url>` |                                                           
| **GitHub 帳戶** | 您的 GitHub 帳戶名稱。 您可以從 https:\//github.com/{帳戶名稱}/{存放庫名稱} 找到此名稱。 瀏覽到此頁面時，系統會提示您輸入 GitHub 帳戶的 GitHub OAuth 認證。 | `<your GitHub account name>` |
| **存放庫名稱**  | 您的 GitHub 程式碼存放庫名稱。 GitHub 帳戶包含 Git 存放庫，可用來管理原始程式碼。 您可以建立新的存放庫，或使用帳戶中既有的存放庫。 | `<your repository name>` |
| **共同作業分支** | 用於發行的 GitHub 共同作業分支。 依預設，它是主要的。 如果您想要從其他分支發行資源，請變更此設定。 | `<your collaboration branch>` |
| **根資料夾** | 在您 GitHub 共同作業分支中的根資料夾。 |`<your root folder name>` |
| **將現有的 Data Factory 資源匯入存放庫** | 指定是否要從 UX 撰寫畫布將現有的資料處理站資源匯入到 GitHub 存放庫。 選取此方塊可將您的資料處理站資源以 JSON 格式匯入到相關聯的 Git 存放庫。 此動作會將每個資源個別匯出 (亦即，已連結的服務和資料集會匯出至個別的 JSON)。 若未選取此方塊，則不會匯入現有資源。 | 已選取 (預設值) |
| **要匯入資源的分支** | 指定要匯入資料處理站資源 (管線、資料集、連結服務等等) 的分支。 您可以將資源匯入下列其中一個分支：a. 共同作業 b. 新建 c. 使用現有的 |  |

### <a name="github-organizations"></a>GitHub 組織

若要連線到 GitHub 組織，組織必須將許可權授與 Azure Data Factory。 具有組織系統管理員許可權的使用者必須執行下列步驟，才能讓 data factory 連接。

#### <a name="connecting-to-github-for-the-first-time-in-azure-data-factory"></a>第一次在 Azure Data Factory 中連接到 GitHub

如果您是第一次從 Azure Data Factory 連接到 GitHub，請遵循下列步驟來連線到 GitHub 組織。

1. 在 [Git 設定] 窗格的 [ *GitHub 帳戶* ] 欄位中，輸入組織名稱。 系統會提示您登入 GitHub。 
1. 使用您的使用者認證登入。
1. 系統會要求您將 Azure Data Factory 作為名為 *AzureDataFactory* 的應用程式來授權。 在此畫面上，您會看到授與許可權給 ADF 以存取組織的選項。 如果您沒有看到授與許可權的選項，請要求系統管理員透過 GitHub 手動授與許可權。

遵循這些步驟之後，您的工廠就能連線到您組織內的公用和私人存放庫。 如果您無法連線，請嘗試清除瀏覽器快取，然後再試一次。

#### <a name="already-connected-to-github-using-a-personal-account"></a>已使用個人帳戶連接到 GitHub

如果您已連線至 GitHub，而且只授與存取個人帳戶的許可權，請遵循下列步驟來授與許可權給組織。 

1. 移至 GitHub 並開啟 [ **設定**]。

    ![開啟 GitHub 設定](media/author-visually/github-settings.png)

1. 選取 **應用程式**。 在 [ **授權的 OAuth 應用程式** ] 索引標籤中，您應該會看到 *AzureDataFactory*。

    ![選取 OAuth 應用程式](media/author-visually/github-organization-select-application.png)

1. 選取應用程式，並將應用程式存取權授與您的組織。

    ![授與存取權](media/author-visually/github-organization-grant.png)

遵循這些步驟之後，您的工廠就能連線到您組織內的公用和私人存放庫。 

### <a name="known-github-limitations"></a>已知的 GitHub 限制

- 您可以將指令碼和資料檔案儲存在 GitHub 存放庫。 不過，您必須手動將檔案上載至 Azure 儲存體。 Data Factory 管線不會自動將 GitHub 存放庫中儲存的指令碼或資料檔案上傳至 Azure 儲存體。

- 早於 2.14.0 的 GitHub Enterprise 版本不適用於 Microsoft Edge 瀏覽器。

- 僅可以在正式推出的 Data Factory 版本中，使用 GitHub 與 Data Factory 視覺化撰寫工具的整合。


- 針對每個資源類型 (例如管線和資料集)，最多可以從單一 GitHub 分支擷取 1,000 個實體。 若達到此限制，則建議您將資源分割成不同處理站。 Azure DevOps Git 沒有此限制。

## <a name="version-control"></a>版本控制

版本控制系統 (也稱為 _原始檔控制_) 可讓開發人員在程式碼上共同作業，並追蹤對程式碼基底所進行的變更。 來源控制是多開發人員專案的必要工具。

### <a name="creating-feature-branches"></a>建立功能分支

與資料處理站相關聯的每個 Azure Repos Git 存放庫都有共同作業分支。  (`main`) 是預設的共同作業分支) 。 使用者也可以按一下 [分支] 下拉式清單中的 [+ 新增分支] 來建立功能分支。 [新增分支] 窗格出現之後，請輸入功能分支的名稱。

![建立新的分支](media/author-visually/new-branch.png)

當您準備好要將功能分支的變更合併到共同作業分支時，請按一下 [分支] 下拉式清單，然後選取 [建立提取要求]。 此動作會將您帶往 Azure Repos Git，您可以在其中發出提取要求、執行程式碼檢閱，以及將變更合併到您的共同作業分支。 (`main` 是預設值)。 您只被允許從您的共同作業分支發佈到 Data Factory 服務。 

![建立新的提取要求](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>設定發佈設定

根據預設，資料處理站會產生已發佈處理站的 Resource Manager 範本，並將這些範本儲存到稱為 `adf_publish` 的分支。 若要設定自訂發佈分支，請將 `publish_config.json` 檔案新增至共同作業分支中的根資料夾。 發佈時，ADF 會讀取此檔案、尋找 `publishBranch` 欄位，並將所有 Resource Manager 範本儲存到指定的位置。 如果該分支不存在，資料處理站會自動加以建立。 以下是此檔案的樣貌範例：

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Azure Data Factory 一次只能有一個發佈分支。 當您指定新的發佈分支時，Data Factory 並不會刪除先前的發佈分支。 如果您想要移除先前的發佈分支，請手動將其刪除。

> [!NOTE]
> Data Factory 只有在載入處理站時才會讀取 `publish_config.json` 檔案。 如果您已經在入口網站中載入處理站，請重新整理瀏覽器以讓變更生效。

### <a name="publish-code-changes"></a>發佈程式碼變更

將變更合併到共同作業分支之後 (`main` 是預設) ，請按一下 [ **發佈** ]，以手動方式將主要分支中的程式碼變更發佈至 Data Factory 服務。

![將變更發佈到 Data Factory 服務](media/author-visually/publish-changes.png)

側邊窗格隨即開啟，以供您確認發佈分支和暫止的變更是否正確。 確認變更之後，請按一下 [確定] 來確認發佈。

![確認正確的發佈分支](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> 主要分支不代表 Data Factory 服務中部署的內容。 主要分支 *必須* 手動發佈至 Data Factory 服務。

## <a name="best-practices-for-git-integration"></a>Git 整合的最佳做法

### <a name="permissions"></a>權限

一般來說，您不希望每個小組成員都具有更新 Data Factory 的許可權。 建議您使用下列權限設定：

*   所有小組成員都應該具有 Data Factory 的讀取權限。
*   只有一組選取的人員才能發佈至 Data Factory。 若要這樣做，他們必須在包含 Data Factory 的 **資源群組** 上擁有 **Data Factory 參與者** 角色。 如需權限的詳細資訊，請參閱 [Azure Data Factory 的角色和權限](concepts-roles-permissions.md)。

建議您不要允許直接簽入至共同作業分支。 此限制可協助避免錯誤 (bug)，因為每個簽入都會經歷[建立功能分支](source-control.md#creating-feature-branches)所述的提取要求檢閱程序。

### <a name="using-passwords-from-azure-key-vault"></a>使用來自 Azure Key Vault 的密碼

建議使用 Azure Key Vault 來儲存 Data Factory 連結服務的任何連接字串或密碼或受控識別驗證。 為求安全，資料處理站不會在 Git 中儲存秘密。 包含秘密 (例如密碼) 的連結服務若有任何變更，都會立即發佈至 Azure Data Factory 服務。

使用 Key Vault 或 MSI 驗證也可以讓持續整合和持續部署變得更輕鬆，因為您不必在 Resource Manager 範本部署期間提供這些秘密。

## <a name="troubleshooting-git-integration"></a>針對 Git 整合進行疑難排解

### <a name="stale-publish-branch"></a>過時的發佈分支

如果發佈分支與主要分支不同步並包含過時的資源（儘管最近發行），請嘗試執行下列步驟：

1. 移除目前的 Git 存放庫
1. 以相同的設定重新設定 Git，但請確定已選取 [將現有的 Data Factory 資源匯入存放庫]，然後選擇 [新增分支]
1. 建立提取要求以將變更合併到共同作業分支 

以下是一些可能會導致過時發佈分支的情況範例：
- 使用者有多個分支。 在一個功能分支中，他們刪除了未 AKV 關聯的已連結服務， (非 AKV 連結服務會立即發行，不論它們是在 Git 中，還是不是) ，而且永遠不會將功能分支合併至共同作業分支。
- 使用者使用 SDK 或 PowerShell 修改過資料處理站
- 使用者已將所有資源移到新的分支，並嘗試進行第一次發佈。 請於匯入資源時手動建立連結服務。
- 使用者以手動方式上傳非 AKV 連結服務或 Integration Runtime JSON。 使用者從另一個資源 (例如資料集、連結服務或管線) 參考該資源。 透過 UX 建立的非 AKV 連結服務會立即發佈，因為認證需要加密。 如果您上傳參考該連結服務的資料集並嘗試發佈，UX 會允許此資料集，因為其存在於 Git 環境中。 此資料集會在發佈時遭到拒絕，因為其不存在於資料處理站服務中。

## <a name="switch-to-a-different-git-repository"></a>切換至不同的 Git 存放庫

若要切換至不同的 Git 存放庫，請移至 [ **原始檔控制**] 下 [管理中樞] 中的 [Git 設定] 頁面。 選取 **[中斷連線]**。 

![Git 圖示](media/author-visually/remove-repository.png)

輸入您的資料處理站名稱，然後按一下 [確認] 來移除與資料處理站相關聯的 Git 存放庫。

![移除與目前 Git 存放庫的關聯](media/author-visually/remove-repository-2.png)

移除與目前存放庫的關聯之後，您可以將 Git 設定設為使用不同的存放庫，然後將現有的 Data Factory 資源匯入到新的存放庫。

> [!IMPORTANT]
> 從資料處理站移除 Git 設定並不會從存放庫中刪除所有內容。 處理站會包含所有已發佈的資源。 您可以繼續直接針對服務來編輯處理站。

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何監視和管理管線，請參閱[以程式設計方式監視和管理管線](monitor-programmatically.md)。
* 若要實作持續整合和持續部署，請參閱 [Azure Data Factory 中的持續整合和傳遞 (CI/CD)](continuous-integration-deployment.md)。
