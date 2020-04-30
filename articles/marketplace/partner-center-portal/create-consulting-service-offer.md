---
title: 在合作夥伴中心建立諮詢服務供應專案-Azure Marketplace
description: 瞭解如何使用合作夥伴中心，將諮詢服務供應專案發佈至 Azure Marketplace 或 AppSource。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: eff37750f0580a28c9644ee1ffb7fe4e95038709
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869786"
---
# <a name="create-a-consulting-service-offer"></a>建立諮詢服務供應專案

> [!IMPORTANT]
> 我們正在將您諮詢服務供應專案的管理從 Cloud Partner 入口網站移至合作夥伴中心。 在您的供應專案遷移之前，請依照[Azure 和 Dynamics 365 諮詢服務供應](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer)專案中的指示進行 Cloud Partner 入口網站，以管理您的供應專案。

本文說明如何將諮詢服務供應專案發佈至[Azure Marketplace](https://azuremarketplace.microsoft.com/)或[AppSource](https://appsource.microsoft.com/)。 根據 AppSource 上的 Microsoft [Dynamics 365](https://dynamics.microsoft.com/)和 Power 平臺，列出諮詢服務供應專案。 根據 Azure Marketplace 上的 Microsoft Azure 列出諮詢服務供應專案。

若要在 Azure Marketplace 或 AppSource 諮詢服務中建立諮詢服務供應專案，您必須先[在合作夥伴中心擁有發行者帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)，而且必須在商業 Marketplace 方案中註冊您的帳戶。 建立供應專案之前，請先參閱[諮詢服務必要條件](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites)中的必要條件。

## <a name="publishing-benefits"></a>發佈的優點

發佈至商用 marketplace 的優點：

- 使用 Microsoft 品牌來宣傳您的公司。
- 透過 Azure Marketplace，有可能觸及超過100000000個 Office 365 和 Dynamics 365 AppSource 和超過200000個組織的使用者。
- 從這些市場獲得高品質的潛在客戶。
- 由 Microsoft 現場和電話銷售團隊推廣您的服務

## <a name="create-a-new-offer"></a>建立新的供應項目

符合上述的需求之後，請遵循下列步驟來建立諮詢服務供應專案。

1. 登入 [[合作夥伴中心](https://partner.microsoft.com)]，然後從頂端功能表中選取 [**儀表板**]。
2. 在左側流覽列中，選取 [**商業 Marketplace**]，然後選取 **[總覽**]。

    :::image type="content" source="media/cs-menu-overview.png" alt-text="說明商用 marketplace 的功能表":::

3. 選取 [ **+ 新增供應**專案]，然後選取 [**諮詢服務**]。

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="說明用來建立新供應專案的按鈕。":::

4. 輸入**供應專案識別碼**。 這是您帳戶中每個供應專案的唯一識別碼。

    - 在 marketplace 供應專案網址中，客戶可以看到此識別碼。
    - 只能使用小寫字母、數位、連字號和底線，但不能有空格。 長度限制為50個字元。 例如，如果您輸入**測試-供應專案-1**，則供應專案 URL 會`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`是。
    - 在您選取 [**建立**] 之後，[供應專案識別碼] 可以&#39;t 變更。

5. 輸入**供應專案別名**。 這是用來在合作夥伴中心參考供應專案的名稱。

    - 此名稱不會在 marketplace 中使用&#39;t。 它&#39;不同于供應專案名稱和其他向客戶顯示的值。 您可以使用此欄位，將名稱指派給提供給您的供應專案，以便在內部識別供應專案;不會向客戶顯示。
    - 在您選取 [**建立**] 之後，[供應專案別名] 可以&#39;t 變更。

輸入這兩個值之後，請選取 [**建立**] 以繼續前往 [**供應專案設定**] 頁面。

## <a name="offer-setup"></a>供應專案設定

輸入供應專案識別碼和供應專案別名之後，合作夥伴中心會建立草稿供應專案，並顯示 [**供應專案設定**] 頁面。 請遵循下列步驟來設定您的供應專案。

### <a name="connect-lead-management"></a>連接潛在客戶管理

當您使用合作夥伴中心將您的供應專案發佈至 marketplace 時，您_必須_將其連接到客戶關係管理（CRM）或行銷自動化系統。 這可讓您在有人對您的產品感興趣或使用時，立即收到客戶連絡人資訊。

1. 選取 **[連線]** ，指定您要讓我們傳送客戶潛在客戶的位置。 合作夥伴中心支援下列系統：

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) For Customer Engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > 如果您的 CRM 系統未列于上列&#39;，請使用[Azure 資料表](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)或[Https 端點](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)來儲存客戶潛在客戶資料，然後將資料匯出至您的 CRM 系統。

2. 在合作夥伴中心發佈時，將您的供應專案連接到潛在客戶目的地。
3. 確認已正確設定與潛在客戶目的地的連接。 在合作夥伴中心發佈之後，我們&#39;會驗證連線，並將測試潛在客戶傳送給您。 當您在供應專案上線前進行預覽時，您也可以嘗試在預覽環境中自行購買供應專案，藉此測試您的潛在客戶連線。
4. 請確定潛在客戶目的地的連線持續更新，讓您不&#39;會失去任何潛在客戶。

以下是一些額外的潛在客戶管理資源：

- [潛在客戶管理總覽](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [潛在客戶管理常見問題集](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [常見潛在客戶設定錯誤](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [潛在客戶管理總覽](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)PDF （請確定您的快顯封鎖程式已關閉）

選取 [**儲存草稿**]，然後繼續進行下一節的 [屬性]。

### <a name="properties"></a>屬性

此頁面可讓您設定諮詢服務提供最佳涵蓋的主要產品、設定諮詢服務類型，以及選擇適用的產品。

1. 從下拉式清單中選取**主要產品**。
2. 從下拉式清單中選取**諮詢服務類型**：

    - **評**量：客戶&#39;s 環境的評估，以判斷解決方案的適用性，並提供成本和時間的估計。
    - **簡報**：介紹解決方案或諮詢服務，使用架構、示範和客戶範例繪製客戶的關注。
    - **執行**：會產生完全正常運作之解決方案的完整安裝。 限制可在兩周內執行的解決方案。
    - **概念證明**：判斷解決方案是否符合客戶需求的有限範圍的執行。
    - **研討會**：在客戶&#39;s 部署上進行互動。 它可以包含以客戶&#39;的資料或環境為基礎的訓練、簡報、評量或示範。

1. 如果您選取了**Azure**的主要產品，請選取最多三個**解決方案區域**。 這讓 Azure Marketplace 的客戶更容易找到您的供應專案。 如果您未選擇 [Azure]&#39;，請略過此步驟。
2. 如果您選取了 Azure_以外_的主要產品，請選取最多三個**適用的產品**。 這讓 AppSource 中的客戶更容易找到您的供應專案。 如需詳細資訊，請參閱[Microsoft AppSource 諮詢服務清單指導方針](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409)（PDF）。
3. 最多可選取您供應專案適用的六個**產業**。 這可讓客戶更輕鬆地找到您的供應專案。
4. 最多可新增**三項**您的公司所獲的專長認證，以顯示在您的諮詢服務供應專案清單中。 除了 Azure 專家 MSP&#39;s 和 Azure 網路 MSP&#39;s 以外，至少需要一個專長認證。

選取 [**儲存草稿**]，然後繼續進行下一節的供應專案清單。

## <a name="offer-listing"></a>供應專案清單

在這裡，您&#39;定義在 marketplace 中顯示的供應專案詳細資料。 這包括供應專案名稱、描述、影像等等。 設定此供應專案時，請務必遵循[Microsoft&#39;s 原則頁面](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services)上詳述的原則。

> [!NOTE]
> 如果供應專案描述以片語開頭，則供應專案詳細資料不&#39;需要英文， &quot;此應用程式僅適用于 [非英文語言]。&quot;它&#39;也可以提供有用的連結，以不同于供應專案清單詳細資料所使用的&#39;語言來提供內容。

### <a name="name"></a>Name

您在此處輸入的名稱會顯示為您供應專案的標題。 當您建立供應專案時，此欄位會預先填入您在 [**供應專案別名**] 方塊中輸入的文字。 您可以稍後變更此名稱。

名稱：

- 可以是商標（您也可以包含商標或著作權符號）。
- &#39;的長度不能超過50個字元。
- 可以&#39;t 包含 emoji。

### <a name="search-results-summary"></a>搜尋結果摘要

提供供應專案的簡短描述。 最多可有100個字元，並用於 marketplace 搜尋結果。

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
  - 受益于供應專案的使用者類型。
  - 客戶需要或發行供應專案位址。
- 請記住，前幾個句子可能會顯示在搜尋結果中。
- Don&#39;t 依賴特性和功能來銷售您的產品。 相反地，請專注于您的供應專案提供的值。
- 請嘗試使用產業專屬詞彙或以權益為基礎的用語。

若要讓您的描述更吸引人，請使用 rich 文字編輯器來格式化您的描述。 Rtf 編輯器可讓您新增數位、專案符號、粗體、斜體和縮排，讓您的描述更容易閱讀。

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="說明用來撰寫供應專案描述的 rich 文字編輯器。" border="false":::

### <a name="keywords"></a>關鍵字

輸入最多三個與您的主要產品和諮詢服務相關的搜尋關鍵字。 這些可讓您更輕鬆地找到您的供應專案。

### <a name="duration"></a>Duration

設定此參與與客戶的預期持續時間。

### <a name="contact-information"></a>連絡資訊

您必須提供**主要**和**次要連絡人**的名稱、電子郵件和電話號碼。 這種資訊不&#39;向客戶顯示。 可供 Microsoft 使用，並可提供給雲端解決方案提供者（CSP）合作夥伴。

### <a name="supporting-documents"></a>支援檔

最多可為您的供應專案新增三個（但至少一個）支援 PDF 檔。

### <a name="marketplace-images"></a>Marketplace 映射

提供要與您的供應專案搭配使用的標誌和影像。 所有影像都必須是 .png 格式。 模糊影像將會遭到拒絕。

>[!Note]
>如果您在上傳檔案時發生問題，請確定您的區域網路不會https://upload.xboxlive.com封鎖合作夥伴中心所使用的服務。

#### <a name="store-logos"></a>Microsoft Store標誌。

請在下列每個圖元大小中提供供應專案的 .png 檔案&#39;s 標誌：

- **小型（48 x 48）**
- **大型（216 x 216）**

所有標誌都是必要的，並且會用於 marketplace 清單中的不同位置。

#### <a name="screenshots-optional"></a>螢幕擷取畫面（選擇性）

新增最多五個螢幕擷取畫面，以顯示供應專案的運作方式。 每個都必須是 1280 x 720 圖元大小和 .png 格式。

#### <a name="videos-optional"></a>影片（選擇性）

最多可新增四個示範您供應專案的影片。 以 1280 x 720 圖元的大小，輸入影片&#39;的名稱、其網址（URL）和縮圖的 png 影像。

選取 [**儲存草稿**]，然後繼續進行下一節 [定價與可用性]。

## <a name="pricing-and-availability"></a>價格與可用性

在這裡，您將定義專案，例如價格、市場和私密金鑰。

1. **市場**：將提供您供應專案的市場。 您只能為每個供應專案選取一個市場。
    1. 若為美國或加拿大市場，請選取 [**編輯州**（或**省**）] 來指定供應專案的可用位置。
2. **預覽物件**：設定用來為您的供應專案設定私人物件的**隱藏金鑰**。
3. **定價**：指定您的供應專案是**免費**或**付費**供應專案。

    > [!NOTE]
    > 諮詢服務供應專案僅適用于清單。 任何交易都會直接在商業 marketplace 外部發生。

4. 若為付費供應專案，請指定**價格和貨幣**，以及價格是**固定**或**預估**。 若預估，您必須在描述中指定會影響價格的因素。
5. 選取 [**儲存草稿**]。

## <a name="review-and-publish"></a>審查和發行

完成供應專案的所有必要區段之後，您可以提交供應專案來進行審查和發佈。

1. 當您準備好發佈諮詢服務供應專案時，請按一下 [**審查併發布**]。
2. 請參閱最終提交頁面的詳細資料。
3. 必要時，如果您認為您的供應專案有任何詳細資料需要說明，請將附注寫入認證小組。
4. 當您準備好時，請選取 [**提交**]。
5. [**供應專案總覽**] 頁面會顯示您的供應專案所在的發佈階段。

如需您的供應專案在每個發佈階段中可預期時間長度的詳細資訊，請參閱[檢查商業 Marketplace 供應專案的發佈狀態](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status)。

## <a name="update-your-existing-consulting-service-offers"></a>更新您現有的諮詢服務供應專案

- [更新商用 marketplace 中的現有供應專案](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
