---
title: 建立諮詢服務供應項目 - Microsoft 商業市集
description: 了解如何使用合作夥伴中心將諮詢服務供應項目發佈至 Microsoft AppSource 或 Azure Marketplace。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: f8ddbc66b8e7775fa35d2407bf3aa8614fe8e835
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606164"
---
# <a name="create-a-consulting-service-offer"></a>建立諮詢服務供應項目

本文說明如何將諮詢服務供應項目發佈至 [Microsoft AppSource](https://appsource.microsoft.com/) 或 [Azure Marketplace](https://azuremarketplace.microsoft.com/)。 根據 Microsoft [Dynamics 365](https://dynamics.microsoft.com/) 和 AppSource 上的Power Platform，列出諮詢服務供應項目。 根據 Azure Marketplace 上的 Microsoft Azure 列出諮詢服務供應項目。 開始之前，請先[在合作夥伴中心建立商業市集](create-account.md) (如果您尚未這麼做)。 請確定您已在商業市集方案中註冊帳戶。

建立供應項目之前，請先檢閱[諮詢服務必要條件](consulting-service-prerequisites.md)中的必要條件。

## <a name="publishing-benefits"></a>發佈的優點

發佈至商業市集的優點：

- 使用 Microsoft 品牌來推廣您的公司。
- 可能接觸到 AppSource 上超過 100 萬個 Office 365 和 Dynamics 365 使用者，且透過 Azure Marketplace 接觸 200,000 個以上的組織。
- 從這些市集獲得高品質潛在客戶。
- 讓 Microsoft 現場和電話銷售團隊推廣您的服務

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左側導覽功能表中，選取 [商業市集] > [概觀]。
3. 在 [概觀] 頁面上，選取 [+ 新增供應項目] > [諮詢服務]。

    ![說明左側導覽功能表。](./media/new-offer-consulting-service.png)

>[!NOTE]
>供應專案發佈之後，在合作夥伴中心中進行的編輯，只會在重新發佈供應專案之後出現在線上商店中。 請務必在進行變更之後，一律重新發佈。

## <a name="new-offer"></a>新增供應項目

輸入**供應項目識別碼**。 這是您帳戶中每個供應項目的唯一識別碼。

- 在市集供應項目的網址中，客戶可以看到此識別碼。
- 請一律使用小寫字母和數字。 其可以包含連字號和底線，但不能有空格，且限制為 50 個字元。 例如，如果您輸入 **test-offer-1**，供應項目網址將為 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
- 選取 [建立] 之後，即無法變更供應項目識別碼。

輸入**供應項目別名**。 這是用於合作夥伴中心內供應項目的名稱。

- 此名稱不會在市集中使用，且與向客戶顯示的供應項目名稱和其他值不同。
- 在您選取 [建立] 之後，就無法變更供應項目別名。

選取 [建立] 以產生供應項目並繼續。

## <a name="offer-setup"></a>供應項目設定

### <a name="connect-lead-management"></a>連線潛在客戶管理

當您使用合作夥伴中心將您的供應項目發佈到市集時，您「必須」將其連線到客戶關係管理 (CRM) 或行銷自動化系統。 這可讓您在有人對您的產品感興趣或使用時，立即收到客戶連絡人資訊。

1. 選取 [連線] 以指定您希望我們傳送客戶潛在客戶的位置。 合作夥伴中心支援下列系統：

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > 如果以上未列出您的 CRM 系統，請使用 [Azure 資料表](commercial-marketplace-lead-management-instructions-azure-table.md)或 [Https 端點](commercial-marketplace-lead-management-instructions-https.md)來儲存客戶潛在客戶資料，然後將資料匯出至您的 CRM 系統。

2. 在合作夥伴中心內發佈時，將供應項目連線到潛在客戶目的地。
3. 確認已正確設定與潛在客戶目的地的連線。 在合作夥伴中心發佈之後，我們會驗證連線並將測試潛在客戶傳送給您。 在上架前預覽供應項目時，您也可以嘗試在預覽環境中自行購買供應項目，藉此測試您的潛在客戶連線。
4. 確認潛在客戶目的地的連線保持在最新狀態，您就不會遺失任何潛在客戶。

以下是一些額外的潛在客戶管理資源：

- [潛在客戶管理概觀](commercial-marketplace-get-customer-leads.md)
- [潛在客戶管理常見問題](../lead-management-for-cloud-marketplace.md#frequently-asked-questions)) 
- [常見的潛在客戶設定錯誤](../lead-management-for-cloud-marketplace.md#publishing-config-errors)) 
- [潛在客戶管理概觀](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (請確定您已關閉快顯封鎖程式)

選取 [儲存草稿] 後再繼續。

### <a name="properties"></a>屬性

此頁面可讓您設定諮詢服務供應項目最佳涵蓋的主要產品、設定諮詢服務類型，以及選擇適用的產品。

1. 從下拉式清單中選取 [主要產品]。
2. 從下拉式清單中選取 [諮詢服務類型]：

    - **評估**：可以評估客戶的環境，以判斷解決方案的適用性，並提供估計的成本和時間。
    - **簡報**：可以介紹解決方案或諮詢服務，使用架構、示範和客戶範例引起客戶的興趣。
    - **實作**：完整安裝以產生完全可行的解決方案。 限制在可於 2 週以內完成實作的解決方案。
    - **概念證明**：可以判斷解決方案是否符合客戶需求的有限範圍實作。
    - **研討會**：在客戶場地進行的互動式交流。 其可以包含以客戶資料或環境為基礎的訓練、簡報、評量或示範。

3. 如果您選取 **Azure** 作為主要產品，請選取最多三個 **解決方案區域**。 這些可讓 Azure Marketplace 的客戶更輕鬆地找到您的供應項目。 如果您未選擇 Azure，請略過此步驟。

    - 分析
    - 應用程式現代化
    - 封存
    - AI + 機器學習服務
    - Backup
    - 巨量資料
    - 資料平台
    - 資料中心管理
    - DevOps
    - 災害復原
    - 身分識別
    - 物聯網
    - 遷移
    - 網路
    - 安全性
    - 儲存體

1. 如果您選取 **Azure** 作為主要產品，您可以選擇最多六個 **產業**。 這些可讓 Azure Marketplace 的客戶更輕鬆地找到您的供應項目。 請參閱供應專案清單 [最佳做法](../gtm-offer-listing-best-practices.md)中的完整產業清單。 如果您未選擇 Azure，請略過此步驟。
1. 如果您選取了 Azure「以外」的主要產品，請選取最多三個**適用的產品**。 這些可讓 AppSource 的客戶更輕鬆地找到您的供應項目。 如需詳細資訊，請參閱 [Microsoft AppSource 諮詢服務清單指導方針](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF)。
1. 如果您選取了 Azure *以外* 的主要產品，則可以選擇為每個產業選取最多兩個 **產業** 和兩個 **縱向** 。 這些可讓 AppSource 的客戶更輕鬆地找到您的供應項目。 請參閱 [供應專案清單最佳做法](../gtm-offer-listing-best-practices.md)的完整產業和縱向清單。
1. 新增最多三個貴公司已取得的 [專長認證]，以顯示在您的諮詢服務供應項目清單中。 除了 Azure Expert MSP 和 Azure 網路 MSP 以外，至少需要一個專長認證。

選取 [儲存草稿] 後再繼續。

## <a name="offer-listing"></a>供應項目清單

您將在這裡定義顯示於市集中的供應項目詳細資料。 這包括供應項目名稱、描述、映像等等。 設定此供應項目時，請務必遵循[商業市集認證原則頁面](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services)中詳述的原則。

> [!NOTE]
> 如果供應項目描述開頭為以下詞句：&quot;此應用程式僅適用於 [非英文語言]。&quot;，則供應項目詳細資料不需要為英文。也可利用不同於供應項目清單詳細資料中所使用的語言，來提供供應項目內容的有用連結。

以下是供應專案資訊在 Azure Marketplace 中的顯示方式範例 (任何列出的價格僅供範例用途，不適合反映實際成本) ：

:::image type="content" source="media/example-consulting-service-offer.png" alt-text="說明這項供應專案在 Azure Marketplace 中的顯示方式。":::

#### <a name="call-out-descriptions"></a>撥出描述

1. 標誌
2. 價格
3. 解決方案區域
4. 產業
5. 供應項目名稱
6. 摘要
7. 描述
8. 螢幕擷取畫面/影片

<br>以下是供應專案資訊在 Microsoft AppSource 中的顯示方式範例 (任何列出的價格僅供範例用途，不適合反映實際成本) ：

:::image type="content" source="media/example-consulting-service-offer-appsource.png" alt-text="說明這項供應專案在 Microsoft AppSource 中的顯示方式。":::

#### <a name="call-out-descriptions"></a>撥出描述

1. 標誌
2. 價格
3. 產品
4. 產業
5. 供應項目名稱
6. 摘要
7. 描述
8. 螢幕擷取畫面/影片
9. 文件

### <a name="name"></a>名稱

您在此處輸入的名稱會顯示為您供應項目的標題。 當您建立供應項目時，此欄位會預先填入您在 [供應項目別名] 方塊中輸入的文字。 您稍後可以變更此名稱。

名稱：

- 可以是商標 (您也可以包含商標或著作權符號)。
- 長度不可超過 50 個字元。
- 不可包含表情圖示。

### <a name="search-results-summary"></a>搜尋結果摘要

提供供應項目的簡短描述。 長度最多可有 100 個字元，並用於市集搜尋結果。

### <a name="description"></a>描述

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="keywords"></a>關鍵字

輸入最多三個與您主要產品和諮詢服務相關的搜尋關鍵字。 這些將方便您尋找供應項目。

### <a name="duration"></a>Duration

設定與客戶交流的預期持續時間。

### <a name="contact-information"></a>連絡資訊

您必須提供 [主要] 以及 [次要連絡人] 的名稱、電子郵件和電話號碼。 客戶不會看到這則資訊。 可供 Microsoft 使用，並可提供給雲端解決方案提供者 (CSP) 合作夥伴。

### <a name="supporting-documents"></a>支援文件

最多可為您的供應項目新增三個 (但至少一個) 支援 PDF 文件。

### <a name="marketplace-images"></a>Marketplace 映像

提供要與您的供應項目搭配使用的標誌和影像。 所有影像都必須是 .png 格式。 模糊影像會被拒絕。

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

>[!Note]
>如果您在上傳檔案時遇到問題，請確定您的區域網路不會封鎖合作夥伴中心所使用的 https://upload.xboxlive.com 服務。

#### <a name="store-logos"></a>儲存標誌

請以下列每個像素大小提供供應項目標誌的 PNG 檔案：

- **小型 (48 x 48)**
- **大型 (216 x 216)**

所有標誌都是必要的，且在市集清單中會用於不同位置。

-   Azure Marketplace 搜尋結果和 Microsoft AppSource [搜尋結果] 頁面上搜尋結果中會出現小型標誌 (48 x 48 圖元) 。 
-   216 x 216 與 350 x 350 圖元之間的大型標誌 () 會出現在 Azure Marketplace 和 Microsoft AppSource 的供應專案清單頁面上。

#### <a name="screenshots-optional"></a>螢幕擷取畫面 (選擇性)

新增最多五個螢幕擷取畫面，以顯示供應項目的運作方式。 每個的大小都必須為 1280 x 720 像素且為 PNG 格式。

#### <a name="videos-optional"></a>影片 (選擇性)

新增最多四個示範您供應項目的影片。 輸入影片的名稱、其網址 (URL)，以及影片的縮圖 PNG 映像 (大小為 1280 x 720 像素)。

選取 [儲存草稿] 後再繼續。

## <a name="pricing-and-availability"></a>價格與可用性

您將在這裡定義元素，例如價格、市場和私密金鑰。

1. **市場**：設定您供應項目可供使用的市場。 您只能為每個供應項目選取一個市場。
    1. 針對美國或加拿大市場，請選取 [編輯州] (或 [省份]) 來指定供應項目的可用位置。
2. **預覽對象**：設定用來為您供應項目設定私人對象的 [隱藏金鑰]。
3. **價格**：指定您的供應項目是 [免費] 還是 [付費] 的供應項目。

    > [!NOTE]
    > 諮詢服務供應項目僅適用於清單。 任何交易都會直接在商業市集外部發生。

4. 若為付費供應項目，請指定 [價格和貨幣] 以及價格是否為 [固定] 還是 [預估]。 若為預估，您必須在描述中指定會影響價格的因素。
5. 選取 [儲存草稿] 後再繼續。

## <a name="review-and-publish"></a>檢閱與發佈

完成供應項目的所有必要區段之後，您可以提交供應項目來進行檢閱和發佈。

1. 當您準備好發佈諮詢服務供應項目時，請按一下 [檢閱與發佈]。
2. 請檢閱最終提交頁面的詳細資料。
3. 必要時，如果您認為您的供應項目有任何詳細資料需要說明，請寫下備註給認證小組。
4. 當您準備好時，請選取 [提交]。
5. [供應項目概觀] 頁面會顯示您供應項目所在的發佈階段。

如需您可預期供應項目會在每個發佈階段中存在多久的詳細資訊，請參閱[檢查您商業市集供應項目的發佈狀態](publishing-status.md)。

## <a name="update-your-existing-consulting-service-offers"></a>更新您現有的諮詢服務供應項目

- [更新商業市集中的現有供應項目](update-existing-offer.md)
