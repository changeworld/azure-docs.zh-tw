---
title: 在合作夥伴中心建立 Azure 容器供應專案-Azure Marketplace
description: 本文說明如何建立和發佈 Azure Marketplace 的容器供應專案。
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 3170849955485c0d098ed9f39c22332456e46fe4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81867354"
---
# <a name="create-an-azure-container-offer"></a>建立 Azure 容器供應項目

> [!IMPORTANT]
> 我們正在將您的 Azure 容器供應專案管理從 Cloud Partner 入口網站移至合作夥伴中心。 在您的供應專案遷移之前，請遵循 Cloud Partner 入口網站[容器](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer)中的指示來管理您的供應專案。

本文說明如何建立和發佈 Azure Marketplace 的容器供應專案。 開始之前，請先[在合作夥伴中心建立商業 Marketplace 帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)。

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)，然後從頂端功能表中選取 [**儀表板**]。
2. 在左側功能表中，依序選取 [**商業 Marketplace**] 和 **[總覽**]。
3. 在 [**總覽**] 頁面上，依序選取 [ **+ 新增供應**專案] 和 [ **Azure 容器**]。 [**新增供應**專案] 對話方塊隨即出現。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-new-container.png" alt-text="在合作夥伴中心的 [總覽] 頁面中說明。[新增供應專案] 按鈕和諮詢服務供應專案會反白顯示。":::

> [!TIP]
> 發行供應專案之後，在合作夥伴中心對其進行的編輯，只會在重新發佈供應專案後出現在店面中。 請務必在進行變更之後，一律重新發佈。

### <a name="offer-id-and-alias"></a>供應專案識別碼和別名

輸入**供應專案識別碼**。 這是您帳戶中每個供應專案的唯一識別碼。

- 在 marketplace 供應專案和 Azure Resource Manager 範本的網址中，客戶可以看到此識別碼（如果適用的話）。
- 請一律使用小寫字母和數字。 它可以包含連字號和底線，但不能有空格，而且限制為50個字元。 例如，如果您輸入 [**測試-供應專案-1**]，則供應專案 web `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`位址會是。
- 選取 [**建立**] 之後，即無法變更供應專案識別碼。

**輸入****供應專案別名**。 這是用來在合作夥伴中心參考供應專案的名稱。

- 此名稱不會在 marketplace 中使用，而且與向客戶顯示的供應專案名稱和其他值不同。
- 選取 [**建立**] 之後就無法變更。

選取 [**建立**]，然後繼續進行。

## <a name="offer-overview"></a>供應項目概觀

[**供應專案總覽**] 頁面會顯示發佈此供應專案所需步驟的視覺標記法（已完成和即將推出），以及每個步驟應完成幾段時間。

此頁面會根據供應專案的目前狀態來顯示不同的連結。 例如：

