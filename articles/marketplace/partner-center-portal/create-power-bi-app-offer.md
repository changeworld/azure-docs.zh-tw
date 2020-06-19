---
title: 在 Microsoft 商業市集中建立 Power BI 應用程式供應項目
description: 了解如何針對 Microsoft AppSource 建立並發佈 Power BI 應用程式供應項目。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 16b44b3a4496e98f8b6b643bf76aa5aa7ce593c6
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849088"
---
# <a name="create-a-power-bi-app-for-microsoft-appsource"></a>針對 Microsoft AppSource 建立 Power BI 應用程式

此文章描述如何針對 Microsoft [AppSource](https://appsource.microsoft.com/) 建立並發佈 Power BI 應用程式供應項目。

開始之前，請先[在合作夥伴中心建立商業市集帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) \(部分機器翻譯\) (如果您尚未這麼做)。 請確定您已在商業市集方案中註冊帳戶。

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左側導覽功能表中，選取 [商業市集] > [概觀]。
3. 在 [概觀] 頁面上，選取 [+ 新增供應項目] > [Power BI 服務應用程式]。

   ![說明左側導覽功能表。](./media/new-offer-pbi-app.png)

> [!NOTE]
> 發佈供應項目之後，在合作夥伴中心對其進行的編輯，只會在重新發佈供應項目之後出現在店面中。 請務必在進行變更之後，一律重新發佈。

> [!IMPORTANT]
> 如果 [Power BI 服務應用程式] 未顯示或未啟用，表示您的帳戶沒有建立此類供應項目的權限。 請檢查您是否已符合此類供應項目的所有[需求](create-power-bi-app-overview.md)，包括註冊開發人員帳戶。

## <a name="new-offer"></a>新增供應項目

輸入 [供應項目識別碼]。 這是您帳戶中每個供應項目的唯一識別碼。

- 客戶可以在市集供應項目和 Azure Resource Manager 範本 (如果適用) 的網址中看到此識別碼。
- 請一律使用小寫字母和數字。 識別碼可以包含連字號和底線，但不能有空格，且限制為 50 個字元。 例如，如果您在此輸入 **test-offer-1**，供應項目網址將為 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
- 選取 [建立] 之後，即無法變更 [供應項目識別碼]。

輸入 [供應項目別名]。 這是用於合作夥伴中心內供應項目的名稱。

- 此名稱不會在市集中使用，且與向客戶顯示的供應項目名稱和其他值不同。
- 選取 [建立] 之後，就無法變更 [供應項目別名]。

選取 [建立] 以產生供應項目並繼續。

## <a name="offer-overview"></a>供應項目概觀

此頁面會顯示發佈此供應項目 (已完成和即將推出) 所需步驟的視覺表示法，以及完成每個步驟所需的時間。

其包含根據您所做的選擇，在此供應項目上執行作業的連結。 例如：

- 如果供應項目為草稿 - [刪除草稿供應項目](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer) \(部分機器翻譯\)
- 如果供應項目已上線 - [停止銷售供應項目](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) \(部分機器翻譯\)
- 如果供應項目處於預覽階段 - [上線](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval) \(部分機器翻譯\)
- 如果您尚未完成發行者登出 - [取消發行](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing) \(部分機器翻譯\)

## <a name="offer-setup"></a>供應項目設定

### <a name="customer-leads"></a>潛在客戶

當您使用合作夥伴中心將供應項目發佈到市集時，您必須將其連線到您的客戶關係管理 (CRM) 系統。 這可在有人對您的產品感興趣或加以使用時，讓您立即收到客戶連絡人資訊。

1. 選取您要我們傳送潛在客戶至其中的潛在客戶目的地。 合作夥伴中心支援下列 CRM 系統：

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) \(部分機器翻譯\) for Customer Engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > 如果以上未列出您的 CRM 系統，請使用 [Azure 資料表](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) \(部分機器翻譯\) 或 [Https 端點](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) \(部分機器翻譯\) 來儲存潛在客戶資料。 接著，將資料匯出至您的 CRM 系統。

2. 在合作夥伴中心內發佈時，將供應項目連線到潛在客戶目的地。
3. 確認已正確設定與潛在客戶目的地的連線。 在合作夥伴中心加以發佈之後，我們會驗證連線並將測試潛在客戶傳送給您。 在供應項目上線前對其進行預覽時，您也可以嘗試在預覽環境中自行購買供應項目，藉此測試您的潛在客戶連線。
4. 確認潛在客戶目的地的連線保持在最新狀態，您就不會遺失任何潛在客戶。

以下是一些額外的潛在客戶管理資源：

