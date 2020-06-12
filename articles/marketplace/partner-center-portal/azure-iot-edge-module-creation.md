---
title: 在 Azure Marketplace 中透過合作夥伴中心建立 Azure IoT Edge 模組供應項目
description: 了解如何使用合作夥伴中心在 Azure Marketplace 中建立、設定及發佈 IoT Edge 模組供應項目
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 5798e3c9c2a345c5cf9d8fd254130b4252a696d8
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83847968"
---
# <a name="create-configure-and-publish-an-iot-edge-module-offer-in-azure-marketplace"></a>在 Azure Marketplace 中建立、設定及發佈 IoT Edge 模組供應項目

本文說明如何建立及發佈 Azure Marketplace 的物聯網 (IoT) Edge 模組供應項目。 開始之前，請先[在合作夥伴中心建立商業市集](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) (如果您尚未這麼做)。 請確定您已在商業市集方案中註冊帳戶。

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左側導覽功能表中，選取 [商業市集] > [概觀]。
3. 在 [概觀] 頁面上，選取 [+ 新增供應項目] > [IoT Edge 模組]。

    ![說明左側導覽功能表。](./media/new-offer-iot-edge.png)

> [!IMPORTANT]
> 發佈供應項目之後，在合作夥伴中心對其進行的編輯，只會在重新發佈供應項目之後出現在店面中。 請務必在進行變更之後，一律重新發佈。

### <a name="offer-id-and-alias"></a>供應項目識別碼和別名

輸入**供應項目識別碼**。 這是您帳戶中每個供應項目的唯一識別碼。

- 客戶可以在市集供應項目和 Azure Resource Manager 範本 (如果適用) 的網址中看到此識別碼。
- 請一律使用小寫字母和數字。 其可以包含連字號和底線，但不能有空格，且限制為 50 個字元。 例如，如果您輸入 **test-offer-1**，供應項目網址將為 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
- 選取 [建立] 之後，即無法變更供應項目識別碼。

輸入**供應項目別名**。 這是用於合作夥伴中心內供應項目的名稱。

- 此名稱不會在市集中使用，且與向客戶顯示的供應項目名稱和其他值不同。
- 選取 [建立] 之後，即無法變更此項目。

選取 [建立] 以產生供應項目並繼續。

## <a name="offer-overview"></a>供應項目概觀

[供應項目概觀] 頁面會顯示發佈此供應項目 (已完成和即將推出) 所需步驟的視覺表示法，以及完成每個步驟所需的時間。

此頁面包含根據您所做的選擇，在此供應項目上執行作業的連結。 例如：

