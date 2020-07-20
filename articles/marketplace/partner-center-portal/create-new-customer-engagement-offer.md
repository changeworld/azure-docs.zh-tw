---
title: 在 Microsoft 商業市集中建立 Dynamics 365 for Customer Engagement 和 PowerApps 供應項目
description: 如何在 Azure Marketplace、AppSource 或透過合作夥伴中心內的雲端解決方案提供者 (CSP) 方案，建立新的 Dynamics 365 for Customer Engagement 和 PowerApps 供應項目。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 06/17/2020
ms.openlocfilehash: e90e45a0c96d8bbd2806a0ab6df70ef83e0cc9f6
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121923"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>建立 Dynamics 365 for Customer Engagement 和 PowerApps 供應項目

本主題說明如何建立新的 Dynamics 365 for Customer Engagement 和 PowerApps 供應項目。 Dynamics 365 for Customer Engagement 的所有應用程式 (PowerApps、Sales、Service、Project Service 及 Field Service) 都必須經過我們的認證程序並支援試用體驗。 認證程序會檢查您的解決方案是否符合標準需求、相容性和適當的做法。 試用體驗可讓使用者將解決方案部署至即時 Dynamics 365 環境。

開始之前，請先[在合作夥伴中心建立商業市集帳戶](create-account.md) (如果您尚未有帳戶)。 請確定您已在商業市集方案中註冊帳戶。

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[[合作夥伴中心]](https://partner.microsoft.com/dashboard/home)。
2. 在左側導覽功能表中，選取 [商業市集] > [概觀]。
3. 在 [概觀] 頁面上，選取 [+ 新增供應項目] > [Dynamics 365 for Customer Engagement 和 PowerApps]。

    ![說明左側導覽功能表。](./media/new-offer-dynamics-365-customer-engagement-powerapps.png)

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

提供有效的 URL （開頭為或），以向客戶列出您的供應專案，以 `http` `https` 取得試用版的連結。  例如： `https://contoso.com/trial/my-app` 。 供應項目清單免費試用版是由您的服務所建立、管理及設定，且沒有由 Microsoft 管理的訂閱。

> [!NOTE]
> 您的應用程式透過試用連結所接收的權杖，只能經由 Azure Active Directory (Azure AD) 取得使用者資訊，以便在應用程式中自動建立帳戶。 Microsoft 帳戶不支援使用此權杖進行驗證。

#### <a name="contact-me"></a>與我連絡

透過連接客戶關係管理 (CRM)系統收集客戶連絡人資訊。 系統會要求客戶提供權限，以分享客戶資訊。 這些客戶詳細資料，連同他們在您的供應項目中找到的供應項目名稱、識別碼和市集來源，都會傳送至您所設定的 CRM 系統。 如需設定 CRM 的詳細資訊，請參閱[潛在客戶管理](#customer-leads)一節。

選取 [儲存草稿] 後再繼續。

### <a name="test-drive"></a>試用產品

試用產品是向潛在客戶展示供應項目的絕佳方式，藉由提供他們「先試用再購買」的選項，進而增加轉換，並且獲得最適合的潛在客戶。 若要深入瞭解，請從[什麼是試用](../what-is-test-drive.md)產品開始。

若要在固定時間啟用試用產品，請選取 [啟用試用產品] 核取方塊。 若要從您的供應項目中移除試用產品，請清除此核取方塊。

### <a name="customer-leads"></a>潛在客戶

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

如需詳細資訊，請參閱[潛在客戶管理概觀](./commercial-marketplace-get-customer-leads.md)。

選取 [儲存草稿] 後再繼續。

## <a name="properties"></a>屬性

此頁面可讓您定義用來在市集上將供應項目分組的類別和產業、應用程式版本以及支援供應項目的法律合約。

### <a name="category"></a>類別

選取 [類別] 和 [子類別]，將您的供應專案放在適當的 marketplace 搜尋區域。 請務必在供應項目描述中描述您的供應項目如何支援這些類別。 選取：

- 至少一個和最多兩個類別，包括主要和次要類別（選擇性）。
- 針對每個主要和/或次要類別，最多可有兩個子類別。 如果您的供應專案沒有適用的子類別，請選取 [**不適用**]。

請參閱[供應專案清單最佳作法](../gtm-offer-listing-best-practices.md)中的類別和子類別的完整清單。

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

以下範例說明如何在 Microsoft AppSource 中顯示供應專案資訊（任何列出的價格僅供範例之用，而非用於反映實際成本）：

:::image type="content" source="media/example-azure-marketplace-d365-customer-engagement.png" alt-text="說明此供應專案在 Microsoft AppSource 中的顯示方式。":::

#### <a name="call-out-descriptions"></a>向外撥說明

1. 標誌
2. 產品
3. 類別
4. 支援位址（連結）
5. 使用規定位址（連結）
6. 供應項目名稱
7. 描述
8. 螢幕擷取畫面/影片

### <a name="name"></a>名稱

您在此處輸入的名稱會作為供應項目清單標題向客戶顯示。 當您建立供應項目時，此欄位會預先填入您為**供應項目別名**輸入的文字，但您可以變更此值。 此名稱可以是商標 (您也可以包含商標或著作權符號)。 名稱不能超過 50 個字元，而且不能包含任何表情圖示。

### <a name="short-description"></a>簡短描述

提供供應項目的簡短描述，最多 100 個字元。 此描述可用於市集搜尋結果。

### <a name="description"></a>描述

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>搜尋關鍵字

您可以選擇輸入最多三個搜尋關鍵字，協助客戶在 Marketplace 中尋找您的供應項目。 為了獲得最佳結果，也請在您的描述中使用這些關鍵字。

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

#### <a name="screenshots"></a>螢幕擷取畫面

新增螢幕擷取畫面，以顯示供應項目的運作方式。 至少需要一個螢幕擷取畫面，最多可以新增至五個。 所有螢幕擷取畫面都必須是 1280 x 720 像素。

#### <a name="videos"></a>影片

您可以選擇新增最多四個示範您供應項目的影片。 這些影片應裝載於 YouTube 和/或 Vimeo 上。 針對每一個影片，輸入影片名稱、URL 和影片的縮圖影像(1280 x 720 像素)

#### <a name="additional-marketplace-listing-resources"></a>其他市集清單資源

[市集供應項目清單的最佳做法](../gtm-offer-listing-best-practices.md)

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

將供應項目在更廣泛的市集供應項目上線之前，您必須先將其提供給有限的**預覽對象**。 在此輸入 [隱藏金鑰] (任何僅用小寫字母及/或數字的字串)。 預覽對象的成員可以使用此隱藏金鑰做為權杖，並在市集中檢視供應項目的預覽。

然後，當準備好讓供應項目可供使用並移除預覽限制時，您將需要移除 [隱藏金鑰] 並重新發佈。

選取 **[儲存草稿]** 後再繼續。

## <a name="technical-configuration"></a>技術設定

此頁面會定義用來連接到供應項目的技術詳細資料。 如果終端客戶選擇購買項目，此連線可讓我們為他們佈建您的供應項目。

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

在本節中，請選取 [+ 新增區域] 以指定可供客戶使用您 CRM 套件的地理區域。 部署至下列主權區域在認證程序期間需要特殊權限和驗證：[德國](../../germany/index.yml)、[US Gov 雲端](../../azure-government/documentation-government-welcome.md)和 TIP。

根據預設，您在上面輸入的 [應用程式設定 URL] 會用於每個區域。 如果您想要的話，也可以針對一或多個特定區域輸入個別的應用程式設定 URL。 

選取 [儲存草稿] 後再繼續。

## <a name="test-drive-technical-configuration"></a>試用產品技術設定

此頁面可讓您設定示範 (即「試用產品」)，讓客戶在購買之前先試用您的供應項目。 在[什麼是試用](../what-is-test-drive.md)產品中深入瞭解。

若要啟用試用產品，請在[[供應項目設定]](#test-drive) 索引標籤中，選取 [啟用試用產品] 核取方塊。若要從您的供應項目中移除試用產品，請清除此核取方塊。

當您完成試用產品的設定後，請選取 [**儲存草稿**] 再繼續。

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
- 若要提交供應項目以進行發佈，請選取 [提交]。 我們會向您傳送一封電子郵件，讓您知道供應項目的預覽版本何時可供檢閱及核准。 返回 [合作夥伴中心]，然後選取 [**上線**]，讓供應專案發佈至 [公開]。

## <a name="next-step"></a>後續步驟

- [更新商業市集中的現有供應項目](./update-existing-offer.md)
