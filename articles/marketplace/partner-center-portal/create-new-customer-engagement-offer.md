---
title: 在商業 Marketplace 中建立新的 Dynamics 365 for Customer Engagement & PowerApps 供應專案
description: 如何使用 Microsoft 合作夥伴中心的商業 Marketplace 入口網站，建立新的 Dynamics 365 for Customer Engagement & PowerApps 供應專案，以便在 Azure Marketplace、AppSource 或透過雲端解決方案提供者（CSP）方案中進行列出或銷售。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 0ec62095f14b38d0c4ffebfabc14c4a0a27a70fb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869832"
---
# <a name="create-a-new-dynamics-365-for-customer-engagement--powerapps-offer"></a>為客戶參與 & PowerApps 供應專案建立新的 Dynamics 365

本主題說明如何建立新的 Dynamics 365 for Customer Engagement & PowerApps 供應專案。 Dynamics 365 for Customer Engagement （PowerApps、銷售、服務、專案服務和現場服務）的所有應用程式都必須經過我們的認證流程，並支援試用版體驗。 認證程序會檢查您的解決方案是否符合標準需求、相容性和適當的做法。 試用體驗可讓使用者將解決方案部署至即時 Dynamics 365 環境。

若要開始建立 Dynamics 365 for Customer Engagement & PowerApps 供應專案，請務必先[建立合作夥伴中心帳戶](./create-account.md)並開啟 [[商業 Marketplace] 儀表板](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)，並選取 [**總覽**] 索引標籤。

![合作夥伴中心的商業 Marketplace 儀表板](./media/new-offer-overview.png)

>[!Note]
> 發行供應專案之後，在 [合作夥伴中心] 中對供應專案所做的編輯，只會在系統中更新，並在重新發佈之後儲存。 進行變更之後，請確定您提交發行集的供應專案。

## <a name="create-a-new-offer"></a>建立新的供應項目

選取 [ **+ 新增供應**專案] 按鈕，然後選取 [ **Dynamics 365 for Customer Engagement & PowerApps** ] 功能表項目。 [**新增供應**專案] 對話方塊隨即出現。

### <a name="offer-id-and-alias"></a>供應專案識別碼和別名

- **供應專案 ID**：您帳戶中每個供應專案的唯一識別碼。 在 marketplace 供應專案的 URL 位址中，客戶會看到此識別碼，而 Azure Resource Manager 範本（如果適用）。 供應專案識別碼必須是小寫的英數位元（包括連字號和底線，但不能有空格）。 識別碼限制為50個字元，且在您選取 [**建立**] 之後無法變更。 例如，如果您在這裡輸入 [*測試-供應專案-1* ]，則供應`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`專案 URL 會是。

- **供應專案別名**：用來參照合作夥伴中心內供應專案的名稱。 此名稱不會在 marketplace 中使用，而且與供應專案名稱和其他將向客戶顯示的值不同。 在您選取 [**建立**] 之後，就無法變更這個值。

輸入**供應專案識別碼**和供應專案**別名**之後，請選取 [**建立**]。 接著，您就能夠處理供應專案的所有不同部分。

## <a name="offer-setup"></a>供應專案設定

[**供應專案設定**] 頁面會要求您提供下列資訊。 完成這些欄位之後，請務必選取 [**儲存**]。

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>您要如何讓潛在客戶與此清單供應專案互動？

選取您想要用於此供應專案的選項。

#### <a name="get-it-now-free"></a>立即取得（免費）

提供可存取您應用程式的有效 URL （從*HTTP*或*HTTPs*開始），免費向客戶列出您的供應專案。  例如：`https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>免費試用（清單）

藉由提供有效的 URL （從*HTTP*或*HTTPs*開始），向客戶列出您的供應專案，以取得試用版的連結。  例如： `https://contoso.com/trial/my-app` 。 供應專案列出免費試用版是由您的服務所建立、管理及設定，而且沒有由 Microsoft 管理的訂用帳戶。

> [!NOTE]
> 您的應用程式將透過試用連結接收的權杖，只能透過 Azure Active Directory （Azure AD）取得使用者資訊，以在您的應用程式中自動建立帳戶。 Microsoft 帳戶不支援使用此權杖進行驗證。

#### <a name="contact-me"></a>與我連絡

