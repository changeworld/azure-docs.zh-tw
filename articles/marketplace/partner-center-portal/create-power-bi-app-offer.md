---
title: 在 Microsoft 商業 marketplace 中建立 Power BI 應用程式供應專案
description: 瞭解如何建立 Power BI 應用程式供應專案，並將其發佈至 Microsoft AppSource。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 17a6b7e46341ee0231ac88b849495cfc7e46ad88
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82839142"
---
# <a name="create-a-power-bi-app-for-microsoft-appsource"></a>建立適用于 Microsoft AppSource 的 Power BI 應用程式

本文說明如何建立 Power BI 應用程式供應專案，並將其發佈至 Microsoft [AppSource](https://appsource.microsoft.com/)。

開始之前，請先[在合作夥伴中心建立商業 Marketplace 帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)（如果您尚未這麼做）。 請確定您的帳戶已在商業 marketplace 方案中註冊。

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左側導覽功能表中，選取 [**商用 Marketplace** > **總覽**]。
3. 在 [總覽] 頁面上，選取 [ **+ 新增供應** > 專案]**Power BI 服務應用程式**]。

   ![說明左側導覽功能表。](./media/new-offer-pbi-app.png)

> [!NOTE]
> 發行供應專案之後，在合作夥伴中心對其進行的編輯，只會在重新發佈供應專案後出現在店面中。 請務必在進行變更之後，一律重新發佈。

> [!IMPORTANT]
> 如果**Power BI 服務應用程式**未顯示或未啟用，表示您的帳戶沒有建立此供應專案類型的許可權。 請確認您已符合此供應專案類型的所有[需求](create-power-bi-app-overview.md)，包括註冊開發人員帳戶。

## <a name="new-offer"></a>新增供應項目

輸入**供應專案識別碼**。 這是您帳戶中每個供應專案的唯一識別碼。

- Marketplace 供應專案和 Azure Resource Manager 範本的網址中，客戶可以看到此識別碼（如果適用的話）。
- 請一律使用小寫字母和數字。 它可以包含連字號和底線，但不能有空格，而且限制為50個字元。 例如，如果您在這裡輸入 [**測試-供應專案-1** ]，則供應專案`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`web 位址會是。
- 選取 [**建立**] 之後，即無法變更供應專案識別碼。

輸入**供應專案別名**。 這是在合作夥伴中心使用供應專案的名稱。

- 此名稱不會在 marketplace 中使用，而且與向客戶顯示的供應專案名稱和其他值不同。
- 在您選取 [**建立**] 之後，即無法變更供應專案別名。

選取 [**建立**] 以產生供應專案並繼續。

## <a name="offer-overview"></a>供應項目概觀

此頁面會顯示發佈此供應專案所需步驟的視覺標記法（已完成和即將推出），以及每個步驟應該花多少時間才能完成。

其中包含的連結，可根據您所做的選擇，在此供應專案上執行作業。 例如：

