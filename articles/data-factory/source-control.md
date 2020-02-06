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
ms.date: 01/09/2019
ms.openlocfilehash: 1cc5932eca520b0bbc0c592b54d36ea8b5942b08
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031624"
---
# <a name="source-control-in-azure-data-factory"></a>Azure Data Factory 中的原始檔控制

Azure Data Factory 的使用者介面體驗（UX）有兩種適用于視覺製作的體驗：

- 直接使用 Data Factory 服務來撰寫
- 使用 Azure Repos Git 或 GitHub 整合進行撰寫

> [!NOTE]
> Azure Government 雲端僅支援直接撰寫 Data Factory 服務。

## <a name="author-directly-with-the-data-factory-service"></a>直接使用 Data Factory 服務來撰寫

直接使用 Data Factory 服務撰寫時，儲存變更的唯一方式是透過 [**全部發佈**] 按鈕。 一旦按一下，您所做的所有變更都會直接發行到 Data Factory 服務。 

![發佈模式](media/author-visually/data-factory-publish.png)

直接使用 Data Factory 服務撰寫，有下列限制：

- Data Factory 服務未包含存放庫，此存放庫會用於儲存變更的 JSON 實體。
- Data Factory 服務並未針對共同作業或版本控制進行最佳化。

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
| **RepositoryName** | 您的 Azure Repos 程式碼存放庫名稱。 Azure Repos 專案包含 Git 存放庫，可隨著您的專案成長管理原始程式碼。 您可以建立新的存放庫，或使用專案中既有的存放庫。 | `<your Azure Repos code repository name>` |
| **共同作業分支** | 用於進行發佈的 Azure Repos 共同作業分支。 根據預設，它是 `master`。 如果您想要從其他分支發行資源，請變更此設定。 | `<your collaboration branch name>` |
| **根資料夾** | 在您 Azure Repos 共同作業分支中的根資料夾。 | `<your root folder name>` |
| **將現有的 Data Factory 資源匯入存放庫** | 指定是否要從 UX **撰寫畫布**將現有的資料處理站資源匯入到 Azure Repos Git 存放庫。 選取此方塊可將您的資料處理站資源以 JSON 格式匯入到相關聯的 Git 存放庫。 此動作會將每個資源個別匯出 (亦即，已連結的服務和資料集會匯出至個別的 JSON)。 若未選取此方塊，則不會匯入現有資源。 | 已選取 (預設值) |
| **要匯入資源的分支** | 指定要匯入資料處理站資源 (管線、資料集、連結服務等等) 的分支。 您可以將資源匯入下列其中一個分支：a. 共同作業 b. 新建 c. 使用現有的 |  |

> [!NOTE]
> 如果您使用 Microsoft Edge，但在 Azure DevOps 帳戶 下拉式清單中看不到任何值，請將 HTTPs：//* visualstudio 新增至信任的網站清單。

### <a name="use-a-different-azure-active-directory-tenant"></a>使用不同的 Azure Active Directory 租用戶

您可以在不同的 Azure Active Directory 租用戶中建立 Azure Repos Git 存放庫。 若要指定不同的 Azure AD 租用戶，您必須擁有所用 Azure 訂用帳戶的系統管理員權限。

### <a name="use-your-personal-microsoft-account"></a>使用您的個人 Microsoft 帳戶

若要使用個人 Microsoft 帳戶進行 Git 整合，您可以將個人的 Azure Repos 連結至貴組織的 Active Directory。

1. 以來賓身分將您的個人 Microsoft 帳戶新增到貴組織的 Active Directory。 如需詳細資訊，請參閱[在 Azure 入口網站中新增 Azure Active Directory B2B 共同作業使用者](../active-directory/b2b/add-users-administrator.md)。

2. 使用您的個人 Microsoft 帳戶登入 Azure 入口網站。 然後切換到貴組織的 Active Directory。

3. 移至 [Azure DevOps] 區段，您現在會在其中看到您的個人存放庫。 選取存放庫並與 Active Directory 連線。

在這些設定步驟之後，當您在 Data Factory UI 中設定 Git 整合時，可以使用您的個人存放庫。

