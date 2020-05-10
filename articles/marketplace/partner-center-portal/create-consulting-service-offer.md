---
title: 建立諮詢服務供應專案-Microsoft 商業 marketplace
description: 瞭解如何使用合作夥伴中心，將諮詢服務供應專案發佈至 Microsoft AppSource 或 Azure Marketplace。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 626dc5a7d1cd52182e68eb0d217b2ac4c653330e
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994357"
---
# <a name="create-a-consulting-service-offer"></a>建立諮詢服務供應項目

本文說明如何將諮詢服務供應專案發佈至[Microsoft AppSource](https://appsource.microsoft.com/)或[Azure Marketplace](https://azuremarketplace.microsoft.com/)。 根據 AppSource 上的 Microsoft [Dynamics 365](https://dynamics.microsoft.com/)和 Power 平臺，列出諮詢服務供應專案。 根據 Azure Marketplace 上的 Microsoft Azure 列出諮詢服務供應專案。 開始之前，請先[在合作夥伴中心建立商業 marketplace 帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)（如果您尚未這麼做）。 請確定您的帳戶已在商業 marketplace 方案中註冊。

建立供應專案之前，請先參閱[諮詢服務必要條件](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites)中的必要條件。

## <a name="publishing-benefits"></a>發佈的優點

發佈至商用 marketplace 的優點：

- 使用 Microsoft 品牌來宣傳您的公司。
- 透過 Azure Marketplace，有可能觸及超過100000000個 Office 365 和 Dynamics 365 AppSource 和超過200000個組織的使用者。
- 從這些市場獲得高品質的潛在客戶。
- 由 Microsoft 現場和電話銷售團隊推廣您的服務

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左側導覽功能表中，選取 [**商用 Marketplace** > **總覽**]。
3. 在 [總覽] 頁面上，選取 [ **+ 新增供應** > 專案**諮詢服務**]。

    ![說明左側導覽功能表。](./media/new-offer-consulting-service.png)

>[!NOTE]
>發行供應專案之後，在合作夥伴中心對其進行的編輯，只會在重新發佈供應專案後出現在店面中。 請務必在進行變更之後，一律重新發佈。

## <a name="new-offer"></a>新增供應項目

輸入**供應專案識別碼**。 這是您帳戶中每個供應專案的唯一識別碼。

- 在 marketplace 供應專案網址中，客戶可以看到此識別碼。
- 請一律使用小寫字母和數字。 它可以包含連字號和底線，但不能有空格，而且限制為50個字元。 例如，如果您輸入 [**測試-供應專案-1**]，則供應專案 web `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`位址會是。
- 選取 [**建立**] 之後，即無法變更供應專案識別碼。

輸入**供應專案別名**。 這是在合作夥伴中心使用供應專案的名稱。

- 此名稱不會在 marketplace 中使用，而且與向客戶顯示的供應專案名稱和其他值不同。
- 在您選取 [**建立**] 之後，即無法變更供應專案別名。

選取 [**建立**] 以產生供應專案並繼續。

## <a name="offer-setup"></a>供應專案設定

### <a name="connect-lead-management"></a>連接潛在客戶管理

當您使用合作夥伴中心將您的供應專案發佈至 marketplace 時，您_必須_將其連接到客戶關係管理（CRM）或行銷自動化系統。 這可讓您在有人對您的產品感興趣或使用時，立即收到客戶連絡人資訊。

1. 選取 **[連線]** ，指定您要讓我們傳送客戶潛在客戶的位置。 合作夥伴中心支援下列系統：

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
- [潛在客戶管理總覽](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)PDF （請確定您的快顯封鎖程式已關閉）

選取 [**儲存草稿**] 再繼續。

### <a name="properties"></a>屬性

此頁面可讓您設定諮詢服務提供最佳涵蓋的主要產品、設定諮詢服務類型，以及選擇適用的產品。

1. 從下拉式清單中選取**主要產品**。
2. 從下拉式清單中選取**諮詢服務類型**：

    - **評**量：評估客戶的環境，以判斷解決方案的適用性，並提供估計的成本和時間。
    - **簡報**：介紹解決方案或諮詢服務，使用架構、示範和客戶範例繪製客戶的關注。
    - **實作**：可進行完整安裝以產生完整有效的解決方案。 限制可在兩周內執行的解決方案。
    - **概念證明**：可以判斷解決方案是否符合客戶需求的有限範圍實作。
    - **研討會**：在客戶的內部部署上進行互動式的互動。 它可以包含以客戶的資料或環境為基礎的訓練、簡報、評量或示範。

3. 如果您選取了**Azure**的主要產品，請選取最多三個**解決方案區域**。 這讓 Azure Marketplace 的客戶更容易找到您的供應專案。 如果您未選擇 [Azure]，請略過此步驟。
4. 如果您選取了 Azure_以外_的主要產品，請選取最多三個**適用的產品**。 這讓 AppSource 中的客戶更容易找到您的供應專案。 如需詳細資訊，請參閱[Microsoft AppSource 諮詢服務清單指導方針](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409)（PDF）。
5. 最多可選取您供應專案適用的六個**產業**。 這可讓客戶更輕鬆地找到您的供應專案。
6. 最多可新增**三項**您的公司所獲的專長認證，以顯示在您的諮詢服務供應專案清單中。 除了 Azure 專家 Msp 和 Azure 網路 Msp 以外，至少需要一個專長認證。

選取 [**儲存草稿**] 再繼續。

## <a name="offer-listing"></a>供應專案清單

在這裡，您將定義在 marketplace 中顯示的供應專案詳細資料。 這包括供應專案名稱、描述、影像等等。 設定此供應專案時，請務必遵循 [[商業 marketplace 認證原則] 頁面](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services)上詳述的原則。

> [!NOTE]
> 如果供應專案描述的開頭是片語，則不需要提供詳細資料， &quot;此應用程式僅適用于 [非英文語言]。&quot;也可以提供有用的連結，以不同于供應專案清單詳細資料所使用的語言來提供內容。

### <a name="name"></a>名稱

您在此處輸入的名稱會顯示為您供應專案的標題。 當您建立供應專案時，此欄位會預先填入您在 [**供應專案別名**] 方塊中輸入的文字。 您可以稍後變更此名稱。

名稱：

- 可以是商標（您也可以包含商標或著作權符號）。
- 長度不能超過50個字元。
- 不能包含 emoji。

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
- 不依賴特性和功能來銷售您的產品。 相反地，請專注于您的供應專案提供的值。
- 請嘗試使用產業專屬詞彙或以權益為基礎的用語。

若要讓您的供應專案描述更吸引人，請使用 rtf 編輯器來套用格式設定。

![使用 rich 文字編輯器](./media/rich-text-editor.png)

| <center>變更文字格式 | <center>新增專案符號或編號 | <center>新增或移除文字縮排 |
| --- | --- | --- |
| <center>![使用 rich 文字編輯器來變更文字格式](./media/text-editor3.png) |  <center>![使用 rich 文字編輯器加入清單](./media/text-editor4.png) |  <center>![使用 rich 文字編輯器縮排](./media/text-editor5.png) |

### <a name="keywords"></a>關鍵字

輸入最多三個與您的主要產品和諮詢服務相關的搜尋關鍵字。 這些可讓您更輕鬆地找到您的供應專案。

### <a name="duration"></a>Duration

設定此參與與客戶的預期持續時間。

### <a name="contact-information"></a>連絡資訊

您必須提供**主要**和**次要連絡人**的名稱、電子郵件和電話號碼。 客戶不會看到這則資訊。 可供 Microsoft 使用，並可提供給雲端解決方案提供者（CSP）合作夥伴。

### <a name="supporting-documents"></a>支援檔

最多可為您的供應專案新增三個（但至少一個）支援 PDF 檔。

### <a name="marketplace-images"></a>Marketplace 映射

提供要與您的供應專案搭配使用的標誌和影像。 所有影像都必須是 .png 格式。 模糊影像將會遭到拒絕。

>[!Note]
>如果您在上傳檔案時發生問題，請確定您的區域網路不會https://upload.xboxlive.com封鎖合作夥伴中心所使用的服務。

#### <a name="store-logos"></a>Microsoft Store標誌。

請在下列每個圖元大小中提供供應專案標誌的 PNG 檔案：

- **小型（48 x 48）**
- **大型（216 x 216）**

所有標誌都是必要的，並且會用於 marketplace 清單中的不同位置。

#### <a name="screenshots-optional"></a>螢幕擷取畫面（選擇性）

新增最多五個螢幕擷取畫面，以顯示供應專案的運作方式。 每個都必須是 1280 x 720 圖元大小和 PNG 格式。

#### <a name="videos-optional"></a>影片（選擇性）

最多可新增四個示範您供應專案的影片。 輸入影片的名稱、其網址（URL），以及影片的縮圖 PNG 影像（大小為 1280 x 720 圖元）。

選取 [**儲存草稿**] 再繼續。

## <a name="pricing-and-availability"></a>價格與可用性

在這裡，您將定義專案，例如價格、市場和私密金鑰。

1. **市場**：將提供您供應專案的市場。 您只能為每個供應專案選取一個市場。
    1. 若為美國或加拿大市場，請選取 [**編輯州**（或**省**）] 來指定供應專案的可用位置。
2. **預覽物件**：設定用來為您的供應專案設定私人物件的**隱藏金鑰**。
3. **定價**：指定您的供應專案是**免費**或**付費**供應專案。

    > [!NOTE]
    > 諮詢服務供應專案僅適用于清單。 任何交易都會直接在商業 marketplace 外部發生。

4. 若為付費供應專案，請指定**價格和貨幣**，以及價格是**固定**或**預估**。 若預估，您必須在描述中指定會影響價格的因素。
5. 選取 [**儲存草稿**] 再繼續。

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
