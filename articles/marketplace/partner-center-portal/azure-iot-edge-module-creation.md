---
title: 在 Azure Marketplace 中建立合作夥伴中心的 Azure IoT Edge 模組供應專案
description: 瞭解如何使用合作夥伴中心在 Azure Marketplace 中建立、設定及發佈 IoT Edge 模組供應專案
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: d69090eb07159c2c188c54499a167f127269df24
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857663"
---
# <a name="create-configure-and-publish-an-iot-edge-module-offer-in-azure-marketplace"></a>在 Azure Marketplace 中建立、設定及發佈 IoT Edge 模組供應專案

本文說明如何建立及發佈適用于 Azure Marketplace 的物聯網（IoT） Edge 模組供應專案。 開始之前，請先[在合作夥伴中心建立商業 Marketplace 帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)（如果您尚未這麼做）。 請確定您的帳戶已在商業 marketplace 方案中註冊。

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左側導覽功能表中，選取 [**商用 Marketplace** > **總覽**]。
3. 在 [總覽] 頁面上，選取 [ **+ 新增供應** > 專案]**IoT Edge 模組**]。

    ![說明左側導覽功能表。](./media/new-offer-iot-edge.png)

> [!IMPORTANT]
> 發行供應專案之後，在合作夥伴中心對其進行的編輯，只會在重新發佈供應專案後出現在店面中。 請務必在進行變更之後，一律重新發佈。

### <a name="offer-id-and-alias"></a>供應專案識別碼和別名

輸入**供應專案識別碼**。 這是您帳戶中每個供應專案的唯一識別碼。

- Marketplace 供應專案和 Azure Resource Manager 範本的網址中，客戶可以看到此識別碼（如果適用的話）。
- 請一律使用小寫字母和數字。 它可以包含連字號和底線，但不能有空格，而且限制為50個字元。 例如，如果您輸入 [**測試-供應專案-1**]，則供應專案 web `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`位址會是。
- 選取 [**建立**] 之後，即無法變更供應專案識別碼。

輸入**供應專案別名**。 這是在合作夥伴中心使用供應專案的名稱。

- 此名稱不會在 marketplace 中使用，而且與向客戶顯示的供應專案名稱和其他值不同。
- 選取 [**建立**] 之後就無法變更。

選取 [**建立**] 以產生供應專案並繼續。

## <a name="offer-overview"></a>供應項目概觀

[**供應專案總覽**] 頁面會顯示發佈此供應專案所需步驟的視覺標記法（已完成和即將推出），以及每個步驟應完成幾段時間。

此頁面包含根據您所做的選擇，在此供應專案上執行作業的連結。 例如：

