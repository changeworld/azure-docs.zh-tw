---
title: 建立 Azure 容器供應項目 - Azure Marketplace
description: 了解如何建立容器供應項目並發佈至 Azure Marketplace。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 06/17/2020
ms.openlocfilehash: cbaca0b9d608d7703beec559e5434f52308629ab
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2020
ms.locfileid: "91709099"
---
# <a name="create-an-azure-container-offer-in-azure-marketplace"></a>在 Azure Marketplace 中建立 Azure 容器供應項目

本文說明如何建立及發佈 Azure Marketplace 的容器供應項目。 開始之前，請先[在合作夥伴中心建立商業市集帳戶](create-account.md) (如果您尚未有帳戶)。 請確定您已在商業市集方案中註冊帳戶。

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。

2. 在左側導覽功能表中，選取 [商業市集] > [概觀]。

3. 在概觀頁面上，選取 [+新增供應項目] > [Azure 容器]。

   ![說明左側導覽功能表。](./media/new-offer-azure-container.png)

> [!TIP]
> 供應專案發佈之後，在合作夥伴中心中進行的編輯，只會在重新發佈供應專案之後出現在線上商店中。 請務必在進行變更之後，一律重新發佈。

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

**供應項目概觀**頁面會顯示發佈此供應項目 (已完成和即將推出) 所需步驟的視覺表示法，以及完成每個步驟所需的時間。

此頁面會根據供應專案的目前狀態顯示不同的連結。 例如：

