---
title: 在 Microsoft 商業 marketplace 中設定試用產品
description: 瞭解試用產品。 試用產品可讓新客戶在承諾購買之前，先測試您的供應專案。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/13/2019
ms.openlocfilehash: 8bd17858fa6866549088de8307f40d7b79daef6b
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82786541"
---
# <a name="learn-about-test-drive-for-your-offer"></a>瞭解您供應專案的試用產品

Microsoft 商業 marketplace 中的 [**試用**產品] 選項可讓您設定實際操作、以自我引導的方式流覽您的產品主要功能。 有了試用產品，新的客戶可以在承諾購買之前先試用您的供應專案。 如需詳細資訊，請參閱[什麼是試用產品？](../cloud-partner-portal/test-drive/what-is-test-drive.md)。

如果您不想再為供應專案提供試用產品，請返回 [供應專案**設定**] 頁面，然後取消核取 [**啟用試用**產品]。

## <a name="technical-configuration"></a>技術設定

下列是可用的試用產品類型，每個都有自己的技術設定需求。

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [邏輯應用程式](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) （不需要技術設定）

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure Resource Manager 試用產品的技術設定

有一個部署範本，其中包含組成您解決方案的所有 Azure 資源。 這適用于僅使用 Azure 資源的產品。 深入瞭解如何設定 Azure Resource Manager 的[試用](../cloud-partner-portal/test-drive/azure-resource-manager-test-drive.md)產品。

- **區域**（必要）：目前有26個 Azure 支援的區域，可供使用您的試用產品。 一般來說，您會想要讓試用產品在您預期最大客戶數目的區域中使用，以便他們可以選取最接近的區域，以獲得最佳效能。 您必須確定您的訂用帳戶可以在您所選取的每個區域中部署所需的所有資源。

- **實例**：選取類型（經常性存取或非經常性存取）和可用的實例數目，這會乘以供應專案可用的區域數目。

  - 經常性 **：此**類型的實例已部署，並會在每個選取的區域等待存取。 客戶可以立即存取試用產品的*熱*實例，而不必等待部署。 缺點是，這些執行個體一直在您的 Azure 訂用帳戶中執行，因此將會產生可觀的運作成本。 強烈建議至少有一個*熱*實例，因為大部分的客戶不想等候完整部署，因此如果沒有可用的*熱*實例，客戶使用量就會有下降。

  - **冷**：這種類型的實例代表每個區域可能部署的實例總數。 冷實例需要整個試用產品 Resource Manager 範本，以便在客戶要求試用產品時進行部署，因此*冷*實例的載入速度會比*熱*實例慢很多。 取捨是您只需要支付試用產品的持續時間，而*不*一定會在您的 Azure 訂用帳戶上執行，就像使用*熱*實例一樣。

- **試用產品 Azure Resource Manager 範本**：上傳包含 Azure Resource Manager 範本的 .zip。  若要深入瞭解如何建立 Azure Resource Manager 範本，請參閱[使用 Azure 入口網站建立和部署 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)的快速入門文章。

- **試用產品持續時間**（必要）：輸入試用產品將維持作用中狀態的時間長度（以小時為單位）。 在此持續時間過後，試用產品將會自動終止。 此持續時間可能只會由整數小時數來設定（例如，"2" 小時，"1.5" 無效）。

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 試用產品的技術設定

Microsoft 可以藉由使用這種類型的試用產品來裝載和維護服務布建和部署，藉此降低設定試用產品的複雜性。 無論試用產品是以商業中心、客戶參與或營運物件為目標，此類型的託管試用產品的設定都相同。

- **最大並行試用**產品（必要）：設定一次可以使用試用產品的客戶數目上限。 當試用產品在使用中時，每個並行使用者都會取用 Dynamics 365 授權，因此您必須確保您有足夠的授權可支援所設定的上限。 建議值為 3-5 個。

