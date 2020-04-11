---
title: 使用合作夥伴中心建立 Azure IoT 邊緣模組產品 /Azure 應用商店
description: 瞭解如何使用合作夥伴中心在 Azure 應用商店中創建 IoT 邊緣模組產品
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 6b56b748ef31bcfd33893e55d3ea5f8d9851a3ff
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674471"
---
# <a name="create-an-iot-edge-module-offer"></a>建立 IoT Edge 模組供應項目

> [!IMPORTANT]
> 我們將IoT Edge模組產品的管理從雲合作夥伴門戶遷移到合作夥伴中心。 在遷移產品/服務之前,請按照 IoT Edge 模組中的說明操作,為雲端合作夥伴門戶[提供發布概述](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts)來管理您的產品/服務。

本文介紹如何為 Azure 應用商店創建和發佈物聯網 (IoT) 邊緣模組產品。

在創建 IoT Edge 模組產品產品之前,必須在合作夥伴中心中具有商業市場帳戶。 如果您尚未建立一個帳戶,請參閱[在合作夥伴中心建立商業市場帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)。

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入合作夥伴中心。
2. 在左邊導航選單中,選擇**商業市場** > **概述**。

    ![說明左導航功能表。](./media/cs-menu-overview.png)

3. 選擇 **= 新產品 IoT** > **邊緣模組**。 將顯示 **「新建產品/服務**」對話框。

> [!IMPORTANT]
> 發佈產品/服務后,在合作夥伴中心中對其所做的編輯僅在重新發佈產品/服務后顯示在網店中。 請確保在進行更改后始終重新發佈。

### <a name="offer-id-and-alias"></a>優惠識別碼與別名

輸入**優惠代碼**。 這是您帳戶中每個產品/服務的唯一標識碼。

- 此 ID 在市場產品/服務的網站位址和 Azure 資源管理器範本(如果適用)中可見。
- 請一律使用小寫字母和數字。 它可以包括連字元和下劃線,但沒有空格,並且限制為 50 個字元。 例如,如果輸入**測試-產品/服務 -1,** 則產品`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`/服務 Web 位址將為 。
- 選擇"建立"後,無法更改優惠 ID。

輸入**產品/服務別名**。 這是用於指合作夥伴中心產品/服務的名稱。

- 此名稱不在市場上使用,並且不同於向客戶顯示的要約名稱和其他值。
- 選擇 後無法變更 **。**

輸入這兩個值後,選擇 **「先創建」,** 然後再繼續下一頁「產品/服務概覽」。

## <a name="offer-overview"></a>供應項目概觀

**"產品/服務概述"** 頁顯示發佈此產品/服務所需的步驟(已完成和即將推出)以及每個步驟應完成多長時間的可視化表示形式。

此頁包含基於您做出的選擇對此產品/服務執行操作的連結。 例如：