- 如果供應項目為草稿 - [刪除草稿供應項目](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- 如果供應項目已上線 - [停止銷售供應項目](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- 如果供應專案處於預覽階段 - [上線](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 如果您尚未完成發行者登出 - [取消發行。](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>供應項目設定

請遵循下列步驟來設定供應項目。

### <a name="customer-leads"></a>潛在客戶

當您使用合作夥伴中心將供應項目發佈到市集時，您可以將其連線到客戶關係管理 (CRM) 系統。 這可讓您在有人對您的產品感興趣或使用時，立即收到客戶連絡人資訊。

1. 選取您要我們傳送潛在客戶至其中的潛在客戶目的地。 合作夥伴中心支援下列 CRM 系統：

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) for Customer Engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > 如果以上未列出您的 CRM 系統，請使用 [Azure 資料表](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)或 [Https 端點](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)來儲存客戶潛在客戶資料，然後將資料匯出至您的 CRM 系統。

2. 在合作夥伴中心內發佈時，將供應項目連線到潛在客戶目的地。
3. 確認已正確設定與潛在客戶目的地的連線。 在合作夥伴中心發佈之後，我們會驗證連線並將測試潛在客戶傳送給您。 在上架前預覽供應項目時，您也可以嘗試在預覽環境中自行購買供應項目，藉此測試您的潛在客戶連線。
4. 確認潛在客戶目的地的連線保持在最新狀態，您就不會遺失任何潛在客戶。

以下是一些額外的潛在客戶管理資源：

- [潛在客戶管理概觀](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [潛在客戶管理常見問題集](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [常見潛在客戶設定錯誤](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#publishing-config-errors)
- [潛在客戶管理概觀](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (請確定您已關閉快顯封鎖程式)。

選取 [儲存草稿] 後再繼續。

### <a name="properties"></a>屬性

此頁面可讓您定義用來在市集上將供應專案分組的類別，以及支援供應項目的法律合約。

#### <a name="category"></a>類別

選取最少一個，最多五個類別。 這些類別是用來將您的供應項目放入適當的市集搜尋區域，並顯示在您的供應項目詳細資料頁面上。 在供應項目描述中，說明您的供應項目如何支援這些類別。 在瀏覽頁面中，所有 IoT Edge 模組都會顯示在[物聯網 > IoT Edge 模組] ****   類別下。

#### <a name="legal"></a>法律

您必須提供供應項目的條款及條件。 您有兩個選擇：

- 使用 Microsoft 商業市集的標準合約。
- 提供您自己的條款及條件。

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 商業市集的標準合約

我們提供標準合約範本，協助促進商業市集中的交易。 您可以選擇在標準合約下提供您的解決方案，而客戶只需要檢查並接受一次。 如果您不想要製作自訂的條款及條件，這是個不錯的選項。

若要深入了解標準合約，請參閱 [Microsoft 商業市集的標準合約](https://docs.microsoft.com/azure/marketplace/standard-contract)。 您也可以下載[標準合約](https://go.microsoft.com/fwlink/?linkid=2041178) PDF (請確定您的快顯封鎖程式已關閉)。

若要使用標準合約，請選取 [使用 Microsoft 商業市集的標準合約] 核取方塊，然後按一下 [接受]。

> [!NOTE]
> 當您使用 Microsoft 商業市集的標準合約發佈供應項目之後，就無法使用自己的自訂條款及條件。 請根據標準合約或您自己的條款及條件來提供您的解決方案。

![說明如何使用 Microsoft 商業市集的標準合約核取方塊。](./media/iot-edge-module-creation/iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>您自己的條款及條件

若要提供您自己的自訂條款及條件，請在 [條款及條件] 方塊中加以輸入。 您可以在此方塊中輸入不限數量的文字字元。 客戶必須先接受這些條款，才能試用您的供應項目。

選取 [儲存草稿]，再繼續進行下一節的供應項目清單。

## <a name="offer-listing"></a>供應項目清單

您將在這裡定義顯示於市集中的供應項目詳細資料。 這包括供應項目名稱、描述、映像等等。 設定此供應項目時，請務必遵循 Microsoft 原則頁面上詳述的原則。

> [!NOTE]
> 如果供應項目描述開頭為以下詞句：「此應用程式僅適用於 [非英文語言]。」，則供應項目詳細資料不需要為英文。 也可利用不同於供應項目清單詳細資料中所使用的語言，來提供供應項目內容的有用連結。

### <a name="name"></a>名稱

您在此處輸入的名稱會顯示為您供應項目的標題。 當您建立供應項目時，此欄位會預先填入您在 [供應項目別名] 方塊中輸入的文字。 您稍後可以變更此名稱。

名稱：

- 可以是商標 (您也可以包含商標或著作權符號)。
- 長度不可超過 50 個字元。
- 不可包含表情圖示。

### <a name="search-results-summary"></a>搜尋結果摘要

提供供應項目的簡短描述。 這長度最多可有 100 個字元，並用於市集搜尋結果。

### <a name="long-summary"></a>完整摘要

請提供您供應項目的詳細描述。 這長度最多可有 256 個字元，並用於市集搜尋結果。

### <a name="description"></a>描述

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

IoT Edge 模組供應項目必須在描述的底部包含最低硬體需求段落，例如：

- 最低硬體需求： Linux x64 和 arm32 OS，1 GB 的 RAM，500 Mb 的儲存空間

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-url"></a>隱私權原則 URL

輸入貴組織隱私權原則的網址。 您必須負責確保您的供應項目符合隱私權法律及規定。 您也必須負責在您的網站上張貼有效的隱私權原則。

#### <a name="useful-links"></a>實用的連結

提供有關您供應項目的補充線上文件。 您最多可以新增 25 個連結。 若要新增連結，請選取 [+ 新增連結] 然後完成下欄欄位：

- **標題** - 客戶會在供應項目的詳細資料頁面上看到標題。
- **連結 (URL)** - 輸入客戶的連結，以檢視您的線上文件。 連結的開頭必須是 http:// 或 https://。

請務必從  [Azure IoT 裝置目錄](https://catalog.azureiotsolutions.com/)新增至少一個說明文件的連結和一個相容 IoT Edge 裝置的連結。

### <a name="contact-information"></a>連絡人資訊

您必須提供 [支援連絡人] 與 [工程連絡人] 的姓名、電子郵件和電話號碼。 客戶不會看到這則資訊。 可供 Microsoft 使用，並可提供給雲端解決方案提供者 (CSP) 合作夥伴。

- 支援連絡人 (必要)：針對一般支援問題。
- 工程連絡人 (必要)：針對技術問題和認證問題。
- CSP 方案連絡人 (選擇性)：針對與 CSP 方案相關的轉銷商問題。

在 [支援連絡人] 區段中，提供 [支援網站] 的網址，合作夥伴可在其中根據該供應項目是否適用於全域 Azure 和/或 Azure Government 來尋找您供應項目的支援。

在 [CSP 方案連絡人] 區段中，提供 CSP 合作夥伴可在其中找到您供應項目行銷資料的連結 (**CSP 方案行銷資料**)。

#### <a name="additional-marketplace-listing-resources"></a>其他市集清單資源

若要深入了解如何建立供應項目清單，請參閱[供應項目清單最佳做法](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)。

### <a name="marketplace-images"></a>Marketplace 映像

提供要與您供應項目搭配使用的標誌和映像。 所有映像都必須是 .png 格式。 模糊映像將會遭到拒絕。

>[!Note]
>如果您在上傳檔案時發生問題，請確定您的區域網路不會封鎖合作夥伴中心所使用的 https://upload.xboxlive.com 服務。

#### <a name="store-logos"></a>儲存標誌

請以下列四個像素的個別大小提供供應項目標誌的 .png 檔案：

- **小型 (48 x 48)**
- **中型 (90 x 90)**
- **大型 (216 x 216)**
- **寬 (255 x 115)**

這四個標誌都是必要的，且在市集清單中會用於不同位置。

#### <a name="screenshots-optional"></a>螢幕擷取畫面 (選擇性)

新增最多五個螢幕擷取畫面，以顯示供應項目的運作方式。 每個的大小都必須為 1280 x 720 像素且為 .png 格式。

#### <a name="videos-optional"></a>影片 (選擇性)

新增最多五個示範您供應項目的影片。 輸入影片的名稱、其網址，以及影片的縮圖 .png 映像 (大小為 1280 x 720 像素)。

#### <a name="offer-examples"></a>供應項目範例

下列範例示範供應項目清單欄位如何出現在供應項目的不同位置。

此螢幕擷取畫面會顯示 Azure Marketplace 中的 [供應項目清單] 頁面。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-offer-listing-page.png" alt-text="說明 Azure Marketplace 中的供應項目清單頁面。":::

此螢幕擷取畫面會顯示 Azure Marketplace 中的搜尋結果：

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-search-results.png" alt-text="說明 Azure Marketplace 中的搜尋結果。":::

此螢幕擷取畫面會顯示 Azure 入口網站中的 [供應項目清單] 頁面。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-listing-page-azure-portal.png" alt-text="說明 Azure 入口網站中的供應項目清單頁面。":::

此螢幕擷取畫面會顯示 Azure 入口網站中的搜尋結果。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-search-results.png" alt-text="說明 Azure 入口網站中的供應項目清單頁面。":::

選取 [儲存草稿]，再繼續進行下一節的「預覽」。

## <a name="preview"></a>預覽

在 [預覽] 索引標籤上，您可以選擇有限的 [預覽對象] 來驗證您的供應項目，再將其即時發佈給更廣泛的市集對象。

> [!IMPORTANT]
> 在預覽中檢視供應項目之後，您必須選取 [上線]，以將您的供應項目發佈至公用。

使用 Azure 訂用帳戶識別碼 GUID 指定您的預覽對象，以及每個使用者的選擇性描述。 客戶無法看到這些欄位。

> [!NOTE]
> 您可以在 Azure 入口網站的 [訂用帳戶] 頁面上找到 Azure 訂用帳戶識別碼。

藉由分別 (最多 10 個) 或上傳 CSV 檔案 (最多 100 個)，新增至少一個 Azure 訂用帳戶識別碼。 您可以藉由新增這些訂用帳戶識別碼，定義哪些人員可以在即時發佈之前預覽您的供應項目。 如果您的供應項目已上線，您可以定義預覽對象，以測試您供應項目的變更或更新。

> [!NOTE]
> 預覽對象與私人對象不同。 [預覽] 對象可以在所有供應項目方案於市集上線之前進行查看及確認，包括只會發佈至 [私人] 對象 (在 [可用性] 索引標籤上設定) 的部分。

選取 [儲存草稿]，再繼續進行下一節的「方案概觀」。

### <a name="plan-overview"></a>方案概觀

此索引標籤可讓您在合作夥伴中心的相同供應項目中提供不同的方案選項。 這些方案先前稱為 SKU 或庫存單位。 方案可能會因可用的雲端而有所不同，例如全域雲端、Government 雲端，以及方案所參考的映像。 若要在市集中列出您的供應項目，您至少必須設定一個方案。

建立方案之後，隨即顯示 [方案概觀] 索引標籤：

- 方案名稱
- 定價模式
- 雲端可用性 (全域或 Government)
- 目前的發佈狀態
- 任何可用的動作

方案概觀中的可用動作會根據方案的目前狀態而有所不同。 其中包括：

- **刪除草稿**：如果方案狀態為 [草稿]。
- **停止銷售方案**：如果方案狀態為 [即時發佈]。

#### <a name="create-new-plan"></a>建立新的方案

選取 [建立新方案]。 [新增方案] 對話方塊隨即出現。

在 [方案識別碼] 方塊中，為此供應項目中的每個方案建立唯一的方案識別碼。 客戶將可在產品網址中看到此識別碼。 僅使用小寫字母和數位、連字號或底線，以及最多 50 個字元。

在 [方案名稱] 方塊中，輸入此方案的名稱。 當客戶決定要在您的供應項目內選取哪一個方案時，會看到此名稱。 為此供應項目中的每個方案建立唯一的名稱。 例如，您可以使用 **Windows Server** 的供應項目名稱，以及 **Windows Server 2016** 和 **Windows Server 2019** 的方案。

> [!NOTE]
> 選取 [建立] 之後，即無法變更方案識別碼。

選取 [建立]。

### <a name="plan-setup"></a>方案設定

此索引標籤可讓您設定可使用方案的雲端。 您在此索引標籤上的答案會影響哪些要顯示在其他索引標籤上的欄位。

#### <a name="cloud-availability"></a>雲端可用性

您的方案必須可在至少一個使用 Azure IoT 中樞的雲端中使用。

選取 [Azure 全域] 選項，以便客戶在所有使用市集的全域 Azure 區域中都能使用您的方案。 如需詳細資料，請參閱[地理可用性和貨幣支援](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)。

選取 [Azure Government 雲端](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)選項，讓您的解決方案顯示在這裡。 這是政府社群雲端，U.S. 聯邦政府、州和地方或部落政府機關的客戶，以及符合資格可提供服務的合作夥伴，擁有此雲端的控管權限。 身為發行者，您必須負責此雲端社區的任何合規性控制、安全性措施和最佳做法。 Azure Government 會使用實際隔離的資料中心和網路 (僅限位於美國地區)。 在[發佈](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)至 Azure Government 之前，請先在該區域內測試並確認您的解決方案，因為結果可能不同。 若要暫存並測試您的解決方案，請從 [Microsoft Azure Government 試用版](https://azure.microsoft.com/global-infrastructure/government/request/)要求試用帳戶。

> [!NOTE]
> 在您的方案發佈且可在特定雲端中使用之後，您就無法移除該雲端。

#### <a name="azure-government-cloud-certifications"></a>Azure Government 雲端認證

只有在 [雲端可用性] 底下選取 [Azure Government 雲端] 時，才會顯示此選項。

Azure Government 服務會處理受限於特定政府法規和需求的資料。 例如，FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS。 若要告知這些程式的認證，您可以提供最多 100 個連結來描述您的認證。 這些可以直接連結到您在程式上的清單或連結到您自己的網站。 只有 Azure Government 客戶可以看到這些連結。

## <a name="plan-listing"></a>方案清單

此索引標籤會顯示相同供應項目中每個不同方案的特定資訊。

### <a name="plan-name"></a>方案名稱

這會預先填入您在建立方案時提供給您方案的名稱。 您可以視需要變更此名稱。 該名稱長度最多可達 50 個字元。 這個名稱在 Azure Marketplace 和 Azure 入口網站中會顯示為此方案的標題。 在準備好使用方案之後，其會用來做為預設的模組名稱。

### <a name="plan-summary"></a>方案摘要

提供您方案 (非供應項目) 的簡短摘要。 此摘要會出現在 Azure Marketplace 搜尋結果中，且最多可包含 100 個字元。

### <a name="plan-description"></a>方案描述

描述此方案獨一無二之處，以及供應項目內方案之間的差異。 請不要描述供應項目，而只描述方案。 此描述會出現在 Azure Marketplace 和 Azure 入口網站的 [供應項目清單] 頁面上。 其可以是您在方案摘要中提供的相同內容，且最多可包含 2,000 個字元。

在完成這些欄位之後，選取 [儲存草稿]。

#### <a name="plan-examples"></a>計劃範例

下列範例示範方案清單欄位如何出現在不同的檢視畫面中。

以下是在檢視方案詳細資料時，Azure Marketplace 中的欄位：

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-plan-details.png" alt-text="說明在 Azure Marketplace 中檢視方案詳細資料時所看到的欄位。":::

這些是 Azure 入口網站的方案詳細資料：

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-plan-details.png" alt-text="說明 Azure 入口網站的方案詳細資料。":::

## <a name="availability"></a>可用性

如果您想要隱藏已發佈的供應項目，讓客戶無法在市集中搜尋、瀏覽或購買，請選取 [可用性] 索引標籤上的 [隱藏方案] 核取方塊。

此欄位通常是在下列情況下使用：

- 此供應項目僅供在另一個應用程式參考時間接使用。
- 供應項目不應個別購買。
- 此方案用於初始測試，且不再相關。
- 此方案用於暫時或季節性的供應項目，因此不應再提供。

## <a name="technical-configuration"></a>技術設定

**IoT Edge 模組**供應項目類型是一種在 IoT Edge 裝置上執行的特定類型容器。 在 [技術設定] 索引標籤上，您將會在 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) 內提供容器映射存放庫的參考資訊，以及讓客戶可輕鬆使用模組的組態設定。

發佈供應項目之後，您的 IoT Edge 容器映射會複製到 Azure Marketplace 的特定公用容器登錄中。 來自 Azure 使用者的所有使用模組要求，都是從 Azure Marketplace 公用容器登錄 (而不是您的私人容器登錄) 提供。

您可以設定多個平台為目標，並使用標籤提供多個版本的模組容器映像。 若要深入了解標籤和版本設定，請參閱[準備 IoT Edge 模組的技術資產](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-iot-edge-module-asset)。

### <a name="image-repository-details"></a>映像存放庫詳細資料

您將會在 [映像存放庫詳細資料] 索引標籤上提供下列資訊。

**選取映像來源**：選取 **Azure Container Registry** 選項。

**Azure 訂用帳戶識別碼**：提供回報資源使用量、以及針對包含您容器映像的 Azure Container Registry 計費服務的訂用帳戶識別碼。 您可以在 Azure 入口網站的[訂用帳戶頁面](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)上找到此識別碼。

**Aure 資源群組名稱**：提供[資源群組](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal)名稱，其中包含具有您容器映像的 Azure Container Registry。 資源群組必須可在訂用帳戶識別碼中存取 (如上)。 您可以在 Azure 入口網站中的[資源群組](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)頁面上找到名稱。

**Azure Container Registry 名稱**：提供具有您容器映像的 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) 名稱。 容器登錄必須存在於您稍早提供的 Azure 資源群組中。 僅提供登錄名稱，而非完整登入伺服器名稱。 請務必從名稱中省略 **azurecr.io**。 您可以在 Azure 入口網站的[容器登錄頁面](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)上找到登錄名稱。

**Azure Container Registry 的管理使用者名稱**：提供與具有容器映像之 Azure Container Registry 相關聯的[管理使用者名稱](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)。 需要使用者名稱和密碼，以確保貴公司有權存取登錄。 若要取得管理使用者名稱和密碼，請使用 Azure 命令列介面 (CLI)，將 **admin-enabled** 屬性設定為 **True**。 您可以選擇性地將 Azure 入口網站中的 [管理使用者] 設定為 [啟用]。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-admin-user.png" alt-text="說明 [更新容器登錄] 對話方塊。":::

**Azure Container Registry 的密碼**：提供與 Azure Container Registry 相關聯且擁有您容器映像之管理使用者名稱的密碼。 需要使用者名稱和密碼，以確保貴公司有權存取登錄。 若要取得 Azure 入口網站的密碼，請前往 [容器登錄] > [存取金鑰]，或是使用 Azure CLI 的 [show 命令](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show)。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-username-password.png" alt-text="說明 Azure 入口網站中的 [存取金鑰] 畫面。":::

**Azure Container Registry 內的存放庫名稱**。 提供具有您映像的 Azure Container Registry 存放庫名稱。 當您將映像推送至登錄時，可以指定存放庫的名稱。 您可以前往[容器登錄](https://azure.microsoft.com/services/container-registry/) > [存放庫] 頁面來尋找儲存機制的名稱。 如需詳細資訊，請參閱[檢視 Azure 入口網站中的容器登錄存放庫](https://docs.microsoft.com/azure/container-registry/container-registry-repositories)。 請注意，設定名稱之後，即無法變更。 請為您帳戶中的每個供應項目使用唯一的名稱。

### <a name="image-tags-for-new-versions-of-your-offer"></a>供應項目新版本的映像標籤

當您發佈更新時，客戶必須能夠從 Azure Marketplace 自動取得更新。 如果他們不想要更新，則必須能夠停留在特定版本的映像上。 您可以在每次對映像進行更新時新增映像標籤來執行此動作。

**映像標籤**。 此欄位必須包含**最新的**標籤，以指向所有支援平台上的最新版映像。 其也必須包含版本標籤 (例如，以 xx.xx.xx 開頭，其中 xx 是數字)。 客戶應使用[資訊清單標籤](https://github.com/estesp/manifest-tool)，以將多個平台設為目標。 也請務必新增資訊清單標籤參考的所有標籤，以便上傳。 所有資訊清單標籤 (除了最新標籤以外) 必須以 X.Y- 或 X.Y.Z- 開頭，其中 X、Y 和 Z 是整數。 例如，如果最新標籤指向 1.0.1-linux-x64、1.0.1-linux-arm32 和 1.0.1-windows-arm32，則必須將這六個標籤新增到此欄位中。 如需標籤和版本設定的詳細資料，請參閱[準備 IoT Edge 模組的技術資產。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets)

### <a name="default-deployment-settings-optional"></a>預設部署設定 (選擇性)

請定義您的 IoT Edge 模組最常用的部署設定。 如此便能讓客戶立即以預設設定啟動 IoT Edge 模組，以此方式使客戶的部署達到最佳化。

**預設路由**。 IoT Edge 中樞會管理模組、IoT 中樞和裝置之間的通訊。 您可以在模組與 IoT 中樞之間設定資料輸入和輸出的路由，讓訊息在傳送時，無須由其他服務處理訊息，也無須撰寫其他程式碼，從而提升訊息傳送的靈活性。 路由會使用名稱/值組構成。 您最多可以定義五個預設路由名稱，每個長度最多 512 個字元。

請務必在您的路由值中使用正確的[路由語法](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) (通常定義為 FROM/message/* INTO $upstream)。 這表示由任何模組傳送的任何訊息都會移至您的 IoT 中樞。 若要參考您的模組，請使用其預設模組名稱，該名稱將是您的**供應項目名稱**，不含空格和特殊字元。 若要參考尚為未知的其他模組，請使用 <FROM_MODULE_NAME> 慣例，讓客戶知道他們需要更新此資訊。 如需 IoT Edge 路由的詳細資料，請參閱[宣告路由](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)。

例如，如果模組 ContosoModule 接聽 ContosoInput 上的輸入和 ContosoOutput 的輸出資料，則應該定義以下兩個預設路由：

- 第 1 個名稱：ToContosoModule
- 第 1 個值：FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- 第 2 個名稱：FromContosoModuleToCloud
- 第 2 個值：FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**預設的模組對應項所需屬性**。 模組對應項是 IoT 中樞內的 JSON 文件，可儲存模組執行個體的狀態資訊，包括所需的屬性。 所需的屬性會搭配報告屬性使用，以便同步處理模組的設定或條件。 解決方案後端可以設定所需屬性，而模組則可加以讀取。 模組也可以接收所需屬性中的變更通知。 所需的屬性會使用最多五個名稱/值組來建立，且每個預設值都必須小於 512 個字元。 最多可以定義五個名稱/值對應項的所需屬性。 對應項所需屬性的值必須是有效的 JSON、非逸出、無陣列，且包含最多四個層級的巢狀階層。 在此案例中，預設值所需的參數無任何意義 (例如，客戶伺服器的 IP 位址)，您可新增參數做為預設值。 若要深入了解對應項所需屬性的詳細資訊，請參閱[定義或更新所需屬性](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties)。

例如，如果模組使用對應項的所需屬性支援可動態設定的重新整理頻率，請定義以下預設的對應項所需屬性：

- 第 1 個名稱：RefreshRate
- 第 1 個值：60

**預設環境變數**。 環境變數可對模組提供有助於設定程序的補充資訊。 環境變數是使用名稱/值組所建立。 每個預設環境變數名稱和值都必須小於 512 個字元，且您最多可以定義五個。 當預設值所需的參數無任何意義 (例如：客戶伺服器的 IP 位址) 時，您可新增參數做為預設值。

例如，如果模組在啟動之前需要接受使用條款，您可以定義以下環境變數：

- 第 1 個名稱：ACCEPT_EULA
- 第 1 個值：Y

**預設容器建立選項**。 容器建立選項會引導建立 IoT Edge 模組 Docker 容器。 IoT Edge 支援 Docker 引擎 API 建立容器選項。 請參閱[列出容器](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList)中的所有選項。 建立選項欄位必須是有效的 JSON、非逸出且小於 512 個字元。

例如，如果模組需要連接埠繫結，請定義下列建立選項：

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="review-and-publish"></a>檢閱與發佈

完成供應項目的所有必要區段之後，您可加以提交來進行檢閱和發佈。

在入口網站的右上角，選取 [檢閱及發佈]。

在 [檢閱] 頁面上，您可以看到發佈狀態：

- 請參閱供應項目每個區段的完成狀態。 在供應項目的所有區段都標示為完成之前，您將無法發佈。
    - **未啟動** - 區段尚未啟動且必須完成。
    - **不完整** - 區段中有需要修正的錯誤，或是需要您提供詳細資訊。 如需指引，請參閱本文件稍早的章節。
    - **完成** - 區段包含所有必要的資料且沒有任何錯誤。 您必須完成供應項目的所有區段，才能提交供應項目。
- 將測試指示提供給認證小組，以確保您的供應項目已正確測試。 此外，請提供有助於了解您供應項目的任何補充附註。

若要提交供應項目進行發佈，請選取 [發佈您的供應項目]。

我們會傳送一封電子郵件給您，讓您知道供應項目的預覽版本何時可供檢閱及核准。 若要將您的供應項目發佈到公開 (或如果為私人供應項目則發佈到私人對象)，請移至 [合作夥伴中心]，然後選取 [上線]。

## <a name="next-steps"></a>後續步驟

- [更新商業市集中的現有供應項目](https://docs.microsoft.com//azure/marketplace/partner-center-portal/update-existing-offer)