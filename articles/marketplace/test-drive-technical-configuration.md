---
title: 試用產品技術設定，Microsoft 商業 marketplace
description: 瞭解試用產品。 試用產品可讓新客戶在承諾購買之前，先測試您的供應專案。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: 9c4bd4fb5fc6a25027c2f01466a0d5afc1694b03
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121481"
---
# <a name="test-drive-technical-configuration"></a>試用產品技術設定

Microsoft 商業 marketplace 中的 [試用產品] 選項可讓您設定實際操作、以自我引導的方式流覽您的產品主要功能。 有了試用產品，新的客戶可以在承諾購買之前先試用您的供應專案。 如需詳細資訊，請參閱[什麼是試用產品？](what-is-test-drive.md)

如果您不想再提供供應專案的試用產品，請返回 [供應專案**設定**] 頁面，並清除 [**啟用測試磁片**] 核取方塊。 並非所有供應專案類型都有可用的試用產品。

## <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager 試用產品

這是虛擬機器或 Azure 應用程式供應專案的唯一試用產品選項，而且也需要相當詳細的設定。 閱讀下列各節以取得[部署訂用帳戶詳細資料](#deployment-subscription-details)和試用產品[清單](#test-drive-listings)，然後繼續進行[Azure Resource Manager 試用](azure-resource-manager-test-drive.md)產品設定的個別主題。

## <a name="hosted-test-drive"></a>託管的試用產品

Microsoft 可以透過使用此類型的試用產品，來裝載和維護服務佈建和部署，藉此降低設定試用產品的複雜性。 無論試用產品是以 Dynamics 365 Business Central、Dynamics 365 Customer Engagement 或 Dynamics 365 Operations 物件為目標，此類型的託管試用產品的設定都相同。

- **同時試用產品數上限** (必要) – 設定一次可以使用試用產品的客戶數上限。 當試用產品在使用中時，每個並行使用者都會取用 Dynamics 365 授權，因此請確定您有足夠的授權可支援所設定的上限。 建議值是 3 至 5。

- **試用產品持續時間** (必要) - 輸入試用產品會保持作用的時數。 此時間過後，工作階段就會結束且不再占用您的授權。 建議值為 2 至 24 小時，視您供應項目的複雜度而定。 此持續時間只能以一整個小時為單位來進行設定 (例如，「2」小時有效；「1.5」小時則無效)。 若使用者時間耗盡且想要再次存取試用產品，可以要求新的工作階段。

- **執行個體 URL** (必要) – 客戶開始試用產品的網址。 這通常是您的應用程式與範例資料安裝所在的 Dynamics 365 執行個體的 URL (例如，`https://testdrive.crm.dynamics.com`)。

- **實例 Web api url** （必要）-若要取出 Dynamics 365 實例的 Web API url，請登入您的 Microsoft 365 帳戶，然後流覽至 [**設定**] [  >  **自訂**]  >  [**開發人員資源**]  >  **[實例 Web API （服務根目錄 URL）**]，複製這裡找到的 URL （例如 `https://testdrive.crm.dynamics.com/api/data/v9.0` ）。

- **角色名稱** (必要) -提供您在自訂 Dynamics 365 試用產品中定義的安全性角色名稱，這將在其試用產品期間指派給使用者 (例如 test-drive-role)。

如需如何設定適用于試用產品的 Dynamics 365 環境，以及授與 AppSource 許可權以在您的租使用者中布建和取消布建試用產品的相關說明，請遵循[這些指示](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)。

## <a name="logic-app-test-drive"></a>邏輯應用程式試用產品

這種類型的試用產品不是由 Microsoft 託管。 使用它來連接 Dynamics 365 供應專案或其他自訂資源，其中包含各種複雜的解決方案架構。 如需設定邏輯應用程式試用產品的詳細資訊，請造訪 GitHub 上的[作業 (英文)](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md)和[客戶參與 (英文)](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)。

- **區域** (必要，單一選項下拉式清單) - 目前有 26 個 Azure 支援的區域可提供試用產品。 邏輯應用程式的資源將會部署在您所選區域中。 如果邏輯應用程式有任何儲存在特定區域中的自訂資源，請務必在此處選取該區域。 最佳方法是在入口網站中您的 Azure 訂閱上，於本機中完整部署邏輯應用程式，並確認其運作正常，然後再進行此選擇。

- **同時試用產品數上限** (必要) – 設定一次可以使用試用產品的客戶數上限。 這些試用產品已部署完成，客戶可以立即存取而不需等候部署。

- **試用產品持續時間** (必要) – 輸入試用產品將維持可用的持續時間，以 # 小時數表示。 在此持續時間過後，試用產品將會自動終止。

- **Azure 資源組名**（必要）–輸入您的邏輯應用程式試用產品儲存所在的[azure 資源群組](../azure-resource-manager/resource-group-overview.md#resource-groups)）名稱。

- **Azure 邏輯應用程式名稱** (必要) – 輸入將試用產品指派給使用者的邏輯應用程式名稱。 此邏輯應用程式必須儲存在上述的 Azure 資源群組中。

- 取消布建**邏輯應用程式名稱**（必要）–輸入當客戶完成後，解除布建試用產品的邏輯應用程式名稱。 此邏輯應用程式必須儲存在上述的 Azure 資源群組中。

## <a name="power-bi-test-drive"></a>Power BI 試用產品

若需要示範產品的互動式 Power BI 視覺效果，可以使用內嵌連結共用自訂建置儀表板作為其試用產品，無須進一步的技術設定。 您只需要上傳內嵌的 Power BI URL 就可以了。

如需設定 Power BI 應用程式的詳細資訊，請參閱[什麼是 Power BI 應用程式？](https://docs.microsoft.com/power-bi/service-template-apps-overview)

## <a name="deployment-subscription-details"></a>部署訂閱詳細資料

若要讓 Microsoft 能夠代表您部署試用產品，請提供另外建立的唯一 Azure 訂用帳戶 (Power BI 試用產品則不需要)。

- **Azure 訂閱識別碼** (針對 Azure Resource Manager 和邏輯應用程式為必要) – 輸入訂閱識別碼以授與您的 Azure 帳戶服務存取權，並用於資源使用量報告和計費。 建議您考慮[建立個別的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)，以便用於試用產品 (如果您沒有訂閱的話)。 您可以透過登入 [Azure 入口網站](https://portal.azure.com/)並瀏覽到左側功能表中的 [訂閱] 索引標籤，以尋找 Azure 訂用帳戶識別碼。 選取此索引標籤會顯示您的訂閱識別碼 (例如「a83645ac-1234-5ab6-6789-1h234g764ghty」)。

- **Azure AD 租使用者識別碼**（必要）–輸入您的 AZURE ACTIVE DIRECTORY （AD）[租使用者識別碼](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)）。 若要尋找此識別碼，請登入 [Azure 入口網站](https://portal.azure.com/)，選取左側功能表中的 [Active Directory] 索引標籤，選取 [屬性]，然後尋找列出的 [目錄識別碼] 號碼 (例如 50c464d3-4930-494c-963c-1e951d15360e)。 您也可以在 [https://www.whatismytenantid.com](https://www.whatismytenantid.com) 中，使用網域名稱位址來查閱組織的租用戶識別碼。

- **Azure AD 租用戶識別碼** (針對 Dynamic 365 為必要) – 輸入您的 Azure Active Directory (AD) 名稱。 若要尋找此名稱，請登入 [Azure 入口網站](https://portal.azure.com/)，位於右上角的租用戶名稱會列在您的帳戶名稱底下。

- **Azure AD 應用程式識別碼**（必要）–輸入您的 AZURE ACTIVE DIRECTORY （AD）[應用程式識別碼](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)）。 若要尋找此識別碼，請登入[Azure 入口網站](https://portal.azure.com/)，選取左側功能表中的 [Active Directory] 索引標籤，選取 [**應用程式註冊**]，然後尋找所列的**應用程式識別碼**編號（例如 `50c464d3-4930-494c-963c-1e951d15360e` ）。

- **Azure AD 應用程式用戶端密碼**（必要）–輸入您的 Azure AD 應用程式[用戶端密碼](../active-directory/develop/howto-create-service-principal-portal.md#create-a-new-application-secret)）。 若要尋找此值，請登入 [Azure 入口網站](https://portal.azure.com/)。 在左側功能表中，選取 [Azure Active Directory] 索引標籤，選取 [應用程式註冊]，然後選擇您的試用產品。 接下來，選取 [憑證和密碼]、選取 [新用戶端密碼]、輸入描述，選取 [到期] 底下的 [永不]，然後選擇 [新增]。 請務必記下此值。 複製值之前，請不要離開頁面。

## <a name="test-drive-listings"></a>試用產品清單

在 [合作夥伴中心] 的 [**試用**產品] 索引標籤下找到的 [**試用產品清單**] 選項，會顯示您的試用產品可用的語言（和市場），目前的英文（美國）是唯一可用的位置。 此外，此頁面會顯示特定語言清單的狀態，以及新增清單的日期/時間。 您必須為每個語言/市場定義試用產品的詳細資料（描述、使用者手冊、影片等等）。

- **描述**（必要）：描述您的試用產品、要示範的專案、要進行實驗的目標、探索的功能，以及可協助使用者決定是否要取得您供應專案的任何相關資訊。 最多可以在此欄位中輸入 3,000 個字元的文字。

- **存取訊號**（Azure Resource Manager 和邏輯試用產品所需）：說明客戶必須知道才能存取和使用此試用產品的內容。 逐步解說使用供應項目的案例，以及客戶在完整試用產品階段中，應該知道的確切存取功能。 最多可以在此欄位中輸入 10,000 個字元的文字。

- **使用者手冊**（必要）：您的試用產品體驗的深入逐步解說。 使用者手冊應涵蓋您想要讓客戶從試用產品中獲得的確切內容，並做為客戶可能會遇到的任何問題的參考。 檔案必須是 PDF 格式，並在上傳後命名 (最多 255 個字元)。

- 影片 **：新增**影片（選擇性）：此處所裝載的影片可以透過連結和縮圖影像（533 x 324 圖元）加以參考，讓客戶可以查看資訊的逐步解說，以協助他們深入瞭解試用產品，包括如何成功使用供應專案的功能，並瞭解強調其優點的案例。
  - **名稱** (必要)
  - **URL** （僅限 YouTube 或 Vimeo; 必要）
  - **縮圖**（533 x 324 圖元）–影像必須是 PNG 格式。

如果您目前是在合作夥伴中心建立試用產品，請選取 [**儲存草稿**] 再繼續。

## <a name="next-step"></a>後續步驟

- [更新商業市集中的現有供應項目](partner-center-portal/update-existing-offer.md)