- 如果要約是草稿 -[刪除草稿要約](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- 如果優惠是即時的 -[停止銷售優惠](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- 如果優惠處於預覽版 -[上線](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 如果您尚未完成發行者登出 -[取消發佈。](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>優惠設定

按照以下步驟設置您的產品/服務。

### <a name="connect-lead-management"></a>連線潛在客戶管理

使用合作夥伴中心將產品/服務發佈到市場時,您可以選擇將其連接到客戶關係管理 (CRM) 系統。 這樣,一旦有人對您的產品表示興趣或使用,您就可以立即接收客戶聯繫資訊。

1. 選取您要我們傳送潛在客戶至其中的潛在客戶目的地。 合作夥伴中心支援以下 CRM 系統:

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
- [潛在客戶管理概述](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)PDF(確保彈出視窗阻止程式已關閉)。

選擇 **"保存"草稿**,然後再繼續下一節"屬性"。

### <a name="properties"></a>屬性

此頁面允許您定義用於在市場上對產品/服務進行分組的類別以及支援您的優惠的法律合同。

#### <a name="category"></a>類別

選擇至少 1 個類別,最多選擇五個類別。 這些類別用於將您的產品/服務放入相應的市場搜索區域,並顯示在您的優惠詳細信息頁面上。 在產品/服務說明中,說明您的優惠如何支援這些類別。 在瀏覽頁面中,所有IoT Edge模組都顯示在 **物聯網> IoT Edge模組** 類別下。

#### <a name="legal"></a>法律

您必須為優惠提供條款和條件。 您有兩個選擇：

- 使用Microsoft商業市場的標準合約。
- 提供您自己的條款和條件。

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>微軟商業市場的標準合同

我們提供標準合同範本,以幫助促進商業市場的交易。 您可以選擇根據標準合同提供解決方案,客戶只需檢查和接受一次。 如果您不想製作自定義條款和條件,這是一個不錯的選擇。

要瞭解有關標準合同的資訊,請參閱[Microsoft 商業市場的標準合約](https://docs.microsoft.com/azure/marketplace/standard-contract)。 您還可以下載[標準合約](https://go.microsoft.com/fwlink/?linkid=2041178)PDF(確保彈出視窗阻止程式已關閉)。

要使用標準合約,請選擇「**使用 Microsoft 商業市場的標準合約**」複選框,然後按一下 **「 接受**」。

> [!NOTE]
> 使用 Microsoft 商業市場的標準合同發佈產品/服務後,不能使用自己的自定義條款和條件。 在標準合同下或根據您自己的條款和條件提供您的解決方案。

![使用 Microsoft 商業市場的標準合同複選框進行說明。](./media/iot-edge-module-creation/iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>您自己的條款和條件

要提供您自己的自定義條款和條件,請在 **「條款和條件」** 框中輸入這些條款和條件。 您可以在此框中輸入無限量的文字字元。 客戶必須先接受這些條款,然後才能試用您的優惠。

選擇 **"保存"草稿**,然後再繼續下一節"優惠清單"。

## <a name="offer-listing"></a>優惠清單

在這裡,您將定義市場上顯示的報價詳細資訊。 這包括產品/服務名稱、說明、圖像等。 在配置此產品/服務時,請務必遵循 Microsoft 策略頁上詳述的策略。

> [!NOTE]
> 如果要約說明以短語開頭,"此應用程式僅以[非英語語言]提供,則優惠詳細資訊不需要以英文表示。 提供有用連結以不同於產品/服務清單詳細資訊中使用的語言提供內容也沒關係。

### <a name="name"></a>名稱

您在此處輸入的名稱顯示為產品/服務的標題。 此欄位預先填充您在建立產品/服務時在 **「產品/服務」別名**框中輸入的文字。 您可以稍後變更此名稱。

名稱:

- 可以註冊商標(您可以包括商標或版權符號)。
- 不能超過50個字元長。
- 不能包括表情符號。

### <a name="search-results-summary"></a>搜尋結果摘要

提供您的優惠的簡短說明。 這最多可達 100 個字元,並且用於市場搜尋結果。

### <a name="long-summary"></a>長摘要

提供更詳細說明您的優惠。 這最多可達 256 個字元,並且用於市場搜尋結果。

### <a name="description"></a>描述

提供較長範圍的報價說明,最多 3,000 個字元。 這在市場清單概述中向客戶顯示。

在描述中包括以下一項或多項:

- 您的產品提供的價值和關鍵優勢
- 類別或行業協會,或兩者
- 應用內購買機會
- 任何必需的披露

IoT Edge 模組產品必須在說明底部包含最低硬體要求段落。 例如：

*最低硬體要求:Linux x64 和 arm32 作業系統、1 GB RAM、500 Mb 儲存*

以下是編寫說明的一些提示:

- 清楚地描述你描述前幾句的報價的價值。 包括以下專案:
    - 供應項目的說明。
    - 從產品/服務中受益的用戶類型。
    - 客戶需求或發出產品/服務位址。
- 請記住,搜尋結果中可能會顯示前幾個句子。
- 不要依賴特性和功能來銷售您的產品。 相反,關注您的產品/服務提供的價值。
- 嘗試使用行業特定的詞彙或基於利益的措辭。

為了使您的產品/**服務更具**吸引力,請使用豐富的文本編輯器來設置說明的格式。 富文字編輯器允許您添加數位、專案符號、粗體、斜體和縮進,以使描述更具可讀性。

:::image type="content" source="media/text-editor2.png" alt-text="說明富文本編輯器。" border="false":::

- 要改變內容的格式,請突出顯示要設定格式的文字並選擇文字樣式,如以下螢幕截圖所示:

     :::image type="content" source="media/text-editor3.png" alt-text="說明富文字編輯器中的文本樣式控制項。" border="false":::

- 要向文字加入項目符號或編號清單,請使用以下螢幕截圖中顯示的選項:
  
    :::image type="content" source="media/text-editor4.png" alt-text="在富文本編輯器中說明項目符號和數位清單控制項。" border="false":::

- 要向文字加入或刪除縮排,請使用以下螢幕截圖中顯示的選項:

    :::image type="content" source="media/text-editor5.png" alt-text="說明富文本編輯器中的縮進控制項。" border="false":::

#### <a name="privacy-policy-url"></a>隱私權原則 URL

輸入組織的隱私政策的網址。 您有責任確保您的報價符合隱私法律和法規。 您還負責在您的網站上發佈有效的隱私政策。

#### <a name="useful-links"></a>實用的連結

提供有關您的優惠的補充在線文檔。 您最多可以添加 25 個連結。 要新增連結,請選擇 **「 新增連結」,** 然後完成以下欄位:

- **標題**- 客戶將在優惠的詳細資訊頁面上看到標題。
- **連結 (URL)** - 輸入客戶檢視連線文件的連結。 鏈接必須以HTTP://或HTTPs://開頭。

請確保至少向文檔添加一個連結,並從 [Azure IoT 設備目錄中](https://catalog.azureiotsolutions.com/)添加一個指向相容 IoT Edge 設備的連結。

### <a name="contact-information"></a>連絡人資訊

您必須提供**支援聯絡人**和**工程連絡人**的姓名、電子郵件和電話號碼。 此資訊不會向客戶顯示。 它可供 Microsoft 使用,並可能提供給雲端解決方案供應商 (CSP) 合作夥伴。

- 支援連絡人(必需):有關一般支持問題。
- 工程聯繫人(必需):有關技術問題和認證問題。
- CSP 計劃聯絡人(可選):對於與 CSP 計劃相關的轉銷商問題。

在 **"支援"連絡人**部分中,提供**支援網站的**Web 位址,合作夥伴可以根據產品/服務在全球 Azure、Azure 政府版或兩者中是否可用來查找產品/服務的支援。

在**CSP 計畫連絡**部分,提供連結 (**CSP 計劃行銷材料**), 其中 CSP 合作夥伴可以找到您的報價的行銷材料.

#### <a name="additional-marketplace-listing-resources"></a>其他市場清單資源

要瞭解有關建立優惠清單的更多資訊,請參閱[產品/服務清單最佳實務](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)。

### <a name="marketplace-images"></a>市場映射

提供徽標和圖像,以便與您的產品配合使用。 所有圖像必須採用 .png 格式。 模糊圖像將被拒絕。

#### <a name="store-logos"></a>Microsoft Store標誌。

在以下四個像素大小中提供產品標誌的 .png 檔:

- **小 (48 x 48)**
- **中等 (90 x 90)**
- **大 (216 x 216)**
- **寬 (255 x 115)**

所有四個徽標都是必需的,並且在市場清單中的不同位置使用。

#### <a name="screenshots-optional"></a>螢幕擷取(選擇性的)

最多添加五個屏幕截圖,顯示您的優惠如何工作。 每個圖元的大小和 .png 格式必須為 1280 x 720 圖元。

#### <a name="videos-optional"></a>視訊(選擇性的)

最多添加五個視頻,演示您的優惠。 輸入影片的名稱、Web 位址和影片的縮圖 .png 圖像,大小為 1280 x 720 圖元。

#### <a name="offer-examples"></a>供應項目範例

以下範例顯示了要約清單欄位如何顯示在要約的不同位置。

此螢幕截圖顯示 Azure 應用商店中的 **「產品/服務」清單**頁。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-offer-listing-page.png" alt-text="在 Azure 應用商店中說明產品/服務列表頁。":::

此螢幕截圖顯示了 Azure 應用商店中的搜尋結果:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-search-results.png" alt-text="在 Azure 應用商店中說明搜尋結果。":::

此螢幕截圖顯示 Azure 門戶中的 **「產品/服務」清單**頁。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-listing-page-azure-portal.png" alt-text="在 Azure 門戶中說明產品/服務列表頁。":::

此螢幕截圖在 Azure 門戶中顯示搜尋結果。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-search-results.png" alt-text="在 Azure 門戶中說明產品/服務列表頁。":::

在繼續下一節「預覽」之前選擇 **「保存草稿**」。。

## <a name="preview"></a>預覽

在 **「預覽」選項卡**上,您可以選擇有限的**預覽受眾**以驗證您的優惠,然後再將其即時發佈到更廣泛的市場受眾。

> [!IMPORTANT]
> 在預覽版中查看優惠后,必須選擇 **「上線」** 以向公眾發佈您的優惠。

使用 Azure 訂閱 ID GUID 指定預覽受眾,以及每個受眾的可選說明。 客戶看不到這兩個字段。

> [!NOTE]
> 您可以在 Azure 門戶中的「訂閱」 頁上找到 Azure 訂閱 ID。

添加至少一個 Azure 訂閱 ID,分別(最多 10 個)或通過上傳 CSV 檔(最多 100 個)。 通過添加這些訂閱 IT,您可以定義誰可以在產品/服務即時發佈之前預覽產品/服務。 如果您的優惠已是即時的,則可以定義預覽受眾以測試產品/服務的變化或更新。

> [!NOTE]
> 預覽觀眾與私人觀眾不同。 **預覽**受眾可以在所有優惠計劃在市場上直播之前查看和確認這些計劃,包括僅向**私人**受眾發佈的計劃(在"可用性"選項卡上設置)。

在繼續下一節"計劃概述"之前選擇 **"保存草稿**"。

### <a name="plan-overview"></a>排程概述

此選項卡允許您在合作夥伴中心的同一優惠中提供不同的計劃選項。 這些計劃以前稱為 SKU 或庫存單位。 計劃在可用雲端方面可能有所不同,例如全域雲、政府雲和計畫引用的圖像。 要在市場上列出您的產品/服務,您必須至少設置一個計劃。

建立計劃後,「**計畫概觀**」選項卡顯示:

- 排程名稱
- 定價模式
- 雲可用性(全球或政府)
- 目前發佈狀態
- 任何可用動作

計劃概述中可用的操作因計劃的當前狀態而異。 其中包括：

- **刪除草稿**:如果計劃狀態為草稿。
- **停止銷售計劃**:如果計劃狀態實時發佈。

#### <a name="create-new-plan"></a>建立新的方案

選擇 **「創建新計畫**」。 將顯示「**新建計畫**」對話框。

在 **「計劃 ID」** 框中,為此產品/服務中的每個計畫創建唯一的計畫 ID。 此 ID 將在產品 Web 位址中對客戶可見。 僅使用小寫字母和數位、破折號或下劃線,最多 50 個字元。

在 **「計畫名稱**」框中,輸入此計畫的名稱。 客戶在決定在您的產品/服務中選擇哪個計劃時,會看到此名稱。 為此產品/服務中的每個計劃創建唯一名稱。 例如,您可以使用**Windows 伺服器**的報價名稱,並計劃 Windows**伺服器 2016**和**Windows 伺服器 2019**。

> [!NOTE]
> 選擇 「**建立**」 後,無法更改計畫 ID。

選取 [建立]  。

### <a name="plan-setup"></a>計畫設定

這個選項卡允許您配置計劃可用的雲端。 此選項卡上的答案會影響其他選項卡上顯示的欄位。

#### <a name="cloud-availability"></a>雲可用性

使用 Azure IoT 中心,您的計畫必須在至少一個雲端中可用。

選擇**Azure 全域**選項,以便使用市場的所有全域 Azure 區域的客戶可以使用計畫。 有關詳細資訊,請參閱[地理可用性和貨幣支援](https://aka.ms/AzureGovCurrencies)。

選擇[Azure 政府雲](https://aka.ms/WhatIsAzureGovernment)選項以使解決方案在此處顯示。 這是一個政府社區雲,由來自美國聯邦、州、地方或部落政府機構的客戶以及有資格為其提供服務的合作夥伴進行受控訪問。 作為發佈者,您負責此雲社區的任何合規性控制、安全措施和最佳實踐。 Azure 政府使用物理隔離的數據中心和網路(僅適用於美國)。 在[發佈到](https://aka.ms/azuregovpublish)Azure 政府之前,請測試並確認解決方案,因為結果可能會有所不同。 要暫示和測試解決方案,請從 Microsoft [Azure 政府試用版](https://aka.ms/AzureGovernmentTrial)請求試用帳戶。

> [!NOTE]
> 計劃發佈並在特定雲中可用后,無法刪除該雲。

#### <a name="azure-government-cloud-certifications"></a>Azure 政府雲認證

僅當在**雲可用性**下選擇**Azure 政府雲**時,此選項才可見。

Azure 政府服務處理受某些政府法規和要求約束的數據。 例如,FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS。 為了提高您對這些計劃的認證的認識,您最多可以提供 100 個描述您的認證的連結。 這些可以是直接連結到您的程式清單或您自己的網站。 這些連結僅對 Azure 政府客戶可見。

## <a name="plan-listing"></a>排程清單

此選項卡顯示同一產品/服務內每個不同計劃的特定資訊。

### <a name="plan-name"></a>計畫名稱

這預先填充了您在創建計畫時給出的名稱。 您可以根據需要更改此名稱。 它可以長達 50 個字元。 此名稱在 Azure 應用商店和 Azure 門戶中顯示為此計劃的標題。 在計劃準備好使用后,它用作預設模組名稱。

### <a name="plan-summary"></a>計劃摘要

提供您的計劃(而不是報價)的簡短摘要。 此摘要顯示在 Azure 應用商店搜尋結果中,最多可以包含 100 個字元。

### <a name="plan-description"></a>計劃描述

描述此計畫的獨特之處,以及優惠內計劃之間的差異。 不要描述這個提議,只是計劃。 此說明將顯示在 Azure 應用商店和「產品/服務」清單頁上的 Azure 門戶中。 它可以與您在計劃摘要中提供的內容相同,並包含最多 2,000 個字元。

完成這些欄位後選擇 **「保存草稿**」。

#### <a name="plan-examples"></a>計劃範例

以下範例顯示了計劃清單欄位如何出現在不同的檢視中。

查看計劃詳細資訊時,Azure 應用商店中的欄位:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-plan-details.png" alt-text="說明在 Azure 應用商店中查看計劃詳細資訊時看到的欄位。":::

以下是 Azure 門戶上的計劃詳細資訊:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-plan-details.png" alt-text="在 Azure 門戶上說明計劃詳細資訊。":::

## <a name="availability"></a>可用性

如果要隱藏已發佈的產品/服務,以便客戶無法在市場上搜索、瀏覽或購買,請在"可用性"選項卡上選中"**隱藏計劃**"複選框。

此欄位通常用於:

- 此優惠僅在另一個應用程式引用時間接使用。
- 報價不應單獨購買。
- 該計劃用於初始測試,不再相關。
- 該計劃用於臨時或季節性優惠,不應再提供。

## <a name="technical-configuration"></a>技術設定

**IoT Edge 模組**提供類型是在 IoT Edge 裝置上運行的特定類型的容器。 在 **「技術設定**」選項卡上,您將在[Azure 容器註冊表](https://aka.ms/ContainerRegistry)內為容器映像儲存庫提供參考資訊,以及允許客戶輕鬆使用模組的配置設定。

發佈產品/服務後,IoT Edge 容器映像將複製到特定公共容器註冊表中的 Azure 應用商店。 Azure 使用者使用模組的所有請求都來自 Azure 應用商店公共容器註冊表,而不是專用容器註冊表。

您可以使用標記定位多個平臺並提供多個版本的模組容器映射。 要瞭解有關標記和版本控制的更多資訊,請參閱[準備 IoT Edge 模組技術資產](https://aka.ms/AzureIoTTechAsset)。

### <a name="image-repository-details"></a>影像儲存函式庫詳細資訊

您將在 **「映像儲存庫詳細資訊**」選項卡上提供以下資訊。

**選擇映像來源**:選擇**Azure 容器註冊表**選項。

**Azure 訂閱 ID**:提供訂閱 ID,其中報告資源使用方式,並為包含容器映射的 Azure 容器註冊表計費服務。 您可以在 Azure 門戶中的[「訂閱」 頁上](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)找到此 ID。

**Azure 資源組名稱**:提供包含容器映射的 Azure 容器註冊表[的資源組](https://aka.ms/ResourceManagerAzurePortal)名稱。 必須在訂閱 ID(上圖)中訪問資源組。 您可以在 Azure 門戶中的[「資源組」](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)頁上找到該名稱。

**Azure 容器註冊表名稱**:提供具有容器映射的[Azure 容器註冊表](https://aka.ms/DockerContainerRegistriesAzure)的名稱。 容器註冊表必須存在於您之前提供的 Azure 資源組中。 僅提供註冊表項名稱,不提供完整的登錄伺服器名稱。 請務必省略名稱**中azurecr.io。** 您可以在 Azure 門戶中的[容器註冊表頁上](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)找到註冊表項。

**Azure 容器註冊表的管理員使用者名稱**:提供與具有容器映像的 Azure 容器註冊表關聯的[管理員使用者名稱](https://aka.ms/AdminAccountContainerRegistry)。 需要使用者名和密碼來確保您的公司有權訪問註冊表。 要取得管理員使用者名稱和密碼,請使用 Azure 命令列介面 (CLI) 將**啟用管理員**的屬性設定為**True。** 可以選擇管理**員使用者**設定為 Azure 門戶中**啟用**。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-admin-user.png" alt-text="演示更新容器註冊表"對話框。":::

**Azure 容器註冊表的密碼**:為與 Azure 容器註冊表關聯的管理員使用者名提供密碼,並具有容器映射。 需要使用者名和密碼來確保您的公司有權訪問註冊表。 您可以通過使用[show 命令](https://aka.ms/azacrcredentialshow)存取**容器註冊表** > **存取金鑰**或使用 Azure CLI 從 Azure 門戶獲取密碼。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-username-password.png" alt-text="說明 Azure 門戶中的訪問鍵螢幕。":::

**Azure 容器註冊表中的儲存函式庫名稱**。 提供具有映射的 Azure 容器註冊表儲存庫的名稱。 將映射推送到註冊表時,可以指定存儲庫的名稱。 您可以通過存[取 容器註冊表](https://aka.ms/ContainerRegistry) > **儲存庫頁**來查找儲存庫的名稱。 有關詳細資訊,請參閱在[Azure 門戶中檢視容器註冊表儲存庫](https://aka.ms/ContainerRegistryRepositoriesAzure)。 請注意,設置名稱后,無法更改它。 對帳戶中的每個產品/服務使用唯一名稱。

### <a name="image-tags-for-new-versions-of-your-offer"></a>產品/服務新版本的影像標籤

發佈更新時,客戶必須能夠自動從 Azure 應用商店獲取更新。 如果他們不想更新,他們必須能夠保留您的映射的特定版本。 通過在每次更新圖像時添加新圖像標記來執行此操作。

**影像標籤**。 此欄位必須包含指向所有受支援平臺上映像的最新版本**的最新**標記。 它還必須包含一個版本標記(例如,從xx.xx.xx 開始,其中xx是數位)。 客戶應使用[清單標記](https://aka.ms/GitHubmanifest-tool)來定位多個平臺。 也請務必新增資訊清單標籤參考的所有標籤，以便上傳。 所有清單標記(最新標記除外)必須以 X.Y 或 X.Y.Z- 開頭,其中 X、Y 和 Z 是整數。 例如,如果最新的標記指向 1.0.1-linux-x64、1.0.1-linux-arm32 和 1.0.1-windows-arm32,則需要將這六個標記添加到此字段中。 有關標記和版本控制的詳細資訊,請參閱[準備 IoT Edge 模組技術資產。](https://aka.ms/PrepareIoTEdgeModTechAssets)

### <a name="default-deployment-settings-optional"></a>預設部署設定 (選擇性的)

請定義您的 IoT Edge 模組最常用的部署設定。 通過允許他們使用這些預設設置啟動開箱即用的 IoT Edge 模組來優化客戶部署。

**預設路由**。 IoT 邊緣中心管理模組、IoT 中心和設備之間的通信。 您可以在模組和 IoT 中心之間設定數據輸入和輸出的路由,從而使您能夠靈活地在需要的消息位置發送消息,而無需其他服務來處理消息或編寫其他代碼。 路由使用名稱/值對構造。 您最多可以定義五個預設路由名稱,每個路由名稱最多為 512 個字元。

請務必在路由值中使用正確的[路由語法](https://aka.ms/DeclareRoutesAzureIoT)(通常定義為 FROM/消息/* into $upstream)。 這意味著任何模組發送的任何消息都發送到 IoT 中心。 要參考您的模組,請使用其預設模組名稱,該名稱將是您的**優惠名稱**,沒有空格或特殊字元。 要參考其他尚不為人所知的模組,請使用<FROM_MODULE_NAME>約定,讓您的客戶知道他們需要更新此資訊。 有關 IoT 邊緣路由的詳細資訊,請參閱[宣告路由](https://aka.ms/DeclareRoutesAzureIoT)。

例如,如果模組 ContosoModule 偵聽 ContosoInput 上的輸入和 ContosoOutput 的輸出資料,則定義以下兩個預設路由是有意義的:

- 名稱#1:toContoso模組
- 值#1:從 /消息/模組/<FROM_MODULE_NAME>/輸出/* 到中轉端點("/模組/Contoso 模組/輸入/Contosoinput")
- 名稱#2:從康托索模組到雲
- 值#2:從 /消息/模組/Contonso 模組/輸出/contoso 輸出到$upstream

**預設模組孿生所需屬性**。 模組孿生是 IoT 中心中的 JSON 文檔,用於儲存模組實例的狀態資訊,包括所需的屬性。 所需屬性與報告的屬性一起使用,以同步模組配置或條件。 解決方案後端可設定所需的屬性,模組可以讀取它們。 模組還可以接收所需屬性中的更改通知。 使用最多五個名稱/值對創建所需的屬性,每個預設值必須小於 512 個字元。 您最多可以定義五個名稱/值孿生所需屬性。 雙所需屬性的值必須為有效的 JSON,非轉義,沒有最大嵌套層次結構為四個級別的陣列。 在預設值所需的參數沒有意義(例如,客戶伺服器的 IP 位址)的情況下,可以添加參數作為預設值。 要瞭解有關雙所需屬性的詳細資訊,請參閱[定義或更新所需屬性](https://aka.ms/DefineUpdateProperties)。

例如,如果模組使用雙所需屬性支援動態可配置的刷新率,則定義以下預設孿生所需屬性是有意義的:

- 名稱#1:刷新率
- 價值#1: 60

**預設環境變數**。 環境變數為幫助配置過程的模組提供補充資訊。 使用名稱/值對創建環境變數。 每個預設環境變數名稱和值必須小於 512 個字元,並且最多可以定義 5 個字元。 當預設值所需的參數沒有意義(例如,客戶伺服器的 IP 位址)時,可以添加參數作為預設值。

例如，如果模組在啟動之前需要接受使用條款，您可以定義以下環境變數：

- 名稱#1:ACCEPT_EULA
- 值#1:Y

**預設容器建立選項**。 容器建立選項直接用於創建 IoT 邊緣模組 Docker 容器。 IoT 邊緣支援 Docker 引擎 API 創建容器選項。 查看[List 容器](https://aka.ms/ContainerList)中的所有選項。 創建選項欄位必須有效 JSON、非轉義字元和少於 512 個字元。

例如,如果模組需要連接埠,請定義以下建立選項:

"主機配置":{"埠綁定":{"5012/tcp":{"主機埠":"5012"|*

## <a name="review-and-publish"></a>稽核和發佈

完成產品/服務所需的所有部分後,可以將其提交以供審核和發佈。

在門戶的右上角,選擇 **「審閱」併發佈**。

在審閱頁上,您可以看到發佈狀態:

- 請參閱產品/服務的每個部分的完成狀態。 在產品/服務的所有部分都標記為完整之前,您無法發佈。
    - **未啟動**- 部分尚未啟動,需要完成。
    - **不完整**- 該部份有需要修復或要求您提供詳細資訊的錯誤。 有關指導,請參閱本文檔前面的部分。
    - **完成**- 該部分具有所有必需的數據,並且沒有錯誤。 優惠的所有部分必須完成,然後才能提交報價。
- 向認證團隊提供測試說明,以確保您的產品/服務測試正確。 此外,提供有助於瞭解您的報價的任何補充說明。

要提交發佈要約,請選擇 **「發佈**」。。

我們將向您發送一封電子郵件,告知您產品/服務預覽版本何時可供審核和批准。 要向公眾發佈您的優惠(或者如果私人優惠,則向私人受眾),請轉到合作夥伴中心並選擇 **"Go-live**"。

## <a name="next-steps"></a>後續步驟

- [更新商業市場中的現有產品/服務](https://docs.microsoft.com//azure/marketplace/partner-center-portal/update-existing-offer)