- [潛在客戶管理概觀](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [潛在客戶管理常見問題集](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [常見潛在客戶設定錯誤](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [潛在客戶管理概觀](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) \(英文\) PDF (請確定您已關閉快顯封鎖程式)

選取 [儲存草稿] 後再繼續。

## <a name="properties"></a>屬性

此頁面可讓您定義用來在市集上將供應項目分組的類別和產業、應用程式版本，以及支援供應項目的法律合約。

### <a name="category"></a>類別

選取最少一個，最多三個類別。 這些類別是用來將您的供應項目置於適當的市集搜尋區域，並會顯示在您的供應項目詳細資料頁面上。 在供應項目描述中，說明您的供應項目如何支援這些類別。

### <a name="industry"></a>業界

您也可以選擇性地選取最多兩個產業，以及在每個產業底下選取最多兩個縱向市場。 雖然類別是用來顯示您的供應項目，不過產業和縱向市場則是用於搜尋篩選，且會套用到店面。 如果您的供應項目是以特定的產業和/或縱向市場為目標，請使用供應項目描述來說明您的供應項目是如何支援選取的產業或縱向市場。 如果您的供應項目並非產業特定，請將此區段保留空白。

> [!NOTE]
> 由於我們仍然在持續引進新的產業和縱向市場以改善供應項目探索體驗，某些產業或縱向市場可能尚未在店面上顯示。 以 (*) 標記的產業和縱向市場將會在未來提供。 所有已發佈的供應項目都可以透過關鍵字搜尋來探索。
<p>&nbsp;

| **產業** | **次產業** |
| --- | --- |
| *汽車 | *汽車 |
| 農業 | *其他 - 未分類者 |
| 散發 | *批發<br>*包裹和包裝運送 |
| 教育訓練 | *高等教育<br>* 初等與中等教育 / K-12<br>*圖書館與博物館 |
| 金融服務 | *銀行與資本市場<br>* 保險 |
| 政府 | *國防與情報 (先前稱為「國家與公共安全」)<br>* 公眾安全與司法<br>*民選政府 |
| 醫療保健 (先前稱為「健康情況」) | *健康照護付款方<br>* 提供者<br>*製藥 |
| 製造與資源 (先前稱為「製造業」) | *化學與農業化學<br>* 離散製造<br>*能源 |
| 零售與消費性商品 (先前稱為「零售」) | *消費性商品<br>* 零售商 |
| *媒體與通訊 (先前稱為「媒體與娛樂」) | *媒體與娛樂<br>* 電信業 |
| 專業服務 | *法律<br>* 合作夥伴專業服務 |
| *建築與營造 (先前稱為「建築工程」) | *其他 - 未分類者 |
| *餐旅 | *旅館與休憩<br>* 旅遊與交通<br>*餐廳與飲食服務 |
| *其他公共事業 | *林業與漁業<br>* 非營利組織 |
| *房地產 | *其他 - 未分類者 |

### <a name="legal"></a>法律

#### <a name="terms-and-conditions"></a>條款及條件

若要提供您自己的自訂條款及條件，請在 [條款及條件] 方塊中輸入最多 10,000 個字元。 如果您的條款及條件需要更長的描述，請輸入可以找到該描述的單一 Web 連結。 這將會向客戶顯示為作用中連結。

客戶必須先接受這些條款，才能試用您的供應項目。

選取 [儲存草稿]，再繼續進行下一節，＜供應項目清單＞。

## <a name="offer-listing"></a>供應項目清單

您將在這裡定義顯示於市集中的供應項目詳細資料。 這包括供應項目名稱、描述、影像等等。

### <a name="language"></a>Language

選取您的供應項目將會以哪一種語言列出。 目前唯一可用的選項是 [英文 (美國)]。

針對每個語言/市場定義市集詳細資料 (例如供應項目名稱、描述和影像)。 選取語言/市場名稱來提供此資訊。

> [!NOTE]
> 如果供應項目描述開頭為以下詞句：「此應用程式僅適用於 [非英文語言]。」，則供應項目詳細資料不需要為英文。 也可利用不同於供應項目清單中所使用的語言，來提供供應項目內容的有用連結。

### <a name="name"></a>名稱

您在此處輸入的名稱會顯示為您供應項目的標題。 當您建立供應項目時，此欄位會預先填入您在 [供應項目別名] 方塊中輸入的文字。 您稍後可以變更此名稱。

名稱：

- 可以是商標 (您也可以包含商標或著作權符號)。
- 長度不可超過 50 個字元。
- 不可包含表情圖示。

### <a name="search-results-summary"></a>搜尋結果摘要

提供供應項目的簡短描述。 長度最多可有 100 個字元，並用於市集搜尋結果。

### <a name="description"></a>描述

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="search-keywords"></a>搜尋關鍵字

輸入最多三個選擇性的搜尋關鍵字，以協助客戶在市集中尋找您的供應項目。 為了獲得最佳結果，也請在您的描述中使用這些關鍵字。

### <a name="helpprivacy-web-addresses"></a>說明/隱私權網址

提供連結以協助客戶進一步了解您的供應項目。

#### <a name="help-link"></a>說明連結

輸入客戶可以深入了解供應項目的網址。

#### <a name="privacy-policy-url"></a>隱私權原則 URL

輸入您組織隱私權原則的網址。 您必須負責確保您的供應項目符合隱私權法律與法規。 您也必須負責在您的網站上張貼有效的隱私權原則。

### <a name="contact-information"></a>連絡資訊

您必須提供 [支援連絡人] 與 [工程連絡人] 的姓名、電子郵件和電話號碼。 客戶不會看到此資訊。 其可供 Microsoft 使用，並可能會提供給雲端解決方案提供者 (CSP) 合作夥伴。

- 支援連絡人 (必要)：針對一般支援問題。
- 工程連絡人 (必要)：針對技術問題和認證問題。
- CSP 方案連絡人 (選擇性)：針對與 CSP 方案相關的轉銷商問題。

在 [支援連絡人] 區段中提供 [支援網站] 的網址，其中合作夥伴可以找到供應項目的支援。

### <a name="supporting-documents"></a>支援文件

以 PDF 格式提供至少一個，最多三個相關的行銷文件。 例如，白皮書、摺頁冊、檢查清單或簡報。

### <a name="marketplace-images"></a>Marketplace 映像

提供要與您的供應項目搭配使用的標誌和影像。 所有影像都必須是 .png 格式。 模糊影像會被拒絕。

>[!NOTE]
>如果您在上傳檔案時遇到問題，請確定您的區域網路不會封鎖合作夥伴中心所使用的 https://upload.xboxlive.com 服務。

#### <a name="store-logos"></a>店面標誌

以兩種像素大小提供您供應項目標誌的 .png 檔案：
- **小型** (48 x 48)
- **大型** (216 x 216)

這兩種標誌都是必要的，且會在市集清單中用於不同位置。

#### <a name="screenshots"></a>螢幕擷取畫面

新增至少一個，最多五個螢幕擷取畫面，以顯示供應項目的運作方式。 每個螢幕擷取畫面的大小都必須為 1280 x 720 像素且為 .png 格式。

#### <a name="videos-optional"></a>影片 (選擇性)

新增最多五個示範您供應項目的影片。 輸入影片的名稱、其網址，以及影片的縮圖 .png 影像 (大小為 1280 x 720 像素)。

#### <a name="additional-marketplace-listing-resources"></a>其他市集清單資源

若要深入了解建立供應項目清單，請參閱[供應項目清單最佳做法](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices) \(部分機器翻譯\)。

## <a name="technical-configuration"></a>技術設定

在 Power BI 服務中將您的應用程式升階至生產環境，並提供 Power BI 應用程式安裝程式連結，來讓客戶得以安裝您的應用程式。 如需詳細資訊，請參閱[在 Power BI 中搭配儀表板和報表發佈應用程式](https://docs.microsoft.com/power-bi/service-create-distribute-apps)。

## <a name="supplemental-content"></a>補充內容

提供供應項目的其他相關資訊以協助我們加以驗證。 此資訊不會向客戶顯示或發佈至市集。

### <a name="validation-assets"></a>驗證資產

您可以選擇性地新增指示 (最多 3,000 個字元)，以協助 Microsoft 驗證小組設定、連線及測試您的應用程式。 包含可用來測試 [連線資料] 選項的一般組態設定、帳戶、參數或其他資訊。 此資訊只能由驗證小組查看，且只會用於驗證目的。

## <a name="review-and-publish"></a>檢閱並發佈

完成供應項目的所有必要區段之後，您可以提交供應項目來進行檢閱和發佈。

在入口網站的右上角，選取 [檢閱並發佈]。

在 [檢閱] 頁面上，您可以：

- 查看供應項目每個區段的完成狀態。 在供應項目的所有區段都標示為完成之前，您將無法發佈。
  - **未開始** - 區段尚未開始且必須完成。
  - **不完整** - 區段中有需要修正的錯誤，或是需要您提供詳細資訊。 如需指引，請參閱此文件稍早的小節。
  - **完成** - 區段包含所有必要的資料且沒有任何錯誤。 您必須完成供應項目的所有區段，才能提交供應項目。
- 將測試指示提供給認證小組，以確保您的應用程式能以正確方式接受測試。 此外，請提供有助於了解您供應項目的任何補充附註。

若要提交供應項目進行發佈，請選取 [發佈]。

我們會傳送一封電子郵件給您，讓您知道供應項目的預覽版本何時可供檢閱及核准。 若要將您的供應項目公開發佈 (或如果為私人供應項目，則發佈到私人對象)，請移至合作夥伴中心，然後選取 [上線]。
