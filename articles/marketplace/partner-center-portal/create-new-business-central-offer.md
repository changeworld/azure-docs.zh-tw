---
title: 在微軟商業市場創建新的 Dynamics 365 商業中心產品
description: 如何創建新的 Dynamics 365 商業中心產品/服務,用於在 Azure 應用商店、Microsoft AppSource 中列出或銷售,或者使用 Microsoft 合作夥伴中心的商業市場門戶透過雲端解決方案供應商 (CSP) 計畫進行上市或銷售。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 9bb1059087bdf8fc4eb852be7a995febc3336713
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115271"
---
# <a name="create-a-new-dynamics-365-business-central-offer"></a>建立新的 Dynamics 365 商業中心產品/服務

本文介紹如何創建新的 Dynamics 365 業務中心產品/ [Microsoft Dynamics 365 商業中心](https://dynamics.microsoft.com/business-central)是一個企業資源規劃 (ERP) 系統,可處理各種業務流程,包括財務、運營、供應鏈、CRM 以及專案管理和電子商務。 高級軟體包還支援經典部署模型和製造。 Dynamics 365 商務中心的所有優惠都必須經過我們的認證流程。

要開始創建 Dynamics 365 商業中心產品/服務,請確保首先[創建合作夥伴中心帳戶](./create-account.md)並打開[商業市場儀表板](https://partner.microsoft.com/dashboard/commercial-marketplace/offers),並選擇 **「概述」** 頁。

![合作夥伴中心上的商業市場儀錶板](./media/new-offer-overview.png)

>[!Note]
> 產品/服務發佈後,只有在重新發佈後,才會在系統和商店前面更新對合作夥伴中心中提供的優惠進行編輯。 請確保在進行更改後提交要發佈的要約。

## <a name="create-a-new-offer"></a>建立新的供應項目

選擇 **+ 新優惠**按鈕,然後選擇**Dynamics 365 商業中心**功能表項。 將顯示「**新建產品/服務**」對話框。

### <a name="offer-id-and-alias"></a>優惠識別碼與別名

- **優惠 ID**:帳戶中每個產品/服務的唯一標識符。 此 ID 將在市場產品/服務的 URL 位址和 Azure 資源管理器範本(如果適用)中向客戶可見。 優惠 ID 必須是小寫字母數位字元(包括連字元和下劃線,但沒有空格),限制為 50 個字元,並且在選擇 「**創建**」後無法更改。  例如,如果您在此處輸入*測試報價 1,* 則產品/服務`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`網址將為 。

- **優惠別名**:用於指合作夥伴中心內產品/服務的名稱。 此名稱不會在市場中使用,並且與向客戶顯示的要約名稱和其他值不同。 選擇 「**建立**」 後,無法更改此值。

輸入**優惠 ID**和**優惠別名**後,選擇 **"創建**"。 然後,您將能夠處理產品/服務的所有不同部分。

## <a name="offer-setup"></a>優惠設定

**"產品/服務設置"** 頁要求提供以下資訊。 請務必在完成這些欄位後選擇 **「保存**」。

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>您希望潛在客戶如何與此上市產品進行交互?

選擇要用於此優惠的選項。

#### <a name="get-it-now-free"></a>立即取得(免費)

通過提供有效的 URL(從*http*或*Hths*開頭),客戶可以存取你的應用,從而免費向客戶列出您的優惠。  例如： `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>免費試用(清單)

通過提供有效的 URL(從*http*或*Htht*開頭),以便客戶獲得試用版,從而向客戶列出您的優惠。"  例如： `https://contoso.com/trial/my-app` 。 免費試用版產品清單的優惠由您的服務創建、管理和配置,並且沒有由 Microsoft 管理的訂閱。

> [!NOTE]
> 應用程式將透過試用連結收到的權杖只能用於透過 Azure 活動目錄 (Azure AD) 取得使用者資訊,以自動在應用中創建帳戶。 使用此令牌進行身份驗證不支援 Microsoft 帳戶。

#### <a name="contact-me"></a>與我連絡

通過連接客戶關係管理 (CRM) 系統收集客戶聯繫資訊。 將請求客戶共用其信息的許可權。 這些客戶詳細資訊以及找到產品/服務的產品/服務的產品/地區的產品/服務名稱、ID和市場源將發送到您配置的 CRM 系統。 有關設定 CRM 的詳細資訊,請參考[連線潛在客戶管理](#connect-lead-management)。 

### <a name="test-drive"></a>試用產品

試駕是向潛在客戶展示您的報價的絕佳方式,為他們提供"購買前試用"選項,從而提高轉化率並生成高素質潛在客戶。 [瞭解有關測試驅動器的更多詳細資訊。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

要啟用試駕,請選中 **「啟用試駕」** 框。 然後,您需要在[測試驅動器技術配置](#test-drive-technical-configuration)配置中配置演示環境,以便客戶在固定時間段內試用您的產品/服務。 

#### <a name="type-of-test-drive"></a>試駕類型

然後選取下列選項：

- **[Azure 資源管理員](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)**:包含構成解決方案的所有 Azure 資源的部署範本。 適合此方案的產品僅使用 Azure 資源。
- **[面向業務中心的動態 365:Microsoft](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** 託管和維護業務中央企業資源規劃系統(財務、運營、供應鏈、CRM 等)的試駕服務(包括配置和部署)。  
- **[面向客戶參與的動態 365:Microsoft](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** 託管和維護客戶參與系統的試駕服務(包括配置和部署)(銷售、服務、專案服務、現場服務等)。  
- **[操作動態 365:](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** Microsoft 託管和維護財務和運營企業資源規劃系統(財務、運營、製造、供應鏈等)的試駕服務(包括配置和部署)。 
- **[邏輯應用](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)**:包含所有複雜解決方案體系結構的部署範本。 任何自定義產品都應使用這種類型的試駕。
- **[Power BI:](https://docs.microsoft.com/power-bi/service-template-apps-overview)** 指向自定義儀錶板的嵌入式連結。 想要展示互動式 Power BI 視覺物件的產品應使用這種類型的測試驅動器。 您只需要上傳內嵌的 Power BI URL。

#### <a name="additional-test-drive-resources"></a>其他試駕資源

- [試駕技術最佳實踐](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [試駕行銷最佳實踐](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [試駕概述一個尋呼機](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>連線潛在客戶管理

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

有關詳細資訊,請參閱[潛在顧客管理概述](./commercial-marketplace-get-customer-leads.md)。

在繼續下一節之前,請記住**儲存**!

## <a name="properties"></a>屬性

屬性**頁面**允許您定義用於在市場對產品/服務進行分組的類別和行業、應用版本和支援產品/服務的法律合同。 完成此頁面後選擇 **「儲存**」。

### <a name="category"></a>類別

選擇至少一個且最多三個類別,這些類別將用於將產品/服務放入相應的市場搜索區域。 請務必在產品/服務說明中標註您的產品/服務如何支援這些類別。 

### <a name="industry"></a>業界

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>應用程式版本

輸入產品/服務的版本號。 客戶將在產品/服務的詳細信息頁面上看到此版本。

### <a name="terms-and-conditions"></a>條款和條件

在條款與條件欄位中提供您自己的法律**條款和條件**。 您還可以提供可在其中找到您的條款和條件的 URL。 客戶在試用您的優惠之前,必須接受這些條款。

## <a name="offer-listing"></a>優惠清單

"產品/服務"列表頁是定義產品/服務的詳細資訊(如名稱、說明、圖像等)的位置。

> [!NOTE]
> 您只能提供一種語言的優惠清單詳細資訊。 它不需要是英文的,只要報價說明以短語開頭,"此應用程式僅以[非英語]提供。 提供*幫助連結 URL*以提供產品/服務列表內容中所用語言以外的語言也是可以接受的。

### <a name="name"></a>名稱

您在此處輸入的名稱將作為產品/服務列表的標題顯示給客戶。 此欄位預填充了創建**產品/服務時為"產品/服務"別名**輸入的文本,但您可以更改此值。 此名稱可以註冊商標(您可以包括商標或版權符號)。 名稱不能超過 50 個字元,不能包含任何表情符號。

### <a name="short-description"></a>簡短描述

提供產品/服務的簡短說明(最多 100 個字元),可在市場搜尋結果中使用。

### <a name="description"></a>描述

提供更久的報價說明(最多 3,000 個字元)。 此描述將顯示在市場清單概述中的客戶。 包括您的產品/服務的價值主張、主要優勢、類別和/或行業協會、應用內購買機會以及任何必需的披露。 

編寫說明的一些提示:  

- 在您的描述中的前幾個句子清楚描述您供應項目的價值主張。 在價值主張中包括以下專案:
  - 產品的描述
  - 從產品中受益的使用者類型
  - 產品解決的客戶需求或痛苦
- 請記住，前幾個句子可能會顯示在搜尋引擎結果中。  
- 請勿依賴特色與功能來銷售您的產品。 反之，要鎖定在您所提供的價值。  
- 請盡可能使用業界特定詞彙或凸顯優點的字眼。
- 請考慮使用 HTML 標記來格式化您的描述,使其更具吸引力。

為了使您的報價描述更具吸引力,請使用豐富的文本編輯器來設置您的說明格式。

![使用富文字編輯器](./media/text-editor2.png)

使用以下說明使用富文字編輯器:

- 要更改內容的格式,請突出顯示要設定格式的文字並選擇文字樣式,如下所示:

     ![使用富文字編輯器改變文字格式](./media/text-editor3.png)

- 要加入文字加入項目符號或編號清單,請使用以下選項:

     ![使用富文字編輯器加入清單](./media/text-editor4.png)

- 要向文字加入或刪除縮排,請使用以下選項:

     ![使用富文字編輯器縮排](./media/text-editor5.png)

### <a name="search-keywords"></a>搜尋關鍵字

您可以選擇輸入最多三個搜索關鍵字,幫助客戶在市場上找到您的產品/服務。 為獲得最佳效果,請嘗試在描述中使用這些關鍵字。

### <a name="products-your-app-works-with"></a>與您的應用程式一起運作的產品

如果您想讓客戶知道你的應用適用於特定產品,請在此處輸入最多三個產品名稱。

### <a name="helpprivacy-urls"></a>說明/隱私網址

本部分允許您提供連結,幫助客戶更好地瞭解您的產品/服務。

#### <a name="help-link"></a>協助連結

輸入客戶可以詳細瞭解您的優惠的 URL。 **您的幫助連結**不能與您的**支援 URL**相同(如下所述)。

#### <a name="privacy-policy-link"></a>隱私政策連結

輸入組織隱私政策的 URL。 您有責任確保您的應用符合隱私法律和法規,並提供有效的隱私政策。

### <a name="contact-information"></a>連絡資訊

在本節中,您必須提供**支援聯繫人**和**工程聯繫人**的姓名、電子郵件和電話號碼。 此資訊不會向客戶顯示,但可供 Microsoft 使用,並且可能會提供給 CSP 合作夥伴。

在 **"支援"連絡人**部分中,還必須提供**支援 URL,** 其中 CSP 合作夥伴可以找到對您的產品/服務的支援。 您的支援 URL 不能與您的**幫助連結**相同。

### <a name="supporting-documents"></a>證明檔案

在此處至少提供一份(最多三份)相關營銷文檔,如白皮書、摺頁冊、檢查表或演示文稿。 這些檔案必須採用 .pdf 格式。

### <a name="marketplace-images"></a>市場映射

在本節中,您可以提供向客戶展示產品/服務時將使用的徽標和圖像。 所有圖像必須採用 .png 格式。 上傳您的優惠徽標有兩種尺寸:**小 (48 x 48)** **和大 (216 x 216)**。

#### <a name="screenshots"></a>螢幕擷取畫面

添加顯示優惠工作原理的螢幕截圖。 至少需要三個屏幕截圖,您最多只能添加五個屏幕截圖。 所有螢幕截圖必須為 1280 x 720 圖元。

#### <a name="videos"></a>影片

您可以選擇添加最多五個演示您的優惠的視頻。 這些影片應託管在 YouTube 和/或 Vimeo 上。 對於每個視訊,輸入影片的名稱、URL 和視訊的縮圖影像(1280 x 720 像素)

#### <a name="additional-marketplace-listing-resources"></a>其他市場清單資源

- [市場產品/服務清單的最佳實務](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>可用性

"**可用性「** 頁面為您提供了有關產品/服務在何處以及如何提供選項。

### <a name="markets"></a>市場

本部分允許您指定報價可用的市場。 為此,請選擇 **「編輯市場**」,該市場將顯示**市場選擇**彈出視窗。

默認情況下,不會選擇任何市場,但您必須選擇至少一個市場才能發佈您的報價。 按下 **「選擇全部」** 以使您的產品/服務在每個可能的市場都可用,或選擇要添加的特定市場。 完成後,選擇"**保存**"。

此處的選擇僅適用於新的收購;如果有人已經擁有你的應用程式在某一市場,你後來刪除該市場,誰已經在這個市場的報價的人可以繼續使用它,但在這個市場沒有新的客戶將能夠得到您的報價。

> [!IMPORTANT]
> 您有責任滿足任何當地法律要求,即使這些要求未在此處或合作夥伴中心列出。

請記住,即使您選擇所有市場、當地法律和限制或其他因素,也可能阻止某些優惠在某些國家和地區上市。

### <a name="preview-audience"></a>預覽受眾

在將優惠即時發布到更廣泛的市場產品/服務之前,您首先需要將其提供給有限的**預覽版受眾**。 在此處輸入**Hide 鍵**(僅使用小寫字母和/或數位的任何字串)。 預覽受眾的成員可以使用此隱藏鍵作為令牌,在市場中查看優惠的預覽。

然後,當您準備好使產品/服務可用並刪除預覽限制時,您需要刪除**隱藏鍵**並再次發佈。

## <a name="technical-configuration"></a>技術設定

"**技術設定**「頁定義用於連接到產品/服務的技術詳細資訊。 此連接使我們能夠為最終客戶提供您的報價,如果他們選擇購買。

### <a name="package-type"></a>套件類型

選擇適用於您的產品/服務的選項:

- **添加**: 附加應用擴展了 Dynamics 365 業務中心的經驗和現有功能。 有關詳細資訊,請參閱[附加應用](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps)。
- **連接**:Connect 應用可用於必須在 Dynamics 365 業務中心和第三方解決方案或服務之間建立點對點連接的情況。 有關詳細資訊,請參閱[連線](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps)。

### <a name="file-upload"></a>檔案上傳

如果選擇上面**的「添加」**, 您將上傳產品/服務包檔,以及其具有依賴項的任何擴展名的包檔。

#### <a name="extensions-package-file"></a>延伸套件檔案

上傳產品/服務擴展包檔 (.app) 檔。

#### <a name="library-package-file"></a>套件檔案

如果必須安裝您的產品/服務以及另一個不會發佈到市場的擴展,則需要這樣做。 如果是這樣,請在此處上傳其 .app 檔。

#### <a name="dependency-package-file"></a>相依套件檔案

如果必須安裝您的產品/服務以及已發佈到市場的另一個擴展,則需要。 如果是這樣,請在此處上傳`.app``.zip`其或檔。

### <a name="url-to-app-installation"></a>套用安裝網址

如果選擇上面的 **「連接**」,請在此處提供應用安裝的 URL。 對於不需要安裝的已連接服務,請為服務登錄頁或註冊頁提供 URL。

## <a name="test-drive-technical-configuration"></a>試駕技術設定

如果在[「產品/服務設置」](#offer-setup)頁中選擇 **「啟用試駕**」,則需要在此處提供詳細資訊,以便客戶體驗您的產品/服務中的試駕。

通過 **「測試驅動器**」頁面,您可以設置示範(或"試駕"),使客戶能夠在承諾購買之前試用您的產品/服務。 在文章"[什麼是試駕"中瞭解更多資訊。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive) 如果您不想再為產品/服務提供試駕,請返回**[「產品/服務設定](#offer-setup)**」頁面並取消選中**啟用試駕**。

提供以下類型的測試驅動器,每種驅動器都有自己的技術配置要求。

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [邏輯應用程式](#technical-configuration-for-logic-app-test-drive)
- [電源 BI(](#technical-configuration-not-required-for-power-bi-test-drives)不需要技術設定 )

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure 資源管理員測試磁碟機的技術設定

包含構成解決方案的所有 Azure 資源的部署範本。 適合此方案的產品僅使用 Azure 資源。 詳細瞭解如何設定 Azure[資源管理員測試磁碟機](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)。

- **區域**(必需):目前有 26 個 Azure 支援的區域,可以在其中提供您的試駕。 通常,您需要在預期客戶數量最多的區域提供試駕,以便他們選擇最接近的區域以獲得最佳性能。 您需要確保允許訂閱部署所選擇的每個區域所需的所有資源。

- **實例**:選擇類型(熱或冷)和可用實例數,這些實例將乘以產品/服務可用的區域數。

**熱**:這種類型的實例已部署,並等待每個選定區域的訪問。 客戶可以立即存取測試驅動器*的熱*實例,而無需等待部署。 缺點是，這些執行個體一直在您的 Azure 訂用帳戶中執行，因此將會產生可觀的運作成本。 強烈建議至少使用一個*熱*實例,因為大多數客戶不希望等待完全部署,如果沒有*熱*實例,則會導致客戶使用量下降。

**冷**:這種類型的實例表示每個區域可能部署的實例總數。 冷實例要求在整個測試驅動器資源管理器範本在客戶請求試駕時進行部署,因此*冷*實例的載入速度比*熱*實例慢得多。 權衡是,您只需要在試駕期間付費,它*並不總是*像*使用熱*實例那樣在 Azure 訂閱上運行。

- **試用 Azure 資源管理員樣本**:上傳包含 Azure 資源管理器範本的 .zip。  在快速入門文章使用[Azure 門戶建立和部署 Azure 資源管理器樣本"](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)中瞭解有關建立 Azure 資源管理器樣本詳細資訊。

- **試駕持續時間**(必需):輸入試駕將保持活動狀態的時間長度(以小時數計)。 在此持續時間過後，試用產品將會自動終止。 此持續時間只能由整個小時數設置(例如,"2"小時,"1.5"無效)。

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 試駕的技術配置

Microsoft 可以通過託管和維護使用此類測試驅動器的服務預配和部署來消除設置測試驅動器的複雜性。 無論試駕是面向業務中心、客戶參與還是運營受眾,此類託管試駕的配置都是相同的。

- **最大併發測試驅動器**(必需):設置一次可以使用試駕的最大客戶數。 每個併發使用者將在測試驅動器處於活動狀態時使用 Dynamics 365 許可證,因此您需要確保有足夠的許可證來支援最大限制集。 建議值為 3-5 個。

- **試駕持續時間**(必需):通過定義小時數輸入試駕將保持活動狀態的時間長度。 在此多小時後,會話將結束,不再使用您的許可證之一。 根據產品/服務的複雜性,我們建議值為 2-24 小時。 此持續時間只能由整個小時數設置(例如,"2"小時,"1.5"無效)。  如果使用者時間已用完,並且希望再次訪問試駕,用戶可以請求新的會話。

- **實體設定網址(** 必要):客戶將開始其試駕的網址。 通常,使用安裝示例數據(例如,)`https://testdrive.crm.dynamics.com`運行應用的 Dynamics 365 實例的 URL。

- **實體 Web API URL(** 必要 ):透過登入到 Microsoft 365 帳戶並瀏覽到 **「設定**\&」gt,檢索 Dynamics 365 實體的 Web API URL;**自定義**\&gt;**開發人員資源**\&gt;**實體 Web API(服務根 URL),** 複製此處找到`https://testdrive.crm.dynamics.com/api/data/v9.0`的網址( 例如。

- **角色名稱**(必需):提供您在自定義 Dynamics 365 試駕中定義的安全角色名稱,這些角色將在使用者試駕期間分配給使用者(例如,測試驅動器角色)。

### <a name="technical-configuration-for-logic-app-test-drive"></a>邏輯應用試駕的技術設定

任何自定義產品都應使用包含各種複雜解決方案體系結構的這種類型的測試驅動器部署範本。 有關設定邏輯應用測試驅動器的詳細資訊,請造訪 GitHub 上的[操作](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md)[和客戶參與度](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)。

- **區域**(必需,單選下拉清單):目前有 26 個 Azure 支援的區域,可以在其中提供您的試駕。 邏輯應用的資源將部署在您選擇的區域中。 如果邏輯應用在特定區域中存儲了任何自定義資源,請確保在此處選擇該區域。 最佳方法是在門戶中的 Azure 訂閱中本地完全部署邏輯應用,並在進行此選擇之前驗證其是否正常工作。

- **最大併發測試驅動器**(必需):設置一次可以使用試駕的最大客戶數。 這些測試驅動器已部署,使客戶能夠立即訪問它們,而無需等待部署。

- **試駕持續時間**(必需):輸入試駕將保持活動狀態的時間長度(以小時數計)。 此時間段結束后,試駕將自動終止。

- **Azure 資源組名稱**(必需):輸入邏輯應用測試驅動器保存的[Azure 資源組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)名稱。

- **Azure 邏輯應用名稱**(必需):輸入將測試驅動器分配給使用者的邏輯應用的名稱。 此邏輯應用必須保存在上面的 Azure 資源組中。

- **取消預配邏輯應用程式名稱**(必需):輸入邏輯應用的名稱,該應用在客戶完成後取消配置試駕。 此邏輯應用必須保存在上面的 Azure 資源組中。

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI 測試磁碟機不需要技術設定

想要示範互動式Power BI視覺物件的產品可以使用嵌入式連結共用自訂儀錶板作為試駕,無需進一步的技術配置。 詳細瞭解如何設置[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)範本應用。

### <a name="deployment-subscription-details"></a>部署訂閱詳細資訊

為了代表您部署測試驅動器,請創建並提供單獨且唯一的 Azure 訂閱。 (電源 BI 測試驅動器不需要)。

- **Azure 訂閱 ID(Azure**資源管理員和邏輯應用需要):輸入訂閱 ID 以授予對 Azure 帳戶服務的訪問許可權,以便進行資源使用方式報告和計費。 如果尚未創建,我們建議您考慮[創建單獨的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)以用於測試驅動器。 您可以通過登入[Azure 門戶](https://portal.azure.com/)並瀏覽到左側選單的 **「訂閱」** 選項卡來查找 Azure 訂閱 ID。 選擇該選項卡將顯示您的訂閱 ID(例如,"a83645ac-1234-5ab6-6789-1h234g764ghty")。

- **Azure AD 租戶代碼(** 必要):輸入 Azure 活動目錄 (AD)[租戶 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 要查找此 ID,請登錄到[Azure 門戶](https://portal.azure.com/),選擇左側功能表中的「活動目錄」選項卡,選擇 「屬性」,然後查找列出的**目錄 ID**號(例如,50c464d3-4930-494c-963c-1e951d15360e)。 您還可以使用網域名稱網址[https://www.whatismytenantid.com](https://www.whatismytenantid.com)在 : 尋找組織的租戶 ID。

- **Azure AD 租戶名稱**(動態 365 需要):輸入 Azure 活動目錄 (AD) 名稱。 要查找此名稱,請登錄到[Azure 門戶](https://portal.azure.com/),在右上角,租戶名稱將列在您的帳戶名稱下。

- **Azure AD 應用程式 ID(** 必要):輸入 Azure 的目錄 (AD)[應用程式代碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 要查找此 ID,請登錄到[Azure 門戶](https://portal.azure.com/),選擇左側功能表中的「活動目錄」選項卡,選擇**應用註冊**,然後查找列出的應用程式**ID**號(例如,50c464d3-4930-494c-963c-1e951d15360e)。

- **Azure AD 應用程式客戶端機密**(必要):輸入 Azure AD 應用程式[客戶端金鑰](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)。 要尋找此值,請登入 Azure[門戶](https://portal.azure.com/)。 選擇左側功能表中的**Azure 活動目錄**選項卡,選擇**應用註冊**,然後選擇試駕應用。 接下來,選擇**證書和機密**,選擇 **"新建用戶端機密**",輸入說明,選擇 **"從不在過期**"下,**Never**然後選擇"**添加**"。 請確保向下複製該值。 (執行此操作之前不要離開頁面,否則您將無法訪問該值。

在繼續下一節之前,請記住**儲存**!

### <a name="test-drive-marketplace-listings"></a>試駕市場清單

測試**驅動器**「選項卡下的 **」應用商店清單**」選項是定義試駕體驗詳細資訊的位置。

> [!NOTE]
> 您只能以一種語言提供試駕清單詳細資訊。 它不需要是英文的,只要報價說明以短語開頭,"此應用程式僅以[非英語]提供。 也可以提供*幫助連結 URL*以測試驅動器列表內容中使用的語言以外的語言提供內容。


- **說明**(必需):描述您的試駕、演示的內容、供用戶試驗的目標、要流覽的功能以及任何相關資訊,以説明使用者確定是否獲取您的產品/服務。 此欄位中最多可輸入 3,000 個字元的文本。 

- **訪問資訊**(Azure 資源管理員和邏輯測試驅動器所需的資訊):解釋客戶需要瞭解哪些內容才能存取和使用此測試驅動器。 演練使用產品/服務的方案,以及客戶在整個試駕中訪問功能的準確情況。 此欄位中最多可輸入 10,000 個字元的文本。

- **用戶手冊**(必需):對試駕體驗的深入演練。 《用戶手冊》應準確介紹您希望客戶從體驗試駕中獲得什麼,並作為他們可能存在的任何問題的參考。 檔案必須採用 PDF 格式,並在上載後命名(最多 255 個字元)。

- **視頻:添加視頻**(可選):視頻可以上傳到 YouTube 或 Vimeo,並在此處引用連結和縮略圖圖像 (533 x 324 圖元),以便客戶可以查看資訊的演練,以幫助他們更好地瞭解試駕,包括如何成功使用您的產品/服務的功能,並瞭解突出其優勢的方案。
  - **名稱**(必要)
  - **網址(僅限 YouTube 或 Vimeo)(** 必填)
  - **縮圖 (533 x 324 px)**: 影像檔案必須採用 PNG 格式。

## <a name="supplemental-content"></a>補集內容

此頁面允許您提供有關您的優惠的其他資訊,以幫助我們驗證您的產品/服務。 此資訊不會向客戶顯示或發佈到市場。

### <a name="target-release"></a>目標版本

指示解決方案目標為「**當前」****、下一個主要**版本或**下一個次要**版本的 Microsoft 動態業務中心版本。 此資訊使我們能夠適當地測試您的解決方案。

### <a name="supported-editions"></a>支援的版本

如果您的產品/服務需要 Microsoft 動態 365 商業中心的高級版,請選擇「**進階」。** 否則,選擇 **「基本」** 和「**高級**」。

### <a name="key-usage-scenario"></a>關鍵使用機制

您必須上傳一個`.pdf`檔案,其中列出文檔(.pdf 格式)中列出的產品/服務的關鍵使用方案。 在我們批准您的市場報價之前,我們的驗證團隊可能會驗證此處列出的所有方案。

### <a name="app-tests-automation"></a>應用測試自動化

您可以選擇在此處上載**應用測試自動化**檔 (.app)。

### <a name="test-accounts"></a>測試帳戶

如果需要測試帳戶,我們的認證團隊才能正確審核您的產品/服務,請上傳包含**測試帳戶**資訊的 .pdf、.doc 或 .docx 檔。

## <a name="publish"></a>發佈

### <a name="submit-offer-to-preview"></a>提交優惠以預覽

完成產品/服務的所有必需部分後,選擇門戶右上角的**發佈**。 您將被重定向到 **「審閱」和「發布」** 頁。 

如果您是首次發佈此產品/服務,您可以:

- 請參閱產品/服務的每個部分的完成狀態。
    - *未啟動*- 表示該部分尚未觸摸且需要完成。
    - *不完整*- 表示該節有需要修復的錯誤或需要提供更多資訊。 返回部分並更新它。
    - *完成*- 表示部分已完成,提供了所有必需的數據,並且沒有錯誤。 報價的所有部分都必須處於完全狀態,然後才能提交報價。
- 在 **「認證備註」** 部分中,除了任何有助於瞭解應用的補充說明外,請向認證團隊提供測試說明,以確保正確測試應用。
- 通過選擇 **「提交**」提交要發布的產品/ 當產品/服務預覽版本可供您審核和批准時,我們將向您發送一封電子郵件。 返回合作夥伴中心,選擇 **「Go-live」** 以優惠形式向公眾發佈您的優惠(如果是私人優惠,則向私人受眾發佈)。

## <a name="next-steps"></a>後續步驟

- [更新商業市集中的現有供應項目](./update-existing-offer.md)