- 如果供應專案為草稿-[刪除草稿供應](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)專案
- 如果供應專案已上線，則會[停止銷售供應](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)專案
- 如果供應專案處於預覽[狀態-上線](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 如果您尚未完成發行者登出，請[取消發佈](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>供應專案設定

### <a name="connect-lead-management"></a>連接潛在客戶管理

當您使用合作夥伴中心將您的供應專案發佈至 marketplace 時，您必須將其連接到您的客戶關係管理（CRM）系統。 這可讓您在有人對您的產品感興趣或使用時，立即收到客戶連絡人資訊。

1. 選取您要我們傳送潛在客戶至其中的潛在客戶目的地。 合作夥伴中心支援下列 CRM 系統：

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) For Customer Engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > 如果您的 CRM 系統未列于上方，請使用[Azure 資料表](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)或[Https 端點](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)來儲存客戶潛在客戶資料。 然後將資料匯出至您的 CRM 系統。

2. 在合作夥伴中心發佈時，將您的供應專案連接到潛在客戶目的地。
3. 確認已正確設定與潛在客戶目的地的連接。 在合作夥伴中心發佈之後，我們會驗證連線並將測試潛在客戶傳送給您。 當您在供應專案上線前進行預覽時，您也可以嘗試在預覽環境中自行購買供應專案，藉此測試您的潛在客戶連線。
4. 請確定與潛在客戶目的地的連線持續更新，讓您不會遺失任何潛在客戶。

以下是一些額外的潛在客戶管理資源：

- [潛在客戶管理總覽](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [潛在客戶管理常見問題集](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [常見潛在客戶設定錯誤](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [潛在客戶管理總覽](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)PDF （請確定您的快顯封鎖程式已關閉）

選取 [**儲存草稿**] 再繼續。

## <a name="properties"></a>屬性

此頁面可讓您定義用來在 marketplace、應用程式版本，以及支援供應專案之法律合約上分組供應專案的類別和產業。

### <a name="category"></a>類別

選取最少1個，最多三個類別。 這些類別是用來將您的供應專案放入適當的 marketplace 搜尋區域，並顯示在您的供應專案詳細資料頁面上。 在 [供應專案描述] 中，說明您的供應專案如何支援這些類別。

### <a name="industry"></a>業界

（選擇性）在每個產業底下選取最多兩個產業和兩個縱向。 雖然類別是用來顯示您的供應專案，但在搜尋篩選器中會使用產業和縱向，並套用於店面。 如果您的供應專案以特定產業和/或垂直為目標，請使用供應專案描述來說明您的供應專案如何支援選取的產業或縱向。 如果您的供應專案不是產業特有的，請將此區段保留空白。

> [!NOTE]
> 當我們打算引進新產業和縱向以改善供應專案探索經驗時，有些產業或縱向可能尚未在店面上顯示。 以（*）標示的產業和縱向將在未來的日期提供。 所有已發佈的供應專案都可以透過關鍵詞搜尋來探索。
<p>&nbsp;

| **業界** | **Subindustry** |
| --- | --- |
| * 汽車 | * 汽車 |
| 農業 | * 其他-Unsegmented |
| 散發 | * 批發<br>包裹和包裹運送 |
| Education | *高等教育<br> *主要和次要教育/K-12<br>* 程式庫和博物館 |
| 金融服務 | *銀行和資本市場<br> *等 |
| 政府 | *國防與情報（用來稱之為國家和公共安全性）<br> *公用安全和司法<br>* 文職政府 |
| 醫療保健（用來稱為「健康情況」） | *健全狀況<br>醫療給付*健全狀況提供者<br>* 製藥 |
| 製造和資源（用來稱為製造） | *化學和 Agrochemical<br> *離散製造<br>* 能源 |
| 零售和消費性商品（用來稱為零售） | *消費性<br>商品*零售商 |
| * 媒體和通訊（用來稱為媒體和娛樂） | *媒體和娛樂<br> *遠端 |
| 專業服務 | *法律<br> *合作夥伴專業服務 |
| * 架構和結構設計（用來稱為架構工程） | * 其他-Unsegmented |
| * 旅遊與旅程 | *飯店和休閒<br> *旅遊與運輸<br>* 餐廳和食物服務 |
| * 其他公用部門產業 | *林業和釣魚<br> *非營利組織 |
| * 房地產 | * 其他-Unsegmented |

### <a name="legal"></a>法律

#### <a name="terms-and-conditions"></a>條款及條件

若要提供您自己的自訂條款及條件，請在 [**條款及條件**] 方塊中輸入最多10000個字元。 如果您的條款及條件需要較長的描述，請輸入單一 web 連結來找到它們的位置。 它會向客戶顯示為作用中連結。

客戶必須接受這些條款，才能試用您的供應專案。

選取 [**儲存草稿**]，然後繼續進行下一節的供應專案清單。

## <a name="offer-listing"></a>供應專案清單

在這裡，您將定義在 marketplace 中顯示的供應專案詳細資料。 這包括供應專案名稱、描述、影像等等。

### <a name="language"></a>語言

選取您的供應專案將會列在哪一種語言。 目前，**英文（美國）** 是唯一可用的選項。

定義每個語言/市場的 marketplace 詳細資料（例如供應專案名稱、描述和影像）。 選取語言/市場名稱來提供此資訊。

> [!NOTE]
> 如果供應專案描述的開頭是片語「，此應用程式僅適用于 [非英文語言]，則不一定要有英文版的供應專案詳細資料。 也可以提供有用的連結，以不同于供應專案清單中所使用的語言來提供內容。

### <a name="name"></a>Name

您在此處輸入的名稱會顯示為供應專案的標題。 當您建立供應專案時，此欄位會預先填入您在 [**供應專案別名**] 方塊中輸入的文字。 您可以稍後變更此名稱。

名稱：

- 可以是商標（您也可以包含商標或著作權符號）。
- 長度不能超過50個字元。
- 不能包含 emoji。

### <a name="search-results-summary"></a>搜尋結果摘要

提供供應專案的簡短描述。 最多可有100個字元，並用於 marketplace 搜尋結果。

### <a name="description"></a>說明

提供供應專案的較長描述，最多3000個字元。 這會在 marketplace 清單總覽中向客戶顯示。

在您的描述中包含下列一或多項：

- 您的供應專案所提供的價值和重要權益。
- 類別或產業關聯，或兩者。
- 應用程式內購買機會。
- 任何必要的公開。

以下是撰寫描述的一些秘訣：

- 清楚描述您的供應專案在您的前幾個句子中的價值。 包含下列專案：
  - 供應項目的說明。
  - 受益于供應專案的使用者類型。
  - 客戶需要或發行供應專案位址。
- 請記住，前幾個句子可能會顯示在搜尋結果中。
- 不依賴特性和功能來銷售您的產品。 相反地，請專注于您的供應專案提供的值。
- 請嘗試使用產業專屬詞彙或以權益為基礎的用語。

若要讓您的供應專案描述更吸引人，請使用 rtf 編輯器來套用格式設定。

![使用 rich 文字編輯器](./media/rich-text-editor.png)

| <center>變更文字格式 | <center>新增專案符號或編號 | <center>新增或移除文字縮排 |
| --- | --- | --- |
| <center>![使用 rich 文字編輯器來變更文字格式](./media/text-editor3.png) |  <center>![使用 rich 文字編輯器加入清單](./media/text-editor4.png) |  <center>![使用 rich 文字編輯器縮排](./media/text-editor5.png) |

### <a name="search-keywords"></a>搜尋關鍵字

輸入最多三個選擇性搜尋關鍵字，以協助客戶在 marketplace 中尋找您的供應專案。 為了獲得最佳結果，請在您的描述中使用這些關鍵字。

### <a name="helpprivacy-web-addresses"></a>說明/隱私權網址

提供連結以協助客戶進一步瞭解您的供應專案。

#### <a name="help-link"></a>說明連結

輸入客戶可以深入瞭解您供應專案的網址。

#### <a name="privacy-policy-url"></a>隱私權原則 URL

輸入您組織的隱私權原則網址。 您必須負責確保您的供應專案符合隱私權法規。 您也必須負責在您的網站上張貼有效的隱私權原則。

### <a name="contact-information"></a>連絡資訊

您必須提供**支援連絡人**的名稱、電子郵件和電話號碼，以及**工程連絡人**。 客戶不會看到這則資訊。 可供 Microsoft 使用，並可提供給雲端解決方案提供者（CSP）合作夥伴。

- 支援連絡人（必要）：針對一般支援問題。
- 工程連絡人（必要）：針對技術問題和認證問題。
- CSP 方案連絡人（選擇性）：適用于與 CSP 計畫相關的轉售商問題。

在 [**支援連絡人**] 區段中，提供**支援網站**的網址，合作夥伴可以在此找到您的供應專案支援。

### <a name="supporting-documents"></a>支援檔

以 PDF 格式提供至少一個和最多三個相關的行銷檔。 例如，白皮書、摺頁冊、檢查清單或簡報。

### <a name="marketplace-images"></a>Marketplace 映射

提供要與您的供應專案搭配使用的標誌和影像。 所有影像都必須是 .png 格式。 模糊影像將會遭到拒絕。

>[!NOTE]
>如果您在上傳檔案時發生問題，請確定您的區域網路不會https://upload.xboxlive.com封鎖合作夥伴中心所使用的服務。

#### <a name="store-logos"></a>Microsoft Store標誌。

以兩個圖元大小提供供應專案標誌的 .png 檔案：
- **小型**（48 x 48）
- **大型**（216 x 216）

這兩個標誌都是必要的，而且會用於 marketplace 清單中的不同位置。

#### <a name="screenshots"></a>螢幕擷取畫面

新增至少一個和最多五個螢幕擷取畫面，以顯示供應專案的運作方式。 每個都必須是 1280 x 720 圖元大小和 .png 格式。

#### <a name="videos-optional"></a>影片（選擇性）

新增最多五個示範您供應專案的影片。 以 1280 x 720 圖元的大小，輸入影片的名稱、其網址和縮圖的 png 影像。

#### <a name="additional-marketplace-listing-resources"></a>其他 marketplace 清單資源

若要深入瞭解如何建立供應專案清單，請參閱[供應專案清單最佳作法](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)。

## <a name="technical-configuration"></a>技術設定

在 Power BI 服務中將您的應用程式升階至生產環境，並提供可讓客戶安裝應用程式的 Power BI 應用程式安裝程式連結。 如需詳細資訊，請參閱[在 Power BI 中使用儀表板和報表發佈應用程式](https://docs.microsoft.com/power-bi/service-create-distribute-apps)。

## <a name="supplemental-content"></a>補充內容

提供供應專案的其他相關資訊，以協助我們進行驗證。 此資訊不會向客戶顯示或發佈至 marketplace。

### <a name="validation-assets"></a>驗證資產

（選擇性）新增指示（最多3000個字元），以協助 Microsoft 驗證小組設定、連接及測試您的應用程式。 包含可用於測試 [連接資料] 選項的一般設定、帳戶、參數或其他資訊。 此資訊僅適用于驗證小組，而且僅供驗證之用。

## <a name="review-and-publish"></a>審查和發行

完成供應專案的所有必要區段之後，您可以提交供應專案來進行審查和發佈。

在入口網站的右上角，選取 [**審查併發布**]。

在 [審查] 頁面上，您可以：

- 請參閱供應專案每個區段的完成狀態。 在供應專案的所有區段都標示為完成之前，您無法發行。
  - **未啟動**-區段尚未啟動，需要完成。
  - **不完整**-區段包含需要修正的錯誤，或需要您提供詳細資訊。 如需指引，請參閱本檔稍早的章節。
  - **Complete** -區段包含所有必要的資料，而且沒有任何錯誤。 供應專案的所有區段都必須完成，您才能提交供應專案。
- 為認證小組提供測試指示，以確保您的應用程式已正確測試。 此外，請提供有助於瞭解您的供應專案的任何補充附注。

若要提交供應專案以供發佈，請選取 [**發佈**]。

我們會傳送一封電子郵件給您，讓您知道供應專案的預覽版本何時可供審查和核准。 若要將您的供應專案發佈到公開（或如果私人供應專案為私人物件），請移至 [合作夥伴中心]，然後選取 [**上線**]。