- **試用產品持續時間**（必要）：藉由定義時數來輸入試用產品保持作用中狀態的時間長度。 過了數小時之後，會話就會結束，而且不再使用您的其中一個授權。 我們建議的值為2-24 小時，視您的供應專案複雜度而定。 此持續時間可能只會由整數小時數來設定（例如，"2" 小時，"1.5" 無效）。  使用者可以要求新的會話（如果工作時間已用盡），並想要再次存取試用產品。

- **實例 url** （必要）：客戶將開始其試用產品的 URL。 通常是您的 Dynamics 365 實例 URL，執行您的應用程式並安裝範例資料（ `https://testdrive.crm.dynamics.com`例如）。

- **實例 Web api url** （必要）：藉由登入您的 Microsoft 365 帳戶並流覽至 [**設定** \&] >，取出 Dynamics 365 實例的 Web API url。**自訂** \&gt;**開發人員資源** \&gt;**實例 WEB API （服務根 URL）**，複製此處找到的 URL （例如， `https://testdrive.crm.dynamics.com/api/data/v9.0`）。

- **角色名稱**（必要）：提供您在自訂 Dynamics 365 試用產品中定義的安全性角色名稱。 這會在其試用產品（例如，測試磁片磁碟機-角色）期間指派給使用者。

### <a name="technical-configuration-for-logic-app-test-drive"></a>邏輯應用程式試用產品的技術設定

任何自訂產品都應該使用此類型的試用產品部署範本，其中包含各種複雜的解決方案架構。 如需設定邏輯應用程式試用產品的詳細資訊，請造訪 GitHub 上的[作業](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md)和[客戶參與](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)。

- **區域**（必要，單一選取下拉式清單）：目前有26個 Azure 支援的區域可供使用您的試用產品。 邏輯應用程式的資源將會部署在您選取的區域中。 如果您的邏輯應用程式有任何自訂資源儲存在特定區域中，請確定已在此處選取該區域。 若要這麼做，最佳方式是在入口網站中的 Azure 訂用帳戶本機上完整部署邏輯應用程式，並確認其正常運作，然後再進行此選取。

- **最大並行試用**產品（必要）：設定一次可以使用試用產品的客戶數目上限。 這些試用產品已部署，讓客戶可以立即存取它們，而不需要等候部署。

- **試用產品持續時間**（必要）：輸入試用產品將維持作用中狀態的時間長度（以小時為單位）。 試用產品會在此時間週期結束後自動終止。

- **Azure 資源組名**（必要）：輸入您的邏輯應用程式試用產品儲存所在的[Azure 資源組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)名。

- **Azure 邏輯應用程式名稱**（必要）：輸入將試用產品指派給使用者的邏輯應用程式名稱。 此邏輯應用程式必須儲存在上述的 Azure 資源群組中。

- 取消布建**邏輯應用程式名稱**（必要）：輸入在客戶完成後取消布建試用產品的邏輯應用程式名稱。 此邏輯應用程式必須儲存在上述的 Azure 資源群組中。

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>不需要 Power BI 試用產品的技術設定

想要示範互動式 Power BI 視覺效果的產品可以使用內嵌連結來共用自訂的儀表板作為其試用產品，而不需要進一步的技術設定。 深入瞭解如何設定[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)範本應用程式。

## <a name="deployment-subscription-details"></a>部署訂用帳戶詳細資料

若要代表您部署試用產品，請建立並提供另一個唯一的 Azure 訂用帳戶。 （不需要用於 Power BI 試用產品）。

