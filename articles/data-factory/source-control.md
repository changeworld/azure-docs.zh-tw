---
title: 原始檔控制
description: 瞭解如何在 Azure 資料工廠中設定原始碼管理
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
ms.openlocfilehash: 3007865c15ceb03b104282c29179ec59a8196b38
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604592"
---
# <a name="source-control-in-azure-data-factory"></a>Azure 資料工廠中的原始碼管理
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure 資料工廠使用者介面體驗 (UX) 有兩種體驗可用於視覺化創作:

- 直接使用 Data Factory 服務來撰寫
- 使用 Azure 儲存函式庫 Git 或 GitHub 整合編寫

> [!NOTE]
> Azure 政府雲中僅支援直接使用數據工廠服務進行創作。

## <a name="author-directly-with-the-data-factory-service"></a>直接使用 Data Factory 服務來撰寫

在直接使用資料工廠服務創作時,保存更改的唯一方法是通過 **「全部發佈」** 按鈕。 按一下後,您所做的所有更改將直接發佈到數據工廠服務。 

![發佈模式](media/author-visually/data-factory-publish.png)

直接使用資料工廠服務創作有以下限制:

- Data Factory 服務未包含存放庫，此存放庫會用於儲存變更的 JSON 實體。
- Data Factory 服務並未針對共同作業或版本控制進行最佳化。

> [!NOTE]
> 配置 Git 儲存庫時,Azure 數據工廠 UX 中將禁用直接使用數據工廠服務進行創作。 可透過 PowerShell 或 SDK 直接對服務進行更改。

## <a name="author-with-azure-repos-git-integration"></a>使用 Azure Repos Git 整合來進行撰寫

使用 Azure Repos Git 整合來進行視覺化撰寫時，可針對您資料處理站管線上的工作支援原始檔控制和共同作業。 您可以將資料處理站與 Azure Repos Git 組織存放庫建立關聯，以進行原始檔控制、共同作業及版本設定等。 一個 Azure Repos Git 組織可以有多個存放庫，但一個 Azure Repos Git 存放庫只能與一個資料處理站建立關聯。 如果您沒有 Azure Repos 組織或存放庫，請依照[這些指示](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)來建立您的資源。

> [!NOTE]
> 您可以將指令碼和資料檔案儲存在 Azure Repos Git 存放庫中。 不過，您必須手動將檔案上載至 Azure 儲存體。 Data Factory 管線不會自動將儲存在 Azure Repos Git 存放庫中的指令碼或資料檔案上傳至「Azure 儲存體」。

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>設定搭配 Azure Data Factory 的 Azure Repos Git 存放庫

您可以透過兩種方法設定搭配資料處理站的 Azure Repos Git 存放庫。

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>設定方法 1:Azure 資料工廠首頁

在 Azure 資料工廠主頁上,選擇 **「設定代碼儲存庫**」 。

