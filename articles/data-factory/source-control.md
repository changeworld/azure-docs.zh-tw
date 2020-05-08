---
title: 原始檔控制
description: 瞭解如何在 Azure Data Factory 中設定原始檔控制
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: ''
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/30/2020
ms.openlocfilehash: f327844be57d7f8e177f3bf72b1e3b56c5147e00
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629315"
---
# <a name="source-control-in-azure-data-factory"></a>Azure Data Factory 中的原始檔控制
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

根據預設，會直接對 Data Factory 服務 Azure Data Factory 使用者介面體驗（UX）作者。 這項體驗有下列限制：

- Data Factory 服務未包含存放庫，此存放庫會用於儲存變更的 JSON 實體。 儲存變更的唯一方式是透過 [**全部發佈**] 按鈕，所有變更都會直接發佈至 data factory 服務。
- Data Factory 服務未針對共同作業和版本控制進行優化。

為了提供更好的撰寫經驗，Azure Data Factory 可讓您使用 Azure Repos 或 GitHub 來設定 Git 存放庫。 Git 是一種版本控制系統，可讓您更輕鬆地進行變更追蹤和共同作業。 本教學課程將概述如何在 git 存放庫中進行設定和工作，以及反白顯示最佳做法和疑難排解指南。

> [!NOTE]
> Azure data factory git 整合無法在 Azure Government 雲端中使用。

## <a name="advantages-of-git-integration"></a>Git 整合的優點

以下是 git 整合提供給撰寫經驗的一些優點清單：

-   **原始檔控制：** 當您的資料處理站工作負載變得很重要時，您會想要整合您的處理站與 Git，以運用數個來源控制優點，如下所示：
    -   追蹤/稽核變更的能力。
    -   還原造成錯誤 (Bug) 之變更的能力。
-   **部分儲存：** 針對 data factory 服務撰寫時，您無法將變更儲存為草稿，而且所有發行都必須通過 data factory 驗證。 不論您的管線是否未完成，或您只是不想在電腦損毀時遺失變更，git 整合允許資料處理站資源的累加式變更，而不論其狀態為何。 設定 git 存放庫可讓您儲存變更，讓您只在測試過您對滿意度的變更時才發行。
-   共同作業**與控制：** 如果您有多個小組成員參與同一個工廠，您可能會想要讓您的團隊能夠透過程式碼審核程式彼此共同作業。 您也可以設定 factory，使其不是每個參與者都具有相同的許可權。 某些小組成員可能只允許透過 Git 進行變更，而小組中的特定人員只允許將變更發佈至 factory。
-   **更好的 CI/CD：** 如果您要使用[持續傳遞](continuous-integration-deployment.md)程式部署至多個環境，git 整合可以讓某些動作更容易。 其中一些動作包括：
    -   將發行管線設定為在對「dev」 factory 進行任何變更時自動觸發。
    -   在您的 factory 中自訂可做為 Resource Manager 範本中參數的屬性。 只保留必要屬性集做為參數並將其他項目以硬式編碼方式撰寫非常實用。
-   **較佳的效能：** 具有 git 整合的平均處理站，會比對 data factory 服務所進行的一次撰寫快載入10倍。 這種效能改進是因為資源是透過 Git 下載。

> [!NOTE]
> 設定 Git 儲存機制時，會在 Azure Data Factory UX 中停用直接使用 Data Factory 服務的撰寫。 您可以透過 PowerShell 或 SDK 直接對服務進行變更。

## <a name="author-with-azure-repos-git-integration"></a>使用 Azure Repos Git 整合來進行撰寫

使用 Azure Repos Git 整合來進行視覺化撰寫時，可針對您資料處理站管線上的工作支援原始檔控制和共同作業。 您可以將資料處理站與 Azure Repos Git 組織存放庫建立關聯，以進行原始檔控制、共同作業及版本設定等。 一個 Azure Repos Git 組織可以有多個存放庫，但一個 Azure Repos Git 存放庫只能與一個資料處理站建立關聯。 如果您沒有 Azure Repos 組織或存放庫，請依照[這些指示](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)來建立您的資源。

