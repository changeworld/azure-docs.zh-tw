---
title: 建立 Power BI 應用產品 /服務 - Azure 應用商店
description: 瞭解如何創建和發佈 Power BI 應用產品/服務到 Microsoft AppSource。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 776311d6e6395cbe462f958bd8685fa0259e1fc2
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674185"
---
# <a name="create-a-power-bi-app"></a>建立 Power BI 應用程式

> [!IMPORTANT]
> 我們將 Power BI 應用產品的管理從雲端合作夥伴門戶遷移到合作夥伴中心。 在遷移產品/服務之前,請按照為雲合作夥伴門戶[創建 Power BI 應用產品/服務](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-create-offer)以管理您的優惠的說明進行操作。

本文介紹如何創建和發佈 Power BI 應用產品/服務到 Microsoft [AppSource](https://appsource.microsoft.com/)。

在創建 Power BI 應用產品/服務之前,必須在合作夥伴中心中具有商業市場帳戶。 如果您尚未建立,請參閱[在合作夥伴中心帳戶中建立商業市場帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)。

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左邊導航選單中,選擇**商業市場** > **概述**。

    :::image type="content" source="media/power-bi-menu-overview.png" alt-text="商業市場選單概述" border="false":::

3. 選擇 **= 新的優惠** > **電源 BI 應用程式**。 將顯示 **「新建產品/服務**」對話框。

> [!IMPORTANT]
> 如果**power BI 應用**選項&#39;未显示或未&#39;未啟用,則您的帳戶&#39;無權創建此產品/服務類型。 請檢查您是否&#39;滿足此產品/服務類型的所有[要求](create-power-bi-app-overview.md),包括註冊開發人員帳戶

### <a name="offer-id-and-alias"></a>優惠識別碼與別名

輸入**優惠代碼**。 這是您帳戶中每個產品/服務的唯一標識碼。

- 此 ID 在市場產品/服務的網站位址和 Azure 資源管理器範本(如果適用)中可見。
- 請一律使用小寫字母和數字。 它可以包括連字元和下劃線,但沒有空格,並且限制為 50 個字元。 例如,如果輸入**測試-產品/服務 -1,** 則產品`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`/服務 Web 位址將為 。

- 選擇 「**建立**」 後,無法更改優惠識別碼。

輸入**產品/服務別名**。 這是用於指合作夥伴中心產品/服務的名稱。

- 此名稱不在市場上使用,並且不同於向客戶顯示的要約名稱和其他值。
- 選擇 後無法變更 **。**

輸入這兩個值後,選擇 **"創建**"以繼續到"產品/服務概覽"頁。

## <a name="offer-overview"></a>供應項目概觀

**"產品/服務概述"** 頁顯示發佈此產品/服務所需的步驟(已完成和即將推出)以及每個步驟應完成多長時間的可視化表示形式。

它包括基於您做出的選擇對此產品/服務執行操作的連結。 例如：

- 如果要約是草稿 -[刪除草稿要約](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- 如果優惠是即時的 -[停止銷售優惠](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- 如果優惠處於預覽版 -[上線](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 如果您尚未完成發行者登出 ─[取消發佈](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>優惠設定

按照以下步驟設置您的產品/服務。

### <a name="connect-lead-management"></a>連線潛在客戶管理

使用合作夥伴中心將產品/服務發佈到市場時,必須將其連接到客戶關係管理 (CRM) 系統。 這樣,一旦有人對您的產品表示興趣或使用,您就可以立即接收客戶聯繫資訊。

1. 選取您要我們傳送潛在客戶至其中的潛在客戶目的地。 合作夥伴中心支援以下 CRM 系統:

    - [客戶互動的動態 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics)
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > 如果上面未列出 CRM 系統,請使用[Azure 表](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)或[Hs 終結點](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)來儲存客戶潛在顧客數據。 然後將數據匯出到 CRM 系統。

2. 在合作夥伴中心發佈時,請將您的產品/服務連接到潛在顧客目標。
3. 確認與潛在顧客目標的連接配置正確。 在合作夥伴中心發佈後,我們將驗證連接並向您發送測試線索。 在產品/服務上線之前預覽產品/服務時,還可以嘗試在預覽環境中自行購買產品/服務,以測試潛在顧客連接。
4. 確保與潛在顧客目標的連接保持更新狀態,這樣您就不會丟失任何潛在顧客。

下面是一些額外的潛在客戶管理資源:

- [潛在客戶管理概述](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [潛在客戶管理常見問題集](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [常見潛在客戶設定錯誤](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [潛在客戶管理概述](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)PDF (確保彈出視窗封鎖程式關閉)

選擇 **"保存"草稿**,然後再繼續下一節"屬性"。

## <a name="properties"></a>屬性

此頁面允許您定義用於在市場對產品/服務進行分組的類別和行業、應用版本和支援您的產品/服務的法律合同。

### <a name="category"></a>類別

選擇至少一個,最多選擇三個類別。 這些類別用於將您的產品/服務放入相應的市場搜索區域,並顯示在您的優惠詳細信息頁面上。 在產品/服務說明中,說明您的優惠如何支援這些類別。

### <a name="industry"></a>業界

可以選擇每個行業下最多選擇兩個行業和兩個垂直行業。 雖然類別用於顯示您的產品/服務,但行業和垂直在搜索篩選器中使用,並應用於網店。 如果您的優惠針對特定行業和/或垂直行業,請使用產品/產品/服務說明來解釋您的產品/服務如何支援所選行業或垂直行業。 如果您的報價不是特定於行業&#39;,請保留此部分空白。

> [!NOTE]
> 當我們努力引入新的行業和垂直,以改善產品供應發現體驗時,某些行業或垂直行業可能尚未在店面中可見。 標有 (*) 的行業和垂直行業將在未來日期提供。 所有已發佈的優惠均可通過關鍵字搜索發現。
<p>&nbsp;

| **業界** | **子行業** |
| --- | --- |
| *汽車 | *汽車 |
| 農業 | *其他 - 未分段 |
| 散發 | *批發<br>包裹和包裹運輸 |
| 教育訓練 | *高等教育<br>* 中小學教育 / K-12<br>*圖書館和博物館 |
| 金融服務 | *銀行和資本市場<br>* 保險 |
| 政府 | *國防和情報(過去稱為國家與公共安全)<br> *公共安全與正義<br>*文官政府 |
| 醫療保健(過去稱為健康) | *健康付款人<br>* 健康提供者<br>*製藥 |
| 製造與資源(過去稱為製造) | *化學和農用化學<br>* 離散製造<br>*能源 |
| 零售和消費品(過去稱為零售) | *消費品<br>* 零售商 |
| *媒體與通訊(過去稱為媒體和娛樂) | *媒體和娛樂<br>* 電信 |
| 專業服務 | *法律<br>* 合作夥伴專業服務 |
| *建築和建築(過去稱為建築工程) | *其他 - 未分段 |
| *酒店和旅行 | *酒店及休閒<br>* 旅行與交通<br>*餐廳和餐飲服務 |
| *其他公共部門行業 | *林業和漁業<br>* 非營利組織 |
| *房地產 | *其他 - 未分段 |

### <a name="legal"></a>法律

#### <a name="terms-and-conditions"></a>條款和條件

要提供您自己的自定義條款和條件,請在 **「條款和條件**」框中輸入最多 10,000 個字元。 如果您的條款和條件需要較長的描述,請輸入單個 Web 連結,指向可以找到它們的位置。 它將作為活動連結向客戶顯示。

客戶必須先接受這些條款,然後才能試用您的優惠。

選擇 **"保存"草稿**,然後再繼續下一節"優惠清單"。

## <a name="offer-listing"></a>優惠清單

在這裡,您將定義市場上顯示的報價詳細資訊。 這包括產品/服務名稱、說明、圖像等。

### <a name="language"></a>Language

選擇您的優惠將在其中列出的語言。 目前,**英語(美國)** 是唯一可用的選項。

定義每種語言/市場的市場詳細資訊(如產品/服務名稱、描述和圖像)。 選擇語言/市場名稱以提供此資訊。

> [!NOTE]
> 如果要約說明以短語開頭,則優惠詳細資訊不需要以英文提供,&quot;此應用程式僅提供 [非英語語言]。&quot;&#39;提供有用的链接,以&#39;與產品/服務清單中使用的語言不同的語言提供內容。

### <a name="name"></a>名稱

您在此處輸入的名稱顯示為產品/服務的標題。 此欄位預先填充您在建立產品/服務時在 **「產品/服務」別名**框中輸入的文字。 您可以稍後變更此名稱。

名稱:

- 可以註冊商標(您可以包括商標或版權符號)。
- 不能超過50個字元長。
- 不能包括表情符號。

### <a name="search-results-summary"></a>搜尋結果摘要

提供您的優惠的簡短說明。 這最多可達 100 個字元,並且用於市場搜尋結果。

### <a name="description"></a>描述

提供較長範圍的報價說明,最多 3,000 個字元。 這在市場清單概述中向客戶顯示。

在描述中包括以下一項或多項:

- 您的產品提供的價值和關鍵優勢。
- 類別或行業協會,或兩者。
- 應用內購買機會。
- 任何必需的披露。

以下是編寫說明的一些提示:

- 清楚地描述你描述前幾句的報價的價值。 包括以下專案:
  - 供應項目的說明。
  - 從產品/服務中受益的用戶類型。
  - 客戶需求或發出產品/服務位址。
- 請記住,搜尋結果中可能會顯示前幾個句子。
- 不要依賴特性和功能來銷售您的產品。 相反,關注您的產品/服務提供的價值。
- 嘗試使用行業特定的詞彙或基於利益的措辭。

為了使您的產品/**服務更具**吸引力,請使用豐富的文本編輯器來設置說明的格式。 富文字編輯器允許您添加數位、專案符號、粗體、斜體和縮進,以使描述更具可讀性。

:::image type="content" source="media/power-bi-rich-text-editor.png" alt-text="說明富文字編輯器" border="false":::

### <a name="search-keywords"></a>搜尋關鍵字

最多輸入三個可選搜索關鍵字,幫助客戶在市場上找到您的產品/服務。 為獲得最佳效果,還應在說明中使用這些關鍵字。

### <a name="helpprivacy-web-addresses"></a>說明/隱私 Web 位址

提供連結,幫助客戶更好地瞭解您的產品/服務。

#### <a name="help-link"></a>協助連結

輸入網址,客戶可以在其中瞭解有關您的優惠的更多內容。

#### <a name="privacy-policy-url"></a>隱私權原則 URL

輸入組織的隱私政策的網址。 您有責任確保您的報價符合隱私法律和法規。 您還負責在您的網站上發佈有效的隱私政策。

### <a name="contact-information"></a>連絡資訊

您必須提供**支援聯絡人**和**工程連絡人**的姓名、電子郵件和電話號碼。 此資訊不會向客戶顯示&#39;。 它可供 Microsoft 使用,並可能提供給雲端解決方案供應商 (CSP) 合作夥伴。

- 支援連絡人(必需):有關一般支持問題。
- 工程聯繫人(必需):有關技術問題和認證問題。
- CSP 計劃聯絡人(可選):對於與 CSP 計劃相關的轉銷商問題。

在 **"支援"連絡人**部分中,提供**支援網站的**網址,合作夥伴可以在其中找到對您的優惠的支援。

### <a name="supporting-documents"></a>證明檔案

以 PDF 格式提供至少一個和最多三個相關的營銷文檔。 例如,白皮書、摺頁冊、檢查表或演示文稿。

### <a name="marketplace-images"></a>市場映射

提供徽標和圖像,以便與您的產品配合使用。 所有圖像必須採用 .png 格式。 模糊圖像將被拒絕。

#### <a name="store-logos"></a>Microsoft Store標誌。

提供您的優惠的 .png 檔案&#39;徽標,兩種大小:**小**(48 x 48 圖元)**和大**(216 x 216 像素)。

這兩個徽標是必需的,並且在市場清單中的不同位置使用。

#### <a name="screenshots"></a>螢幕擷取畫面

添加至少一個最多五個屏幕截圖,顯示您的優惠如何工作。 每個圖元的大小和 .png 格式必須為 1280 x 720 圖元。

#### <a name="videos-optional"></a>視訊(選擇性的)

最多添加五個視頻,演示您的優惠。 輸入影片&#39;名稱、Web 位址和縮圖 .png 影片影像,大小為 1280 x 720 圖元。

#### <a name="additional-marketplace-listing-resources"></a>其他市場清單資源

要瞭解有關建立優惠清單的更多資訊,請參閱[產品/服務清單最佳實務](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)。

## <a name="technical-configuration"></a>技術設定

將 Power BI 服務中的應用提升為生產,並提供 Power BI 應用安裝程式連結,使客戶能夠安裝你的應用。 有關詳細資訊,請參閱在[Power BI 中發表具有儀表板和報表的應用](https://docs.microsoft.com/power-bi/service-create-distribute-apps)。

## <a name="supplemental-content"></a>補集內容

提供有關您的優惠的其他資訊,以幫助我們驗證它。 此資訊不會向客戶顯示&#39;,也不會發佈到市場。

### <a name="validation-assets"></a>驗證資產

或者,添加說明(最多 3,000 個字元),以説明 Microsoft 驗證團隊配置、連接和測試你的應用。 包括可用於測試"連接資料"選項的典型配置設置、帳戶、參數或其他資訊。 此資訊僅對驗證團隊可見,僅用於驗證目的。

## <a name="review-and-publish"></a>稽核和發佈

&#39;完成产品/服務的所有必需部分後,您可以提交報價以供審核和發佈。

在門戶的右上角,選擇 **「審閱」併發佈**。

在評論頁面上,您可以:

- 請參閱產品/服務的每個部分的完成狀態。 在產品/服務的所有部分都標記為完整之前,您&#39;發佈。
  - **未啟動**- 部分尚未啟動,需要完成。
  - **不完整**- 該部份有需要修復或要求您提供詳細資訊的錯誤。 有關指導,請參閱本文檔前面的部分。
  - **完成**- 該部分具有所有必需的數據,並且沒有錯誤。 優惠的所有部分必須完成,然後才能提交報價。
- 向認證團隊提供測試說明,以確保應用測試正確。 此外,提供有助於瞭解您的報價的任何補充說明。

要提交發佈要約,請選擇 **「發佈**」。。

我們將向您發送一封電子郵件,告知您產品/服務預覽版本何時可供審核和批准。 要向公眾發佈您的優惠(或者如果私人優惠,則向私人受眾),請轉到合作夥伴中心並選擇 **"Go-live**"。