如需將 Azure Repos 連線至貴組織 Active Directory 的詳細資訊，請參閱[將您的 Azure DevOps 組織連線至 Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)。

## <a name="author-with-github-integration"></a>使用 GitHub 整合進行撰寫

使用 GitHub 整合所進行的視覺化撰寫，會對資料處理站管線上的工作支援原始檔控制和共同作業功能。 您可以將資料處理站與 GitHub 帳戶存放庫建立關聯，以進行原始檔控制、共同作業及版本設定。 一個 GitHub 帳戶可以有多個存放庫，但一個 GitHub 存放庫只能與一個資料處理站建立關聯。 如果您沒有 GitHub 帳戶或存放庫，請遵循 [這些指示](https://github.com/join)  \(英文\) 來建立您的資源。

GitHub 與 Data Factory 的整合支援公用 GitHub (即 [https://github.com](https://github.com)) 與 GitHub Enterprise。 只要您具備 GitHub 中存放庫的讀取和寫入權限，就能搭配 Data Factory 使用公用和私人 GitHub 存放庫。

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
| **GitHub Enterprise URL** | GitHub Enterprise 的根 URL。 例如： https://github.mydomain.com 。 只有在選取 **使用 GitHub Enterprise**時才需要 | `<your GitHub enterprise url>` |                                                           
| **GitHub 帳戶** | 您的 GitHub 帳戶名稱。 您可以從 HTTPs：\//github.com/{account name}/{repository name} 找到此名稱。 瀏覽到此頁面時，系統會提示您輸入 GitHub 帳戶的 GitHub OAuth 認證。 | `<your GitHub account name>` |
| **存放庫名稱**  | 您的 GitHub 程式碼存放庫名稱。 GitHub 帳戶包含 Git 存放庫，可用來管理原始程式碼。 您可以建立新的存放庫，或使用帳戶中既有的存放庫。 | `<your repository name>` |
| **共同作業分支** | 用於發行的 GitHub 共同作業分支。 根據預設，它是 master。 如果您想要從其他分支發行資源，請變更此設定。 | `<your collaboration branch>` |
| **根資料夾** | 在您 GitHub 共同作業分支中的根資料夾。 |`<your root folder name>` |
| **將現有的 Data Factory 資源匯入存放庫** | 指定是否要從 UX 撰寫畫布將現有的 data factory 資源匯入到 GitHub 存放庫。 選取此方塊可將您的資料處理站資源以 JSON 格式匯入到相關聯的 Git 存放庫。 此動作會將每個資源個別匯出 (亦即，已連結的服務和資料集會匯出至個別的 JSON)。 若未選取此方塊，則不會匯入現有資源。 | 已選取 (預設值) |
| **要匯入資源的分支** | 指定要匯入資料處理站資源 (管線、資料集、連結服務等等) 的分支。 您可以將資源匯入下列其中一個分支：a. 共同作業 b. 新建 c. 使用現有的 |  |

### <a name="known-github-limitations"></a>已知的 GitHub 限制

- 您可以將指令碼和資料檔案儲存在 GitHub 存放庫。 不過，您必須手動將檔案上載至 Azure 儲存體。 Data Factory 管線不會自動將 GitHub 存放庫中儲存的指令碼或資料檔案上傳至 Azure 儲存體。

- 早於 2.14.0 的 GitHub Enterprise 版本不適用於 Microsoft Edge 瀏覽器。

- GitHub 與 Data Factory 的視覺效果撰寫工具整合僅適用于 Data Factory 的正式運作版本。

- 針對每個資源類型（例如管線和資料集），最多可以從單一 GitHub 分支提取1000個實體。 若達到此限制，建議您將資源分割成不同的工廠。 Azure DevOps Git 沒有這項限制。

## <a name="switch-to-a-different-git-repo"></a>切換至不同的 Git 存放庫

若要切換至不同的 Git 存放庫，請按一下 Data Factory [總覽] 頁面右上角的 [Git 存放庫**設定**] 圖示。 如果看不到該圖示，請清除您的本機瀏覽器快取。 選取適當圖示以移除與目前存放庫的關聯。

![Git 圖示](media/author-visually/remove-repo.png)

出現 [存放庫設定] 窗格之後，請選取 [**移除 Git**]。 輸入您的資料處理站名稱，然後按一下 [**確認**] 以移除與您的 data factory 相關聯的 Git 存放庫。

![移除與目前 Git 存放庫的關聯](media/author-visually/remove-repo2.png)

移除與目前存放庫的關聯之後，您可以將 Git 設定設定為使用不同的存放庫，然後將現有的 Data Factory 資源匯入到新的存放庫。 

## <a name="version-control"></a>版本控制

版本控制系統 (也稱為_原始檔控制_) 可讓開發人員在程式碼上共同作業，並追蹤對程式碼基底所進行的變更。 來源控制是多開發人員專案的必要工具。

### <a name="creating-feature-branches"></a>建立功能分支

與資料處理站相關聯的每個 Azure Repos Git 存放庫都有共同作業分支。 (`master` 是預設的共同作業分支)。 使用者也可以按一下 [分支] 下拉式清單中的 [ **+ 新增分支**] 來建立功能分支。 [新增分支] 窗格出現之後，請輸入功能分支的名稱。

![建立新的分支](media/author-visually/new-branch.png)

當您準備好要將功能分支中的變更合併到共同作業分支時，請按一下 [分支] 下拉式清單，然後選取 [**建立提取要求**]。 此動作會將您帶往 Azure Repos Git，您可以在其中發出提取要求、執行程式碼檢閱，以及將變更合併到您的共同作業分支。 (`master` 是預設值)。 您只被允許從您的共同作業分支發佈到 Data Factory 服務。 

![建立新的提取要求](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>設定發佈設定

若要設定發佈分支 (亦即儲存 Resource Manager 範本的分支)，請將 `publish_config.json` 檔案新增至共同作業分支中的根資料夾。 Data Factory 會讀取此檔案、尋找 `publishBranch` 欄位，然後使用所提供的值來建立新分支 (如果尚未存在)。 接著，它會將所有 Resource Manager 範本都儲存到指定的位置。 例如，

```json
{
    "publishBranch": "factory/adf_publish"
}
```

當您指定新的發佈分支時，Data Factory 並不會刪除先前的發佈分支。 如果您想要移除先前的發佈分支，請以手動方式刪除它。

> [!NOTE]
> Data Factory 只有在載入處理站時才會讀取 `publish_config.json` 檔案。 如果您已經在入口網站中載入處理站，請重新整理瀏覽器以讓變更生效。

### <a name="publish-code-changes"></a>發佈程式碼變更

將變更合併到共同作業分支之後（`master` 是預設值），請按一下 [**發佈**]，以手動方式將主要分支中的程式碼變更發佈至 Data Factory 服務。

![將變更發佈到 Data Factory 服務](media/author-visually/publish-changes.png)

側邊窗格隨即開啟，您可以在其中確認發佈分支和暫止變更是否正確。 驗證變更後，按一下 **[確定]** 確認發佈。

![確認正確的發佈分支](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> 主要分支不代表在 Data Factory 服務中部署的內容。 「必須」以手動方式將主要分支主發佈至 Data Factory 服務。

## <a name="advantages-of-git-integration"></a>Git 整合的優點

-   **原始程式碼控制**。 當您的資料處理站工作負載變得更重要時，您可以將您的處理站與 Git 整合，以利用數個原始程式碼控制優點，例如：
    -   追蹤/稽核變更的能力。
    -   還原造成錯誤 (Bug) 之變更的能力。
-   **部分儲存**。 當您在處理站中進行許多變更時，您將了解在一般 LIVE 模式中，您無法將變更儲存為草稿，因為您還沒有準備好，或您不想要在電腦當機的情況下損失您的變更。 使用 Git 整合時，您能繼續以增量方式儲存您的變更，並只在您準備好時才將其發行到處理站。 Git 可做為您工作的暫存位置，直到您測試變更直到滿意為止。
-   **共同作業與控制**. 若您有多個參與相同處理站的小組成員，您可以透過程式碼檢閱程序讓您的小組成員彼此共同作業。 您也可以設定您的處理站，使並非該處理站的所有參與者都有部署到處理站的權限。 您可能讓小組成員可以透過 Git 進行變更，但只有小組中的特定人員可以將變更「發行」到處理站。
-   **顯示差異**。 在 Git 模式中，您可能會看到即將發行到處理站的承載差異。 此差異會顯示上次發行到處理站之後修改/新增/刪除的所有資源/實體。 根據此差異，您可以繼續發行，或返回並檢查您的變更並稍後再返回。
-   **更好的 CI/CD**。 若您使用 Git 模式，您可以設定您的發行管線，以在開發人員處理站中發生任何變更時儘快自動觸發。 您也可以自訂處理站中的屬性，這些屬性是以 Resource Manager 範本中參數的形式提供。 只保留必要屬性集做為參數並將其他項目以硬式編碼方式撰寫非常實用。
-   **更好的效能**。 在 Git 模式中，平均 factory 會比一般即時模式更快載入十倍，因為資源是透過 Git 下載。

## <a name="best-practices-for-git-integration"></a>Git 整合的最佳做法

### <a name="permissions"></a>權限

通常您不會希望每個小組成員都擁有更新處理站的許可權。 建議使用下列許可權設定：

*   所有小組成員都應該有資料處理站的唯讀權限。
*   只應允許一組選取的人員發佈至 factory。 若要這麼做，他們必須擁有 Factory 的「 **Data Factory 參與者**」角色。 如需許可權的詳細資訊，請參閱[Azure Data Factory 的角色和許可權](concepts-roles-permissions.md)。
   
建議您不要允許直接簽入共同作業分支。 這項限制可協助防止錯誤，因為每個簽入都將經歷[建立功能分支](source-control.md#creating-feature-branches)中所述的提取要求審核程式。

### <a name="using-passwords-from-azure-key-vault"></a>使用 Azure Key Vault 的密碼

建議使用 Azure Key Vault 來儲存 Data Factory 連結服務的任何連接字串或密碼。 基於安全性理由，我們不會將任何這類秘密資訊儲存在 Git 中，因此對連結服務所做的任何變更都會立即發佈至 Azure Data Factory 服務。

使用 Key Vault 也可以讓持續整合和部署更輕鬆，因為您不需要在 Resource Manager 範本部署期間提供這些秘密。

## <a name="troubleshooting-git-integration"></a>針對 Git 整合進行疑難排解

### <a name="stale-publish-branch"></a>過時的發行分支

如果發行分支與 master 分支不同步，而且即使最近發佈也包含過期的資源，請嘗試執行下列步驟：

1. 移除目前的 Git 存放庫
1. 以相同的設定重新設定 Git，但請確定已選取 [**將現有的 Data Factory 資源匯入到存放庫**]，然後選擇 [**新增分支**]
1. 建立提取要求以將變更合併到共同作業分支 

以下是一些可能會導致過時發佈分支的情況範例：
- 使用者有多個分支。 在一個功能分支中，他們會刪除未 AKV 關聯的連結服務（非 AKV 連結服務會立即發行，不論它們是否在 Git 中），而且永遠不會將功能分支合併到共同作業 brnach。
- 使用者使用 SDK 或 PowerShell 修改過 data factory
- 使用者已將所有資源移到新的分支，並嘗試第一次發行。 匯入資源時，應該手動建立連結服務。
- 使用者以手動方式上傳非 AKV 連結服務或 Integration Runtime JSON。 它們會從另一個資源（例如資料集、連結的服務或管線）參考該資源。 透過 UX 建立的非 AKV 連結服務會立即發佈，因為認證需要加密。 如果您上傳參考該連結服務的資料集並嘗試發佈，UX 會允許它，因為它存在於 git 環境中。 它會在發行時遭到拒絕，因為它不存在於 data factory 服務中。

## <a name="provide-feedback"></a>提供意見反應
選取 [意見反應] 可為功能加上註解，也可以向 Microsoft 通報工具問題：

![意見](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何監視和管理管線，請參閱[以程式設計方式監視和管理管線](monitor-programmatically.md)。
* 若要執行持續整合和部署，請參閱[Azure Data Factory 中的持續整合與傳遞（CI/CD）](continuous-integration-deployment.md)。
