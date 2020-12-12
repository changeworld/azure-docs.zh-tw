---
title: 在 Microsoft 商業市集中建立 Dynamics 365 for Customer Engagement 和 PowerApps 供應項目
description: 如何在 Microsoft AppSource 中建立 Dynamics 365 for Customer Engagement & PowerApps 供應專案。 在 AppSource 中列出或銷售您的供應專案，或透過雲端解決方案提供者 (CSP) 方案。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 12/02/2020
ms.openlocfilehash: 0c220daab0d1d9ae7d50d37d9303d6677bd52cc1
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97360300"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>建立 Dynamics 365 for Customer Engagement 和 PowerApps 供應項目

本文說明如何建立新的 Dynamics 365 for Customer Engagement & PowerApps 供應專案。 適用于 Dynamics 365 for Customer Engagement 的所有應用程式 (PowerApps、銷售、服務、專案服務和現場服務) 必須通過我們的認證程式，以檢查您的解決方案中是否有標準需求、相容性和適當的作法。 試用體驗可讓使用者將解決方案部署至即時 Dynamics 365 環境。

開始之前，請先[在合作夥伴中心建立商業市集帳戶](create-account.md) (如果您尚未有帳戶)。 請確定您已在商業市集方案中註冊帳戶。

>[!NOTE]
> 發佈供應專案之後，只有在您重新提交供應專案以供發行時，才會在合作夥伴中心和線上商店更新供應專案的編輯。

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左側導覽功能表中，選取 [商業市集] > [概觀]。
3. 在 [概觀] 頁面上，選取 [+ 新增供應項目] > [Dynamics 365 for Customer Engagement 和 PowerApps]。

    ![說明左側導覽功能表。](./media/new-offer-dynamics-365-customer-engagement-powerapps.png)

## <a name="new-offer"></a>新增供應項目

輸入 **供應項目識別碼**。 這是您帳戶中每個供應項目的唯一識別碼。

- 客戶可以在市集供應項目和 Azure Resource Manager 範本 (如果適用) 的網址中看到此識別碼。
- 與發行者識別碼結合的供應專案識別碼長度必須低於40個字元。
- 請一律使用小寫字母和數字。 它可以包括連字號和底線，但不能包含空格。 例如，如果您的發行者識別碼為， `testpublisherid` 且您輸入了 **測試供應專案-1**，則供應專案的網址將會是 `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` 。
- 當您選取 [ **建立**] 之後，就無法變更此識別碼。

輸入 **供應項目別名**。 這是用於合作夥伴中心內供應項目的名稱。

- 此名稱不會在市集中使用，且與向客戶顯示的供應項目名稱和其他值不同。
- 選取 [ **建立**] 之後，就無法變更此名稱。

選取 [建立] 以產生供應項目並繼續。

## <a name="offer-setup"></a>供應項目設定

### <a name="alias"></a>Alias