![設定 Azure Repos 程式碼存放庫](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>設定方法 2：UX 撰寫畫布
在 Azure 資料工廠 UX 創作畫布中,選擇 **「數據工廠**下拉菜單」,然後選擇 **「設定代碼存儲庫**」。

![設定用於 UX 撰寫的程式碼存放庫設定](media/author-visually/configure-repo-2.png)

這兩種方法都打開存儲庫設置配置窗格。

![設定程式碼存放庫設定](media/author-visually/repo-settings.png)

設定窗格顯示以下 Azure 儲存函式庫設定:

| 設定 | 描述 | 值 |
|:--- |:--- |:--- |
| **存放庫類型** | Azure Repos 程式碼存放庫的類型。<br/> | Azure 開發人員計劃 Git 或 GitHub |
| **Azure Active Directory** | 您的 Azure AD 租用戶名稱。 | `<your tenant name>` |
| **Azure Repos 組織** | 您的 Azure Repos 組織名稱。 您可以在 `https://{organization name}.visualstudio.com` 找到您的 Azure Repos 組織名稱。 您可以[登入您的 Azure Repos 組織](https://www.visualstudio.com/team-services/git/)，以存取 Visual Studio 設定檔和查看您的存放庫與專案。 | `<your organization name>` |
| **ProjectName** | 您的 Azure Repos 專案名稱。 您可以在 `https://{organization name}.visualstudio.com/{project name}` 找到您的 Azure Repos 專案名稱。 | `<your Azure Repos project name>` |
| **儲存庫名稱** | 您的 Azure Repos 程式碼存放庫名稱。 Azure Repos 專案包含 Git 存放庫，可隨著您的專案成長管理原始程式碼。 您可以建立新的存放庫，或使用專案中既有的存放庫。 | `<your Azure Repos code repository name>` |
| **共同作業分支** | 用於進行發佈的 Azure Repos 共同作業分支。 預設情況下,它是`master`。 如果您想要從其他分支發行資源，請變更此設定。 | `<your collaboration branch name>` |
| **根資料夾** | 在您 Azure Repos 共同作業分支中的根資料夾。 | `<your root folder name>` |
| **將現有的 Data Factory 資源匯入存放庫** | 指定是否要從 UX **撰寫畫布**將現有的資料處理站資源匯入到 Azure Repos Git 存放庫。 選取此方塊可將您的資料處理站資源以 JSON 格式匯入到相關聯的 Git 存放庫。 此動作會將每個資源個別匯出 (亦即，已連結的服務和資料集會匯出至個別的 JSON)。 若未選取此方塊，則不會匯入現有資源。 | 已選取 (預設值) |
| **要匯入資源的分支** | 指定要匯入資料處理站資源 (管線、資料集、連結服務等等) 的分支。 您可以將資源匯入下列其中一個分支：a. 共同作業 b. 新建 c. 使用現有的 |  |

> [!NOTE]
> 如果使用 Microsoft Edge,並且 Azure DevOps 帳戶下拉清單中看不到任何值,請將 https://.visualstudio.com 添加到受信任的網站清單中。

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

使用 GitHub 整合所進行的視覺化撰寫，會對資料處理站管線上的工作支援原始檔控制和共同作業功能。 您可以將資料處理站與 GitHub 帳戶存放庫建立關聯，以進行原始檔控制、共同作業及版本設定。 一個 GitHub 帳戶可以有多個存放庫，但一個 GitHub 存放庫只能與一個資料處理站建立關聯。 如果您沒有 GitHub 帳戶或儲存庫,請按照 [這些說明](https://github.com/join) 創建資源。

GitHub 與數據工廠的整合同時支援公共 GitHub(即[https://github.com](https://github.com))和 GitHub 企業版。 只要您具備 GitHub 中存放庫的讀取和寫入權限，就能搭配 Data Factory 使用公用和私人 GitHub 存放庫。

要設定 GitHub 儲存函式庫,您必須具有正在使用的 Azure 訂閱的管理員許可權。

如需此功能的 9 分鐘簡介與示範，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>使用 Azure 資料工廠設定 GitHub 儲存函式庫

您可以透過兩種方法設定搭配資料處理站的 GitHub 存放庫。

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>設定方法 1:Azure 資料工廠首頁

在 Azure 資料工廠主頁上,選擇 **「設定代碼儲存庫**」 。

![設定 Azure Repos 程式碼存放庫](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>設定方法 2：UX 撰寫畫布

在 Azure 資料工廠 UX 創作畫布中,選擇 **「數據工廠**下拉菜單」,然後選擇 **「設定代碼存儲庫**」。

![設定用於 UX 撰寫的程式碼存放庫設定](media/author-visually/configure-repo-2.png)

這兩種方法都打開存儲庫設置配置窗格。

![GitHub 存放庫設定](media/author-visually/github-integration-image2.png)

設定窗格顯示以下 GitHub 儲存函式庫設定:

| **設定** | **說明**  | **ReplTest1**  |
|:--- |:--- |:--- |
| **存放庫類型** | Azure Repos 程式碼存放庫的類型。 | GitHub |
| **使用 GitHub Enterprise** | 選取 GitHub Enterprise 的核取方塊 | 沒有選取(預設) |
| **GitHub Enterprise URL** | GitHub 企業根網址(必須是本地 GitHub 企業伺服器的 HTTPS)。 例如： https://github.mydomain.com 。 僅當選擇**使用 GitHub 企業版**時才必 | `<your GitHub enterprise url>` |                                                           
| **GitHub 帳戶** | 您的 GitHub 帳戶名稱。 可以從 HTcrit 找到此名稱:/github.com/{\/帳戶名稱}/[儲存庫名稱]。 瀏覽到此頁面時，系統會提示您輸入 GitHub 帳戶的 GitHub OAuth 認證。 | `<your GitHub account name>` |
| **儲存庫名稱**  | 您的 GitHub 程式碼存放庫名稱。 GitHub 帳戶包含 Git 存放庫，可用來管理原始程式碼。 您可以建立新的存放庫，或使用帳戶中既有的存放庫。 | `<your repository name>` |
| **共同作業分支** | 用於發行的 GitHub 共同作業分支。 默認情況下,它是主控形狀。 如果您想要從其他分支發行資源，請變更此設定。 | `<your collaboration branch>` |
| **根資料夾** | 在您 GitHub 共同作業分支中的根資料夾。 |`<your root folder name>` |
| **將現有的 Data Factory 資源匯入存放庫** | 指定是否將現有資料工廠資源從 UX 創作畫布導入 GitHub 儲存庫。 選取此方塊可將您的資料處理站資源以 JSON 格式匯入到相關聯的 Git 存放庫。 此動作會將每個資源個別匯出 (亦即，已連結的服務和資料集會匯出至個別的 JSON)。 若未選取此方塊，則不會匯入現有資源。 | 已選取 (預設值) |
| **要匯入資源的分支** | 指定要匯入資料處理站資源 (管線、資料集、連結服務等等) 的分支。 您可以將資源匯入下列其中一個分支：a. 共同作業 b. 新建 c. 使用現有的 |  |

### <a name="known-github-limitations"></a>已知的 GitHub 限制

- 您可以將指令碼和資料檔案儲存在 GitHub 存放庫。 不過，您必須手動將檔案上載至 Azure 儲存體。 Data Factory 管線不會自動將 GitHub 存放庫中儲存的指令碼或資料檔案上傳至 Azure 儲存體。

- 早於 2.14.0 的 GitHub Enterprise 版本不適用於 Microsoft Edge 瀏覽器。

- GitHub 與資料工廠可視化創作工具的整合僅適用於資料工廠的普遍可用版本。

- 每個資源類型最多可以獲取 1,000 個實體(如管道和數據集),可以從單個 GitHub 分支獲取。 如果達到此限制,建議將您的資源拆分為單獨的工廠。 Azure DevOps Git 沒有此限制。

## <a name="switch-to-a-different-git-repo"></a>切換至不同的 Git 存放庫

要切換到其他 Git 儲存庫,請單擊「數據工廠」概述頁面右上角的**Git 儲存庫設置**圖示。 如果看不到該圖示,請清除本地瀏覽器快取。 選取適當圖示以移除與目前存放庫的關聯。

![Git 圖示](media/author-visually/remove-repo.png)

出現「儲存庫設定」窗格後,選擇 **「刪除 Git」。。** 輸入資料工廠名稱,然後單擊 **「確認」** 以刪除與數據工廠關聯的 Git 儲存庫。

![移除與目前 Git 存放庫的關聯](media/author-visually/remove-repo2.png)

刪除與目前儲存庫的關聯後,可以將 Git 設置配置為使用不同的儲存庫,然後將現有數據工廠資源導入到新的儲存庫。 

## <a name="version-control"></a>版本控制

版本控制系統 (也稱為_原始檔控制_) 可讓開發人員在程式碼上共同作業，並追蹤對程式碼基底所進行的變更。 來源控制是多開發人員專案的必要工具。

### <a name="creating-feature-branches"></a>建立要素分支

與資料處理站相關聯的每個 Azure Repos Git 存放庫都有共同作業分支。 (`master` 是預設的共同作業分支)。 使用者還可以通過按一下分支下拉清單中**的新分支**來創建功能分支。 出現新分支窗格后,輸入要素分支的名稱。

![建立新分支](media/author-visually/new-branch.png)

當您準備好將更改從要素分支合併到協作分支時,按下分支下拉,然後選擇 **「創建拉取請求**」。 此動作會將您帶往 Azure Repos Git，您可以在其中發出提取要求、執行程式碼檢閱，以及將變更合併到您的共同作業分支。 (`master` 是預設值)。 您只被允許從您的共同作業分支發佈到 Data Factory 服務。 

![建立新的提取要求](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>設定發佈設定

若要設定發佈分支 (亦即儲存 Resource Manager 範本的分支)，請將 `publish_config.json` 檔案新增至共同作業分支中的根資料夾。 Data Factory 會讀取此檔案、尋找 `publishBranch` 欄位，然後使用所提供的值來建立新分支 (如果尚未存在)。 接著，它會將所有 Resource Manager 範本都儲存到指定的位置。 例如：

```json
{
    "publishBranch": "factory/adf_publish"
}
```

當您指定新的發佈分支時，Data Factory 並不會刪除先前的發佈分支。 如果要刪除以前的發佈分支,請手動刪除它。

> [!NOTE]
> Data Factory 只有在載入處理站時才會讀取 `publish_config.json` 檔案。 如果您已經在入口網站中載入處理站，請重新整理瀏覽器以讓變更生效。

### <a name="publish-code-changes"></a>發佈程式碼變更

合併協作分支的更改(`master`預設值)後,按兩下 **「發布」** 以手動將主分支中的代碼更改發佈到數據工廠服務。

![將變更發佈到 Data Factory 服務](media/author-visually/publish-changes.png)

將在其中打開一個側窗格,確認發佈分支和掛起的更改是正確的。 驗證更改後,單擊"**確定「** 以確認發佈。

![確認正確的發佈分支](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> 主要分支不代表在 Data Factory 服務中部署的內容。 「必須」** 以手動方式將主要分支主發佈至 Data Factory 服務。

## <a name="advantages-of-git-integration"></a>Git 整合的優點

-   **原始碼管理**。 當您的資料處理站工作負載變得更重要時，您可以將您的處理站與 Git 整合，以利用數個原始程式碼控制優點，例如：
    -   追蹤/稽核變更的能力。
    -   還原造成錯誤 (Bug) 之變更的能力。
-   **部分儲存**。 當您在工廠中進行大量更改時,您會意識到,在常規 LIVE 模式下,無法將更改另存為草稿,因為您尚未準備好,或者不希望在電腦崩潰時丟失更改。 使用 Git 整合時，您能繼續以增量方式儲存您的變更，並只在您準備好時才將其發行到處理站。 Git 可做為您工作的暫存位置，直到您測試變更直到滿意為止。
-   **共同作業與控制**. 若您有多個參與相同處理站的小組成員，您可以透過程式碼檢閱程序讓您的小組成員彼此共同作業。 您也可以設定您的處理站，使並非該處理站的所有參與者都有部署到處理站的權限。 您可能讓小組成員可以透過 Git 進行變更，但只有小組中的特定人員可以將變更「發行」到處理站。
-   **顯示差異**。 在 Git 模式下,您可以看到即將發表到工廠的有效負載的一個很好的差異。 此差異會顯示上次發行到處理站之後修改/新增/刪除的所有資源/實體。 根據此差異，您可以繼續發行，或返回並檢查您的變更並稍後再返回。
-   **更好的 CI/CD**。 若您使用 Git 模式，您可以設定您的發行管線，以在開發人員處理站中發生任何變更時儘快自動觸發。 您也可以自訂處理站中的屬性，這些屬性是以 Resource Manager 範本中參數的形式提供。 只保留必要屬性集做為參數並將其他項目以硬式編碼方式撰寫非常實用。
-   **更好的效能**。 在 Git 模式下,平均工廠載入速度比常規 LIVE 模式快十倍,因為資源是透過 Git 下載的。

## <a name="best-practices-for-git-integration"></a>Git 整合的最佳做法

### <a name="permissions"></a>權限

通常,您不希望每個團隊成員都具有更新工廠的許可權。 建議設定以下權限:

*   所有小組成員都應該有資料處理站的唯讀權限。
*   只允許一組選定的人員發佈到工廠。 為此,他們必須在工廠所位於的資源組中具有**數據工廠參與者**角色。 有關權限的詳細資訊,請參閱[Azure 資料工廠的角色和權限](concepts-roles-permissions.md)。
   
建議不允許直接簽入協作分支。 此限制有助於防止 Bug,因為每次簽入都將通過[「創建功能分支](source-control.md#creating-feature-branches)」中描述的拉取請求審閱過程。

### <a name="using-passwords-from-azure-key-vault"></a>使用 Azure 金鑰保存的密碼

建議使用 Azure 密鑰保管庫來儲存數據工廠連結服務的任何連接字串或密碼。 出於安全原因,我們不會在 Git 中存儲任何此類機密資訊,因此對連結服務的任何更改將立即發佈到 Azure 數據工廠服務。

使用密鑰保管庫還使持續集成和部署更容易,因為在資源管理器範本部署期間不必提供這些機密。

## <a name="troubleshooting-git-integration"></a>容錯排除 Git 整合

### <a name="stale-publish-branch"></a>陳舊發佈分支

如果發佈分支與主分支不同步,並且包含最近發佈的過期資源,請嘗試執行以下步驟:

1. 刪除目前 Git 儲存函式庫
1. 使用相同的設定重新設定 Git,但請確保選擇**將現有資料工廠資源匯入儲存庫**並選擇 **「新增分支**」
1. 建立拉取要求以合併到協作分支的變更 

下面是可能導致發佈分支陳舊的一些情況示例:
- 使用者有多個分支。 在一個功能分支中,他們刪除了非 AKV 關聯的連結服務(無論它們是否在 Git 中,都會立即發佈非 AKV 連結服務),並且從未將功能分支合併到協作 brnach 中。
- 使用者使用 SDK 或 PowerShell 修改資料工廠
- 使用者將所有資源移動到新分支,並首次嘗試發佈。 導入資源時,應手動創建連結的服務。
- 用戶手動上載非 AKV 連結服務或整合式執行時 JSON。 它們引用來自其他資源(如數據集、連結服務或管道)的資源。 通過 UX 創建的非 AKV 連結服務將立即發佈,因為憑據需要加密。 如果上載引用該連結服務的數據集並嘗試發佈,UX 將允許它,因為它存在於 git 環境中。 它將在發佈時被拒絕,因為它在數據工廠服務中不存在。

## <a name="provide-feedback"></a>提供意見反應
選取 [意見反應]**** 可為功能加上註解，也可以向 Microsoft 通報工具問題：

![意見反應](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何監視和管理管線，請參閱[以程式設計方式監視和管理管線](monitor-programmatically.md)。
* 要實現持續整合和部署,請參閱[Azure 資料工廠中的連續整合和交付 (CI/CD)。](continuous-integration-deployment.md)