- 如果供應項目為草稿 - [刪除草稿供應項目](update-existing-offer.md#delete-a-draft-offer)
- 如果供應項目已上線 - [停止銷售供應項目](update-existing-offer.md#stop-selling-an-offer-or-plan)
- 如果供應專案處於預覽階段 - [上線](publishing-status.md#publisher-approval)
- 如果您尚未完成發行者登出 - [取消發行](update-existing-offer.md#cancel-publishing)

## <a name="offer-setup"></a>供應項目設定

請遵循下列步驟來設定供應項目。

### <a name="customer-leads--optional"></a>潛在客戶 - 選用

使用合作夥伴中心將供應項目發佈到商業 Marketplace 時，您可以將其連線到客戶關係管理 (CRM) 系統。 這可讓您在有人對您的產品感興趣或使用時，立即收到客戶連絡人資訊。

1. **選取您要我們傳送潛在客戶至其中的潛在客戶目的地**。 合作夥伴中心支援下列 CRM 系統：

   - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
   - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
   - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

   > [!NOTE]
   > 如果以上未列出您的 CRM 系統，請使用 [Azure 資料表](commercial-marketplace-lead-management-instructions-azure-table.md)或 [Https 端點](commercial-marketplace-lead-management-instructions-https.md)來儲存客戶潛在客戶資料，然後將資料匯出至您的 CRM 系統。

2. 在合作夥伴中心內發佈時，將供應項目連線到潛在客戶目的地。
3. 確認已正確設定與潛在客戶目的地的連線。 在合作夥伴中心發佈之後，我們會驗證連線並將測試潛在客戶傳送給您。 在上架前預覽供應項目時，您也可以嘗試在預覽環境中自行購買供應項目，藉此測試您的潛在客戶連線。
4. 確認潛在客戶目的地的連線保持在最新狀態，您就不會遺失任何潛在客戶。

以下是一些額外的潛在客戶管理資源：

- [來自您的商業市集供應項目的潛在客戶](commercial-marketplace-get-customer-leads.md)
- [潛在客戶管理的常見問題](../lead-management-faq.md#common-questions-about-lead-management)
- [針對潛在客戶設定錯誤進行疑難排解](../lead-management-faq.md#publishing-config-errors)
- [潛在客戶管理概觀](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (請確定您已關閉快顯封鎖程式)。

選取 [儲存草稿] 後再繼續。

### <a name="properties"></a>屬性

此頁面可讓您定義用來在市集上將供應專案分組的類別，以及支援供應項目的法律合約。

#### <a name="category"></a>類別

選取類別和子類別，以將您的供應專案放在適當的 marketplace 搜尋區域中。 請務必在供應項目描述中描述您的供應項目如何支援這些類別。 選取：

- 至少一個和多個類別，包括主要和次要類別 (選擇性) 。
- 每個主要和/或次要類別最多可有兩個子類別。 如果沒有任何子類別適用于您的供應專案，請選取 [ **不適用**]。

請參閱 [供應專案清單最佳做法](../gtm-offer-listing-best-practices.md)中的類別和子類別的完整清單。 容器一律會出現在 **[容器] 和 [** **容器映射** ] 類別之下。

#### <a name="legal"></a>法律

您必須提供供應項目的條款及條件。 有兩個選項：

- 使用 Microsoft 商業市集的標準合約。
- 提供您自己的條款及條件。

#### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 商業市集的標準合約

我們提供標準合約範本，協助促進商業市集中的交易。 您可以選擇在標準合約下提供您的解決方案，而客戶只需要檢查並接受一次。 如果您不想建立自訂的條款及條件，這是個不錯的選項。

若要深入了解標準合約，請參閱 [Microsoft 商業市集的標準合約](../standard-contract.md)。 您也可以下載[標準合約](https://go.microsoft.com/fwlink/?linkid=2041178) PDF (請確定您的快顯封鎖程式已關閉)。

若要使用標準合約，請選取 [使用 Microsoft 商用 marketplace 的標準合約] (。/standard-contract.md) 

> [!NOTE]
> 當您使用 Microsoft 商業市集的標準合約發佈供應項目之後，就無法使用自己的自訂條款及條件。 請根據標準合約或您自己的條款及條件來提供您的解決方案。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="說明如何使用 Microsoft 商業市集的標準合約核取方塊。":::

##### <a name="your-own-terms-and-conditions"></a>您自己的條款及條件

若要提供您自己的自訂條款及條件，請在 [條款及條件] 方塊中加以輸入。 您可以在此方塊中輸入不限數目的文字字元。 客戶必須先接受這些條款，才能試用您的供應項目。

選取 [儲存草稿]，再繼續進行下一節，＜供應項目清單＞。

## <a name="offer-listing"></a>供應項目清單

此頁面可讓您定義顯示於商業市集中的供應項目詳細資料。 這包括供應項目名稱、描述和圖片。

> [!NOTE]
> 如果供應項目描述開頭為以下詞句：「此應用程式僅適用於 [非英文語言]。」，則供應項目詳細資料不需要為英文。 也可利用不同於供應項目清單詳細資料中所使用的語言，來提供供應項目內容的有用連結。

### <a name="name"></a>名稱

您在此處輸入的名稱會顯示為您供應項目的標題。 當您建立供應項目時，此欄位會預先填入您在 [供應項目別名] 方塊中輸入的文字。 您稍後可以變更此名稱。

名稱：

- 可以是商標 (您也可以包含商標和著作權符號)。
- 長度不可超過 50 個字元。
- 不可包含表情圖示。

### <a name="search-results-summary"></a>搜尋結果摘要

供應項目的簡短描述。 長度最多可有 100 個字元，並用於市集搜尋結果。

### <a name="long-summary"></a>完整摘要

供應項目的更詳細描述。 長度最多可有 256 個字元，並用於市集搜尋結果。

### <a name="description"></a>描述

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>隱私權原則連結

輸入貴組織隱私權原則的網址。 您必須負責確保您的供應項目符合隱私權法律及規定。 您也必須負責在您的網站上張貼有效的隱私權原則。

#### <a name="useful-links"></a>實用的連結

提供有關您供應項目的補充線上文件。 您最多可以新增 25 個連結。 若要新增連結，請選取 [+ 新增連結] 然後完成下欄欄位：

- **標題** - 客戶會在供應項目的詳細資料頁面上看到標題。
- **連結 (URL)** - 輸入客戶的連結，以檢視您的線上文件。 連結的開頭必須是 http:// 或 https://。

### <a name="contact-information"></a>連絡資訊

您必須提供 [支援連絡人] 與 [工程連絡人] 的姓名、電子郵件和電話號碼。 此資訊不會向客戶顯示，但可供 Microsoft 使用； 也可以提供給雲端解決方案提供者 (CSP) 合作夥伴。

- 支援連絡人 (必要)：針對一般支援問題。
- 工程連絡人 (必要)：針對技術問題和認證問題。
- CSP 方案連絡人 (選擇性)：針對與 CSP 方案相關的轉銷商問題。

在 [支援連絡人] 區段中，提供 [支援網站]，合作夥伴可在其中根據該供應項目是否適用於全域 Azure 和/或 Azure Government 來尋找您供應項目的支援。

在 [CSP 方案連絡人] 區段中，提供 CSP 合作夥伴可在其中找到您供應項目行銷資料的連結 (**CSP 方案行銷資料**)。

#### <a name="additional-marketplace-listing-resources"></a>其他市集清單資源

若要深入了解如何建立供應項目清單，請參閱[供應項目清單最佳做法](../gtm-offer-listing-best-practices.md)

### <a name="marketplace-images"></a>Marketplace 映像

提供要與您供應項目搭配使用的標誌和映像。 所有影像都必須採用 PNG 格式。 模糊映像將會遭到拒絕。

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>如果您在上傳檔案時遇到問題，請確定您的區域網路不會封鎖合作夥伴中心所使用的 https://upload.xboxlive.com 服務。

#### <a name="store-logos"></a>儲存標誌

提供 **大型** 大小標誌的 PNG 檔案。 合作夥伴中心將使用這個來建立 **小型** 和 **中型** 標誌。 您可以選擇性地使用不同的映射取代這些映射。

- 從 216 x 216 到 350 x 350 px 的**大型** (，需要) 
- **中型** (90 x 90 px，選擇性) 
- **Small** (48 x 48 px，選擇性) 

這些標誌可用於清單中的不同位置：

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>螢幕擷取畫面 (選擇性)

新增最多五個螢幕擷取畫面，以顯示供應項目的運作方式。 每個的大小都必須為 1280 x 720 像素且為 PNG 格式。

#### <a name="videos-optional"></a>影片 (選擇性)

新增最多五個示範您供應項目的影片。 輸入影片的名稱、其網址，以及影片的縮圖 PNG 映像 (大小為 1280 x 720 像素)。

#### <a name="offer-examples"></a>供應項目範例

下列範例示範供應項目清單欄位如何出現在供應項目的不同位置。

這會顯示 Azure Marketplace 中的**供應項目清單**頁面：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="說明如何使用 Microsoft 商業市集的標準合約核取方塊。" :::

這會顯示 Azure Marketplace 中的搜尋結果：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="說明如何使用 Microsoft 商業市集的標準合約核取方塊。":::

這會顯示 Azure 入口網站中的**供應項目清單**頁面：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="說明如何使用 Microsoft 商業市集的標準合約核取方塊。":::

這會顯示 Azure 入口網站中的搜尋結果：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="說明如何使用 Microsoft 商業市集的標準合約核取方塊。":::

## <a name="preview"></a>預覽

在預覽索引標籤上，您可以選擇有限的 [預覽對象] 來驗證您的供應項目，再即時發佈。

> [!IMPORTANT]
> 在**預覽**中檢視供應項目之後，您必須選取 [上線]，以將您的供應項目發佈至公用。

使用 Azure 訂用帳戶識別碼 GUID 指定您的預覽對象，以及每個使用者的選擇性描述。 客戶無法看到這些欄位。

> [!NOTE]
> 您可以在 Azure 入口網站的訂用帳戶頁面上找到 Azure 訂用帳戶識別碼。

藉由分別 (最多 10 個) 或上傳 CSV 檔案 (最多 100 個)，新增至少一個 Azure 訂用帳戶識別碼。 您可以藉由新增這些訂用帳戶識別碼，決定哪些人員可以在即時發佈之前預覽您的供應項目。 如果您的供應項目已上線，您可以選擇預覽對象，以測試您供應項目的變更或更新。

選取 [儲存草稿] 後再繼續。

## <a name="plan-overview"></a>方案概觀

此索引標籤可讓您在相同供應項目中提供不同的方案選項。  (先前稱為 Sku 的方案) 在有哪些雲端可用，例如全域雲端、政府雲端，以及方案所參考的映射方面，會有所不同。 若要在商業市集中列出您的供應項目，至少必須設定一個方案。

您最多可以為每個供應專案建立100個方案：這些供應專案的最高45個都可以是私用。 深入瞭解 [Microsoft 商業市場中私](../private-offers.md)用供應專案的私人方案。

建立方案之後，隨即顯示 [方案概觀] 索引標籤：

- 方案名稱
- 定價模式
- Azure 區域 (全球或政府) 
- 目前的發佈狀態
- 任何可用的動作

方案概觀中的可用動作會根據方案的目前狀態而有所不同。 其中包括：

- **刪除草稿** - 如果方案狀態為草稿。
- **停止銷售方案** - 如果方案狀態為即時發佈。

### <a name="create-new-plan"></a>建立新的方案

選取 [建立新方案]。 [新增方案] 對話方塊隨即出現。

在 [方案識別碼] 方塊中，為此供應項目中的每個方案建立唯一的方案識別碼。 客戶將可在產品網址中看到此識別碼。 僅使用小寫字母和數位、連字號或底線，以及最多 50 個字元。

> [!NOTE]
> 選取 [建立] 之後，即無法變更方案識別碼。

在 [方案名稱] 方塊中，輸入此方案的名稱。 當客戶決定要在您的供應項目內選取哪一個方案時，會看到此名稱。 為此供應項目中的每個方案建立唯一的名稱。 例如，您可以使用 **Windows Server** 的供應項目名稱，以及 **Windows Server 2016** 和 **Windows Server 2019** 的方案。

### <a name="plan-setup"></a>方案設定

此索引標籤可讓您選擇可使用方案的雲端。 您在此索引標籤上的答案會影響哪些要顯示在其他索引標籤上的欄位。

#### <a name="azure-regions"></a>Azure 區域

Azure 容器供應專案的所有方案都會自動在 **Azure Global**中提供。  您的方案可供所有使用商用 marketplace 的全球 Azure 區域中的客戶使用。 如需詳細資料，請參閱[地理可用性和貨幣支援](../marketplace-geo-availability-currencies.md) \(部分機器翻譯\)。

選取 [ [Azure Government](../../azure-government/documentation-government-welcome.md) ] 選項，讓您的解決方案顯示于此處。 這是政府社群雲端，U.S. 聯邦政府、州和地方或部落政府機關的客戶，以及符合資格可提供服務的合作夥伴，擁有此雲端的控管權限。 身為發行者，您必須負責此雲端社區的任何合規性控制、安全性措施和最佳做法。 Azure Government 會使用實際隔離的資料中心和網路 (僅限位於美國地區)。 在[發佈](../../azure-government/documentation-government-manage-marketplace-partners.md)至 Azure Government 之前，請先在該區域內測試並確認您的解決方案，因為結果可能不同。 若要建立並測試您的解決方案，請從 [Microsoft Azure Government 試用版](https://azure.microsoft.com/global-infrastructure/government/request/)要求試用帳戶。

> [!NOTE]
> 在您的方案發佈並可在特定區域中使用之後，您就無法移除該區域。

#### <a name="azure-government-certifications"></a>Azure Government 認證

只有在**Azure 區域**下選取**Azure Government**時，才會看到此選項。

Azure Government 服務會處理受限於特定政府法規和需求的資料。 例如，FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS。

若要顯示這些方案的認證，您可以提供最多 100 個描述這些認證的連結。 這些可以直接連結到您在方案上的清單或連結到您自己的網站。 只有 Azure Government 客戶可以看到這些連結。

### <a name="plan-listing"></a>方案清單

此索引標籤會顯示目前供應項目中每個不同方案的特定資訊。

### <a name="plan-name"></a>方案名稱

這會預先填入您在建立方案時提供給您方案的名稱。 您可以視需要變更此名稱。 該名稱長度最多可達 50 個字元。 這個名稱在 Azure Marketplace 和 Azure 入口網站中會顯示為此方案的標題。 在準備好使用方案之後，其會用來做為預設的模組名稱。

### <a name="plan-summary"></a>方案摘要

您軟體方案 (非供應項目) 的簡短摘要。 此摘要會出現在 Azure Marketplace 搜尋結果中，且最多可包含 100 個字元。

### <a name="plan-description"></a>方案描述

描述此軟體方案獨一無二之處，以及供應項目內方案之間的差異。 請不要描述供應項目，而只描述方案。 此描述會出現在 Azure Marketplace 和 Azure 入口網站的 [供應項目清單] 頁面上。 其可以是您在方案摘要中提供的相同內容，且最多可包含 2,000 個字元。

在完成這些欄位之後，選取 [儲存]。

#### <a name="plan-examples"></a>計劃範例

下列範例示範方案清單欄位如何出現在不同的檢視畫面中。

以下是在檢視方案詳細資料時，Azure Marketplace 中的欄位：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="說明如何使用 Microsoft 商業市集的標準合約核取方塊。":::

這些是 Azure 入口網站的方案詳細資料：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="說明如何使用 Microsoft 商業市集的標準合約核取方塊。":::

### <a name="plan-availability"></a>方案可用性

如果您想要隱藏已發佈的供應項目，讓客戶無法在市集中搜尋、瀏覽或購買，請選取 [可用性] 索引標籤上的 [隱藏方案] 核取方塊。

此欄位是在下列情況下使用：

- 這項供應專案是要在透過另一個應用程式參考時間接使用。
- 供應項目不應個別購買。
- 此方案用於初始測試，且不再相關。
- 此方案用於暫時或季節性的供應項目，因此不應再提供。

## <a name="technical-configuration"></a>技術設定

容器映像必須裝載於私人的 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) 中。 在 [技術設定] 索引標籤上，您將會在 Azure Container Registry 內提供容器映像存放庫的參考資訊。

發佈供應項目之後，您的容器映像會複製到 Azure Marketplace 的特定公用容器登錄中。 使用您容器映像的所有要求，都是從 Azure Marketplace 公用容器登錄 (而不是您的私人容器登錄) 提供。 如需詳細資訊，請參閱[準備您的 Azure Container 技術資產](create-azure-container-technical-assets.md)。

### <a name="image-repository-details"></a>映像存放庫詳細資料

[映像存放庫詳細資料] 索引標籤上會提供下列資訊。

**Azure 訂用帳戶識別碼** - 提供回報使用量、以及針對包含您容器映像的 Azure Container Registry 計費服務的訂用帳戶識別碼。 您可以在 Azure 入口網站的[訂用帳戶頁面](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)上找到此識別碼。

**Azure 資源群組名稱** - 提供[資源群組](../../azure-resource-manager/management/manage-resource-groups-portal.md)名稱，其中包含具有您容器映像的 Azure Container Registry。 資源群組必須可在訂用帳戶識別碼中存取 (如上)。 您可以在 Azure 入口網站中的[資源群組](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)頁面上找到名稱。

**Azure Container Registry 名稱** - 提供具有您容器映像的 [Azure Container Registry](../../container-registry/container-registry-intro.md) 名稱。 容器登錄必須位於您稍早提供的 Azure 資源群組中。 僅包括登錄名稱，而非完整登入伺服器名稱。 請務必從名稱中省略 **azurecr.io**。 您可以在 Azure 入口網站的[容器登錄頁面](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)上找到登錄名稱。

**Azure Container Registry** 的系統管理員使用者名稱–提供系統 [管理員使用者名稱](../../container-registry/container-registry-authentication.md#admin-account)) 與具有容器映射的 Azure Container Registry 連結。 需要使用者名稱和密碼，以確保貴公司有權存取登錄。 若要取得管理使用者名稱和密碼，請使用 Azure 命令列介面 (CLI)，將 **admin-enabled** 屬性設定為 **True**。 您可以選擇性地將 Azure 入口網站中的 [管理使用者] 設定為 [啟用]。

 :::image type="content" source="media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="說明如何使用 Microsoft 商業市集的標準合約核取方塊。":::

**Azure Container Registry 密碼** - 提供與 Azure Container Registry 相關聯且擁有您容器映像之管理使用者名稱的密碼。 需要使用者名稱和密碼，以確保貴公司有權存取登錄。 若要取得 Azure 入口網站的密碼，請前往 [容器登錄] > [存取金鑰]，或是使用 Azure CLI 的 [show 命令](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show)。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="說明如何使用 Microsoft 商業市集的標準合約核取方塊。":::

**Azure Container Registry 內的存放庫名稱**。 提供具有您映像的 Azure Container Registry 存放庫名稱。 當您將映像推送至登錄時，包含存放庫的名稱。 您可以前往[容器登錄](https://azure.microsoft.com/services/container-registry/) > [存放庫] 頁面來尋找儲存機制的名稱。 如需詳細資訊，請參閱[檢視 Azure 入口網站中的容器登錄存放庫](../../container-registry/container-registry-repositories.md)。

> [!NOTE]
> 設定名稱之後，即無法變更。 請為您帳戶中的每個供應項目使用唯一的名稱。

### <a name="image-tags-for-new-versions-of-your-offer"></a>供應項目新版本的映像標籤

當您發佈更新時，客戶必須能夠從 Azure Marketplace 自動取得更新。 如果不想更新，則必須能夠停留在特定版本的映像上。 您可以在每次對映像進行更新時新增映像標籤來執行此動作。

### <a name="image-tag"></a>映像標籤

此欄位必須包含**最新的**標籤，以指向所有支援平台上的最新版映像。 其也必須包含版本標籤 (例如，以 xx.xx.xx 開頭，其中 xx 是數字)。 客戶應使用[資訊清單標籤](https://github.com/estesp/manifest-tool)，以將多個平台設為目標。 也請務必新增資訊清單標籤參考的所有標籤，以便上傳。

所有資訊清單標籤 (除了最新標籤以外) 必須以 X.Y **-** 或 X.Y.Z- 開頭，其中 X、Y 和 Z 是整數。 例如，如果**最新**標籤指向 1.0.1-linux-x64、1.0.1-linux-arm32 和 1.0.1-windows-arm32，則必須將這六個標籤新增到此欄位中。 如需詳細資訊，請參閱[準備您的 Azure Container 技術資產](create-azure-container-technical-assets.md)。

> [!NOTE]
> 請記得將測試標籤新增到您的映像，以便您在測試期間識別該映像。

## <a name="review-and-publish"></a>檢閱與發佈

完成供應項目的所有必要區段之後，您可加以提交來進行檢閱和發佈。

在入口網站的右上角，選取 [檢閱] 及 [發佈]。

在 [檢閱] 頁面上，您可以：

- 查看供應項目每個區段的完成狀態。 在供應項目的所有區段都標示為完成之前，您將無法發佈。
  - **未啟動** - 尚未啟動且必須完成。
  - **不完整** - 有需要修正的錯誤，或是需要您提供詳細資訊。 如需協助，請參閱本文件稍早的章節。
  - **完成** –包含所有必要的資料，且並未發生錯誤。 您必須完成供應項目的所有區段，才能提交供應項目。
- 將測試指示提供給認證小組，以確保您的供應項目已正確測試。 此外，請提供有助於了解您供應項目的任何補充附註。

若要提交供應項目進行發佈，請選取 [發佈]。

我們會傳送一封電子郵件給您，讓您知道供應項目的預覽版本何時可供檢閱及核准。

若要將您的供應專案發佈至公用，請移至合作夥伴中心並選取 [ **上線**]。

## <a name="next-step"></a>後續步驟

- [更新商業市集中的現有供應項目](update-existing-offer.md)
