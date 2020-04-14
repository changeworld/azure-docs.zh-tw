---
title: 在合作夥伴中心 - Azure 應用商店中創建 Azure 容器產品/服務
description: 本文介紹如何為 Azure 應用商店創建和發佈容器產品/服務。
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b96fe12ccc0292bb5f689fbecabd53d2af54846e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266314"
---
# <a name="create-an-azure-container-offer"></a>建立 Azure 容器產品/服務

> [!IMPORTANT]
> 我們將 Azure 容器產品的管理從雲端合作夥伴門戶遷移到合作夥伴中心。 在遷移產品/服務之前,請按照雲合作夥伴門戶[容器](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer)中的說明來管理您的優惠。

本文介紹如何為 Azure 應用商店創建和發佈容器產品/服務。 在[合作夥伴中心建立商業市場帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)。

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home),然後從頂端選單中選擇**儀表板**。
2. 在左側功能表中,選擇**商業市場**,然後**選擇「概述**」。
3. 在 **「概述」** 頁上,選擇 **+新產品/服務**,然後選擇**Azure 容器**。 將顯示 **「新建產品/服務**」對話框。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-new-container.png" alt-text="在合作夥伴中心中說明「概述」頁。突出顯示"新優惠"按鈕和諮詢服務優惠。":::

> [!TIP]
> 發佈產品/服務后,在合作夥伴中心中對其所做的編輯僅在重新發佈產品/服務后顯示在網店中。 請確保在進行更改后始終重新發佈。

### <a name="offer-id-and-alias"></a>優惠識別碼與別名

輸入**優惠代碼**。 這是您帳戶中每個產品/服務的唯一標識碼。

- 客戶可以在市場產品/服務的網站位址和 Azure 資源管理器範本(如果適用)中看到此 ID。
- 請一律使用小寫字母和數字。 它可以包括連字元和下劃線,但沒有空格,並且限制為 50 個字元。 例如,如果輸入**測試-產品/服務 -1,** 則產品`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`/服務 Web 位址將為 。
- 選擇 「**建立**」 後,無法更改優惠識別碼。

**輸入****產品/服務別名**。 這是用於指合作夥伴中心產品/服務的名稱。

- 此名稱不在市場上使用,並且不同於向客戶顯示的要約名稱和其他值。
- 選擇 後無法變更 **。**

選擇 **「在**繼續之前創建」。

## <a name="offer-overview"></a>供應項目概觀

**"產品/服務概述"** 頁顯示發佈此產品/服務所需的步驟(已完成和即將推出)以及每個步驟應完成多長時間的可視化表示形式。

此頁面根據產品/服務的狀態顯示不同的連結。 例如：