輸入一個描述性名稱，此名稱只會在合作夥伴中心中用來參考此供應專案。 此名稱 (預先填入您在建立供應專案時所輸入的內容) 不會在 marketplace 中使用，且與向客戶顯示的供應專案名稱不同。 如果您稍後想要更新供應專案名稱，請移至 [ [供應專案清單](#offer-listing) ] 頁面。

### <a name="setup-details"></a>設定詳細資料

**如果您希望潛在客戶與此清單供應專案互動**，請選取您想要用於此供應專案的選項。

- **立即取得 (免費)** –將您的供應專案免費列出給客戶。
- **免費試用 (清單)** -向客戶列出您的供應專案，並提供免費試用版的連結。 供應項目清單免費試用版是由您的服務所建立、管理及設定，且沒有由 Microsoft 管理的訂閱。

    > [!NOTE]
    > 您的應用程式透過試用連結所接收的權杖，只能經由 Azure Active Directory (Azure AD) 取得使用者資訊，以便在應用程式中自動建立帳戶。 Microsoft 帳戶不支援使用此權杖進行驗證。

- **洽詢我** -將客戶關係管理連接 (CRM) 系統，以收集客戶連絡人資訊。 系統會要求客戶提供權限，以分享客戶資訊。 這些客戶詳細資料，連同他們在您的供應項目中找到的供應項目名稱、識別碼和市集來源，都會傳送至您所設定的 CRM 系統。 如需設定 CRM 的詳細資訊，請參閱[潛在客戶管理](#customer-leads)一節。

### <a name="test-drive"></a>試用產品

試用產品是向潛在客戶展示供應項目的絕佳方式，藉由提供他們「先試用再購買」的選項，進而增加轉換，並且獲得最適合的潛在客戶。 若要深入瞭解，請從 [什麼是試用](../what-is-test-drive.md)產品著手。

若要在固定時間啟用試用產品，請選取 [啟用試用產品] 核取方塊。 若要從您的供應項目中移除試用產品，請清除此核取方塊。

### <a name="customer-leads"></a>潛在客戶

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

如需詳細資訊，請參閱[潛在客戶管理概觀](./commercial-marketplace-get-customer-leads.md)。

選取 [儲存草稿] 後再繼續。

## <a name="properties"></a>屬性

此頁面可讓您定義用來在市集上將供應項目分組的類別和產業、應用程式版本以及支援供應項目的法律合約。

### <a name="categories"></a>類別

選取類別和子類別，以將您的供應專案放在適當的 marketplace 搜尋區域中。 請務必在供應項目描述中描述您的供應項目如何支援這些類別。 選取：

- 至少一個和多個類別，包括主要和次要類別 (選擇性) 。
- 每個主要和/或次要類別最多可有兩個子類別。 如果沒有任何子類別適用于您的供應專案，請選取 [ **不適用**]。

請參閱 [供應專案清單最佳做法](../gtm-offer-listing-best-practices.md)中的類別和子類別的完整清單。

### <a name="industries"></a>產業

[!INCLUDE [Industry Taxonomy](includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>適用的 Dynamics 365 產品

選取此供應項目適用的所有 Dynamics 365 產品。

### <a name="app-version"></a>應用程式版本

輸入供應項目的版本號碼。 客戶會看到此版本列在供應項目的詳細資料頁面上。<!-- If you are only updating the version number due to marketing/descriptive changes, check the **Marketing only change** box. This option allows the offer to bypass the certification and provisioning stages.-->

### <a name="terms-and-conditions"></a>條款及條件

請在此提供您自己的法律條款及條件。 您也可以提供網址以供客戶找到條款及條件。 客戶將必須先接受這些條款，才能試用您的供應項目。

選取 [儲存草稿] 後再繼續。

## <a name="offer-listing"></a>供應項目清單

<!--This page displays the languages in which your offer will be listed. Currently, **English (United States)** is the only available option.

Define marketplace details for each language/market here, such as offer name, description, and images. Select the language/market name to provide this information.-->This page lets you define offer details such as offer name, description, links, and contacts.

> [!NOTE]
> 僅提供一種語言的供應專案清單詳細資料。 如果供應項目描述開頭為以下詞句：「此應用程式僅適用於 [非英文語言]。」，則供應項目詳細資料不一定要使用英文。 您也可以接受提供有用的 *連結 URL* ，以不同于供應專案清單內容中所使用的語言提供內容。

以下是供應專案資訊在 Microsoft AppSource 中的顯示方式範例 (任何列出的價格僅供範例用途，不適合反映實際成本) ：
<!-- update screen? -->
:::image type="content" source="media/example-azure-marketplace-d365-customer-engagement.png" alt-text="說明這項供應專案在 Microsoft AppSource 中的顯示方式。":::

#### <a name="call-out-descriptions"></a>撥出描述

1. 標誌
1. 產品
1. 類別
1. 支援位址 (連結) 
1. 使用規定
1. 供應項目名稱
1. 說明
1. 螢幕擷取畫面/影片

### <a name="marketplace-details"></a>Marketplace 詳細資料

您在此處輸入的 **名稱** 將會向客戶顯示為供應專案清單的標題。 當您建立供應項目時，此欄位會預先填入您為 **供應項目別名** 輸入的文字，但您可以變更此值。 此名稱可以是商標 (您也可以包含商標或著作權符號)。 名稱不能超過 50 個字元，而且不能包含任何表情圖示。

針對 **搜尋結果摘要** 提供供應專案的簡短描述（最多100個字元）。 此描述可用於市集搜尋結果。

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

您可以選擇性地輸入最多三個 **搜尋關鍵字** ，以協助客戶在 marketplace 中尋找您的供應專案。 為了獲得最佳結果，也請在您的描述中使用這些關鍵字。

如果您想要讓客戶知道 **您的應用程式可以使用哪些產品**，請輸入最多三個產品名稱。

### <a name="helpprivacy-urls"></a>說明/隱私權 URL

輸入 **應用程式** 的 [說明] 連結 (URL) 客戶可以在此深入瞭解您的供應專案。 您的說明 URL 不能與您的支援 URL 相同。

輸入 [ **隱私權原則] 連結** (URL) 至您組織的隱私權原則。 您必須負責確保您的應用程式符合隱私權法律和法規，並提供有效的隱私權原則。

### <a name="contact-information"></a>連絡人資訊

請提供 [支援連絡人] 與 [工程連絡人] 的姓名、電子郵件和電話號碼。 這項資訊不會向客戶顯示，但會提供給 Microsoft 使用，並可提供給 CSP 合作夥伴。

在 [支援連絡人] 區段中提供 [支援 URL]，其中雲端解決方案提供者合作夥伴可以在此找到供應項目的支援。 您的支援 URL 不能與您的說明 URL 相同。

### <a name="supporting-documents"></a>支援文件

在此提供至少一個 (最多三個) 相關的行銷文件，例如白皮書、小手冊、檢查清單或簡報 (PDF 格式)。

### <a name="marketplace-media"></a>Marketplace 媒體

提供向客戶顯示您的供應專案時所使用的標誌和影像。 所有影像都必須採用 PNG 格式。

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>如果在上傳檔案時發生問題，請確定您的區域網路不會封鎖合作夥伴中心所使用的 https://upload.xboxlive.com 服務。

#### <a name="logos"></a>標誌

提供 **大型** 大小標誌的 PNG 檔案。 合作夥伴中心將會使用這個來建立其他所需的大小。 您可以選擇性地將此取代為不同的映射。

這些標誌可用於清單中的不同位置：

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>螢幕擷取畫面

新增螢幕擷取畫面，以顯示供應項目的運作方式。 至少需要一個螢幕擷取畫面，最多可以新增至五個。 所有螢幕擷取畫面都必須是 1280 x 720 圖元和 PNG 格式。

#### <a name="videos"></a>影片

您可以選擇新增最多四個示範您供應項目的影片。 影片必須託管在外部網站上。 針對每一個，輸入影片的名稱、位址和影片的縮圖影像， (1280 x 720 圖元) 。

如需其他 Marketplace 清單資源，請參閱 [Marketplace 供應項目清單的最佳做法](../gtm-offer-listing-best-practices.md) \(部分機器翻譯\)。

選取 [儲存草稿] 後再繼續。

## <a name="availability"></a>可用性

此頁面可讓您定義用來提供供應項目的位置和方式。

### <a name="markets"></a>市場

若要指定提供供應專案的市場，請選取 [ **編輯市場** ] 以顯示 **市場選取範圍** 的快顯視窗。

至少選取一個市場。 選擇 [全 **選** ]，讓您的供應專案可在每個可能的市場中使用，或只選取您想要的特定市場。 完成之後，選取 [儲存]。

您的選擇僅適用于新的收購;如果有人在某市場中已經有您的應用程式，而您後來又移除該市場，則在該市場中已經有供應專案的人可以繼續使用，但該市場中的新客戶將無法取得您的供應專案。

> [!IMPORTANT]
> 即使這些需求並未列於此處或合作夥伴中心，您仍有責任配合當地法律的任何需求。 即使您選取所有市場、當地法律、限制或其他因素，也可能會導致某些供應專案無法列在某些國家/地區和區域中。

### <a name="preview-audience"></a>預覽對象

將供應項目在更廣泛的市集供應項目上線之前，您必須先將其提供給有限的 **預覽對象**。 在此輸入 [隱藏金鑰] (任何僅用小寫字母及/或數字的字串)。 預覽對象的成員可以使用此隱藏金鑰做為權杖，並在市集中檢視供應項目的預覽。

然後，當準備好讓供應項目可供使用並移除預覽限制時，您將需要移除 [隱藏金鑰] 並重新發佈。

選取 [儲存草稿] 後再繼續。

## <a name="technical-configuration"></a>技術設定

此頁面會定義用來連接到供應項目的技術詳細資料。 如果終端客戶選擇購買項目，此連線可讓我們為他們佈建您的供應項目。

### <a name="offer-information"></a>供應專案資訊

**基本授權模型** 會決定客戶在 CRM 系統管理中心中指派應用程式的方式。 選取 [資源] (若為執行個體型授權) 或 [使用者] (如果會針對每一租用戶指派一個授權)。

[ **需要 s2s 輸出和 CRM 安全存放區存取** ] 核取方塊可讓您設定 CRM 安全存放區或伺服器對伺服器 (S2S) 輸出存取。 這項功能需要 Dynamics 365 小組在認證階段期間的特殊考量。 Microsoft 會連絡您完成其他步驟，以支援這項功能。

將 **應用程式設定 URL** 保留空白;它是供日後使用。

### <a name="crm-package"></a>CRM 套件

針對 **套件位置的 url**，輸入包含所上傳之 CRM 封裝 .zip 檔案 Azure Blob 儲存體帳戶的 url。 請在 URL 中包含唯讀的 SAS 金鑰，以便 Microsoft 可以挑選您的套件來進行驗證。

> [!IMPORTANT]
> 若要避免發佈封鎖，請確定您 Blob 儲存體 URL 中的到期日尚未到期。 您可以存取原則來修改此日期。 建議您將 **到期時間** 至少設定為未來一個月後。

如果適用，請選取 [我的套件檔案] 方塊 **中有一個以上的 CRM 套件** 。 若是如此，請務必在 .zip 檔案中納入所有套件。

如需如何建置套件並更新其結構的詳細資訊，請參閱[步驟3：為應用程式建立 AppSource 套件](/powerapps/developer/common-data-service/create-package-app-appsource)。

### <a name="crm-package-availability"></a>CRM 套件可用性

選取 [ **+ 新增區域** ]，以指定您的 CRM 套件將可供客戶使用的地理區域。 部署至下列主權區域在認證程序期間需要特殊權限和驗證：[德國](../../germany/index.yml)、[US Gov 雲端](../../azure-government/documentation-government-welcome.md)和 TIP。

根據預設，您在上面輸入的 [應用程式設定 URL] 會用於每個區域。 如果您想要的話，也可以針對一或多個特定區域輸入個別的應用程式設定 URL。

選取 [儲存草稿] 後再繼續。

<!-- ## Test drive technical configuration

This page lets you set up a demonstration ("test drive") that allows customers to try your offer before purchasing it. Learn more in [What is test drive](../what-is-test-drive.md).

To enable a test drive, select the **Enable a test drive** check box on the [Offer setup](#test-drive) tab. To remove test drive from your offer, clear this check box.

When you've finished setting up your test drive, select **Save draft** before continuing. -->

## <a name="supplemental-content"></a>補充內容

此頁面可讓您提供其他資訊，以協助我們驗證您的供應專案。 此資訊不會向客戶顯示或發佈至市集。

### <a name="key-usage-scenario"></a>重要使用方式案例

請上傳會列出供應項目重要使用方式案例的 PDF 檔案。 我們的驗證小組可能會先驗證所有案例，然後才核准您將供應項目發佈到市集。

選取 [儲存草稿] 後再繼續。

## <a name="publish"></a>發佈

### <a name="submit-offer-to-preview"></a>提交供應項目以供預覽

完成供應專案的所有必要區段之後，請選取入口網站右上角的 [ **審核併發布** ]。

如果這是您第一次發佈此供應項目，您可以：

- 查看供應項目每個區段的完成狀態。
    - **未啟動** -尚未觸及區段，必須完成。
    - **未完成** -區段有需要修正的錯誤，或需要詳細資訊。 回到上一個區段並加以更新。
    - **完成** -區段已完成，已提供所有必要的資料，而且沒有任何錯誤。 供應項目的所有區段皆需為完成狀態，您才能提交供應項目。
- 在 [認證注意事項] 區段中，請將測試指示提供給認證小組，以確保您的應用程式已正確測試，並加上有助於了解應用程式的任何補充注意事項。
- 若要提交供應項目以進行發佈，請選取 [提交]。 當預覽版本的供應專案可供審查和核准時，我們會傳送電子郵件給您。 返回合作夥伴中心並選取 [ **上線** ]，將您的供應專案發佈到公用。

## <a name="next-steps"></a>後續步驟

- [更新商業市集中的現有供應項目](./update-existing-offer.md)
