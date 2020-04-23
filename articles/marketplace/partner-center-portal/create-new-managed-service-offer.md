---
title: 在商業市場中創建新的託管服務產品/服務
description: 如何使用合作夥伴中心中的商業應用商店門戶創建新的託管服務產品/服務,以便在 Azure 應用商店中列出。
author: JnHs
ms.author: jenhayes
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: c040907c6101d11ee645f9967278d3cf297bd74a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869806"
---
# <a name="create-a-new-managed-service-offer"></a>建立新的託管服務產品/服務

> [!IMPORTANT]
> 我們將託管服務產品的管理從雲合作夥伴門戶遷移到合作夥伴中心。 在遷移產品/服務之前,請按照[向 Azure 應用商店發佈託管服務產品/服務以](../../lighthouse/how-to/publish-managed-services-offers.md)在雲合作夥伴門戶中管理產品/服務的說明進行操作。

託管服務提供了啟用[Azure 燈塔](../../lighthouse/overview.md)方案的説明。 當客戶接受託管服務產品/服務時,他們將能夠將資源用於 Azure[委派的資源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)。

要開始創建託管服務產品/服務,請確保首先[創建合作夥伴中心帳戶](./create-account.md)並打開[「商業市場」儀表板](https://partner.microsoft.com/dashboard/commercial-marketplace/offers),並選擇 **「概述」** 頁。 您必須具有[銀牌或金雲平臺能力級別](https://partner.microsoft.com/membership/cloud-platform-competency),或必須是 Azure[專家 MSP](https://partner.microsoft.com/membership/azure-expert-msp)才能發佈託管服務產品/服務。

![合作夥伴中心上的商業市場儀錶板](./media/new-offer-overview.png)

>[!Note]
> 產品/服務發佈後,只有在重新發佈後,才會在系統和商店前面更新對合作夥伴中心中提供的優惠進行編輯。 請確保在進行更改後提交要發佈的要約。

## <a name="create-a-new-offer"></a>建立新的供應項目

選擇 **"+ 新產品/服務**"按鈕,然後選擇 **"託管服務**"功能表項。 將顯示「**新建產品/服務**」對話框。

### <a name="offer-id-and-alias"></a>優惠識別碼與別名

- **優惠 ID**:帳戶中每個產品/服務的唯一標識符。 此 ID 將在市場產品/服務 URL 位址中對客戶可見。 此 ID 只能包含小寫字母數位字元(包括連字元和下劃線,但沒有空格),限制為 50 個字元,在選擇 「**創建**」後無法更改。  例如,如果您在此處輸入*測試報價 1,* 則產品/服務`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`網址將為 。

- **優惠別名**:用於指合作夥伴中心內產品/服務的名稱。 此名稱不會在市場中使用,並且與向客戶顯示的要約名稱和其他值不同。 選擇 「**建立**」 後,無法更改此值。

輸入**優惠 ID**和**優惠別名**後,選擇 **"創建**"。 然後,您將能夠處理產品/服務的所有不同部分。

## <a name="offer-setup"></a>優惠設定

**"產品/服務設置"** 頁要求提供以下資訊。 請務必在完成這些欄位後選擇 **「保存**」。

## <a name="connect-lead-management"></a>連線潛在客戶管理

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

請注意，根據[受控服務認證原則](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)，必須要有**潛在客戶目的地**。 這將在每次客戶部署您的產品/服務時在 CRM 系統中創建記錄。

有關詳細資訊,請參閱[潛在顧客管理概述](./commercial-marketplace-get-customer-leads.md)。

在繼續下一節之前,請記住**保存**頁面。

## <a name="properties"></a>屬性

屬性**頁面**允許您定義用於在市場上對產品/服務進行分組的類別以及支援您的優惠的法律合同。 完成此頁面後選擇 **「儲存**」。

### <a name="category"></a>類別

選擇至少一個且最多五個類別,這些類別將用於將您的產品/服務放入相應的市場搜索區域。 請務必在產品/服務說明中標註您的產品/服務如何支援這些類別。

### <a name="terms-and-conditions"></a>條款及條件

在條款與條件欄位中提供您自己的法律**條款和條件**。 您還可以提供可在其中找到您的條款和條件的 URL。 客戶在試用您的優惠之前,必須接受這些條款。

## <a name="offer-listing"></a>優惠清單

產品 **/服務"列表**頁允許您為您的產品/服務定義市場詳細資訊(產品/服務名稱、說明、圖像等)。

> [!NOTE]
> 報價清單內容(如說明、文檔、螢幕截圖、使用條款等)不需要以英文表示,只要要約說明以短語開頭,"此應用程式僅以 [非英語語言]提供。 提供*有用的連結 URL*以提供產品/服務列表內容中所用語言以外的語言提供內容也是可接受的。

### <a name="name"></a>名稱

您在此處輸入的名稱將作為產品/服務列表的標題顯示給客戶。 此欄位預填充了創建產品/服務時為 **「產品/服務」別名**輸入的文本,但您可以更改此值。 此名稱可以註冊商標(您可以包括商標或版權符號)。 名稱不能超過 50 個字元,不能包含任何表情符號。

### <a name="search-results-summary"></a>搜尋結果摘要

提供產品/服務的簡短說明(最多 100 個字元),可在市場搜尋結果中使用。

### <a name="long-summary"></a>長摘要

提供更久的報價說明(最多 256 個字元)。 此長摘要也可用於市場搜尋結果。

### <a name="description"></a>描述

提供更久的報價說明(最多 3,000 個字元)。 此描述將顯示在市場清單概述中的客戶。 包括您的產品/服務的價值主張、主要優勢、類別和/或行業協會、應用內購買機會以及任何必需的披露。

編寫說明的一些提示:  

- 在您的描述中的前幾個句子清楚描述您供應項目的價值主張。 在價值主張中包括以下專案:
  - 優惠說明
  - 從產品/服務中受益的使用者類型
  - 產品/服務解決的客戶需求或痛苦
- 請記住，前幾個句子可能會顯示在搜尋引擎結果中。  
- 請勿依賴特色與功能來銷售您的產品。 反之，要鎖定在您所提供的價值。  
- 請盡可能使用業界特定詞彙或凸顯優點的字眼。

為了使您的報價描述更具吸引力,請使用豐富的文本編輯器來設置您的說明格式。

![使用富文字編輯器](./media/text-editor2.png)

使用以下說明使用富文字編輯器:

- 要更改內容的格式,請突出顯示要設定格式的文字並選擇文字樣式,如下所示:

     ![使用富文字編輯器改變文字格式](./media/text-editor3.png)

- 要加入文字加入項目符號或編號清單,請使用以下選項:

     ![使用富文字編輯器加入清單](./media/text-editor4.png)

- 要向文字加入或刪除縮排,請使用以下選項:

     ![使用富文字編輯器縮排](./media/text-editor5.png)

### <a name="privacy-policy-link"></a>隱私政策連結

輸入組織隱私政策的 URL(託管在您的網站上)。 您有責任確保您的應用符合隱私法律和法規,並提供有效的隱私政策。

### <a name="useful-links"></a>實用的連結

提供有關解決方案的可選補充連線文件。 通過按下 **「添加連結**」添加其他有用的連結。

### <a name="contact-information"></a>連絡資訊

在本節中,您必須提供**支援聯繫人**和**工程聯繫人**的姓名、電子郵件和電話號碼。 此資訊不會向客戶顯示,但可供 Microsoft 使用,並且可能會提供給 CSP 合作夥伴。

### <a name="support-urls"></a>支援網址

如果有 Azure**全域客戶**和/或 Azure**政府客戶**的支援網站,請在此處提供這些 URL。

### <a name="marketplace-images"></a>市場映射

在本節中,您可以提供向客戶展示產品/服務時將使用的徽標和圖像。 所有圖像必須採用 .png 格式。

>[!Note]
>如果上傳文件出現問題,請確保本地網路不會阻止合作夥伴中心使用https://upload.xboxlive.com的服務。

#### <a name="marketplace-logos"></a>市場徽標

需要四個徽標大小:**小 (40x40)**、**中等 (90x90)**、**大 (115x115)** 和 寬 **(255x115)**。 您的標誌應遵循這些指導方針：

- Azure 設計具有簡單的調色盤。 限制標誌上的主要和次要色彩數目。
- 入口網站的佈景主題色彩是白色與黑色。 請勿使用這些色彩作為標誌的背景色彩。 請使用可讓標誌在入口網站突顯出來的色彩。 建議您使用簡單的主要色彩。
- 如果您使用透明背景，請確定標誌與文字不是白色、黑色或藍色。
- 標誌的外觀與風格應該是「一般」，且避免使用漸層。 請不要在標誌上使用漸層背景。
- 請勿在標誌上放置文字 (甚至是公司或品牌名稱)。
- 確定標誌不會自動縮放。

#### <a name="screenshots"></a>螢幕擷取畫面

最多添加五個屏幕截圖,顯示您的優惠如何工作。 所有螢幕截圖必須為 1280 x 720 圖元。

#### <a name="videos"></a>影片

您可以選擇添加最多五個演示您的優惠的視頻。 這些影片應託管在 YouTube 和/或 Vimeo 上。 對於每個視訊,輸入影片的名稱、URL和視訊的縮圖影像(1280 x 720 圖元)。

#### <a name="additional-marketplace-listing-resources"></a>其他市場清單資源

- [市場產品/服務清單的最佳實務](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>預覽

在將優惠即時發佈到更廣泛的市場產品/服務之前,首先需要將其提供給有限的預覽受眾。 這樣,您就可以在向客戶提供產品之前確認所提供的服務在 Azure 應用商店中顯示的方式。 Microsoft 支援和工程團隊還可以在此預覽期內查看您的產品/服務。

您可以通過在 **「預覽受眾」** 部分中輸入 Azure 訂閱標識來定義預覽受眾。 您可以手動輸入多達 10 個訂閱 I,或上傳最多包含 100 個訂閱的 ID 的 .csv 檔。

與這些訂閱關聯的任何客戶都可以在 Azure 應用商店中查看產品/服務。 請務必在此處包含您自己的訂閱,以便預覽產品/服務。

## <a name="plan-overview"></a>排程概述

每個供應項目都必須有一或多個方案 (有時稱為 SKU)。 您可以新增多個方案來支援設定為不同價格的不同功能，或為特定客戶的限定對象自訂特定方案。 客戶可以檢視父代供應項目下可供他們使用的方案。

在**計畫概述「** 頁上,選擇 **」創建新計畫**」。 然後輸入**計畫 ID**與**排程名稱**。 這兩個值只能包含小寫字母數位字元、破折號和下劃線,最多 50 個字元。 這些值可能向客戶可見,並且在發佈產品/服務后無法更改這些值。

選擇 **「在**輸入這些值後創建」以繼續執行計畫。 有三個部份要完成:**計劃清單**、**定價與可用性**,**以及技術設定**。

### <a name="plan-listing"></a>排程清單

首先,提供計畫的**搜尋結果摘要**。 這是您的計劃(最多 100 個字元)的簡短說明,可用於市場搜尋結果。

接下來,輸入一個描述,該**說明**提供計劃的更詳細說明。

### <a name="pricing-and-availability"></a>價格與可用性

目前,只有一種定價模型可用於託管服務產品/服務:**自帶許可證 (BYOL)。** 這表示您會直接向您的客戶收取此供應項目的相關費用，而且 Microsoft 不會向您收取任何費用。

**「 計畫可見性**」 部份允許您指示此計畫是否應為[私有](../../marketplace/private-offers.md)。 如果取消選中「**這是私有計畫**」框,則您的計畫將不限於特定客戶(或特定數量的客戶)。

若要使此方案只有特定客戶才能取得，請選取 [是]****。 當您這麼做時，必須提供訂用帳戶識別碼來識別客戶。 可以逐個輸入(最多 10 個訂閱)或通過上載 .csv 檔(在所有計劃中最多 10,000 個訂閱)來輸入這些檔。 請務必在此包含您自己的訂用帳戶，以便您可以測試及驗證供應項目。

> [!IMPORTANT]
> 計劃一旦公開發佈,您就無法將其更改為私有。 要控制哪些客戶可以接受您的報價並委派資源,請使用私人計劃。 使用公共計畫時,您無法將可用性限制為特定客戶,甚至限制特定數量的客戶(儘管如果您選擇這樣做,您可以完全停止銷售計劃)。 只有在發佈產品/服務時,在**角色定義**中包含「**授權」** 設定為[「託管服務註冊分配刪除角色」時](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role),才能在客戶接受產品/服務後取消對[委派的存取權限](../../lighthouse/how-to/onboard-customer.md#remove-access-to-a-delegation)。 您也可以聯絡客戶,要求他們[移除您的存取權限](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)。

### <a name="technical-configuration"></a>技術設定

計劃的此部分創建一個清單,其中提供了用於管理客戶資源的授權資訊。 為了啟用[Azure 委派的資源管理](../../lighthouse/concepts/azure-delegated-resource-management.md),需要此資訊。

請務必查看[Azure 燈塔方案中的租戶、角色和使用者](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles),以瞭解支援哪些角色以及定義授權的最佳做法。

> [!NOTE]
> 如先前所述，您**授權**項目中的使用者和角色將適用於購買方案的每位客戶。 如果您想要限制對特定客戶的存取，則必須發佈私人方案以供其專屬使用。

#### <a name="manifest"></a>file:///

首先，提供資訊清單的 [版本]****。 使用的格式為 *n.n.n* (例如，1.2.5)。

接下來，輸入您的 [租用戶識別碼]****。 這是與組織的 Azure 活動目錄 (Azure AD) 租戶 ID 關聯的 GUID;也就是說,您將從中訪問客戶的資源的管理租戶。 如果您目前沒有此資訊，您可將滑鼠指標暫留在 Azure 入口網站右上角的帳戶名稱上，或選取 [切換目錄]**** 就能看到它。

如果您發佈新版本的報價,並且需要創建更新的清單,請選擇 **「新清單**」。 請確保從以前的清單版本增加版本號。

#### <a name="authorization"></a>授權

授權定義管理租戶中的實體,這些實體可以訪問購買計劃的客戶的資源和訂閱。 每個實體都分配了一個內置角色,該角色授予特定級別的訪問。

您可以為每個計畫創建多達 20 個授權。

> [!TIP]
> 在大多數情況下,您需要將角色分配給 Azure AD 使用者組或服務主體,而不是一系列單獨的使用者帳戶。 如此一來，當您的存取需求變更時，就可以新增或移除個別使用者的存取權，而不需要更新並重新發佈方案。 將角色分配給 Azure AD 群組時[,請確保**組類型**為 **「安全」,** 而不是**Office 365**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。 如需其他建議，請參閱 [Azure Lighthouse 案例中的租用戶、角色和使用者](../../lighthouse/concepts/tenants-users-roles.md)。

您必須為每個**授權**提供下列項目。 然後,您可以根據需要多次選擇 **+ 添加授權**,以添加更多使用者和角色定義。

- **Azure AD 物件 ID**:使用者、使用者組或應用程式的 Azure AD 識別符,這些許可權將被授予客戶資源的特定許可權(由角色定義定義)。
- **Azure AD 物件顯示名稱**:一個友好名稱,用於幫助客戶瞭解此授權的目的。 客戶會在委派資源時看到此名稱。
- **角色定義**:從清單中選擇一個可用的 Azure AD 內建角色。 此角色將會決定 [Azure AD 物件識別碼]**** 欄位中的使用者，對於您的客戶資源會有那些權限。 有關這些角色的說明,請參閱[Azure 委派資源管理的](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management)[內建角色](../../role-based-access-control/built-in-roles.md)和角色支援。
  > [!NOTE]
  > 由於適用的新內置角色已添加到 Azure 中,它們將在此處可用,儘管在出現之前可能會有一些延遲。
- **可分配角色**:僅當已在此授權**的角色定義**中選擇「使用者存取管理員」時,才會顯示此選項。 若是如此，您必須在此新增一或多個可指派的角色。 Azure AD**物件 ID**欄位中的使用者將能夠將這些角色分配給[託管標識](../../active-directory/managed-identities-azure-resources/overview.md),這是[部署可以修復的策略](../../lighthouse/how-to/deploy-policy-remediation.md)所必需的。 請注意，不會有其他一般與「使用者存取系統管理員」角色相關聯的權限套用至此使用者。

> [!TIP]
> 為確保在需要時可以[刪除對委派的存取](../../lighthouse/how-to/onboard-customer.md#remove-access-to-a-delegation),請包括授權 **,** 其中**的角色定義**設定為[託管服務註冊分配刪除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)。 如果未指派此角色，則只有客戶租用戶中的使用者可以移除委派的資源。

完成計劃的所有部分後,可以選擇 **「創建新計畫**」的次數,以創建其他計畫。 完成時，選取 [儲存]****。

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

### <a name="customer-experience-and-offer-management"></a>客戶體驗和產品管理

當客戶部署產品/服務時,他們將能夠委派訂閱或資源組以進行[Azure 委派的資源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)。 有關此過程的更多,請參閱[客戶入職流程](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process)。

您可以隨時[發佈供應項目的更新版本](update-existing-offer.md)。 例如，您可能會想要將新的角色定義新增至先前發佈的供應項目。 當您這麼做時，已新增供應項目的客戶會在 Azure 入口網站的 [[服務提供者****](../../lighthouse/how-to/view-manage-service-providers.md)] 頁面中看到一個圖示，讓他們知道有可用的更新。 每個客戶都可以檢閱變更，並決定是否要更新為新的版本。

## <a name="next-steps"></a>後續步驟

- [更新商業市集中的現有供應項目](./update-existing-offer.md)
- [瞭解 Azure 燈塔](../../lighthouse/overview.md)