- 如果要約是草稿 -[刪除草稿要約](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- 如果優惠是即時的 -[停止銷售優惠](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- 如果優惠處於預覽版 -[上線](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 如果您尚未完成發行者登出 ─[取消發佈](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>優惠設定

按照以下步驟設置您的產品/服務。

### <a name="connect-lead-management--optional"></a>連線潛在客戶管理 = 選擇

使用合作夥伴中心將產品/服務發佈到市場時,您可以將其連接到客戶關係管理 (CRM) 系統。 這樣,一旦有人對您的產品表示興趣或使用,您就可以立即接收客戶聯繫資訊。

1. **選擇您希望我們傳送客戶串列的潛在客戶目的地**。 合作夥伴中心支援以下 CRM 系統:

- [客戶互動的動態 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics)
- [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
- [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

> [!NOTE]
> 如果上面未列出 CRM 系統,請使用[Azure 表](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)或[Hs 終結點](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)來儲存客戶潛在顧客資料,然後將資料匯出到 CRM 系統。

2. 在合作夥伴中心發佈時,請將您的產品/服務連接到潛在顧客目標。
3. 確認與潛在顧客目標的連接配置正確。 在合作夥伴中心發佈後,我們將驗證連接並向您發送測試線索。 在產品/服務上線之前預覽產品/服務時,還可以嘗試在預覽環境中自行購買產品/服務,以測試潛在顧客連接。
4. 確保與潛在顧客目標的連接保持更新狀態,這樣您就不會丟失任何潛在顧客。

下面是一些額外的潛在客戶管理資源:

- [潛在客戶管理概述](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [潛在客戶管理常見問題集](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [常見潛在客戶設定錯誤](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [潛在客戶管理概述](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)PDF (確保彈出視窗封鎖程式關閉)

選擇 **"保存"草稿**,然後再繼續下一節"屬性"。

### <a name="properties"></a>屬性

此頁面允許您定義用於在市場上對產品/服務進行分組的類別以及支援您的優惠的法律合同。

#### <a name="category"></a>類別

選擇至少 1 個類別,最多選擇五個類別。 這些類別用於將產品/服務放置在相應的市場搜索區域,並顯示在您的優惠詳細信息頁面上。 在產品/服務說明中,說明您的優惠如何支援這些類別。 容器顯示在 **「容器**」下,然後是 **「容器圖像**」類別。

#### <a name="legal"></a>法律

您必須為優惠提供條款和條件。 有兩個選項：

- 使用Microsoft商業市場的標準合約。
- 提供您自己的條款和條件。

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>微軟商業市場的標準合同

我們提供標準合同範本,以幫助促進商業市場的交易。 您可以選擇根據標準合同提供解決方案,客戶只需檢查和接受一次。 如果您不想創建自定義條款和條件,這是一個不錯的選擇。

要瞭解有關標準合同的更多資訊,請參閱[Microsoft 商業市場的標準合約](https://docs.microsoft.com/azure/marketplace/standard-contract)。 您還可以下載[標準合約](https://go.microsoft.com/fwlink/?linkid=2041178)PDF(確保彈出視窗阻止程式已關閉)。

要使用標準合約,請選擇「**使用 Microsoft 商業市場的標準合約**」複選框,然後按一下 **「 接受**」。

> [!NOTE]
> 使用 Microsoft 商業市場的標準合同發佈產品/服務後,不能使用自己的自定義條款和條件。 在標準合同下或根據您自己的條款和條件提供您的解決方案。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="演示了"使用標準合同進行微軟商業市場"複選框。":::

##### <a name="your-own-terms-and-conditions"></a>您自己的條款和條件

要提供您自己的自定義條款和條件,請在 **「條款和條件」** 框中輸入這些條款和條件。 您可以在此框中輸入無限量的文字字元。 客戶必須先接受這些條款,然後才能試用您的優惠。

選擇 **"保存"草稿**,然後再繼續下一節"優惠清單"。

## <a name="offer-listing"></a>優惠清單

此頁面允許您定義在商業市場中顯示的產品/服務詳細資訊。 這包括產品/服務名稱、說明和圖像。

> [!NOTE]
> 如果優惠說明以短語開頭,則優惠詳細資訊不需要以英文表示:「此應用程式僅提供 [非英語語言]。 提供有用連結以不同於產品/服務清單詳細資訊中使用的語言提供內容也沒關係。

### <a name="name"></a>名稱

您在此處輸入的名稱顯示為產品/服務的標題。 此欄位預先填充您在建立產品/服務時在 **「產品/服務」別名**框中輸入的文字。 您可以稍後變更此名稱。

名稱:

- 可以註冊商標(可能包括商標和版權符號)。
- 不能超過50個字元長。
- 不能包括表情符號。

### <a name="search-results-summary"></a>搜尋結果摘要

您的報價的簡短說明。 這最多可達 100 個字元,並且用於市場搜尋結果。

### <a name="long-summary"></a>長摘要

更詳細說明您的優惠。 這最多可達 256 個字元,並且用於市場搜尋結果。

### <a name="description"></a>描述

提供較長範圍的報價說明,最多 3,000 個字元。 這在市場清單概述中向客戶顯示。

在描述中包括以下一項或多項:

- 您的產品提供的價值和關鍵優勢
- 類別或行業協會,或兩者
- 應用內購買機會
- 任何必需的披露

以下是編寫說明的一些提示:

- 清楚地描述你描述前幾句的報價的價值。 包括以下專案:
  - 供應項目的說明。
  - 從產品/服務中受益的使用者類型
  - 客戶需求或發出產品/服務位址。
- 請記住,搜尋結果中可能會顯示前幾個句子。
- 不要依賴特性和功能來銷售您的產品。 相反,關注您的產品/服務提供的價值。
- 嘗試使用行業特定的詞彙或基於利益的措辭。

為了使您的報價**描述**更具吸引力,請使用豐富的文本編輯器來設置說明的格式。 具有編號、專案符號、粗體、斜體和縮進,使描述更具可讀性。

:::image type="content" source="media/text-editor2.png" alt-text="說明富文本編輯器。" border="false" :::

- 使用此下拉清單將段落樣式應用於文本。

    :::image type="content" source="media/text-editor3.png" alt-text="說明富文字編輯器中的文本樣式控制項。" border="false":::

- 使用這些圖示將編號或項目符號應用於文本。

     :::image type="content" source="media/text-editor4.png" alt-text="在富文本編輯器中說明項目符號和數位清單控制項。" border="false":::

- 使用這些圖示向文本添加或刪除縮進或從文本中刪除縮進。

    :::image type="content" source="media/text-editor5.png" alt-text="說明富文本編輯器中的縮進控制項。" border="false":::

#### <a name="privacy-policy-link"></a>隱私政策連結

輸入組織的隱私政策的網址。 您有責任確保您的報價符合隱私法律和法規。 您還負責在您的網站上發佈有效的隱私政策。

#### <a name="useful-links"></a>實用的連結

提供有關您的優惠的補充在線文檔。 您最多可以添加 25 個連結。 要新增連結,請選擇 **「 新增連結」,** 然後完成以下欄位:

- **標題**– 客戶將在您產品/服務的詳細資訊頁面上看到此內容。
- **連結 (URL)** – 輸入客戶查看連線文件的連結。 鏈接必須以HTTP://或HTTPs://開頭。

### <a name="contact-information"></a>連絡資訊

您必須提供**支援聯絡人**和**工程連絡人**的姓名、電子郵件和電話號碼。 此資訊不會向客戶顯示,但 Microsoft 可以獲取此資訊。 它還可能提供給雲端解決方案供應商 (CSP) 合作夥伴。

- 支援連絡人(必需):有關一般支持問題。
- 工程聯繫人(必需):有關技術問題和認證問題。
- CSP 計劃聯絡人(可選):對於與 CSP 計劃相關的轉銷商問題。

在 **"支援"連絡人**部分中,提供**支援網站**,合作夥伴可以根據產品/服務是全域 Azure、Azure 政府版還是兩者提供,查找對產品/服務的支援。

在**CSP 計畫連絡**部分,提供連結 ( **CSP 計劃行銷材料**), 其中 CSP 合作夥伴可以找到您的報價的行銷材料.

#### <a name="additional-marketplace-listing-resources"></a>其他市場清單資源

要瞭解有關創建產品/服務清單詳細資訊,請參閱[產品/服務列表最佳實踐](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

### <a name="marketplace-images"></a>市場映射

提供徽標和圖像,以便與您的產品配合使用。 所有圖像必須採用 PNG 格式。 模糊圖像將被拒絕。

#### <a name="store-logos"></a>Microsoft Store標誌。

 在以下四個像素大小中提供產品標誌的 PNG 檔:

- **小**(48 x 48)
- **中等**(90 X 90)
- **大**(216 x 216)
- **寬**(255 X 115)

所有四個徽標都是必需的,並且在市場清單中的不同位置使用。

#### <a name="screenshots-optional"></a>螢幕擷取(選擇性的)

最多添加五個屏幕截圖,顯示您的優惠如何工作。 每個必須為 1280 x 720 像素的大小和 PNG 格式。

#### <a name="videos-optional"></a>視訊(選擇性的)

最多添加五個視頻,演示您的優惠。 輸入影片的名稱、Web 位址以及視訊的縮圖 PNG 圖像,大小為 1280 x 720 圖元。

#### <a name="offer-examples"></a>供應項目範例

以下範例顯示了要約清單欄位如何顯示在要約的不同位置。

這顯示了 Azure 應用商店中的 **「產品/服務」列表**頁:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="在 Azure 應用商店中說明產品/服務列表頁。" :::

這顯示了 Azure 應用商店中的搜尋結果:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="在 Azure 應用商店中說明搜尋結果。":::

這顯示了 Azure 門戶中的 **「產品/服務」列表**頁:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="在 Azure 門戶中說明產品/服務列表頁。":::

這顯示了 Azure 門戶中的搜尋結果:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="在 Azure 門戶中說明搜尋結果。":::

## <a name="preview"></a>預覽

在「預覽」選項卡上,您可以選擇有限的**預覽受眾**,以在即時發佈產品/服務之前驗證產品/服務。

> [!IMPORTANT]
> 在**預覽**版中查看優惠后,必須選擇 **「上線」** 以向公眾發佈您的優惠。

使用 Azure 訂閱 ID GUID 指定預覽受眾,以及每個受眾的可選說明。 客戶看不到這兩個字段。

> [!NOTE]
> 您可以在 Azure 門戶中的「訂閱」 頁上找到 Azure 訂閱 ID。

添加至少一個 Azure 訂閱 ID,分別(最多 10 個)或通過上傳 CSV 檔(最多 100 個)。 通過添加這些訂閱 IT,您可以確定誰可以在產品/服務即時發佈之前預覽產品/服務。 如果您的優惠已即時,您可以選擇預覽受眾以測試產品/服務的變化或更新。

> [!NOTE]
> 預覽觀眾與私人觀眾不同。 **預覽**受眾可以在所有優惠計劃在市場上直播之前查看和確認這些計劃,包括僅向**私人**受眾發佈的計劃(在"可用性"選項卡上設置)。

選擇 **"保存"草稿**,然後再繼續。

### <a name="plan-overview"></a>排程概述

這個選項卡允許您在同一產品/服務中提供不同的計劃選項。 這些計劃以前稱為 SKU 或庫存單位。 計劃在可用雲端方面可能有所不同,例如全域雲、政府雲和計畫引用的圖像。 要在商業市場列出您的優惠,您必須至少設置一個計劃。

建立計劃後,「**計畫概觀**」選項卡顯示:

- 排程名稱
- 定價模式
- 雲可用性(全球或政府)
- 目前發佈狀態
- 任何可用動作

計劃概述中可用的操作因計劃的當前狀態而異。 其中包括：

- **刪除草稿**= 如果計劃狀態為草稿。
- **停止銷售計劃**– 如果計劃狀態即時發佈。

#### <a name="create-new-plan"></a>建立新的方案

選擇 **「創建新計畫**」。 將顯示「**新建計畫**」對話框。

在 **「計劃 ID」** 框中,為此產品/服務中的每個計畫創建唯一的計畫識別符。 此 ID 將在產品的 Web 位址中對客戶可見。 僅使用小寫字母和數位、破折號或下劃線,最多 50 個字元。

> [!NOTE]
> 選擇 「**建立**」 後,無法更改計畫 ID。

在 **「計畫名稱**」框中,輸入此計畫的名稱。 客戶在決定在您的產品/服務中選擇哪個計劃時,會看到此名稱。 為此產品/服務中的每個計劃創建唯一名稱。 例如,您可以使用**Windows 伺服器**的報價名稱,並計劃 Windows**伺服器 2016**和**Windows 伺服器 2019**。

### <a name="plan-setup"></a>計畫設定

這個選項卡允許您選擇計劃可用的雲端。 此選項卡上的答案會影響其他選項卡上顯示的欄位。

#### <a name="cloud-availability"></a>雲可用性

您的計劃必須在至少一個雲中可用。

選擇**Azure 全域**選項,以便使用商業市場的所有全域 Azure 區域的客戶可以使用您的計畫。 有關詳細資訊,請參閱[地理可用性和貨幣支援](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)。

選擇[**Azure 政府雲**](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)選項以使解決方案在此處顯示。 這是一個政府社區雲,由來自美國聯邦、州、地方或部落政府機構的客戶以及有資格為其提供服務的合作夥伴進行受控訪問。 作為發佈者,您負責此雲社區的任何合規性控制、安全措施和最佳實踐。 Azure 政府使用物理隔離的數據中心和網路(僅適用於美國)。

在[發佈到](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)Azure 政府之前,請測試並確認解決方案,因為結果可能會有所不同。 要創建和測試解決方案,請從[Microsoft Azure 政府試用版](https://azure.microsoft.com/global-infrastructure/government/request/)請求試用帳戶。

> [!NOTE]
> 計劃發佈並在特定雲中可用后,無法刪除該雲。

#### <a name="azure-government-cloud-certifications"></a>Azure 政府雲認證

僅當 Azure**政府雲**在**雲可用性**下被選中時,才能看到此選項。

Azure 政府服務處理受某些政府法規和要求約束的數據。 例如,FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS。

要顯示這些計畫的認證,您最多可以提供 100 個描述這些程式的連結。 這些可以是直接連結到您的程式清單或您自己的網站。 這些連結僅對 Azure 政府客戶可見。

## <a name="plan-listing"></a>排程清單

此選項卡顯示當前產品/服務中每個不同計劃的特定資訊。

### <a name="plan-name"></a>計畫名稱

這預先填充了您在創建計畫時給出的名稱。 您可以根據需要更改此名稱。 它可以長達 50 個字元。 此名稱在 Azure 應用商店和 Azure 門戶中顯示為此計劃的標題。 在計劃準備好使用后,它用作預設模組名稱。

### <a name="plan-summary"></a>計劃摘要

軟體計劃的簡短摘要(不是產品/服務)。 此摘要顯示在 Azure 應用商店搜尋結果中,最多可以包含 100 個字元。

### <a name="plan-description"></a>計劃描述

描述是什麼讓此軟體計畫獨一無二,以及您產品/服務中計劃之間的差異。 不要描述這個提議,只是計劃。 此說明將顯示在 Azure 應用商店和 **「產品/服務」清單**頁上的 Azure 門戶中。 它可以與您在計劃摘要中提供的內容相同,並包含最多 2,000 個字元。

完成這些欄位後選擇 **「保存**」。

#### <a name="plan-examples"></a>計劃範例

以下範例顯示了計劃清單欄位如何出現在不同的檢視中。

查看計劃詳細資訊時,Azure 應用商店中的欄位:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="說明在 Azure 應用商店中查看計劃詳細資訊時看到的欄位。":::

以下是 Azure 門戶上的計劃詳細資訊:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="在 Azure 門戶上說明計劃詳細資訊。":::

## <a name="plan-availability"></a>計劃可用性

如果要隱藏已發佈的產品/服務,以便客戶無法在市場上搜索、瀏覽或購買,請在 **「可用性」** 選項卡上選中 **「 隱藏計畫**」複選框。

此欄位用於:

- 當通過另一個應用程式引用此要約時,該產品/服務將間接使用。
- 報價不應單獨購買。
- 該計劃用於初始測試,不再相關。
- 該計劃用於臨時或季節性優惠,不應再提供。

## <a name="technical-configuration"></a>技術設定

容器映射必須託管在私有的[Azure 容器註冊表](https://azure.microsoft.com/services/container-registry/)中。 在 **「技術設定」** 選項卡上,為 Azure 容器註冊表中的容器映像儲存庫提供參考資訊。

發佈產品/服務後,容器映射將複製到特定公共容器註冊表中的 Azure 應用商店。 使用容器映射的所有請求都來自 Azure 應用商店公共容器註冊表,而不是私有註冊表。 有關詳細資訊,請參閱[準備 Azure 容器技術資產](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)。

### <a name="image-repository-details"></a>影像儲存函式庫詳細資訊

在 **「映像儲存庫詳細資訊**」選項卡上提供以下資訊。

**Azure 訂閱 ID** – 提供訂閱 ID,其中報告使用方式,並為包含容器映射的 Azure 容器註冊表計費服務。 您可以在 Azure 門戶中的[「訂閱」 頁上](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)找到此 ID。

**Azure 資源群組名稱**– 提供包含容器映射的 Azure 容器註冊表[的資源組](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal)名稱。 必須在訂閱 ID(上圖)中訪問資源組。 您可以在 Azure 門戶中的[「資源組」](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)頁上找到該名稱。

**Azure 容器註冊表名稱**– 提供具有容器映射的[Azure 容器註冊表](https://docs.microsoft.com/azure/container-registry/container-registry-intro)的名稱。 容器註冊表必須位於之前提供的 Azure 資源組中。 僅包括註冊表項,不包括完整的登錄伺服器名稱。 請務必省略名稱**中azurecr.io。** 您可以在 Azure 門戶中的[容器註冊表頁上](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)找到註冊表項。

**Azure 容器註冊表的管理員使用者名**– 提供與具有容器映像的 Azure 容器註冊表連結的[管理員使用者名稱](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)。 需要使用者名和密碼來確保您的公司有權訪問註冊表。 要取得管理員使用者名稱和密碼,請使用 Azure 命令列介面 (CLI) 將**啟用管理員**的屬性設定為**True。** 可以選擇管理**員使用者**設定為 Azure 門戶中**啟用**。

 :::image type="content" source="media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="演示"更新容器註冊表"對話框。":::

**Azure 容器註冊表的密碼**– 提供與 Azure 容器註冊表關聯的管理員使用者名的密碼,並具有容器映射。 需要使用者名和密碼來確保您的公司有權訪問註冊表。 您可以通過使用[show 命令](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show)存取**容器註冊表** > **存取金鑰**或使用 Azure CLI 從 Azure 門戶獲取密碼。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="說明訪問鍵功能表。":::

**Azure 容器註冊表中的儲存函式庫名稱**。 提供具有映射的 Azure 容器註冊表儲存庫的名稱。 將映射推送到註冊表時,包括存儲庫的名稱。 您可以通過存[取 容器註冊表](https://azure.microsoft.com/services/container-registry/) > **儲存庫**頁來查找儲存庫的名稱。 有關詳細資訊,請參閱在[Azure 門戶中檢視容器註冊表儲存庫](https://docs.microsoft.com/azure/container-registry/container-registry-repositories)。

> [!NOTE]
> 設置名稱後,無法更改它。 對帳戶中的每個產品/服務使用唯一名稱。

### <a name="image-tags-for-new-versions-of-your-offer"></a>產品/服務新版本的影像標籤

發佈更新時,客戶必須能夠自動從 Azure 應用商店獲取更新。 如果他們不想更新,他們必須能夠保留您的映射的特定版本。 通過在每次更新圖像時添加新圖像標記來執行此操作。

### <a name="image-tag"></a>映像標籤

此欄位必須包含指向所有受支援平臺上映像的最新版本**的最新**標記。 它還必須包含一個版本標記(例如,從xx.xx.xx 開始,其中xx是數位)。 客戶應使用[清單標記](https://github.com/estesp/manifest-tool)來定位多個平臺。 也請務必新增資訊清單標籤參考的所有標籤，以便上傳。

所有清單標記(最新標記除外)必須以 X.Y**-** 或 X.Y.Z 開頭,其中 X、Y 和 Z 是整數。 例如,如果**最新的**標記指向 1.0.1-linux-x64、1.0.1-linux-arm32 和 1.0.1-windows-arm32,則需要將這六個標記添加到此字段中。 有關詳細資訊,請參閱[準備 Azure 容器技術資產](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)。

> [!NOTE]
> 請記得將測試標籤新增到您的映像，以便您在測試期間識別該映像。

## <a name="review-and-publish"></a>稽核和發佈

完成產品/服務所需的所有部分後,可以將其提交以供審核和發佈。

在門戶的右上角,選擇 **「審閱」併****發佈**。

在評論頁面上,您可以:

- 請參閱產品/服務的每個部分的完成狀態。 在產品/服務的所有部分都標記為完整之前,您無法發佈。
  - **尚未啟動**= 尚未啟動,需要完成。
  - **不完整**– 有需要修復或要求您提供詳細資訊的錯誤。 有關説明,請參閱本文檔前面的部分。
  - **完成**= 包括所有必需的資料,沒有錯誤。 優惠的所有部分必須完成,然後才能提交報價。
- 向認證團隊提供測試說明,以確保您的產品/服務測試正確。 此外,提供有助於瞭解您的報價的任何補充說明。

要提交發佈要約,請選擇 **「發佈**」。。

我們將向您發送一封電子郵件,告知您產品/服務預覽版本何時可供審核和批准。

要向公眾發佈您的優惠(或者如果私人優惠,則向私人受眾),請轉到合作夥伴中心並選擇 **"Go-live**"。

## <a name="next-step"></a>後續步驟

- [更新商業市集中的現有供應項目](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