- 如果供應專案為草稿-[刪除草稿供應](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)專案
- 如果供應專案已上線，則會[停止銷售供應](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)專案
- 如果供應專案處於預覽[狀態-上線](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 如果您尚未完成發行者登出，請[取消發佈](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>供應專案設定

請遵循下列步驟來設定您的供應專案。

### <a name="connect-lead-management--optional"></a>連接潛在客戶管理–選擇性

當您使用合作夥伴中心將您的供應專案發佈至 marketplace 時，您可以將它連接到您的客戶關係管理（CRM）系統。 這可讓您在有人對您的產品感興趣或使用時，立即收到客戶連絡人資訊。

1. **選取您想要讓我們傳送潛在客戶的潛在客戶目的地**。 合作夥伴中心支援下列 CRM 系統：

- [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) For Customer Engagement
- [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
- [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

> [!NOTE]
> 如果您的 CRM 系統未列于上方，請使用[Azure 資料表](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)或[Https 端點](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)來儲存客戶潛在客戶資料，然後將資料匯出至您的 crm 系統。

2. 在合作夥伴中心發佈時，將您的供應專案連接到潛在客戶目的地。
3. 確認已正確設定與潛在客戶目的地的連接。 在合作夥伴中心發佈之後，我們會驗證連線並將測試潛在客戶傳送給您。 當您在供應專案上線前進行預覽時，您也可以嘗試在預覽環境中自行購買供應專案，藉此測試您的潛在客戶連線。
4. 請確定與潛在客戶目的地的連線持續更新，讓您不會遺失任何潛在客戶。

以下是一些額外的潛在客戶管理資源：

- [潛在客戶管理總覽](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [潛在客戶管理常見問題集](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [常見潛在客戶設定錯誤](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [潛在客戶管理總覽](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)PDF （請確定您的快顯封鎖程式已關閉）

選取 [**儲存草稿**]，然後繼續進行下一節的 [屬性]。

### <a name="properties"></a>屬性

此頁面可讓您定義用來在 marketplace 上將供應專案分組的類別，以及支援供應專案的法律合約。

#### <a name="category"></a>類別

選取最少1個，最多五個類別。 這些類別是用來將您的供應專案放在適當的 marketplace 搜尋區域，並顯示在您的供應專案詳細資料頁面上。 在 [供應專案描述] 中，說明您的供應專案如何支援這些類別。 容器會出現在 [**容器**] 和 [**容器映射**] 類別之下。

#### <a name="legal"></a>法律

您必須提供供應專案的條款及條件。 有兩個選項：

- 使用 Microsoft 商業 marketplace 的標準合約。
- 提供您自己的條款及條件。

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 商業 marketplace 的標準合約

我們提供標準合約範本，協助促進商業 marketplace 中的交易。 您可以選擇在標準合約下提供您的解決方案，而客戶只需要檢查並接受一次。 如果您不想要建立自訂條款及條件，這是個不錯的選項。

若要深入瞭解標準合約，請參閱[Microsoft 商業 marketplace 的標準合約](https://docs.microsoft.com/azure/marketplace/standard-contract)。 您也可以下載[標準合約](https://go.microsoft.com/fwlink/?linkid=2041178)PDF （請確定您的快顯封鎖程式已關閉）。

若要使用標準合約，請選取 [**使用 Microsoft 商用 marketplace 的標準合約**] 核取方塊，然後按一下 [**接受**]。

> [!NOTE]
> 當您使用 Microsoft 商業 marketplace 的標準合約發佈供應專案之後，就無法使用自己的自訂條款及條件。 請根據標準合約或您自己的條款及條件來提供您的解決方案。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="說明如何使用 Microsoft 的商用 marketplace 標準合約核取方塊。":::

##### <a name="your-own-terms-and-conditions"></a>您自己的條款及條件

若要提供您自己的自訂條款及條件，請在 [**條款及條件**] 方塊中輸入。 您可以在此方塊中輸入不限數量的文字字元。 客戶必須接受這些條款，才能試用您的供應專案。

選取 [**儲存草稿**]，然後繼續進行下一節的供應專案清單。

## <a name="offer-listing"></a>供應專案清單

此頁面可讓您定義顯示在商業 marketplace 中的供應專案詳細資料。 這包括供應專案名稱、描述和影像。

> [!NOTE]
> 若供應專案描述的開頭為「非英文語言」，則不需要使用英文版的供應專案詳細資料。 也可以提供有用的連結，以不同于供應專案清單詳細資料所使用的語言來提供內容。

### <a name="name"></a>Name

您在此處輸入的名稱會顯示為供應專案的標題。 當您建立供應專案時，此欄位會預先填入您在 [**供應專案別名**] 方塊中輸入的文字。 您可以稍後變更此名稱。

名稱：

- 可能是商標（而且您可以包含商標和著作權符號）。
- 長度不能超過50個字元。
- 不能包含 emoji。

### <a name="search-results-summary"></a>搜尋結果摘要

您的供應專案簡短描述。 最多可有100個字元，並用於 marketplace 搜尋結果。

### <a name="long-summary"></a>完整摘要

更詳細的供應專案描述。 最多可有256個字元，並用於 marketplace 搜尋結果。

### <a name="description"></a>描述

提供供應專案的較長描述，最多3000個字元。 這會在 marketplace 清單總覽中向客戶顯示。

在您的描述中包含下列一或多項：

- 您的供應專案提供的價值與主要優點
- 類別或產業關聯，或兩者
- 應用程式內購買機會
- 任何必要的公開

以下是撰寫描述的一些秘訣：

- 清楚描述您的供應專案在您的前幾個句子中的價值。 包含下列專案：
  - 供應項目的說明。
  - 受益于供應專案的使用者類型
  - 客戶需要或發行供應專案位址。
- 請記住，前幾個句子可能會顯示在搜尋結果中。
- 不依賴特性和功能來銷售您的產品。 相反地，請專注于您的供應專案提供的值。
- 請嘗試使用產業專屬詞彙或以權益為基礎的用語。

若要讓您的供應專案**描述**更吸引人，請使用 rich 文字編輯器來格式化您的描述。 使用編號、專案符號、粗體、斜體和縮排，讓您的描述更容易閱讀。

:::image type="content" source="media/text-editor2.png" alt-text="說明 rich 文字編輯器。" border="false" :::

- 使用此下拉式按鈕，將段落樣式套用至文字。

    :::image type="content" source="media/text-editor3.png" alt-text="說明 rtf 文字編輯器中的文字樣式控制項。" border="false":::

- 使用這些圖示將編號或專案符號套用至文字。

     :::image type="content" source="media/text-editor4.png" alt-text="說明 rtf 文字編輯器中的專案符號和數位清單控制項。" border="false":::

- 使用這些圖示來新增或移除文字的縮排。

    :::image type="content" source="media/text-editor5.png" alt-text="說明 rtf 文字編輯器中的縮排控制項。" border="false":::

#### <a name="privacy-policy-link"></a>隱私權原則連結

輸入貴組織的隱私權原則網址。 您必須負責確保您的供應專案符合隱私權法規。 您也必須負責在您的網站上張貼有效的隱私權原則。

#### <a name="useful-links"></a>實用的連結

提供有關您供應專案的補充線上檔。 您最多可以新增25個連結。 若要新增連結，請選取 [ **+ 新增連結**]，然後完成下欄欄位：

- **標題**–客戶會在您供應專案的詳細資料頁面上看到此資訊。
- **連結（URL）** –輸入客戶的連結以查看您的線上檔。 此連結的開頭必須是 HTTP://或 HTTPs://。

### <a name="contact-information"></a>連絡資訊

您必須提供**支援連絡人**的名稱、電子郵件和電話號碼，以及**工程連絡人**。 這種資訊不會向客戶顯示，但可供 Microsoft 使用。 也可以提供給雲端解決方案提供者（CSP）合作夥伴。

- 支援連絡人（必要）：針對一般支援問題。
- 工程連絡人（必要）：針對技術問題和認證問題。
- CSP 方案連絡人（選擇性）：適用于與 CSP 計畫相關的轉售商問題。

在**支援連絡人**一節中，提供**支援網站**，合作夥伴可以根據供應專案是否可在全域 Azure、Azure Government 或兩者中找到支援服務。

在 [ **Csp 方案連絡人**] 區段中，提供可供 csp 合作夥伴尋找供應專案行銷資料的連結（ **Csp 計畫行銷材料**）。

#### <a name="additional-marketplace-listing-resources"></a>其他 marketplace 清單資源

若要深入瞭解如何建立供應專案清單，請參閱[供應專案清單最佳做法](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

### <a name="marketplace-images"></a>Marketplace 映射

提供要與您的供應專案搭配使用的標誌和影像。 所有影像都必須是 PNG 格式。 模糊影像將會遭到拒絕。

>[!Note]
>如果您在上傳檔案時發生問題，請確定您的區域網路不會https://upload.xboxlive.com封鎖合作夥伴中心所使用的服務。

#### <a name="store-logos"></a>Microsoft Store標誌。

 在下列四個圖元大小的每一個中，提供您供應專案標誌的 PNG 檔案：

- **小型**（48 x 48）
- **中**（90 X 90）
- **大型**（216 x 216）
- **寬**（255 X 115）

這四個標誌都是必要的，並且用於 marketplace 清單中的不同位置。

#### <a name="screenshots-optional"></a>螢幕擷取畫面（選擇性）

新增最多五個螢幕擷取畫面，以顯示供應專案的運作方式。 每個都必須是 1280 x 720 圖元大小和 PNG 格式。

#### <a name="videos-optional"></a>影片（選擇性）

新增最多五個示範您供應專案的影片。 以 1280 x 720 圖元的大小，輸入影片的名稱、其網址和影片的縮圖 PNG 影像。

#### <a name="offer-examples"></a>供應項目範例

下列範例顯示供應專案清單欄位如何出現在供應專案的不同位置。

這會在 Azure Marketplace 中顯示**供應專案清單**頁面：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="說明 Azure Marketplace 中的供應專案清單頁面。" :::

這會在 Azure Marketplace 中顯示搜尋結果：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="說明 Azure Marketplace 中的搜尋結果。":::

這會在 Azure 入口網站中顯示**供應專案清單**頁面：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="說明 Azure 入口網站中的供應專案清單頁面。":::

這會在 Azure 入口網站中顯示搜尋結果：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="說明 Azure 入口網站中的搜尋結果。":::

## <a name="preview"></a>預覽

在 [預覽] 索引標籤上，您可以選擇有限的**預覽物件**來驗證您的供應專案，然後再即時發佈。

> [!IMPORTANT]
> 在**預覽**中觀看供應專案之後，您必須選取 [**上線**]，將您的供應專案發佈至公用。

使用 Azure 訂用帳戶識別碼 Guid 指定您的預覽物件，以及每個使用者的選擇性描述。 客戶無法看到這兩個欄位。

> [!NOTE]
> 您可以在 Azure 入口網站的 [訂用帳戶] 頁面上找到您的 Azure 訂用帳戶識別碼。

新增至少一個 Azure 訂用帳戶識別碼，分別是（最多10個）或上傳 CSV 檔案（最多100個）。 藉由新增這些訂用帳戶識別碼，您可以決定誰可以預覽您的供應專案，然後才能即時發佈。 如果您的供應專案已上線，您可以選擇預覽物件，以測試您的供應專案變更或更新。

> [!NOTE]
> 預覽物件與私用物件不同。 **預覽**物件可以在所有供應專案在 marketplace 中上線之前查看並加以確認，包括只會發佈給**私人**物件的方案（在 [可用性] 索引標籤上設定）。

選取 [**儲存草稿**] 再繼續。

### <a name="plan-overview"></a>計畫總覽

此索引標籤可讓您在相同的供應專案中提供不同的方案選項。 這些方案先前稱為 Sku 或庫存單位。 方案可能會因可用的雲端而有所不同，例如全域雲端、政府雲端，以及方案所參考的影像。 若要在商業 marketplace 中列出您的供應專案，您至少必須設定一個方案。

建立計畫之後，[**計畫總覽**] 索引標籤會顯示：

- 計畫名稱
- 定價模式
- 雲端可用性（全球或政府）
- 目前的發行狀態
- 任何可用的動作

計畫總覽中的可用動作會根據方案的目前狀態而有所不同。 其中包括：

- **刪除草稿**–如果計畫狀態為草稿。
- **停止銷售方案**–如果計畫狀態是 [即時發行]。

#### <a name="create-new-plan"></a>建立新的方案

選取 [**建立新方案**]。 [**新增方案**] 對話方塊隨即出現。

在 [**方案 ID** ] 方塊中，為此供應專案中的每個方案建立唯一的方案識別碼。 客戶會在產品的網址看到此識別碼。 僅使用小寫字母和數位、連字號或底線，以及最多50個字元。

> [!NOTE]
> 選取 [**建立**] 之後，就無法變更方案識別碼。

在 [**方案名稱**] 方塊中，輸入此計畫的名稱。 客戶在決定要在您的供應專案內選取哪一個方案時，會看到此名稱。 為此供應專案中的每個方案建立唯一的名稱。 例如，您可以使用**Windows server**的供應專案名稱，並規劃**windows Server 2016**和**windows server 2019**。

### <a name="plan-setup"></a>規劃設定

此索引標籤可讓您選擇方案可供使用的雲端。 您在此索引標籤上的答案會影響哪些欄位會顯示在其他索引標籤上。

#### <a name="cloud-availability"></a>雲端可用性

您的方案至少必須在一個雲端中提供。

選取 [ **Azure 全域**] 選項，讓使用商業 marketplace 的所有全球 Azure 區域中的客戶可以使用您的方案。 如需詳細資訊，請參閱[地理可用性和貨幣支援](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)。

選取 [ [**Azure Government 雲端**](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)] 選項，讓您的解決方案顯示在這裡。 這是一家政府社區雲端，針對美國聯邦、州和地方或部落政府機關的客戶，以及符合其服務資格的合作夥伴提供控制存取。 身為發行者，您必須負責此雲端社區的任何合規性控制、安全性措施和最佳作法。 Azure Government 會使用實際隔離的資料中心和網路（僅限美國地區）。

[發行](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)至 Azure Government 之前，請在該區域內測試並確認您的解決方案，因為結果可能會不同。 若要建立並測試您的解決方案，請從[Microsoft Azure Government 試用](https://azure.microsoft.com/global-infrastructure/government/request/)版要求試用帳戶。

> [!NOTE]
> 在您的方案發佈且可在特定雲端中使用之後，您就無法移除該雲端。

#### <a name="azure-government-cloud-certifications"></a>Azure Government 雲端認證

只有在 [**雲端可用性**] 下選取 [ **Azure Government 雲端**] 時，才會看到此選項。

Azure Government 服務會處理受限於特定政府法規和需求的資料。 例如，FedRAMP、NIST 800.171 （DIB）、ITAR、IRS 1075、DoD L4 和 CJIS。

若要顯示您在這些程式中的認證，您可以提供最多100個描述的連結。 這些可以直接連結到您在程式上的清單或您自己的網站。 只有 Azure Government 客戶可以看到這些連結。

## <a name="plan-listing"></a>計畫清單

此索引標籤會顯示目前供應專案中每個不同方案的特定資訊。

### <a name="plan-name"></a>計畫名稱

這會預先填入您在建立方案時提供給您的計畫名稱。 您可以視需要變更此名稱。 長度最多可達50個字元。 這個名稱在 Azure Marketplace 和 Azure 入口網站中會顯示為此計畫的標題。 在準備好使用計畫之後，它會用來做為預設的模組名稱。

### <a name="plan-summary"></a>計畫摘要

軟體方案的簡短摘要（不是供應專案）。 此摘要會出現在 Azure Marketplace 搜尋結果中，而且最多可包含100個字元。

### <a name="plan-description"></a>計畫描述

描述此軟體方案的獨特之處，以及供應專案內方案之間的差異。 請不要描述供應專案，只是計畫。 此描述會出現在 [Azure Marketplace] 和 [供應專案**清單**] 頁面上的 [Azure 入口網站中。 它可以是您在計畫摘要中提供的相同內容，且最多可包含2000個字元。

完成這些欄位之後，請選取 [**儲存**]。

#### <a name="plan-examples"></a>計劃範例

下列範例顯示計畫清單欄位如何出現在不同的視圖中。

這些是查看方案詳細資料時 Azure Marketplace 中的欄位：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="說明在 Azure Marketplace 中查看計畫詳細資料時所看到的欄位。":::

這些是 Azure 入口網站的計畫詳細資料：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="說明 Azure 入口網站的方案詳細資料。":::

## <a name="plan-availability"></a>規劃可用性

如果您想要隱藏已發佈的供應專案，讓客戶無法在 marketplace 中搜尋、流覽或購買，請選取 [**可用性**] 索引標籤上的 [**隱藏方案**] 核取方塊。

此欄位是在下列情況下使用：

- 此供應專案的目的是要在其他應用程式參考時間接使用。
- 供應專案不應個別購買。
- 此計畫用於初始測試，不再相關。
- 此計畫用於暫時或季節性的供應專案，因此不應再提供。

## <a name="technical-configuration"></a>技術設定

容器映射必須裝載在私人[Azure Container Registry](https://azure.microsoft.com/services/container-registry/)中。 在 [**技術**設定] 索引標籤上，提供 Azure Container Registry 內容器映射存放庫的參考資訊。

供應專案發佈之後，您的容器映射會複製到特定公用容器登錄中的 Azure Marketplace。 使用您的容器映射的所有要求，都是從 Azure Marketplace 公用容器登錄（而非您的私用）登錄提供。 如需詳細資訊，請參閱[準備您的 Azure 容器技術資產](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)。

### <a name="image-repository-details"></a>映射存放庫詳細資料

在 [**映射存放庫詳細資料**] 索引標籤上提供下列資訊。

**Azure 訂**用帳戶識別碼-提供回報使用量的訂用帳戶識別碼，並為包含您容器映射的 Azure Container Registry 計費服務。 您可以在 Azure 入口網站的 [[訂閱] 頁面](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)上找到此識別碼。

**Azure 資源組名**–提供包含容器映射 Azure Container Registry 的[資源組](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal)名。 資源群組必須可在訂用帳戶識別碼（如上）中存取。 您可以在 [[資源群組](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)] 頁面上的 [Azure 入口網站中找到名稱。

**Azure Container Registry 名稱**–提供具有容器映射的[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro)名稱。 Container registry 必須位於您稍早提供的 Azure 資源群組中。 只包含登錄名稱，而非完整登入伺服器名稱。 請務必從名稱中省略**azurecr.io** 。 您可以在 [Azure 入口網站的 [容器登錄][頁面](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)上找到登錄名稱。

**Azure Container Registry 的系統管理員使用者名稱**–提供與具有您的容器映射的 Azure Container Registry 連結的[管理員使用者名稱](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)。 需要使用者名稱和密碼，以確保您的公司有權存取登錄。 若要取得管理員使用者名稱和密碼，請使用 Azure 命令列介面（CLI），將 [**已啟用系統管理員**] 屬性設為 [ **True** ]。 您可以選擇性地將 [**管理使用者**] 設定為 [在 Azure 入口網站中**啟用**]。

 :::image type="content" source="media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="說明 [更新容器登錄] 對話方塊。":::

**Azure Container Registry 的密碼**–提供與 Azure Container Registry 相關聯之系統管理員使用者名稱的密碼，並擁有您的容器映射。 需要使用者名稱和密碼，以確保您的公司有權存取登錄。 您可以移至 [**容器** > ] [登錄] [**存取金鑰**]，或使用 [顯示][命令](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show)Azure CLI，從 Azure 入口網站取得密碼。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="說明 [存取金鑰] 功能表。":::

**Azure Container Registry 中的存放庫名稱**。 提供具有您映射的 Azure Container Registry 存放庫名稱。 將映射推送至登錄時，請包含存放庫的名稱。 您可以前往 [[容器](https://azure.microsoft.com/services/container-registry/) > 登錄**庫**] 頁面來尋找存放庫的名稱。 如需詳細資訊，請參閱[Azure 入口網站中的 View container registry 存放庫](https://docs.microsoft.com/azure/container-registry/container-registry-repositories)。

> [!NOTE]
> 設定名稱之後，即無法變更。 請為您帳戶中的每個供應專案使用唯一的名稱。

### <a name="image-tags-for-new-versions-of-your-offer"></a>供應專案新版本的映射標記

當您發行更新時，客戶必須能夠從 Azure Marketplace 自動取得更新。 如果他們不想要更新，他們必須能夠停留在特定版本的映射上。 您可以在每次對映射進行更新時新增影像標記來執行此動作。

### <a name="image-tag"></a>映像標籤

此欄位必須包含**最新**的標記，以指向所有支援平臺上的最新映射版本。 它也必須包含版本戳記（例如，從 xx. xx. x 開始，其中 xx 是數位）。 客戶應該使用[資訊清單標記](https://github.com/estesp/manifest-tool)來以多個平臺為目標。 也請務必新增資訊清單標籤參考的所有標籤，以便上傳。

所有資訊清單標記（最新標記除外）的開頭必須是 X. **-** y 或 x. z，其中 x、y 和 z 是整數。 例如，如果**最新**的標記指向 1.0.1-linux-x64、1.0.1-linux-arm32 和 1.0.1-windows arm32，則必須將這六個標籤新增到此欄位中。 如需詳細資訊，請參閱[準備您的 Azure 容器技術資產](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)。

> [!NOTE]
> 請記得將測試標籤新增到您的映像，以便您在測試期間識別該映像。

## <a name="review-and-publish"></a>審查和發行

當您完成供應專案的所有必要區段之後，您可以提交它來進行審查和發佈。

在入口網站的右上角，選取 [**審查並****發佈**]。

在 [審查] 頁面上，您可以：

- 請參閱供應專案每個區段的完成狀態。 在供應專案的所有區段都標示為完成之前，您無法發行。
  - **未啟動**–尚未啟動，必須完成。
  - **不完整**–具有必須修正的錯誤，或需要您提供詳細資訊。 如需協助，請參閱本檔稍早的章節。
  - **Complete** –包含所有必要的資料，而不會發生任何錯誤。 供應專案的所有區段都必須完成，您才能提交供應專案。
- 將測試指示提供給認證小組，以確保您的供應專案已正確測試。 此外，請提供有助於瞭解您的供應專案的任何補充附注。

若要提交供應專案以供發佈，請選取 [**發佈**]。

我們會傳送一封電子郵件給您，讓您知道供應專案的預覽版本何時可供審查和核准。

若要將您的供應專案發佈到公開（或如果私人供應專案為私人物件），請移至 [合作夥伴中心]，然後選取 [**上線**]。

## <a name="next-step"></a>後續步驟

- [更新商業市集中的現有供應項目](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