- **Azure 訂**用帳戶識別碼（Azure Resource Manager 和邏輯應用程式的必要）：輸入訂用帳戶識別碼，以授與存取權給您的 azure 帳戶服務，以進行資源使用量報告和計費。 我們建議您考慮[建立個別的 Azure 訂](https://docs.microsoft.com/azure/billing/billing-create-subscription)用帳戶，以用於試用產品（如果您還沒有的話）。 您可以藉由登入[Azure 入口網站](https://portal.azure.com/)並流覽至左側功能表的 [**訂閱**] 索引標籤，來尋找您的 AZURE 訂用帳戶識別碼。 選取此索引標籤會顯示您的訂用`a83645ac-1234-5ab6-6789-1h234g764ghty`帳戶識別碼（例如）。

- **Azure AD 租使用者識別碼**（必要）：輸入您的 AZURE ACTIVE DIRECTORY （AD）[租使用者識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要尋找此識別碼，請登入[Azure 入口網站](https://portal.azure.com/)，選取左側功能表中的 [Active Directory] 索引標籤，選取 [**屬性**]，然後尋找列出的**目錄識別碼**（例如`50c464d3-4930-494c-963c-1e951d15360e`，）。 您也可以在下列位置使用您的功能變數名稱 URL 來查詢組織的租使用者[https://www.whatismytenantid.com](https://www.whatismytenantid.com)識別碼：。

- **Azure AD 租使用者名稱**（動態365所需）：輸入您的 AZURE ACTIVE DIRECTORY （AD）名稱。 若要尋找此名稱，請登入[Azure 入口網站](https://portal.azure.com/)，在右上角，您的租使用者名稱會列在 [您的帳戶名稱] 底下。

- **Azure AD 應用程式識別碼**（必要）：輸入您的 AZURE ACTIVE DIRECTORY （AD）[應用程式識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要尋找此識別碼，請登入[Azure 入口網站](https://portal.azure.com/)，選取左側功能表中的 [Active Directory] 索引標籤，選取 [**應用程式註冊**]，然後尋找所列的**應用程式識別碼**編號（例如， `50c464d3-4930-494c-963c-1e951d15360e`）。

- **Azure AD 應用程式用戶端密碼**（必要）：輸入您的 Azure AD 應用程式[用戶端密碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)。 若要尋找此值，請登入[Azure 入口網站](https://portal.azure.com/)。 選取左側功能表中的 [ **Azure Active Directory** ] 索引標籤，選取 [**應用程式註冊**]，然後選取您的試用產品應用程式。 接下來，依序選取 [**憑證和秘密**]、[**新增用戶端密碼**]、[描述]、[**永不****過期**]，然後選擇 [**新增**]。 請務必向下複製值。 （在執行此動作之前，請不要離開頁面，否則您將無法存取此值。）

繼續進行下一節之前，請記得先**儲存**！

## <a name="test-drive-listings-optional"></a>試用產品清單（選擇性）

在 [**試用**產品] 索引標籤下找到的 [**試用產品清單**] 選項會顯示您的試用產品可供使用的語言（和市場），目前的英文（美國）是唯一可用的位置。 此外，此頁面會顯示特定語言清單的狀態，以及它所加入的日期/時間。 您必須為每個語言/市場定義試用產品的詳細資料（描述、使用者手冊、影片等等）。

- **描述**（必要）：描述您的試用產品、要示範的專案、要進行實驗的目標、探索的功能，以及可協助使用者決定是否要取得您供應專案的任何相關資訊。 最多可以在此欄位中輸入3000個字元的文字。

- **存取訊號**（Azure Resource Manager 和邏輯試用產品所需）：說明客戶必須知道才能存取和使用此試用產品的內容。 逐步解說使用您的供應專案的案例，以及客戶在整個試用產品中存取功能的確切方式。 最多可以在此欄位中輸入10000個字元的文字。

- **使用者手冊**（必要）：您的試用產品體驗的深入逐步解說。 使用者手冊應涵蓋您想要讓客戶從試用產品中取得的確切內容，並做為參考，以取得他們可能會遇到的任何問題。 檔案必須是 PDF 格式，並在上傳後命名為（最多255個字元）。

- 影片 **：新增**影片（選擇性）：您可以將影片上傳到 YouTube 或 Vimeo，並在這裡參考連結和縮圖影像（533 x 324 圖元），讓客戶可以查看資訊的逐步解說，以協助他們深入瞭解試用產品，包括如何成功使用供應專案的功能，並瞭解強調其優點的案例。
  - **名稱**（必要）
  - **URL （僅限 YouTube 或 Vimeo）** （必要）
  - **縮圖（533 x 324 px）** -影像檔案必須是 PNG 格式。

完成這些欄位之後，請選取 [**儲存**]。

## <a name="next-steps"></a>後續步驟

[更新商業市集中的現有供應項目](./update-existing-offer.md)