- 如果供應專案為草稿-[刪除草稿供應](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)專案
- 如果供應專案已上線，則會[停止銷售供應](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)專案
- 如果供應專案處於預覽[狀態-上線](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 如果您尚未完成發行者登出，請[取消發佈。](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>供應專案設定

請遵循下列步驟來設定您的供應專案。

### <a name="connect-lead-management"></a>連接潛在客戶管理

當您使用合作夥伴中心將您的供應專案發佈至 marketplace 時，您可以選擇性地將它連接到您的客戶關係管理（CRM）系統。 這可讓您在有人對您的產品感興趣或使用時，立即收到客戶連絡人資訊。

1. 選取您要我們傳送潛在客戶至其中的潛在客戶目的地。 合作夥伴中心支援下列 CRM 系統：

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
- [常見潛在客戶設定錯誤](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#publishing-config-errors)
- [潛在客戶管理總覽](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)PDF （請確定您的快顯封鎖程式已關閉）。

選取 [**儲存草稿**] 再繼續。

### <a name="properties"></a>屬性

此頁面可讓您定義用來在 marketplace 上將供應專案分組的類別，以及支援供應專案的法律合約。

#### <a name="category"></a>類別

選取最少1個，最多五個類別。 這些類別是用來將您的供應專案放入適當的 marketplace 搜尋區域，並顯示在您的供應專案詳細資料頁面上。 在 [供應專案描述] 中，說明您的供應專案如何支援這些類別。 在 [流覽] 頁面中，所有 IoT Edge 模組都會顯示在 [ **物聯網 > IoT Edge 模組** ] 類別之下。

#### <a name="legal"></a>法律

您必須提供供應專案的條款及條件。 您有兩個選擇：

- 使用 Microsoft 商業 Marketplace 的標準合約。
- 提供您自己的條款及條件。

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 商業 Marketplace 的標準合約

我們提供標準合約範本，協助促進商業 marketplace 中的交易。 您可以選擇在標準合約下提供您的解決方案，而客戶只需要檢查並接受一次。 如果您不想要製作自訂的條款及條件，這是個不錯的選項。

若要深入瞭解標準合約，請參閱[Microsoft 商業 Marketplace 的標準合約](https://docs.microsoft.com/azure/marketplace/standard-contract)。 您也可以下載[標準合約](https://go.microsoft.com/fwlink/?linkid=2041178)PDF （請確定您的快顯封鎖程式已關閉）。

若要使用標準合約，請選取 [**使用 Microsoft 商用 marketplace 的標準合約**] 核取方塊，然後按一下 [**接受**]。

> [!NOTE]
> 當您使用 Microsoft 商業 marketplace 的標準合約發佈供應專案之後，就無法使用自己的自訂條款及條件。 請根據標準合約或您自己的條款及條件來提供您的解決方案。

![說明如何使用 Microsoft 的商用 marketplace 的標準合約核取方塊。](./media/iot-edge-module-creation/iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>您自己的條款及條件

若要提供您自己的自訂條款及條件，請在 [**條款及條件**] 方塊中輸入。 您可以在此方塊中輸入不限數量的文字字元。 客戶必須接受這些條款，才能試用您的供應專案。

選取 [**儲存草稿**]，然後繼續進行下一節的供應專案清單。

## <a name="offer-listing"></a>供應專案清單

在這裡，您將定義在 marketplace 中顯示的供應專案詳細資料。 這包括供應專案名稱、描述、影像等等。 設定此供應專案時，請務必遵循 Microsoft 原則頁面上詳述的原則。

> [!NOTE]
> 如果供應專案描述的開頭是片語「，此應用程式僅適用于 [非英文語言]，則不一定要有英文版的供應專案詳細資料。 也可以提供有用的連結，以不同于供應專案清單詳細資料所使用的語言來提供內容。

### <a name="name"></a>名稱

您在此處輸入的名稱會顯示為供應專案的標題。 當您建立供應專案時，此欄位會預先填入您在 [**供應專案別名**] 方塊中輸入的文字。 您可以稍後變更此名稱。

名稱：

- 可以是商標（您也可以包含商標或著作權符號）。
- 長度不能超過50個字元。
- 不能包含 emoji。

### <a name="search-results-summary"></a>搜尋結果摘要

提供供應專案的簡短描述。 最多可有100個字元，並用於 marketplace 搜尋結果。

### <a name="long-summary"></a>完整摘要

提供更詳細的供應專案描述。 最多可有256個字元，並用於 marketplace 搜尋結果。

### <a name="description"></a>描述

提供供應專案的較長描述，最多3000個字元。 這會在 marketplace 清單總覽中向客戶顯示。

在您的描述中包含下列一或多項：

- 您的供應專案提供的價值與主要優點
- 類別或產業關聯，或兩者
- 應用程式內購買機會
- 任何必要的公開

IoT Edge 模組供應專案必須在描述的底部包含最低硬體需求段落。 例如：

*最低硬體需求： Linux x64 和 arm32 作業系統，1 GB RAM，500 Mb 的儲存空間*

以下是撰寫描述的一些秘訣：

- 清楚描述您的供應專案在您的前幾個句子中的價值。 包含下列專案：
    - 供應項目的說明。
    - 受益于供應專案的使用者類型。
    - 客戶需要或發行供應專案位址。
- 請記住，前幾個句子可能會顯示在搜尋結果中。
- 不依賴特性和功能來銷售您的產品。 相反地，請專注于您的供應專案提供的值。
- 請嘗試使用產業專屬詞彙或以權益為基礎的用語。

若要讓您的供應專案**描述**更吸引人，請使用 rtf 編輯器來格式化您的描述。 Rtf 編輯器可讓您新增數位、專案符號、粗體、斜體和縮排，讓您的描述更容易閱讀。

:::image type="content" source="media/text-editor2.png" alt-text="說明 rich 文字編輯器。" border="false":::

- 若要變更內容的格式，請反白顯示您想要格式化的文字，然後選取文字樣式，如下列螢幕擷取畫面所示：

     :::image type="content" source="media/text-editor3.png" alt-text="說明 rtf 文字編輯器中的文字樣式控制項。" border="false":::

- 若要將專案符號或編號清單新增至文字，請使用此螢幕擷取畫面中所示的選項：
  
    :::image type="content" source="media/text-editor4.png" alt-text="說明 rtf 文字編輯器中的專案符號和數位清單控制項。" border="false":::

- 若要新增或移除文字的縮排，請使用此螢幕擷取畫面中所示的選項：

    :::image type="content" source="media/text-editor5.png" alt-text="說明 rtf 文字編輯器中的縮排控制項。" border="false":::

#### <a name="privacy-policy-url"></a>隱私權原則 URL

輸入貴組織的隱私權原則網址。 您必須負責確保您的供應專案符合隱私權法規。 您也必須負責在您的網站上張貼有效的隱私權原則。

#### <a name="useful-links"></a>實用的連結

提供有關您供應專案的補充線上檔。 您最多可以新增25個連結。 若要新增連結，請選取 [ **+ 新增連結**]，然後完成下欄欄位：

- **標題**-客戶會在供應專案的詳細資料頁面上看到標題。
- **連結（URL）** -輸入客戶的連結，以查看您的線上檔。 此連結的開頭必須是 HTTP://或 HTTPs://。

請務必將至少一個連結新增至您的檔，並從 [Azure IoT 裝置目錄](https://catalog.azureiotsolutions.com/)加入一個連結至相容的 IoT Edge 裝置。

### <a name="contact-information"></a>連絡人資訊

您必須提供**支援連絡人**的名稱、電子郵件和電話號碼，以及**工程連絡人。** 客戶不會看到這則資訊。 可供 Microsoft 使用，並可提供給雲端解決方案提供者（CSP）合作夥伴。

- 支援連絡人（必要）：針對一般支援問題。
- 工程連絡人（必要）：針對技術問題和認證問題。
- CSP 方案連絡人（選擇性）：適用于與 CSP 計畫相關的轉售商問題。

在 [**支援連絡人**] 區段中，提供**支援網站**的網址，合作夥伴可以根據該供應專案是否適用于全域 Azure、Azure Government 或兩者，來尋找您的供應專案支援。

在 [ **Csp 方案連絡人**] 區段中，提供可供 csp 合作夥伴尋找供應專案行銷資料的連結（**Csp 計畫行銷材料**）。

#### <a name="additional-marketplace-listing-resources"></a>其他 marketplace 清單資源

若要深入瞭解如何建立供應專案清單，請參閱[供應專案清單最佳作法](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)。

### <a name="marketplace-images"></a>Marketplace 映射

提供要與您的供應專案搭配使用的標誌和影像。 所有影像都必須是 .png 格式。 模糊影像將會遭到拒絕。

>[!Note]
>如果您在上傳檔案時發生問題，請確定您的區域網路不會https://upload.xboxlive.com封鎖合作夥伴中心所使用的服務。

#### <a name="store-logos"></a>Microsoft Store標誌。

在下列四個圖元大小中，提供您供應專案標誌的 .png 檔案：

- **小型（48 x 48）**
- **中（90 x 90）**
- **大型（216 x 216）**
- **寬（255 x 115）**

這四個標誌都是必要的，並且用於 marketplace 清單中的不同位置。

#### <a name="screenshots-optional"></a>螢幕擷取畫面（選擇性）

新增最多五個螢幕擷取畫面，以顯示供應專案的運作方式。 每個都必須是 1280 x 720 圖元大小和 .png 格式。

#### <a name="videos-optional"></a>影片（選擇性）

新增最多五個示範您供應專案的影片。 以 1280 x 720 圖元的大小，輸入影片的名稱、其網址和影片的 png 影像。

#### <a name="offer-examples"></a>供應項目範例

下列範例顯示供應專案清單欄位如何出現在供應專案的不同位置。

這個螢幕擷取畫面顯示 Azure Marketplace 中的**供應專案清單**頁面。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-offer-listing-page.png" alt-text="說明 Azure Marketplace 中的供應專案清單頁面。":::

此螢幕擷取畫面顯示 Azure Marketplace 中的搜尋結果：

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-search-results.png" alt-text="說明 Azure Marketplace 中的搜尋結果。":::

此螢幕擷取畫面顯示 Azure 入口網站中的**供應專案清單**頁面。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-listing-page-azure-portal.png" alt-text="說明 Azure 入口網站中的供應專案清單頁面。":::

此螢幕擷取畫面顯示 Azure 入口網站中的搜尋結果。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-search-results.png" alt-text="說明 Azure 入口網站中的供應專案清單頁面。":::

選取 [**儲存草稿**]，然後繼續進行下一節 [預覽]。

## <a name="preview"></a>預覽

在 [**預覽]** 索引標籤上，您可以選擇有限的**預覽物件**來驗證您的供應專案，再將其即時發佈到更廣泛的 marketplace 物件。

> [!IMPORTANT]
> 在預覽中觀看供應專案之後，您必須選取 [**上線**]，將您的供應專案發佈至公用。

使用 Azure 訂用帳戶識別碼 Guid 指定您的預覽物件，以及每個使用者的選擇性描述。 客戶無法看到這兩個欄位。

> [!NOTE]
> 您可以在 Azure 入口網站的 [訂用帳戶] 頁面上找到您的 Azure 訂用帳戶識別碼。

新增至少一個 Azure 訂用帳戶識別碼，分別是（最多10個）或上傳 CSV 檔案（最多100個）。 藉由新增這些訂用帳戶識別碼，您可以定義誰可以在現場發佈之前預覽您的供應專案。 如果您的供應專案已上線，您可以定義預覽物件，以測試您的供應專案變更或更新。

> [!NOTE]
> 預覽物件與私用物件不同。 **預覽**物件可以在所有供應專案在 marketplace 中上線之前查看並加以確認，包括只會發佈給**私人**物件的方案（在 [可用性] 索引標籤上設定）。

選取 [**儲存草稿**]，然後繼續進行下一節 [計畫總覽]。

### <a name="plan-overview"></a>計畫總覽

此索引標籤可讓您在合作夥伴中心的相同供應專案中提供不同的方案選項。 這些方案先前稱為 Sku 或庫存單位。 方案可能會因可用的雲端而有所不同，例如全域雲端、政府雲端，以及方案所參考的影像。 若要在 marketplace 中列出您的供應專案，您至少必須設定一個方案。

建立計畫之後，[**計畫總覽**] 索引標籤會顯示：

- 計畫名稱
- 定價模式
- 雲端可用性（全球或政府）
- 目前的發行狀態
- 任何可用的動作

計畫總覽中的可用動作會根據方案的目前狀態而有所不同。 其中包括：

- **刪除草稿**：如果計畫狀態為草稿。
- **停止銷售方案**：如果計畫狀態是 [即時發行]。

#### <a name="create-new-plan"></a>建立新的方案

選取 [**建立新方案**]。 [**新增方案**] 對話方塊隨即出現。

在 [**方案識別碼**] 方塊中，為此供應專案中的每個方案建立唯一的方案識別碼。 此識別碼將會顯示在產品網址中的客戶。 僅使用小寫字母和數位、連字號或底線，以及最多50個字元。

在 [**方案名稱**] 方塊中，輸入此計畫的名稱。 客戶在決定要在您的供應專案內選取哪一個方案時，會看到此名稱。 為此供應專案中的每個方案建立唯一的名稱。 例如，您可以使用**Windows server**的供應專案名稱，並規劃**windows Server 2016**和**windows server 2019**。

> [!NOTE]
> 選取 [**建立**] 之後，就無法變更方案識別碼。

選取 [建立]  。

### <a name="plan-setup"></a>規劃設定

此索引標籤可讓您設定可使用方案的雲端。 您在此索引標籤上的答案會影響哪些欄位會顯示在其他索引標籤上。

#### <a name="cloud-availability"></a>雲端可用性

您的方案至少必須要有一個雲端使用 Azure IoT 中樞。

選取 [ **Azure 全域**] 選項，讓所有使用 marketplace 的全球 Azure 區域中的客戶都能使用您的方案。 如需詳細資訊，請參閱[地理可用性和貨幣支援](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)。

選取 [ [Azure Government 雲端](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)] 選項，讓您的解決方案顯示在這裡。 這是一家政府社區雲端，針對美國聯邦、州和地方或部落政府機關的客戶，以及符合其服務資格的合作夥伴提供控制存取。 身為發行者，您必須負責此雲端社區的任何合規性控制、安全性措施和最佳作法。 Azure Government 會使用實際隔離的資料中心和網路（僅限美國地區）。 [發行](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)至 Azure Government 之前，請在該區域內測試並確認您的解決方案，因為結果可能會不同。 若要暫存並測試您的解決方案，請從[Microsoft Azure Government 試用](https://azure.microsoft.com/global-infrastructure/government/request/)版要求試用帳戶。

> [!NOTE]
> 在您的方案發佈且可在特定雲端中使用之後，您就無法移除該雲端。

#### <a name="azure-government-cloud-certifications"></a>Azure Government 雲端認證

只有在 [**雲端可用性**] 下選取 [ **Azure Government 雲端**] 時，才會顯示此選項。

Azure Government 服務會處理受限於特定政府法規和需求的資料。 例如，FedRAMP、NIST 800.171 （DIB）、ITAR、IRS 1075、DoD L4 和 CJIS。 若要讓您知道這些程式的認證，您可以提供最多100個連結來描述您的認證。 這些可以直接連結到您在程式上的清單或您自己的網站。 只有 Azure Government 客戶可以看到這些連結。

## <a name="plan-listing"></a>計畫清單

此索引標籤會顯示相同供應專案中每個不同方案的特定資訊。

### <a name="plan-name"></a>計畫名稱

這會預先填入您在建立方案時提供給您的計畫名稱。 您可以視需要變更此名稱。 長度最多可達50個字元。 這個名稱在 Azure Marketplace 和 Azure 入口網站中會顯示為此計畫的標題。 在準備好使用計畫之後，它會用來做為預設的模組名稱。

### <a name="plan-summary"></a>計畫摘要

提供您方案的簡短摘要（不是供應專案）。 此摘要會出現在 Azure Marketplace 搜尋結果中，而且最多可包含100個字元。

### <a name="plan-description"></a>計畫描述

描述讓此計畫獨一無二的專案，以及供應專案內方案之間的差異。 請不要描述供應專案，只是計畫。 此描述會出現在 [Azure Marketplace] 和 [供應專案清單] 頁面上的 [Azure 入口網站中。 它可以是您在計畫摘要中提供的相同內容，且最多可包含2000個字元。

完成這些欄位之後，請選取 [**儲存草稿**]。

#### <a name="plan-examples"></a>計劃範例

下列範例顯示計畫清單欄位如何出現在不同的視圖中。

這些是查看方案詳細資料時 Azure Marketplace 中的欄位：

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-plan-details.png" alt-text="說明在 Azure Marketplace 中查看計畫詳細資料時所看到的欄位。":::

這些是 Azure 入口網站的計畫詳細資料：

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-plan-details.png" alt-text="說明 Azure 入口網站的方案詳細資料。":::

## <a name="availability"></a>可用性

如果您想要隱藏已發佈的供應專案，讓客戶無法在 marketplace 中搜尋、流覽或購買，請選取 [可用性] 索引標籤上的 [**隱藏方案**] 核取方塊。

此欄位通常是在下列情況下使用：

- 此供應專案僅供在另一個應用程式參考時間接使用。
- 供應專案不應個別購買。
- 此計畫用於初始測試，不再相關。
- 此計畫用於暫時或季節性的供應專案，因此不應再提供。

## <a name="technical-configuration"></a>技術設定

**IoT Edge 模組**供應專案類型是在 IoT Edge 裝置上執行的特定容器類型。 在 [**技術**設定] 索引標籤上，您將會在[Azure Container Registry](https://azure.microsoft.com/services/container-registry/)內提供容器映射存放庫的參考資訊，以及讓客戶輕鬆地使用模組的設定。

發佈供應專案之後，您的 IoT Edge 容器映射會複製到特定公用容器登錄中的 Azure Marketplace。 來自 Azure 使用者以使用模組的所有要求，都是從 Azure Marketplace 公用容器登錄（而不是您的私人容器登錄）提供。

您可以將目標設為多個平臺，並使用標記提供數個版本的模組容器映射。 若要深入瞭解標記和版本設定，請參閱[準備您的 IoT Edge 模組技術資產](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-iot-edge-module-asset)。

### <a name="image-repository-details"></a>映射存放庫詳細資料

您會在 [**映射存放庫詳細資料**] 索引標籤上提供下列資訊。

**選取 [映射來源**]：選取 [ **Azure Container Registry** ] 選項。

**Azure 訂**用帳戶識別碼：提供回報資源使用量的訂用帳戶識別碼，並為包含您容器映射的 Azure Container Registry 支付服務費用。 您可以在 Azure 入口網站的 [[訂閱] 頁面](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)上找到此識別碼。

**Azure 資源組名**：提供包含容器映射 Azure Container Registry 的[資源組](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal)名。 資源群組必須可在訂用帳戶識別碼（如上）中存取。 您可以在 [[資源群組](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)] 頁面上的 [Azure 入口網站中找到名稱。

**Azure container registry 名稱**：提供具有您容器映射的[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro)名稱。 容器登錄必須存在於您稍早提供的 Azure 資源群組中。 僅提供登錄名稱，而不是完整登入伺服器名稱。 請務必從名稱中省略**azurecr.io** 。 您可以在 [Azure 入口網站的 [容器登錄][頁面](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)上找到登錄名稱。

**Azure Container Registry 的系統管理員使用者名稱**：提供與具有容器映射的 Azure Container Registry 相關聯的系統[管理員使用者名稱](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)。 需要使用者名稱和密碼，以確保您的公司有權存取登錄。 若要取得管理員使用者名稱和密碼，請使用 Azure 命令列介面（CLI），將 [**已啟用系統管理員**] 屬性設為 [ **True** ]。 您可以選擇性地將 [**管理使用者**] 設定為 [在 Azure 入口網站中**啟用**]。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-admin-user.png" alt-text="說明 [更新容器登錄] 對話方塊。":::

**Azure Container Registry 的密碼**：提供與 Azure Container Registry 相關聯之系統管理員使用者名稱的密碼，並擁有您的容器映射。 需要使用者名稱和密碼，以確保您的公司有權存取登錄。 您可以移至 [**容器** > ] [登錄] [**存取金鑰**]，或使用 [顯示][命令](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show)Azure CLI，從 Azure 入口網站取得密碼。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-username-password.png" alt-text="說明 Azure 入口網站中的 [存取金鑰] 畫面。":::

**Azure Container Registry 中的存放庫名稱**。 提供具有您映射的 Azure Container Registry 存放庫名稱。 當您將映射推送至登錄時，可以指定存放庫的名稱。 您可以前往 [[容器](https://azure.microsoft.com/services/container-registry/) > 登錄**庫] 頁面**來尋找存放庫的名稱。 如需詳細資訊，請參閱[Azure 入口網站中的 View container registry 存放庫](https://docs.microsoft.com/azure/container-registry/container-registry-repositories)。 請注意，設定名稱之後，即無法變更。 請為您帳戶中的每個供應專案使用唯一的名稱。

### <a name="image-tags-for-new-versions-of-your-offer"></a>供應專案新版本的映射標記

當您發行更新時，客戶必須能夠從 Azure Marketplace 自動取得更新。 如果他們不想要更新，他們必須能夠停留在特定版本的映射上。 您可以在每次對映射進行更新時新增影像標記來執行此動作。

**影像標記**。 此欄位必須包含**最新**的標記，以指向所有支援平臺上的最新映射版本。 它也必須包含版本戳記（例如，從 xx. xx. x 開始，其中 xx 是數位）。 客戶應該使用[資訊清單標記](https://github.com/estesp/manifest-tool)來以多個平臺為目標。 也請務必新增資訊清單標籤參考的所有標籤，以便上傳。 所有資訊清單標記（最新標記除外）的開頭必須是 X. Y-或 X. Z，其中 X、Y 和 Z 是整數。 例如，如果最新的標記指向 1.0.1-linux-x64、1.0.1-linux-arm32 和 1.0.1-windows arm32，則必須將這六個標籤新增到此欄位中。 如需標記和版本設定的詳細資訊，請參閱[準備您的 IoT Edge 模組技術資產。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets)

### <a name="default-deployment-settings-optional"></a>預設部署設定（選擇性）

請定義您的 IoT Edge 模組最常用的部署設定。 讓客戶能以這些預設設定來啟動您的 IoT Edge 模組，以優化客戶部署。

**預設路由**。 IoT Edge 中樞會管理模組、IoT 中樞和裝置之間的通訊。 您可以在模組與 IoT 中樞之間設定資料輸入和輸出的路由，讓您有彈性地將訊息傳送至需要的位置，而不需要額外的服務來處理訊息或撰寫額外的程式碼。 路由是使用名稱/值配對來建立。 您最多可以定義五個預設路由名稱，每個長度最多512個字元。

請務必在您的路由值中使用正確的[路由語法](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)（通常定義為 FROM/message/*，以 $upstream）。 這表示任何模組所傳送的任何訊息都會移至您的 IoT 中樞。 若要參考您的模組，請使用其預設的模組名稱，這會是您的**供應專案名稱**，不含空格或特殊字元。 若要參考其他尚不知道的模組，請使用 <FROM_MODULE_NAME> 慣例，讓您的客戶知道他們需要更新此資訊。 如需 IoT Edge 路由的詳細資訊，請參閱宣告[路由](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)。

例如，如果模組 ContosoModule 在 ContosoOutput 上接聽 ContosoInput 和輸出資料的輸入，則定義下列兩個預設路由是合理的：

- 名稱 #1： ToContosoModule
- 值 #1：從/messages/modules/<FROM_MODULE_NAME>/outputs/* 轉換為 BrokeredEndpoint （"/modules/ContosoModule/inputs/ContosoInput"）
- 名稱 #2： FromContosoModuleToCloud
- 值 #2：從/messages/modules/ContonsoModule/outputs/ContosoOutput 到 $upstream

**預設模組對應項所需屬性**。 模組對應項是 IoT 中樞中的 JSON 檔，可儲存模組實例的狀態資訊，包括所需的屬性。 所需屬性會與報告屬性一起使用，以同步處理模組設定或狀況。 解決方案後端可以設定所需的屬性，而模組可以讀取它們。 此模組也可以接收所需屬性中的變更通知。 所需的屬性會使用最多五個名稱/值組來建立，而且每個預設值都必須小於512個字元。 您可以定義最多五個名稱/值對應項所需的屬性。 對應項所需屬性的值必須是有效的 JSON、非轉義的，而不含具有四個層級之最大嵌套階層的陣列。 在預設值所需的參數不合理的情況下（例如客戶服務器的 IP 位址），您可以將參數新增為預設值。 若要深入瞭解對應項所需屬性的詳細資訊，請參閱[定義或更新所需屬性](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties)。

例如，如果模組支援使用對應項所需屬性可動態設定的重新整理頻率，則定義下列預設對應項所需屬性是合理的：

- 名稱 #1： RefreshRate
- 值 #1：60

**預設的環境變數**。 環境變數會提供補充資訊給協助設定程式的模組。 環境變數是使用名稱/值組所建立。 每個預設環境變數名稱和值都必須小於512個字元，而且您最多可以定義五個。 當預設值所需的參數沒有意義時（例如客戶服務器的 IP 位址），您可以將參數新增為預設值。

例如，如果模組在啟動之前需要接受使用條款，您可以定義以下環境變數：

- 名稱 #1： ACCEPT_EULA
- 值 #1： Y

**預設容器建立選項**。 容器建立選項會引導您建立 IoT Edge 模組的 Docker 容器。 IoT Edge 支援 Docker 引擎 API 建立容器選項。 請參閱[清單容器](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList)中的所有選項。 [建立選項] 欄位必須是有效的 JSON、非轉義且小於512個字元。

例如，如果模組需要端口系結，請定義下列建立選項：

"HostConfig"： {"即可"： {"5012/tcp"： [{"HostPort"： "5012"}]}

## <a name="review-and-publish"></a>審查和發行

當您完成供應專案的所有必要區段之後，您可以提交它來進行審查和發佈。

在入口網站的右上角，選取 [**審查併發布**]。

在 [審查] 頁面上，您可以看到發行狀態：

- 請參閱供應專案每個區段的完成狀態。 在供應專案的所有區段都標示為完成之前，您無法發行。
    - **未啟動**-區段尚未啟動，需要完成。
    - **不完整**-區段包含需要修正的錯誤，或需要您提供詳細資訊。 如需指引，請參閱本檔稍早的章節。
    - **Complete** -區段包含所有必要的資料，而且沒有任何錯誤。 供應專案的所有區段都必須完成，您才能提交供應專案。
- 將測試指示提供給認證小組，以確保您的供應專案已正確測試。 此外，請提供有助於瞭解您的供應專案的任何補充附注。

若要提交供應專案以供發佈，請選取 [**發佈**]。

我們會傳送一封電子郵件給您，讓您知道供應專案的預覽版本何時可供審查和核准。 若要將您的供應專案發佈到公開（或如果私人供應專案為私人物件），請移至 [合作夥伴中心]，然後選取 [**上線**]。

## <a name="next-steps"></a>後續步驟

- [更新商用 marketplace 中的現有供應專案](https://docs.microsoft.com//azure/marketplace/partner-center-portal/update-existing-offer)