---
title: 在 Microsoft 商業市集中建立 Dynamics 365 for Customer Engagement 和 PowerApps 供應項目
description: 如何在 Azure Marketplace、AppSource 或透過合作夥伴中心內的雲端解決方案提供者 (CSP) 方案，建立新的 Dynamics 365 for Customer Engagement 和 PowerApps 供應項目。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: e49ed542af140b28e8ebe58e5c8920d0959c9387
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848894"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>建立 Dynamics 365 for Customer Engagement 和 PowerApps 供應項目

本主題說明如何建立新的 Dynamics 365 for Customer Engagement 和 PowerApps 供應項目。 Dynamics 365 for Customer Engagement 的所有應用程式 (PowerApps、Sales、Service、Project Service 及 Field Service) 都必須經過我們的認證程序並支援試用體驗。 認證程序會檢查您的解決方案是否符合標準需求、相容性和適當的做法。 試用體驗可讓使用者將解決方案部署至即時 Dynamics 365 環境。

開始之前，請先[在合作夥伴中心建立商業市集帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) (如果您尚未有帳戶)。 請確定您已在商業市集方案中註冊帳戶。

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[[合作夥伴中心]](https://partner.microsoft.com/dashboard/home)。
2. 在左側導覽功能表中，選取 [商業市集] > [概觀]。
3. 在 [概觀] 頁面上，選取 [+ 新增供應項目] > [Dynamics 365 for Customer Engagement 和 PowerApps]。

    ![說明左側導覽功能表。](./media/new-offer-dynamics-365-cepa.png)

> [!NOTE]
> 發佈供應項目之後，在合作夥伴中心對其進行的編輯，只會在重新發佈供應項目之後出現在店面中。 請務必在進行變更之後，一律重新發佈。

## <a name="new-offer"></a>新增供應項目

輸入**供應項目識別碼**。 這是您帳戶中每個供應項目的唯一識別碼。

- 客戶可以在市集供應項目和 Azure Resource Manager 範本 (如果適用) 的網址中看到此識別碼。
- 請一律使用小寫字母和數字。 其可以包含連字號和底線，但不能有空格，且限制為 50 個字元。 例如，如果您輸入 **test-offer-1**，供應項目網址將為 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
- 選取 [建立] 之後，即無法變更供應項目識別碼。

輸入**供應項目別名**。 這是用於合作夥伴中心內供應項目的名稱。

- 此名稱不會在市集中使用，且與向客戶顯示的供應項目名稱和其他值不同。
- 選取 [建立] 之後，即無法變更此項目。

選取 [建立] 以產生供應項目並繼續。

## <a name="offer-setup"></a>供應項目設定

請遵循下列步驟來設定供應項目。

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>您要讓潛在客戶如何與此清單供應項目互動？

選取您想要用於此供應項目的選項。

#### <a name="get-it-now-free"></a>立即取得 (免費)

提供有效的 URL (網址開頭為 HTTP 或 HTTPS)，免費向客戶列出您的供應項目，使其能存取您的應用程式。  例如： `https://contoso.com/my-app` 。

#### <a name="free-trial-listing"></a>免費試用版 (清單)

提供有效的 URL (網址開頭為 *HTTP* 或 *HTTPS*)，以免費試用連結向客戶列出供應項目，使其能取得免費試用版。  例如： `https://contoso.com/trial/my-app` 。 供應項目清單免費試用版是由您的服務所建立、管理及設定，且沒有由 Microsoft 管理的訂閱。

> [!NOTE]
> 您的應用程式透過試用連結所接收的權杖，只能經由 Azure Active Directory (Azure AD) 取得使用者資訊，以便在應用程式中自動建立帳戶。 Microsoft 帳戶不支援使用此權杖進行驗證。

#### <a name="contact-me"></a>與我連絡

透過連接客戶關係管理 (CRM)系統收集客戶連絡人資訊。 系統會要求客戶提供權限，以分享客戶資訊。 這些客戶詳細資料，連同他們在您的供應項目中找到的供應項目名稱、識別碼和市集來源，都會傳送至您所設定的 CRM 系統。 如需設定 CRM 的詳細資訊，請參閱[潛在客戶管理](#customer-leads)一節。

選取 [儲存草稿] 後再繼續。

### <a name="test-drive"></a>試用產品

試用產品是向潛在客戶展示供應項目的絕佳方式，藉由提供他們「先試用再購買」的選項，進而增加轉換，並且獲得最適合的潛在客戶。 [深入了解試用產品](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)。

若要在固定時間啟用試用產品，請選取 [啟用試用產品] 核取方塊。 若要從您的供應項目中移除試用產品，請清除此核取方塊。 如何設定試用產品環境會在本主題稍後的[試用產品技術設定](#test-drive-technical-configuration)一節中提及。

如需詳細資訊，請參閱[在商業市集中提供供應項目試用產品](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive) (機器翻譯)。

#### <a name="type-of-test-drive"></a>試用產品的類型

然後選取下列選項：

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** – 包含組成解決方案的所有 Azure 資源的部署範本。 適用於此案例的產品為僅使用 Azure 資源的產品。
- **[Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** – Microsoft 會代管並維護 Business Central 企業資源規劃系統 (財務、營運、供應鏈、CRM 等) 的試用產品服務 (包含佈建和部署)。  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** – Microsoft 會代管並維護客戶參與系統 (銷售、服務、專案服務、現場服務等) 的試用產品服務 (包含佈建和部署)。  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** – Microsoft 會代管並維護財務與營運企業資源規劃系統 (財務、營運、製造、供應鏈等) 的試用產品服務 (包含佈建和部署)。 
- **[邏輯應用程式](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** – 包含所有複雜解決方案架構的部署範本。 所有自訂產品都應該使用此類型的試用產品。
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** – 指向自訂建置儀表板的內嵌連結。 若需要示範產品的互動式 Power BI 視覺效果，則應該使用此類型的試用產品。 您只需要上傳內嵌的 Power BI URL。

#### <a name="additional-test-drive-resources"></a>其他試用產品資源

- [技術最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [行銷最佳做法](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [試用產品概觀](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (請確定您已關閉快顯封鎖程式)

### <a name="customer-leads"></a>潛在客戶

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

如需詳細資訊，請參閱[潛在客戶管理概觀](./commercial-marketplace-get-customer-leads.md)。

選取 [儲存草稿] 後再繼續。

## <a name="properties"></a>屬性

此頁面可讓您定義用來在市集上將供應項目分組的類別和產業、應用程式版本以及支援供應項目的法律合約。

### <a name="category"></a>類別

請選取最少一個和最多三個類別。 這些類別可用來將您的供應項目放入適當的市集搜尋區域。 請務必在供應項目描述中描述您的供應項目如何支援這些類別。

### <a name="industry"></a>業界

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>適用的 Dynamics 365 產品

選取此供應項目適用的所有 Dynamics 365 產品。

### <a name="app-version"></a>應用程式版本

輸入供應項目的版本號碼。 客戶會看到此版本列在供應項目的詳細資料頁面上。 如果您單純是因為行銷/描述方面有所變更而要更新版本號碼，請核取 [僅行銷變更] 方塊。 此選項可讓供應項目略過憑證和佈建階段。

### <a name="terms-and-conditions"></a>條款及條件

請在此提供您自己的法律條款及條件。 您也可以提供網址以供客戶找到條款及條件。 客戶將必須先接受這些條款，才能試用您的供應項目。

選取 [儲存草稿] 後再繼續。

## <a name="offer-listing"></a>供應項目清單

此頁面會顯示所列出供應項目提供的語言。 目前**英文 (美國)** 是唯一可用的選項。

請在此定義每個語言/市場的市集詳細資料，例如供應項目名稱、描述和影像。 選取語言/市場名稱來提供此資訊。

> [!NOTE]
> 供應項目清單內容 (例如描述、文件、螢幕擷取畫面和使用規定) 不一定要使用英文，供應項目只需用以下句子描述開頭即可：「此應用程式僅適用於 [非英文語言]」。 也可利用不同於供應項目清單內容中所使用的語言，來提供供應項目內容的*有用連結 URL*。

### <a name="name"></a>名稱

您在此處輸入的名稱會作為供應項目清單標題向客戶顯示。 當您建立供應項目時，此欄位會預先填入您為**供應項目別名**輸入的文字，但您可以變更此值。 此名稱可以是商標 (您也可以包含商標或著作權符號)。 名稱不能超過 50 個字元，而且不能包含任何表情圖示。

### <a name="short-description"></a>簡短描述

提供供應項目的簡短描述，最多 100 個字元。 此描述可用於市集搜尋結果。

### <a name="description"></a>描述

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>搜尋關鍵字

您可以選擇輸入最多三個搜尋關鍵字，協助客戶在市集中尋找您的供應項目。 為了獲得最佳結果，也請在您的描述中使用這些關鍵字。

### <a name="products-your-app-works-with"></a>與您的應用程式一起運作的產品

如果您想要讓客戶知道您的應用程式可搭配特定產品，請在這裡輸入最多三個產品名稱。

### <a name="support-urls"></a>支援 URL

本節可讓您提供連結，協助客戶深入了解您的供應項目。

#### <a name="help-link"></a>說明連結

輸入客戶可以深入了解供應項目的網址。

#### <a name="privacy-policy-url"></a>隱私權原則 URL

輸入貴組織隱私權原則的網址。 您必須負責確保您的應用程式符合隱私權法律和法規，並提供有效的隱私權原則。

### <a name="contacts"></a>連絡人

請提供 [支援連絡人] 與 [工程連絡人] 的姓名、電子郵件和電話號碼。 此資訊不會向客戶顯示，但可供 Microsoft 使用，可能也會提供給雲端解決方案提供者合作夥伴。

在 [支援連絡人] 區段中，您還必須提供 [支援 URL]，其中雲端解決方案提供者合作夥伴可以在此找到供應項目的支援。

### <a name="supporting-documents"></a>支援文件

在此提供至少一個 (最多三個) 相關的行銷文件，例如白皮書、小手冊、檢查清單或簡報 (PDF 格式)。

### <a name="marketplace-images"></a>Marketplace 映像

請提供供應項目的標誌和影像。 所有影像都必須採用 PNG 格式。

>[!NOTE]
>如果在上傳檔案時發生問題，請確定您的區域網路不會封鎖合作夥伴中心所使用的 https://upload.xboxlive.com 服務。

#### <a name="store-logos"></a>儲存標誌

請提供三種像素大小的供應項目標誌：
- **小型** (必要；48 x 48)
- **大型** (必要；216 x 216)
- **寬型** (選擇性；255 x 115)

#### <a name="hero"></a>主圖

主圖影像是選擇性的。 如果您提供影像，則必須為 815 x 290 像素。

#### <a name="screenshots"></a>螢幕擷取畫面

新增螢幕擷取畫面，以顯示供應項目的運作方式。 至少需要一個螢幕擷取畫面，最多可以新增至五個。 所有螢幕擷取畫面都必須是 1280 x 720 像素。

#### <a name="videos"></a>影片

您可以選擇新增最多四個示範您供應項目的影片。 這些影片應裝載於 YouTube 和/或 Vimeo 上。 針對每一個影片，輸入影片名稱、URL 和影片的縮圖影像(1280 x 720 像素)

#### <a name="additional-marketplace-listing-resources"></a>其他市集清單資源

[市集供應項目清單的最佳做法](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

選取 [儲存草稿] 後再繼續。

## <a name="availability"></a>可用性

此頁面可讓您定義用來提供供應項目的位置和方式。

### <a name="markets"></a>市場

請指定可提供供應項目的市場。 若要這麼做，請選取 [編輯市場]，隨即會顯示 [市場選取項目] 快顯視窗。

預設不會選取任何市場，但您必須至少選取一個市場才能發佈供應項目。 選擇 [選取所有]，在每個可能的市場中提供供應項目，或選取您想要新增的特定市場。

您在此選擇的項目僅適用於新的銷售；如果有人在特定市場上已有您的應用程式，而您後來移除了該市場，則該市場中已有該供應項目的人可以繼續使用，但該市場中的新客戶將無法取得供應項目。

> [!IMPORTANT]
> 即使這些需求並未列於此處或合作夥伴中心，您仍有責任配合當地法律的任何需求。 即使您選取所有市場，當地法律和限制或其他因素可能會導致某些國家/地區不會列出特定供應項目。

### <a name="preview-audience"></a>預覽對象

將供應項目在更廣泛的市集供應項目上線之前，您必須先將其提供給有限的**預覽對象**。 在此輸入 [隱藏金鑰] (任何僅用小寫字母及/或數字的字串)。 預覽對象的成員可以使用此隱藏金鑰作為權杖，並在市集中檢視供應項目的預覽。

然後，當準備好讓供應項目可供使用並移除預覽限制時，您將需要移除 [隱藏金鑰] 並重新發佈。

選取 [儲存草稿] 後再繼續。

## <a name="technical-configuration"></a>技術設定

此頁面會定義用來連線到供應項目的技術詳細資料。 如果終端客戶選擇購買項目，此連線可讓我們為他們佈建您的供應項目。

### <a name="base-license-model"></a>基本授權模型

基本授權模型會決定客戶如何在 CRM 系統管理中心指派您的應用程式。 選取 [資源] (若為執行個體型授權) 或 [使用者] (如果會針對每一租用戶指派一個授權)。

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>需要 S2S 輸出和 CRM 安全存放區存取

核取此方塊可啟用 CRM 安全存放區或伺服器對伺服器 (S2S) 輸出存取的設定。 這項功能需要 Dynamics 365 小組在認證階段期間的特殊考量。 Microsoft 會連絡您完成其他步驟，以支援這項功能。

### <a name="application-configuration-url"></a>應用程式設定 URL

提供設定網頁的 URL 以供客戶設定您的應用程式。

### <a name="crm-package"></a>CRM 套件

在 [套件位置的 URL] 欄位中，輸入已上傳的 CRM 套件 .zip 檔案所在 Azure Blob 儲存體帳戶的 URL。 請在 URL 中包含唯讀的 SAS 金鑰，以便 Microsoft 可以挑選您的套件來進行驗證。

> [!IMPORTANT]
> 若要避免發佈封鎖，請確定您 Blob 儲存體 URL 中的到期日尚未到期。 您可以存取原則來修改此日期。 建議您將**到期時間**至少設定為未來一個月後。

選取標示為**我的套件檔案中有多個 CRM 套件** 的方塊 (如果適用)。 若是如此，請務必在 .zip 檔案中納入所有套件。

如需如何建置套件並更新其結構的詳細資訊，請參閱[步驟3：為應用程式建立 AppSource 套件](https://docs.microsoft.com/powerapps/developer/common-data-service/create-package-app-appsource)。

### <a name="crm-package-availability"></a>CRM 套件可用性

在本節中，請選取 [+ 新增區域] 以指定可供客戶使用您 CRM 套件的地理區域。 部署至下列主權區域在認證程序期間需要特殊權限和驗證：[德國](https://docs.microsoft.com/azure/germany/)、[US Gov 雲端](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)和 TIP。

根據預設，您在上面輸入的 [應用程式設定 URL] 會用於每個區域。 如果您想要的話，也可以針對一或多個特定區域輸入個別的應用程式設定 URL。 

選取 [儲存草稿] 後再繼續。

## <a name="test-drive-technical-configuration"></a>試用產品技術設定

此頁面可讓您設定示範 (即「試用產品」)，讓客戶在購買之前先試用您的供應項目。 若要深入了解，請參閱[什麼是試用產品？](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive) (機器翻譯)。

若要啟用試用產品，請在 [[供應項目設定]](#test-drive) 索引標籤中，選取 [啟用試用產品] 核取方塊。若要從您的供應項目中移除試用產品，請清除此核取方塊。

若要深入了解，請參閱[什麼是試用產品？](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive) (機器翻譯)。

下列是可用的試用產品類型，每個都有所需的技術設定需求：

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [邏輯應用程式](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (不需技術設定)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure Resource Manager 試用產品的技術設定

包含組成解決方案的所有 Azure 資源的部署範本。 適用於此案例的產品為僅使用 Azure 資源的產品。 深入了解如何設定 [Azure Resource Manager 試用產品](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive) (機器翻譯)。

- **區域** (必要) - 目前有 26 個 Azure 支援的區域可提供試用產品。 一般來說，建議您在預期客戶數最多的區域提供試用產品，以便客戶可以選取最接近的區域以獲得最佳效能。 請先確認您的訂閱允許在每個所選區域部署所有需要的資源。
- **執行個體** – 選取類型 (經常性存取層或非經常性存取層) 和可用執行個體的數目，其會乘以供應項目可用的區域數目。

    **經常性存取層** – 此類型的執行個體為已部署，並會在每個所選區域等候存取。 客戶可以立即存取試用產品的「經常性存取層」執行個體，而不需要等候部署。 缺點是，這些執行個體一直在您的 Azure 訂用帳戶中執行，因此將會產生可觀的運作成本。 強烈建議您擁有至少一個「經常性存取層」執行個體，因為您的大部分客戶都不會想要等候完整部署，因此若沒有可用的「經常性存取層」執行個體，客戶會減少使用量。

    **非經常性存取層** – 此類型的執行個體代表每個區域可能部署的執行個體總數。 在客戶要求時試用產品時，「非經常性存取層」執行個體需要整個試用產品 Resource Manager 範本執行部署程序，因此較「經常性存取層」執行個體緩慢許多。 這個類型的好處在於您只需要支付試用產品持續時間的費用，而不像「經常性存取層」執行個體一樣，須一律在 Azure 訂閱上執行。

- **試用產品 Azure Resource Manager 範本** – 上傳包含您的 Azure Resource Manager 範本的 .zip 檔案。 在快速入門文章中，深入了解如何建立 Azure Resource Manager 範本，[使用 Azure 入口網站建立及部署 ARM 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。

- **試用產品持續時間** (必要) - 輸入試用產品會保持作用的時數。 在此持續時間過後，testdDrive 將會自動終止。 此持續時間只能以一整個小時為單位來進行設定 (例如，「2」小時有效；「1.5」小時則無效)。

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 試用產品的技術設定

Microsoft 可以透過使用此類型的試用產品，來裝載和維護服務佈建和部署，藉此降低設定試用產品的複雜性。 無論試用產品是以商業中心、客戶參與或營運物件為目標，此類型裝載的試用產品設定皆相同。

- **同時試用產品數上限** (必要) – 設定一次可以使用試用產品的客戶數上限。 當試用產品在使用中時，每位同時使用者都會取用 Dynamics 365 授權，因此您必須確保有足夠的授權可支援所設上限數目。 建議值是 3 至 5。

- **試用產品持續時間** (必要) - 輸入試用產品會保持作用的時數。 此時間過後，工作階段就會結束且不再占用您的授權。 建議值為 2 至 24 小時，視您供應項目的複雜度而定。 此持續時間只能以一整個小時為單位來進行設定 (例如，「2」小時有效；「1.5」小時則無效)。 若使用者時間耗盡且想要再次存取試用產品，可以要求新的工作階段。

- **執行個體 URL** (必要) – 客戶開始試用產品的網址。 這通常是您的應用程式與範例資料安裝所在的 Dynamics 365 執行個體的 URL (例如，`https://testdrive.crm.dynamics.com`)。

- **執行個體 Web API URL** (必要) – 若要擷取 Dynamics 365 執行個體的 Web API URL，請登入您的 Microsoft 365 帳戶，然後瀏覽至 [設定] \& [自訂] \& [開發人員資源] \& [執行個體Web API (服務根 URL)]，複製此處找到的 URL (例如，`https://testdrive.crm.dynamics.com/api/data/v9.0`)。

- **角色名稱** (必要) – 提供您在自訂 Dynamics 365 試用產品中定義的安全性角色名稱。 此資訊安全角色名稱會在試用產品 (例如，test-drive-role) 期間指派給使用者。

### <a name="technical-configuration-for-logic-app-test-drive"></a>邏輯應用程式試用產品的技術設定

任何自訂產品都應使用此類型的試用產品部署範本，其中包含各種複雜的解決方案架構。 如需設定邏輯應用程式試用產品的詳細資訊，請造訪 GitHub 上的[作業 (英文)](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md)和[客戶參與 (英文)](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)。

- **區域** (必要，單一選項下拉式清單) - 目前有 26 個 Azure 支援的區域可提供試用產品。 邏輯應用程式的資源將會部署在您所選區域中。 如果邏輯應用程式有任何儲存在特定區域中的自訂資源，請務必在此處選取該區域。 若要這麼做，最佳方法是在入口網站中您的 Azure 訂閱上，於本機中完整部署邏輯應用程式，並確認其運作正常，然後再進行此選擇。

- **同時試用產品數上限** (必要) – 設定一次可以使用試用產品的客戶數上限。 這些試用產品已部署完成，客戶可以立即存取而不需等候部署。

- **試用產品持續時間** (必要) – 輸入試用產品將維持可用的持續時間，以 # 小時數表示。 在此持續時間過後，試用產品將會自動終止。

- **Azure 資源群組名稱** (必要) – 在您的邏輯應用程式試用產品儲存位置輸入 [Azure 資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)名稱。

- **Azure 邏輯應用程式名稱** (必要) – 輸入將試用產品指派給使用者的邏輯應用程式名稱。 此邏輯應用程式必須儲存在上述的 Azure 資源群組中。

- **取消佈建邏輯應用程式名稱** (必要) – 客戶試用完後，輸入要取消佈建試用產品的邏輯應用程式名稱。 此邏輯應用程式必須儲存在上述的 Azure 資源群組中。

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI 試用產品不需技術設定

若需要示範產品的互動式 Power BI 視覺效果，可以使用內嵌連結共用自訂建置儀表板作為其試用產品，無須進一步的技術設定。 深入了解如何設定 [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) 範本應用程式。

### <a name="deployment-subscription-details"></a>部署訂閱詳細資料

若要讓 Microsoft 能夠代表您部署試用產品，請提供另外建立的唯一 Azure 訂用帳戶 (Power BI 試用產品則不需要)。

- **Azure 訂閱識別碼** (針對 Azure Resource Manager 和邏輯應用程式為必要) – 輸入訂閱識別碼以授與您的 Azure 帳戶服務存取權，並用於資源使用量報告和計費。 建議您考慮[建立個別的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)，以便用於試用產品 (如果您沒有訂閱的話)。 您可以透過登入 [Azure 入口網站](https://portal.azure.com/)並瀏覽到左側功能表中的 [訂閱] 索引標籤，以尋找 Azure 訂用帳戶識別碼。 選取此索引標籤會顯示您的訂閱識別碼 (例如「a83645ac-1234-5ab6-6789-1h234g764ghty」)。

- **Azure AD 租用戶識別碼** (必要) – 請輸入您的 Azure Active Directory (AD) [租用戶識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要尋找此識別碼，請登入 [Azure 入口網站](https://portal.azure.com/)，選取左側功能表中的 [Active Directory] 索引標籤，選取 [屬性]，然後尋找列出的 [目錄識別碼] 號碼 (例如 50c464d3-4930-494c-963c-1e951d15360e)。 您也可以在 [https://www.whatismytenantid.com](https://www.whatismytenantid.com) 中，使用網域名稱 URL 來查閱組織的租用戶識別碼。

- **Azure AD 租用戶識別碼** (針對 Dynamic 365 為必要) – 輸入您的 Azure Active Directory (AD) 名稱。 若要尋找此名稱，請登入 [Azure 入口網站](https://portal.azure.com/)，位於右上角的租用戶名稱會列在您的帳戶名稱底下。

- **Azure AD 應用程式識別碼** (必要) – 輸入您的 Azure Active Directory (AD) [應用程式識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要尋找此識別碼，請登入 [Azure 入口網站](https://portal.azure.com/)，選取左側功能表中的 [Active Directory] 索引標籤，選取 [應用程式註冊]，然後尋找列出的 [應用程式識別碼] 號碼 (例如 50c464d3-4930-494c-963c-1e951d15360e)。

- **Azure AD 應用程式用戶端密碼** (必要) – 輸入 Azure AD 應用程式[用戶端密碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)。 若要尋找此值，請登入 [Azure 入口網站](https://portal.azure.com/)。 在左側功能表中，選取 [Azure Active Directory] 索引標籤，選取 [應用程式註冊]，然後選擇您的試用產品。 接下來，選取 [憑證和密碼]、選取 [新用戶端密碼]、輸入描述，選取 [到期] 底下的 [永不]，然後選擇 [新增]。 請務必記下此值。 請不要在複製下此值之前離開頁面，否則您將無法存取此值。

### <a name="test-drive-marketplace-listings"></a>試用產品市集清單

在 [試用產品] 索引標籤下找到的 [Marketplace 清單] 選項中會顯示您的試用產品可供使用的語言。 目前 [英文 (美國)] 是唯一可用的位置。 選取語言名稱，以輸入描述試用產品體驗的資訊。

- **描述** (必要) - 描述您的試用產品、示範的項目、使用者可實驗的目標、可探索的功能，以及可協助使用者決定是否要購買供應項目的任何相關資訊。 最多可以在此欄位中輸入 3,000 個字元的文字。 

- **存取資訊** (針對 Azure Resource Manager 和邏輯試用產品為必要) – 說明客戶必須知道才能存取與使用此試用產品的內容。 逐步解說使用供應項目的案例，以及客戶在完整試用產品階段中，應該知道的確切存取功能。 最多可以在此欄位中輸入 10,000 個字元的文字。

- **使用者手冊** (必要) - 這是試用產品體驗的詳細說明。 使用者手冊應涵蓋您想要讓客戶從試用產品中獲得的確切內容，並作為客戶可能會遇到的任何問題的參考。 檔案必須是 PDF 格式，並在上傳後命名 (最多 255 個字元)。

- **影片** (選擇性) - 將影片上傳至外部裝載的網站，其參考此處的連結和縮圖影像 (533 x 324 像素)。 這會讓客戶能夠檢視逐步解說資訊，以協助客戶深入了解試用產品，包括如何成功地使用供應項目的功能，並了解能夠凸顯其優點的案例。
  - **名稱** (必要)
  - **網址** (必要；僅限 YouTube 或 Vimeo)
  - **縮圖**影像 (影像檔必須是 PNG 格式，且為 533 x 324 像素)

選取 [儲存草稿] 後再繼續。

## <a name="supplemental-content"></a>補充內容

此頁面可讓您提供供應項目的其他相關資訊，以協助我們驗證您的供應項目。 此資訊不會向客戶顯示或發佈至市集。

### <a name="key-usage-scenario"></a>重要使用方式案例

請上傳會列出供應項目重要使用方式案例的 PDF 檔案。 我們的驗證小組可能會先驗證所有案例，然後才核准您將供應項目發佈到市集。

選取 [儲存草稿] 後再繼續。

## <a name="publish"></a>發佈

### <a name="submit-offer-to-preview"></a>提交供應項目以供預覽

在完成供應項目的所有必要區段之後，請選取入口網站右上角的 [檢閱並發佈]。

如果這是您第一次發佈此供應項目，您可以：

- 查看供應項目每個區段的完成狀態。
    - **尚未開始** - 此區段應該已完成卻尚未觸及。
    - **不完整** - 此區段中有需要修正的錯誤，或是需要您提供更多詳細資訊。 回到上一區段並加以更新。
    - **完成** - 此區段已完成，並已提供所有必要的資料且沒有任何錯誤。 供應項目的所有區段皆需為完成狀態，您才能提交供應項目。
- 在 [認證注意事項] 區段中，請將測試指示提供給認證小組，以確保您的應用程式已正確測試，並加上有助於了解應用程式的任何補充注意事項。
- 若要提交供應項目以進行發佈，請選取 [提交]。 我們向您傳送一封電子郵件，讓您知道供應項目的預覽版本何時可供檢閱及核准。 若要將供應項目公開發佈 (若是私人供應項目則向私人對象發佈)，請回到 [合作夥伴中心]，然後選取 [上線]。

## <a name="next-step"></a>後續步驟

- [更新商業市集中的現有供應項目](./update-existing-offer.md)