藉由連接客戶關係管理（CRM）系統來收集客戶連絡人資訊。 系統會要求客戶提供共用其資訊的許可權。 這些客戶詳細資料以及他們找到您供應專案的供應專案名稱、識別碼和 marketplace 來源，都會傳送至您所設定的 CRM 系統。 如需設定 CRM 的詳細資訊，請參閱[連接潛在客戶管理](#connect-lead-management)。 

### <a name="test-drive"></a>試用產品

試用產品是向潛在客戶展示您的供應專案的絕佳方式，方法是讓他們選擇「在購買前試用」，進而提升轉換並產生高度合格的潛在客戶。 [深入瞭解試用產品。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

若要啟用試用產品，請核取 [**啟用試用**產品] 方塊。 接著，您必須在試用產品的[技術](#test-drive-technical-configuration)設定中設定示範環境，讓客戶可以在一段固定時間內試用您的供應專案。 

#### <a name="type-of-test-drive"></a>試用產品的類型

然後選取下列選項：

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)**：部署範本，其中包含構成解決方案的所有 Azure 資源。 符合此案例的產品只會使用 Azure 資源。
- **[Dynamics 365 For Business central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)**： Microsoft 主控並維護 business central 企業資源規劃系統（財務、營運、供應鏈、CRM 等）的試用產品服務（包括布建和部署）。  
- **[Dynamics 365 For Customer engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)**： Microsoft 會託管並維護客戶參與系統（銷售、服務、專案服務、現場服務等）的試用產品服務（包括布建和部署）。  
- **[適用于作業的 Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)**： Microsoft 會主控並維護財務和營運企業資源規劃系統（財務、營運、製造、供應鏈等）的試用產品服務（包括布建和部署）。 
- **[邏輯應用程式](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)**：包含所有複雜解決方案架構的部署範本。 任何自訂產品都應該使用這種類型的試用產品。
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)**：自訂建立儀表板的內嵌連結。 想要示範互動式 Power BI 視覺效果的產品應該使用這種類型的試用產品。 您只需要上傳內嵌的 Power BI URL。

#### <a name="additional-test-drive-resources"></a>其他試用產品資源

- [試用技術最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [試用產品行銷最佳做法](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [試用產品總覽一頁](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>連接潛在客戶管理

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

如需詳細資訊，請參閱[潛在客戶管理總覽](./commercial-marketplace-get-customer-leads.md)。

請記得先**儲存**再繼續進行下一節。

## <a name="properties"></a>屬性

[**屬性**] 頁面可讓您定義用來在 marketplace 上分組供應專案的類別和產業、應用程式版本，以及支援供應專案的法律合約。 完成此頁面後，選取 [**儲存**]。

### <a name="category"></a>類別

選取最少1個，最多三個類別。 **類別**將用來將您的供應專案放入適當的 marketplace 搜尋區域。 請務必在供應專案描述中，呼叫您的供應專案如何支援這些類別。

### <a name="industry"></a>業界

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>適用的 Dynamics 365 產品

選取此供應專案適用的所有 Dynamics 365 產品。

### <a name="app-version"></a>應用程式版本

輸入供應專案的版本號碼。 客戶會看到此版本列在供應專案的詳細資料頁面上。 如果您只是因為行銷/描述性變更而更新版本號碼，請核取 [**僅限行銷] 變更**框。 此選項可讓供應專案略過認證和布建階段。

### <a name="terms-and-conditions"></a>條款及條件

在 [**條款及條件**] 欄位中提供您自己的法律條款與條件。 您也可以提供 URL，以便找到您的條款及條件。 客戶必須接受這些條款，才能試用您的供應專案。

## <a name="offer-listing"></a>供應專案清單

[供應專案清單] 頁面會顯示您的供應專案列出的語言。 目前，**英文（美國）** 是唯一可用的選項。

您將需要針對每個語言/市場定義 marketplace 詳細資料（供應專案名稱、描述、影像等）。 選取語言/市場名稱來提供此資訊。

> [!NOTE]
> 供應專案清單內容（例如描述、檔、螢幕擷取畫面、使用規定等）不一定要使用英文，只要供應專案的描述開頭為片語，「此應用程式僅適用于 [非英文語言]。」 您也可以提供*有用的連結 URL*來提供內容，而不是供應專案清單內容所使用的語言。

### <a name="name"></a>Name

您在此處輸入的名稱會向客戶顯示，做為供應專案清單的標題。 當您建立供應專案時，此欄位會預先填入您在**供應專案別名**中輸入的文字，但您可以變更此值。 此名稱可能是商標（您可以包含商標或著作權符號）。 名稱不能超過50個字元，而且不能包含任何 emoji。

### <a name="short-description"></a>簡短描述

提供供應專案的簡短描述（最多100個字元）。 此描述可用於 marketplace 搜尋結果。











### <a name="description"></a>描述

提供供應專案的較長描述（最多3000個字元）。 在 marketplace 清單總覽中，客戶會看到這項描述。 包含您供應專案的價值主張、主要優點、類別及/或產業關聯、應用程式內購買機會，以及任何必要的公開。

撰寫描述的一些秘訣：  

- 在您的描述中的前幾個句子清楚描述您供應項目的價值主張。 在您的價值主張中包含下列資訊：
  - 產品的描述
  - 受益于產品的使用者類型
  - 客戶需要或痛苦的產品位址
- 請記住，前幾個句子可能會顯示在搜尋引擎結果中。  
- 請勿依賴特色與功能來銷售您的產品。 反之，要鎖定在您所提供的價值。  
- 請盡可能使用業界特定詞彙或凸顯優點的字眼。
- 請考慮使用 HTML 標籤來格式化您的描述，使其更吸引人。

若要讓您的供應專案描述更吸引人，請使用 rtf 編輯器來格式化您的描述。

![使用 rich 文字編輯器](./media/text-editor2.png)

使用下列指示來使用 rich 文字編輯器：

- 若要變更內容的格式，請反白顯示您想要格式化的文字，然後選取文字樣式，如下所示：

     ![使用 rich 文字編輯器來變更文字格式](./media/text-editor3.png)

- 若要將專案符號或編號清單新增至文字，請使用下列選項：

     ![使用 rich 文字編輯器加入清單](./media/text-editor4.png)

- 若要新增或移除文字的縮排，請使用下列選項：

     ![使用 rich 文字編輯器縮排](./media/text-editor5.png)







### <a name="search-keywords"></a>搜尋關鍵字

您可以選擇性地輸入最多三個搜尋關鍵字，以協助客戶在 marketplace 中尋找您的供應專案。 為了獲得最佳結果，請在您的描述中也嘗試使用這些關鍵字。

### <a name="products-your-app-works-with"></a>與您的應用程式一起運作的產品

如果您想要讓客戶知道您的應用程式可以使用特定產品，請在這裡輸入最多三個產品名稱。

### <a name="support-urls"></a>支援 Url

本節可讓您提供連結，以協助客戶深入瞭解您的供應專案。

#### <a name="help-link"></a>說明連結

輸入客戶可以深入瞭解您供應專案的 URL。

#### <a name="privacy-policy-url"></a>隱私權原則 URL

輸入您組織隱私權原則的 URL。 貴使用者必須負責確保您的應用程式符合隱私權法律和法規，以及提供有效的隱私權原則。

### <a name="contacts"></a>連絡人

在本節中，您必須提供**支援連絡人**的名稱、電子郵件和電話號碼，以及**工程連絡人**。 此資訊不會向客戶顯示，但可供 Microsoft 使用，並可提供給 CSP 合作夥伴。

在**支援連絡人**區段中，您也必須提供**支援 URL** ，讓 CSP 合作夥伴可以在其中找到您的供應專案支援。

### <a name="supporting-documents"></a>支援檔

您必須在此提供至少一個（及最多三個）相關的行銷檔，例如白皮書、摺頁冊、檢查清單或簡報。 這些檔必須是 .pdf 格式。

### <a name="marketplace-images"></a>Marketplace 映射

在本節中，您可以提供向客戶顯示供應專案時所要使用的標誌和影像。 所有影像都必須是 .png 格式。

>[!Note]
>如果您在上傳檔案時發生問題，請確定您的區域網路不會https://upload.xboxlive.com封鎖合作夥伴中心所使用的服務。

#### <a name="store-logos"></a>Microsoft Store標誌。

您可以提供下列三種大小的供應專案標誌：**小型（48 x 48）**、**大型（216 x 216）** 和**寬（255 x 115）**。 需要小型和大型大小。

#### <a name="hero"></a>主圖

主圖影像是選擇性的。 如果您提供一個，則必須測量 815 x 290 圖元。

#### <a name="screenshots"></a>螢幕擷取畫面

新增螢幕擷取畫面，以顯示供應專案的運作方式。 至少需要一個螢幕擷取畫面，而且您最多可以加上五個。 所有螢幕擷取畫面都必須是 1280 x 720 圖元。

#### <a name="videos"></a>影片

您可以選擇性地新增最多四個示範您供應專案的影片。 這些影片應裝載于 YouTube 和/或 Vimeo。 針對每一個，輸入影片的名稱、其 URL 和影片的縮圖影像（1280 x 720 圖元）

#### <a name="additional-marketplace-listing-resources"></a>其他 marketplace 清單資源

- [Marketplace 供應專案清單的最佳做法](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>可用性

[**可用性**] 頁面可讓您選擇要提供供應專案的位置和方式。

### <a name="markets"></a>市場

此區段可讓您指定供應專案可供使用的市場。 若要這麼做，請選取 [**編輯市場**]，這將會顯示 [**市場選擇**] 快顯視窗。

根據預設，不會選取任何市場，但您必須至少選取一個市場，才能發佈您的供應專案。 按一下 [**全選**]，讓您的供應專案在每個可能的市場推出，或選取您想要新增的特定市場。 完成後，請選取 [**儲存**]。

這裡的選擇僅適用于新的收購;如果有人在某個市場上已經有您的應用程式，而您後來又移除該市場，則該市場中已經有該供應專案的人員可以繼續使用它，但該市場中沒有新的客戶能夠取得您的供應專案。

> [!IMPORTANT]
> 即使這些需求並未列于此處或合作夥伴中心，您仍須負責符合任何當地法律需求。 即使您選取 [所有市場]，當地法律和限制或其他因素可能會導致某些國家/地區不會列出特定優惠。

### <a name="preview-audience"></a>預覽物件

將您的供應專案即時發佈到更廣泛的 marketplace 供應專案之前，您必須先將其提供給有限的**預覽物件**。 在這裡輸入**隱藏索引鍵**（僅使用小寫字母和/或數位的任何字串）。 預覽物件的成員可以使用此隱藏金鑰做為權杖，以在 marketplace 中查看供應專案的預覽。

然後，當您準備好讓供應專案可供使用並移除預覽限制時，您將需要移除**隱藏金鑰**，然後再發佈一次。

## <a name="technical-configuration"></a>技術設定

[**技術**設定] 頁面會定義用來連線到您的供應專案的技術詳細資料。 此連線可讓我們為終端客戶布建您的供應專案（如果他們選擇取得）。

### <a name="base-license-model"></a>基本授權模型

基本授權模型會決定客戶如何在 CRM 系統管理中心指派您的應用程式。 針對 [以實例為基礎的授權] 選取 [**資源**]，如果每個租**使用者**指派授權一項

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>需要 S2S 輸出和 CRM 安全存放區存取

核取此方塊，以啟用 CRM 安全存放區或伺服器對伺服器（S2S）輸出存取的設定。 這項功能需要 Dynamics 365 小組在認證階段期間的特殊考量。 Microsoft 會連絡您完成其他步驟，以支援這項功能。

### <a name="application-configuration-url"></a>應用程式設定 URL

提供設定網頁的 URL，讓客戶能夠設定您的應用程式。

### <a name="crm-package"></a>CRM 套件

在 [**您的套件位置的 url** ] 欄位中，輸入包含上傳的 CRM 封裝 .zip 檔案之 Azure 儲存體帳戶的 url。 此 URL 應包含唯讀 SAS 金鑰，以允許 Microsoft 收取您的套件以進行驗證。

核取 [**我的套件檔案中有一個以上的 CRM 套件**] 方塊（如果有的話）。 若是如此，請務必將所有套件包含在您的 .zip 檔案中。

### <a name="crm-package-availability"></a>CRM 套件可用性

在本節中，選取 [ **+ 新增區域**]，以指定可供客戶使用 CRM 套件的地理區域。 部署到下列主權區域需要在認證程式期間進行特殊許可權和驗證：[德國](https://docs.microsoft.com/azure/germany/)、[美國政府雲端](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)和秘訣。

根據預設，您在上方輸入的**應用程式設定 URL**將會用於每個區域。 如果您想要的話，可以針對一或多個特定區域輸入個別的應用程式設定 URL。 

## <a name="test-drive-technical-configuration"></a>試用產品技術設定

如果您在 [[供應專案設定](#offer-setup)] 頁面中選取 [**啟用試用**產品]，您必須在這裡提供詳細資料，讓客戶體驗您供應專案的試用產品。

[**試用**產品] 頁面可讓您設定示範（或「試用產品」），讓客戶可以在承諾購買之前先試用您的供應專案。 若要深入瞭解，請參閱[什麼是試用產品？](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)一文。 如果您不想再為供應專案提供試用產品，請返回 [供應專案**[設定](#offer-setup)**] 頁面，然後取消核取 [**啟用試用**產品]。

下列是可用的試用產品類型，每個都有自己的技術設定需求。

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [邏輯應用程式](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) （不需要技術設定）

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure Resource Manager 試用產品的技術設定

部署範本，其中包含組成您解決方案的所有 Azure 資源。 符合此案例的產品只會使用 Azure 資源。 深入瞭解如何設定 Azure Resource Manager 的[試用](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)產品。

- **區域**（必要）：目前有26個 Azure 支援的區域，可供使用您的試用產品。 一般來說，您會想要讓試用產品在您預期最大客戶數目的區域中使用，以便他們可以選取最接近的區域，以獲得最佳效能。 您必須確定您的訂用帳戶可以在您所選取的每個區域中部署所需的所有資源。

- **實例**：選取類型（經常性存取或非經常性存取）和可用的實例數目，這會乘以供應專案可用的區域數目。

經常性 **：此**類型的實例已部署，並會在每個選取的區域等待存取。 客戶可以立即存取試用產品的*熱*實例，而不必等待部署。 缺點是，這些執行個體一直在您的 Azure 訂用帳戶中執行，因此將會產生可觀的運作成本。 強烈建議至少有一個*熱*實例，因為大部分的客戶不想等候完整部署，因此如果沒有可用的*熱*實例，客戶使用量就會有下降。

**冷**：這種類型的實例代表每個區域可能部署的實例總數。 冷實例需要整個試用產品 Resource Manager 範本，以便在客戶要求試用產品時進行部署，因此*冷*實例的載入速度會比*熱*實例慢很多。 取捨是您只需要支付試用產品的持續時間，而*不*一定會在您的 Azure 訂用帳戶上執行，就像使用*熱*實例一樣。

- **試用產品 Azure Resource Manager 範本**：上傳包含 Azure Resource Manager 範本的 .zip。  若要深入瞭解如何建立 Azure Resource Manager 範本，請參閱[使用 Azure 入口網站建立和部署 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)的快速入門文章。

- **試用產品持續時間**（必要）：輸入試用產品將維持作用中狀態的時間長度（以小時為單位）。 在此持續時間過後，試用產品將會自動終止。 此持續時間可能只會由整數小時數（例如，"2" 小時）設定。"1.5" 無效）。

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 試用產品的技術設定

Microsoft 可以藉由使用這種類型的試用產品來裝載和維護服務布建和部署，藉此降低設定試用產品的複雜性。 無論試用產品是以商業中心、客戶參與或營運物件為目標，此類型的託管試用產品的設定都相同。

- **最大並行試用**產品（必要）：設定一次可以使用試用產品的客戶數目上限。 當試用產品在使用中時，每個並行使用者都會取用 Dynamics 365 授權，因此您必須確保您有足夠的授權可支援所設定的上限。 建議值為 3-5 個。

- **試用產品持續時間**（必要）：藉由定義時數來輸入試用產品保持作用中狀態的時間長度。 過了數小時之後，會話就會結束，而且不再使用您的其中一個授權。 我們建議的值為2-24 小時，視您的供應專案複雜度而定。 此持續時間可能只會由整數小時數（例如，"2" 小時）設定。"1.5" 無效）。 使用者可以要求新的會話（如果工作時間已用盡），並想要再次存取試用產品。

- **實例 url** （必要）：客戶將開始其試用產品的 URL。 通常是您的 Dynamics 365 實例 URL，執行您的應用程式並安裝範例資料（ `https://testdrive.crm.dynamics.com`例如）。

- **實例 Web api url** （必要）：藉由登入您的 Microsoft 365 帳戶並流覽至 [**設定** \&] >，取出 Dynamics 365 實例的 Web API url。**自訂** \&gt;**開發人員資源** \&gt;**實例 WEB API （服務根 URL）**，複製此處找到的 URL （例如， `https://testdrive.crm.dynamics.com/api/data/v9.0`）。

- **角色名稱**（必要）：提供您在自訂 Dynamics 365 試用產品中定義的安全性角色名稱。 此安全性角色名稱將會在其試用產品（例如，測試磁片磁碟機-角色）期間指派給使用者。

### <a name="technical-configuration-for-logic-app-test-drive"></a>邏輯應用程式試用產品的技術設定

任何自訂產品都應該使用這種類型的試用產品部署範本，其中包含各種複雜的解決方案架構。 如需設定邏輯應用程式試用產品的詳細資訊，請造訪 GitHub 上的[作業](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md)和[客戶參與](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)。

- **區域**（必要，單一選取下拉式清單）：目前有26個 Azure 支援的區域可供使用您的試用產品。 邏輯應用程式的資源將會部署在您選取的區域中。 如果您的邏輯應用程式有任何自訂資源儲存在特定區域中，請確定已在此處選取該區域。 執行此動作的最佳方式是在入口網站中的 Azure 訂用帳戶本機上完整部署邏輯應用程式，並確認其正常運作，然後再進行此選取。

- **最大並行試用**產品（必要）：設定一次可以使用試用產品的客戶數目上限。 這些試用產品已部署，讓客戶可以立即存取它們，而不需要等候部署。

- **試用產品持續時間**（必要）：輸入試用產品將維持作用中狀態的時間長度（以小時為單位）。 試用產品會在此時間週期結束後自動終止。

- **Azure 資源組名**（必要）：輸入您的邏輯應用程式試用產品儲存所在的[Azure 資源組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)名。

- **Azure 邏輯應用程式名稱**（必要）：輸入將試用產品指派給使用者的邏輯應用程式名稱。 此邏輯應用程式必須儲存在上述的 Azure 資源群組中。

- 取消布建**邏輯應用程式名稱**（必要）：輸入在客戶完成後取消布建試用產品的邏輯應用程式名稱。 此邏輯應用程式必須儲存在上述的 Azure 資源群組中。

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>不需要 Power BI 試用產品的技術設定

想要示範互動式 Power BI 視覺效果的產品可以使用內嵌連結來共用自訂的儀表板作為其試用產品，而不需要進一步的技術設定。 深入瞭解如何設定[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)範本應用程式。

### <a name="deployment-subscription-details"></a>部署訂用帳戶詳細資料

若要代表您部署試用產品，請建立並提供另一個唯一的 Azure 訂用帳戶（Power BI 試用產品並不需要）。

- **Azure 訂**用帳戶識別碼（Azure Resource Manager 和邏輯應用程式的必要）：輸入訂用帳戶識別碼，以授與存取權給您的 azure 帳戶服務，以進行資源使用量報告和計費。 我們建議您考慮[建立個別的 Azure 訂](https://docs.microsoft.com/azure/billing/billing-create-subscription)用帳戶，以用於試用產品（如果您還沒有的話）。 您可以藉由登入[Azure 入口網站](https://portal.azure.com/)並流覽至左側功能表的 [**訂閱**] 索引標籤，來尋找您的 AZURE 訂用帳戶識別碼。 選取此索引標籤會顯示您的訂用帳戶識別碼（例如，"a83645ac-1234-5ab6-6789-1h234g764ghty"）。

- **Azure AD 租使用者識別碼**（必要）：輸入您的 AZURE ACTIVE DIRECTORY （AD）[租使用者識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要尋找此識別碼，請登入[Azure 入口網站](https://portal.azure.com/)，選取左側功能表中的 [Active Directory] 索引標籤，選取 [**屬性**]，然後尋找列出的**目錄識別碼**（例如，50c464d3-4930-494c-963c-1e951d15360e）。 您也可以在下列位置使用您的功能變數名稱 URL 來查詢組織的租使用者[https://www.whatismytenantid.com](https://www.whatismytenantid.com)識別碼：。

- **Azure AD 租使用者名稱**（動態365所需）：輸入您的 AZURE ACTIVE DIRECTORY （AD）名稱。 若要尋找此名稱，請登入[Azure 入口網站](https://portal.azure.com/)，在右上角，您的租使用者名稱會列在 [您的帳戶名稱] 底下。

- **Azure AD 應用程式識別碼**（必要）：輸入您的 AZURE ACTIVE DIRECTORY （AD）[應用程式識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要尋找此識別碼，請登入[Azure 入口網站](https://portal.azure.com/)，選取左側功能表中的 [Active Directory] 索引標籤，選取 [**應用程式註冊**]，然後尋找所列的**應用程式識別碼**編號（例如，50c464d3-4930-494c-963c-1e951d15360e）。

- **Azure AD 應用程式用戶端密碼**（必要）：輸入您的 Azure AD 應用程式[用戶端密碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)。 若要尋找此值，請登入[Azure 入口網站](https://portal.azure.com/)。 選取左側功能表中的 [ **Azure Active Directory** ] 索引標籤，選取 [**應用程式註冊**]，然後選取您的試用產品應用程式。 接下來，依序選取 [**憑證和秘密**]、[**新增用戶端密碼**]、[描述]、[**永不****過期**]，然後選擇 [**新增**]。 請務必向下複製值。 在您複製值或無法存取值之前，請不要離開頁面。）

### <a name="test-drive-marketplace-listings"></a>試用產品 marketplace 清單

[**試用**產品] 索引標籤下的 [ **Marketplace 清單**] 選項會顯示您的試用產品可用的語言。 目前，**英文（美國）** 是唯一可用的位置。 選取語言名稱，以輸入描述試用產品體驗的資訊。

- **描述**（必要）：描述您的試用產品、要示範的專案、要進行實驗的目標、探索的功能，以及可協助使用者決定是否要取得您供應專案的任何相關資訊。 最多可以在此欄位中輸入3000個字元的文字。 

- **存取訊號**（Azure Resource Manager 和邏輯試用產品所需）：說明客戶必須知道才能存取和使用此試用產品的內容。 逐步解說使用您的供應專案的案例，以及客戶在整個試用產品中存取功能的確切方式。 最多可以在此欄位中輸入10000個字元的文字。

- **使用者手冊**（必要）：您的試用產品體驗的深入逐步解說。 使用者手冊應涵蓋您想要讓客戶從試用產品中取得的確切內容，並做為參考，以取得他們可能會遇到的任何問題。 檔案必須是 PDF 格式，並在上傳後命名為（最多255個字元）。

- 影片 **：新增**影片（選擇性）：您可以將影片上傳到 YouTube 或 Vimeo，並在這裡參考連結和縮圖影像（533 x 324 圖元），讓客戶可以查看資訊的逐步解說，以協助他們深入瞭解試用產品，包括如何成功使用供應專案的功能，並瞭解強調其優點的案例。
  - **名稱**（必要）
  - **URL （僅限 YouTube 或 Vimeo）** （必要）
  - **縮圖（533 x 324px）**：影像檔案必須是 PNG 格式。

## <a name="supplemental-content"></a>補充內容

此頁面可讓您提供供應專案的其他相關資訊，以協助我們驗證您的供應專案。 此資訊不會向客戶顯示或發佈至 marketplace。

### <a name="key-usage-scenario"></a>金鑰使用案例

必須上傳一個 .pdf 檔案，其中列出您供應專案的金鑰使用案例，該檔列于檔中（.pdf 格式）。 在我們核准您的 marketplace 供應專案之前，我們的驗證小組可能會驗證此處所列的所有案例。

## <a name="publish"></a>發行

### <a name="submit-offer-to-preview"></a>提交供應專案以供預覽

當您完成供應專案的所有必要區段之後，請選取入口網站右上角的 [**發佈**]。 系統會將您重新導向至 [**審核及發佈**] 頁面。

如果這是您第一次發佈此供應專案，您可以：

- 請參閱供應專案每個區段的完成狀態。
    - *未啟動*-表示區段尚未觸及，應完成。
    - *不完整*-表示區段具有必須修正的錯誤，或需要提供更多資訊的詳細資訊。 回到一節，並加以更新。
    - *Complete* -表示區段已完成，所有必要的資料都已提供，而且沒有任何錯誤。 供應專案的所有區段都必須處於「完整」狀態，您才能提交供應專案。
- 在 [**認證的注意事項**] 區段中，提供測試指示給認證小組，確保您的應用程式已正確測試，以及有助於瞭解應用程式的任何補充注意事項。
- 選取 [**提交**] 以提交供應專案進行發佈。 我們會傳送一封電子郵件給您，讓您知道供應專案的預覽版本何時可供您審查和核准。 返回 [合作夥伴中心]，並選取供應專案的 [**上線**]，以發佈至 [公開] （如果是私人供應專案，則為私人物件）。

## <a name="next-steps"></a>後續步驟

- [更新商業市集中的現有供應項目](./update-existing-offer.md)