> [!NOTE]
> 您可以將指令碼和資料檔案儲存在 Azure Repos Git 存放庫中。 不過，您必須手動將檔案上載至 Azure 儲存體。 Data Factory 管線不會自動將儲存在 Azure Repos Git 存放庫中的指令碼或資料檔案上傳至「Azure 儲存體」。

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>設定搭配 Azure Data Factory 的 Azure Repos Git 存放庫

您可以透過兩種方法設定搭配資料處理站的 Azure Repos Git 存放庫。

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>設定方法1： Azure Data Factory 首頁

在 [Azure Data Factory] 首頁上，選取 [**設定程式碼存放庫**]。

![設定 Azure Repos 程式碼存放庫](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>設定方法 2：UX 撰寫畫布
在 [Azure Data Factory UX 撰寫畫布] 中，選取 [ **Data Factory** ] 下拉式功能表，然後選取 [**設定程式碼存放庫**]。

![設定用於 UX 撰寫的程式碼存放庫設定](media/author-visually/configure-repo-2.png)

這兩種方法都會開啟 [存放庫設定] 設定窗格。

![設定程式碼存放庫設定](media/author-visually/repo-settings.png)

[設定] 窗格會顯示下列 Azure Repos 程式碼存放庫設定：

| 設定 | 描述 | 值 |
|:--- |:--- |:--- |
| **存放庫類型** | Azure Repos 程式碼存放庫的類型。<br/> | Azure DevOps Git 或 GitHub |
| **Azure Active Directory** | 您的 Azure AD 租用戶名稱。 | `<your tenant name>` |
| **Azure Repos 組織** | 您的 Azure Repos 組織名稱。 您可以在 `https://{organization name}.visualstudio.com` 找到您的 Azure Repos 組織名稱。 您可以[登入您的 Azure Repos 組織](https://www.visualstudio.com/team-services/git/)，以存取 Visual Studio 設定檔和查看您的存放庫與專案。 | `<your organization name>` |
| **ProjectName** | 您的 Azure Repos 專案名稱。 您可以在 `https://{organization name}.visualstudio.com/{project name}` 找到您的 Azure Repos 專案名稱。 | `<your Azure Repos project name>` |
| **Event.pushnotification.repositoryname** | 您的 Azure Repos 程式碼存放庫名稱。 Azure Repos 專案包含 Git 存放庫，可隨著您的專案成長管理原始程式碼。 您可以建立新的存放庫，或使用專案中既有的存放庫。 | `<your Azure Repos code repository name>` |
| **共同作業分支** | 用於進行發佈的 Azure Repos 共同作業分支。 根據預設，其`master`為。 如果您想要從其他分支發行資源，請變更此設定。 | `<your collaboration branch name>` |
| **根資料夾** | 在您 Azure Repos 共同作業分支中的根資料夾。 | `<your root folder name>` |
| **將現有的 Data Factory 資源匯入存放庫** | 指定是否要從 UX **撰寫畫布**將現有的資料處理站資源匯入到 Azure Repos Git 存放庫。 選取此方塊可將您的資料處理站資源以 JSON 格式匯入到相關聯的 Git 存放庫。 此動作會將每個資源個別匯出 (亦即，已連結的服務和資料集會匯出至個別的 JSON)。 若未選取此方塊，則不會匯入現有資源。 | 已選取 (預設值) |
| **要匯入資源的分支** | 指定要匯入資料處理站資源 (管線、資料集、連結服務等等) 的分支。 您可以將資源匯入下列其中一個分支：a. 共同作業 b. 新建 c. 使用現有的 |  |

> [!NOTE]
> 如果您使用 Microsoft Edge，但在 Azure DevOps 帳戶] 下拉式清單中看不到任何值，請將 HTTPs：//* visualstudio 新增至信任的網站清單。

### <a name="use-a-different-azure-active-directory-tenant"></a>使用不同的 Azure Active Directory 租用戶

Azure Repos Git 存放庫可以位於不同的 Azure Active Directory 租使用者中。 若要指定不同的 Azure AD 租用戶，您必須擁有所用 Azure 訂用帳戶的系統管理員權限。

### <a name="use-your-personal-microsoft-account"></a>使用您的個人 Microsoft 帳戶

若要使用個人 Microsoft 帳戶進行 Git 整合，您可以將個人的 Azure Repos 連結至貴組織的 Active Directory。

1. 以來賓身分將您的個人 Microsoft 帳戶新增到貴組織的 Active Directory。 如需詳細資訊，請參閱[在 Azure 入口網站中新增 Azure Active Directory B2B 共同作業使用者](../active-directory/b2b/add-users-administrator.md)。

2. 使用您的個人 Microsoft 帳戶登入 Azure 入口網站。 然後切換到貴組織的 Active Directory。

3. 移至 [Azure DevOps] 區段，您現在會在其中看到您的個人存放庫。 選取存放庫並與 Active Directory 連線。

在這些設定步驟之後，當您在 Data Factory UI 中設定 Git 整合時，可以使用您的個人存放庫。

如需將 Azure Repos 連線至貴組織 Active Directory 的詳細資訊，請參閱[將您的 Azure DevOps 組織連線至 Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)。

## <a name="author-with-github-integration"></a>使用 GitHub 整合進行撰寫

使用 GitHub 整合所進行的視覺化撰寫，會對資料處理站管線上的工作支援原始檔控制和共同作業功能。 您可以將資料處理站與 GitHub 帳戶存放庫建立關聯，以進行原始檔控制、共同作業及版本設定。 一個 GitHub 帳戶可以有多個存放庫，但一個 GitHub 存放庫只能與一個資料處理站建立關聯。 如果您沒有 GitHub 帳戶或存放庫，請遵循 [這些指示](https://github.com/join) 來建立您的資源。

GitHub 與 Data Factory 整合同時支援公用 GitHub （也就是[https://github.com](https://github.com)）和 GitHub Enterprise。 只要您具備 GitHub 中存放庫的讀取和寫入權限，就能搭配 Data Factory 使用公用和私人 GitHub 存放庫。

若要設定 GitHub 存放庫，您必須具有您所使用之 Azure 訂用帳戶的系統管理員許可權。

如需此功能的 9 分鐘簡介與示範，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>使用 Azure Data Factory 設定 GitHub 存放庫

您可以透過兩種方法設定搭配資料處理站的 GitHub 存放庫。

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>設定方法1： Azure Data Factory 首頁

在 [Azure Data Factory] 首頁上，選取 [**設定程式碼存放庫**]。

![設定 Azure Repos 程式碼存放庫](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>設定方法 2：UX 撰寫畫布

在 [Azure Data Factory UX 撰寫畫布] 中，選取 [ **Data Factory** ] 下拉式功能表，然後選取 [**設定程式碼存放庫**]。

![設定用於 UX 撰寫的程式碼存放庫設定](media/author-visually/configure-repo-2.png)

這兩種方法都會開啟 [存放庫設定] 設定窗格。

![GitHub 存放庫設定](media/author-visually/github-integration-image2.png)

[設定] 窗格會顯示下列 GitHub 存放庫設定：

| **設定** | **說明**  | **ReplTest1**  |
|:--- |:--- |:--- |
| **存放庫類型** | Azure Repos 程式碼存放庫的類型。 | GitHub |
| **使用 GitHub Enterprise** | 選取 GitHub Enterprise 的核取方塊 | 未選取（預設值） |
| **GitHub Enterprise URL** | GitHub Enterprise 根 URL （必須是本機 GitHub Enterprise 伺服器的 HTTPS）。 例如： `https://github.mydomain.com` 。 只有在選取 [**使用 GitHub Enterprise**時才需要 | `<your GitHub enterprise url>` |                                                           
| **GitHub 帳戶** | 您的 GitHub 帳戶名稱。 您可以從 HTTPs：\//github.com/{account name}/{repository name} 找到此名稱。 瀏覽到此頁面時，系統會提示您輸入 GitHub 帳戶的 GitHub OAuth 認證。 | `<your GitHub account name>` |
| **存放庫名稱**  | 您的 GitHub 程式碼存放庫名稱。 GitHub 帳戶包含 Git 存放庫，可用來管理原始程式碼。 您可以建立新的存放庫，或使用帳戶中既有的存放庫。 | `<your repository name>` |
| **共同作業分支** | 用於發行的 GitHub 共同作業分支。 預設為主要。 如果您想要從其他分支發行資源，請變更此設定。 | `<your collaboration branch>` |
| **根資料夾** | 在您 GitHub 共同作業分支中的根資料夾。 |`<your root folder name>` |
| **將現有的 Data Factory 資源匯入存放庫** | 指定是否要從 UX 撰寫畫布將現有的 data factory 資源匯入到 GitHub 存放庫。 選取此方塊可將您的資料處理站資源以 JSON 格式匯入到相關聯的 Git 存放庫。 此動作會將每個資源個別匯出 (亦即，已連結的服務和資料集會匯出至個別的 JSON)。 若未選取此方塊，則不會匯入現有資源。 | 已選取 (預設值) |
| **要匯入資源的分支** | 指定要匯入資料處理站資源 (管線、資料集、連結服務等等) 的分支。 您可以將資源匯入下列其中一個分支：a. 共同作業 b. 新建 c. 使用現有的 |  |

### <a name="known-github-limitations"></a>已知的 GitHub 限制

- 您可以將指令碼和資料檔案儲存在 GitHub 存放庫。 不過，您必須手動將檔案上載至 Azure 儲存體。 Data Factory 管線不會自動將 GitHub 存放庫中儲存的指令碼或資料檔案上傳至 Azure 儲存體。

- 早於 2.14.0 的 GitHub Enterprise 版本不適用於 Microsoft Edge 瀏覽器。

- GitHub 與 Data Factory 的視覺效果撰寫工具整合僅適用于 Data Factory 的正式運作版本。

- 針對每個資源類型（例如管線和資料集），最多可以從單一 GitHub 分支提取1000個實體。 若達到此限制，建議您將資源分割成不同的工廠。 Azure DevOps Git 沒有這項限制。

## <a name="version-control"></a>版本控制

版本控制系統 (也稱為_原始檔控制_) 可讓開發人員在程式碼上共同作業，並追蹤對程式碼基底所進行的變更。 來源控制是多開發人員專案的必要工具。

### <a name="creating-feature-branches"></a>建立功能分支

與資料處理站相關聯的每個 Azure Repos Git 存放庫都有共同作業分支。 (`master` 是預設的共同作業分支)。 使用者也可以按一下 [分支] 下拉式清單中的 [ **+ 新增分支**] 來建立功能分支。 [新增分支] 窗格出現之後，請輸入功能分支的名稱。

![建立新的分支](media/author-visually/new-branch.png)

當您準備好要將功能分支中的變更合併到共同作業分支時，請按一下 [分支] 下拉式清單，然後選取 [**建立提取要求**]。 此動作會將您帶往 Azure Repos Git，您可以在其中發出提取要求、執行程式碼檢閱，以及將變更合併到您的共同作業分支。 (`master` 是預設值)。 您只被允許從您的共同作業分支發佈到 Data Factory 服務。 

![建立新的提取要求](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>設定發佈設定

根據預設，data factory 會產生已發佈處理站的 Resource Manager 範本，並將它們儲存到`adf_public`名為的分支中。 若要設定自訂發行分支，請`publish_config.json`將檔案新增至共同作業分支中的根資料夾。 發佈時，ADF 會讀取此檔案、尋找欄位`publishBranch`，並將所有 Resource Manager 範本儲存到指定的位置。 如果該分支不存在，data factory 會自動加以建立。 以下是此檔案的樣子範例：

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Azure Data Factory 一次只能有一個發佈分支。 當您指定新的發佈分支時，Data Factory 並不會刪除先前的發佈分支。 如果您想要移除先前的發佈分支，請以手動方式刪除它。

> [!NOTE]
> Data Factory 只有在載入處理站時才會讀取 `publish_config.json` 檔案。 如果您已經在入口網站中載入處理站，請重新整理瀏覽器以讓變更生效。

### <a name="publish-code-changes"></a>發佈程式碼變更

將變更合併到共同作業分支之後（`master`是預設值），請按一下 [**發佈**]，以手動方式將主要分支中的程式碼變更發佈至 Data Factory 服務。

![將變更發佈到 Data Factory 服務](media/author-visually/publish-changes.png)

側邊窗格隨即開啟，您可以在其中確認發佈分支和暫止變更是否正確。 驗證變更後，按一下 **[確定]** 確認發佈。

![確認正確的發佈分支](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> 主要分支不代表在 Data Factory 服務中部署的內容。 「必須」** 以手動方式將主要分支主發佈至 Data Factory 服務。

## <a name="best-practices-for-git-integration"></a>Git 整合的最佳做法

### <a name="permissions"></a>權限

通常您不會希望每個小組成員都擁有更新處理站的許可權。 建議使用下列許可權設定：

*   所有小組成員都應該有資料處理站的唯讀權限。
*   只應允許一組選取的人員發佈至 factory。 若要這麼做，他們必須擁有 Factory 所在資源群組的「 **Data Factory 參與者**」角色。 如需許可權的詳細資訊，請參閱[Azure Data Factory 的角色和許可權](concepts-roles-permissions.md)。
   
建議您不要允許直接簽入共同作業分支。 這項限制可協助防止錯誤，因為每個簽入都將經歷[建立功能分支](source-control.md#creating-feature-branches)中所述的提取要求審核程式。

### <a name="using-passwords-from-azure-key-vault"></a>使用 Azure Key Vault 的密碼

建議使用 Azure Key Vault 來儲存 Data Factory 連結服務的任何連接字串或密碼或受控識別驗證。 基於安全性理由，data factory 不會在 Git 中儲存秘密。 包含秘密（例如密碼）之連結服務的任何變更都會立即發佈到 Azure Data Factory 服務。

使用 Key Vault 或 MSI 驗證也可以讓持續整合和部署更輕鬆，因為您不需要在 Resource Manager 範本部署期間提供這些秘密。

## <a name="troubleshooting-git-integration"></a>針對 Git 整合進行疑難排解

### <a name="stale-publish-branch"></a>過時的發行分支

如果發行分支與 master 分支不同步，而且即使最近發佈也包含過期的資源，請嘗試執行下列步驟：

1. 移除目前的 Git 存放庫
1. 以相同的設定重新設定 Git，但請確定已選取 [**將現有的 Data Factory 資源匯入到存放庫**]，然後選擇 [**新增分支**]
1. 建立提取要求以將變更合併到共同作業分支 

以下是一些可能會導致過時發佈分支的情況範例：
- 使用者有多個分支。 在一個功能分支中，他們會刪除未 AKV 關聯的連結服務（非 AKV 連結服務會立即發行，不論它們是否在 Git 中），而且永遠不會將功能分支合併到共同作業分支。
- 使用者使用 SDK 或 PowerShell 修改過 data factory
- 使用者已將所有資源移到新的分支，並嘗試第一次發行。 匯入資源時，應該手動建立連結服務。
- 使用者以手動方式上傳非 AKV 連結服務或 Integration Runtime JSON。 它們會從另一個資源（例如資料集、連結的服務或管線）參考該資源。 透過 UX 建立的非 AKV 連結服務會立即發佈，因為認證需要加密。 如果您上傳參考該連結服務的資料集並嘗試發佈，UX 會允許它，因為它存在於 git 環境中。 它會在發行時遭到拒絕，因為它不存在於 data factory 服務中。

## <a name="switch-to-a-different-git-repository"></a>切換至不同的 Git 存放庫

若要切換至不同的 Git 存放庫，請按一下 Data Factory [總覽] 頁面右上角的 [Git 儲存機制**設定**] 圖示。 如果您看不到此圖示，請清除您的本機瀏覽器快取。 選取適當圖示以移除與目前存放庫的關聯。

![Git 圖示](media/author-visually/remove-repo.png)

出現 [存放庫設定] 窗格之後，請選取 [**移除 Git**]。 輸入您的資料處理站名稱，然後按一下 [**確認**] 以移除與您的 data factory 相關聯的 Git 存放庫。

![移除與目前 Git 存放庫的關聯](media/author-visually/remove-repo2.png)

移除與目前存放庫的關聯之後，您可以將 Git 設定設定為使用不同的存放庫，然後將現有的 Data Factory 資源匯入到新的存放庫。

> [!IMPORTANT]
> 從資料處理站移除 Git 設定並不會從存放庫中刪除任何專案。 Factory 會包含所有已發佈的資源。 您可以繼續直接針對服務編輯工廠。

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何監視和管理管線，請參閱[以程式設計方式監視和管理管線](monitor-programmatically.md)。
* 若要執行持續整合和部署，請參閱[Azure Data Factory 中的持續整合與傳遞（CI/CD）](continuous-integration-deployment.md)。
