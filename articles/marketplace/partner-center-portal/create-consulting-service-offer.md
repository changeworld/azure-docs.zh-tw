---
title: 在合作夥伴中心 - Azure 應用商店中創建諮詢服務產品
description: 瞭解如何使用合作夥伴中心將諮詢服務產品發佈到 Azure 應用商店或 AppSource。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: f1354ce824bc4e57ab55846c714d4a0a73a46966
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80873815"
---
# <a name="create-a-consulting-service-offer"></a>建立諮詢服務產品

> [!IMPORTANT]
> 我們將您的諮詢服務服務服務從雲合作夥伴門戶遷移到合作夥伴中心。 在遷移產品/服務之前,請按照 Azure[和 Dynamics 365 雲合作夥伴門戶諮詢服務產品提供](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer)的說明進行管理產品/服務。

本文介紹如何將諮詢服務產品發佈到[Azure 應用商店](https://azuremarketplace.microsoft.com/)或[AppSource](https://appsource.microsoft.com/)。 基於微軟[動態365](https://dynamics.microsoft.com/)和AppSource上的電源平臺提供諮詢服務。 基於 Azure 應用商店上的 Microsoft Azure 的諮詢服務產品。

要在 Azure 應用商店或 AppSource 諮詢服務中創建諮詢服務產品,您必須首先[在合作夥伴中心中擁有發行者帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account),並且必須在商業市場計劃中註冊您的帳戶。 在建立產品/服務之前,請查看[諮詢服務先決條件中的先決條件](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites)。

## <a name="publishing-benefits"></a>發佈的優點

發佈到商業市場的好處:

- 使用 Microsoft 品牌推廣您的公司。
- AppSource 上可能覆蓋超過 1 億個 Office 365 和 Dynamics 365 使用者,並通過 Azure 應用商店覆蓋 200,000 多個組織。
- 從這些市場獲得高質量的潛在客戶。
- 讓 Microsoft 現場和電話銷售團隊推廣您的服務

## <a name="create-a-new-offer"></a>建立新的供應項目

滿足上述要求后,請按照以下步驟創建諮詢服務產品。

1. 登錄到[合作夥伴中心](https://partner.microsoft.com),然後從頂部功能表中選擇 **「儀錶板**」。
2. 在左導航欄中,選擇 **「商業市場**」,然後選擇 **「概述**」。

    :::image type="content" source="media/cs-menu-overview.png" alt-text="說明商業市場的選單":::

3. 選擇 **= 新優惠**,然後選擇**諮詢服務**。

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="說明創建新產品/服務/服務/服務按鈕。":::

4. 輸入**優惠代碼**。 這是您帳戶中每個產品/服務的唯一標識碼。

    - 此 ID 在市場產品/服務的網站位址中對客戶可見。
    - 僅使用小寫字母、數位、破折號和下劃線,但不使用空格。 長度限制為 50 個字元。 例如,如果輸入**測試-產品/服務 -1,** 則產品`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`/服務網址將為 。
    - 選擇 「**建立**」 後,&#39;无法更改优惠 ID。

5. 輸入**產品/服務別名**。 這是用於指合作夥伴中心產品/服務的名稱。

    - 此名稱&#39;在市場上不使用。 它&#39;與客戶顯示的要約名稱和其他值不同。 您可以使用此欄位為產品/服務分配更有用的名稱,該名稱對於內部標識產品/服務更有用;它不向客戶顯示。
    - 選擇 「**建立**」後,&#39;无法更改产品/服務別名。

輸入這兩個值後,選擇 **"創建**"以繼續到 **"產品/服務設置"** 頁。

## <a name="offer-setup"></a>優惠設定

輸入優惠 ID 和要約別名後,合作夥伴中心將創建草稿產品/服務並顯示 **「優惠設置**」頁面。 按照以下步驟設置您的產品/服務。

### <a name="connect-lead-management"></a>連線潛在客戶管理

使用合作夥伴中心向市場發佈產品/服務時,_必須_將其連接到客戶關係管理 (CRM) 或行銷自動化系統。 這樣,一旦有人對您的產品表示興趣或使用,您就會立即收到客戶聯繫資訊。

1. 選擇 **「連線」** 以指定您希望我們發送客戶潛在顧客的位置。 合作夥伴中心支援以下系統:

    - [客戶互動的動態 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics)
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > 如果 CRM 系統&#39;上面未列出,請使用[Azure 表](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)或[Hs 終結點](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)來儲存客戶潛在顧客資料,然後將資料匯出到 CRM 系統。

2. 在合作夥伴中心發佈時,請將您的產品/服務連接到潛在顧客目標。
3. 確認與潛在顧客目標的連接配置正確。 在合作夥伴中心發佈後,我們將&#39;驗證連接並向您發送測試線索。 在產品/服務上線之前預覽產品/服務時,還可以嘗試在預覽環境中自行購買產品/服務,以測試潛在顧客連接。
4. 確保與潛在顧客目標的連接保持更新,這樣您就不會丟失任何潛在顧客&#39;。

下面是一些額外的潛在客戶管理資源:

- [潛在客戶管理概述](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [潛在客戶管理常見問題集](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [常見潛在客戶設定錯誤](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [潛在客戶管理概述](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)PDF (確保彈出視窗封鎖程式關閉)

選擇 **"保存"草稿**,然後再繼續下一節"屬性"。

### <a name="properties"></a>屬性

此頁面允許您設置諮詢服務提供的最佳封面的主要產品,設置諮詢服務類型,並選擇適用的產品。

1. 從下拉清單中選擇**主要產品**。
2. 從下拉清單中選擇**諮詢服務類型**:

    - **評估**:評估客戶&#39;環境,以確定解決方案的適用性,並提供成本和時間的估計。
    - **簡介**:使用框架、演示和客戶範例,介紹解決方案或諮詢服務,以吸引客戶的興趣。
    - **實現**:一個完整的安裝,導致一個完全工作的解決方案。 限制在兩周或更短時間即可實施的解決方案。
    - **概念證明**:確定解決方案是否符合客戶需求的有限範圍實現。
    - **研討會**:在客戶&#39;的場所進行互動互動。 它可以包括基於客戶&#39;的數據或環境的培訓、簡報、評估或演示。

1. 如果選擇**Azure**的主要產品,請選擇最多三個**解決方案區域**。 這些功能使 Azure 應用商店中的客戶更容易找到您的產品/服務。 如果未&#39;未選擇 Azure,請跳過此步驟。
2. 如果選擇了 Azure_以外的_主要產品,請選擇最多三個**適用產品**。 這些使 AppSource 中的客戶更容易找到您的產品/服務。 有關詳細資訊,請參閱[Microsoft AppSource 諮詢服務列表指南](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409)(PDF)。
3. 最多選擇您優惠的六**個產業**。 這將使客戶更容易找到您的產品/服務。
4. 將貴公司獲得的三**項能力**添加到諮詢服務產品清單中。 除了 Azure 專家 MSP&#39;和 Azure 網路 MSP&#39;, 至少需要一項能力。

選擇 **"保存"草稿**,然後再繼續下一節"優惠清單"。

## <a name="offer-listing"></a>優惠清單

在這裡&#39;定義市場上顯示的報價詳細資訊。 這包括產品/服務名稱、說明、圖像等。 在配置此產品/服務時,請務必遵循[Microsoft&#39;策略页上](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services)詳細說明的策略。

> [!NOTE]
> 如果要約說明以短語開頭,則報價詳細資訊&#39;不需要英文,&quot;此應用程式僅提供 [非英語語言]。&quot;提供有用連結&#39;提供與優惠清單詳細資訊中所用語言&#39;內容也沒關係。

### <a name="name"></a>名稱

您在此處輸入的名稱顯示為產品的標題。 此欄位預先填充您在建立產品/服務時在 **「產品/服務」別名**框中輸入的文字。 您可以稍後變更此名稱。

名稱:

- 可以註冊商標(您可以包括商標或版權符號)。
- 不能&#39;不超過50個字符長。
- 不能&#39;不包括表情符號。

### <a name="search-results-summary"></a>搜尋結果摘要

提供您的優惠的簡短說明。 這最多可達 100 個字元,並且用於市場搜尋結果。

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
  - 從產品/服務中受益的用戶類型。
  - 客戶需求或發出產品/服務位址。
- 請記住,搜尋結果中可能會顯示前幾個句子。
- 不要&#39;依賴特性和功能來銷售您的產品。 相反,關注您的產品/服務提供的價值。
- 嘗試使用行業特定的詞彙或基於利益的措辭。

為了使您的描述更具吸引力,請使用豐富的文本編輯器來設置描述格式。 富文字編輯器允許您添加數位、專案符號、粗體、斜體和縮進,以使描述更具可讀性。

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="說明豐富的文本編輯器以編寫要約說明。" border="false":::

### <a name="keywords"></a>關鍵字

最多輸入三個與您的主要產品和諮詢服務相關的搜索關鍵字。 這些將更容易找到您的報價。

### <a name="duration"></a>Duration

設置與客戶的此參與的預期持續時間。

### <a name="contact-information"></a>連絡資訊

您必須提供**主要**和**次要連絡人**的名稱、電子郵件和電話號碼。 此資訊不會向客戶顯示&#39;。 它可供 Microsoft 使用,並可能提供給雲端解決方案供應商 (CSP) 合作夥伴。

### <a name="supporting-documents"></a>證明檔案

為您的優惠添加最多三個(但至少一個)支援 PDF 文檔。

### <a name="marketplace-images"></a>市場映射

提供徽標和圖像,以便與您的產品配合使用。 所有圖像必須採用 .png 格式。 模糊圖像將被拒絕。

#### <a name="store-logos"></a>Microsoft Store標誌。

在以下每個像素大小中提供&#39;的 /png 產品/服務檔案的 :png 檔:

- **小 (48 x 48)**
- **大 (216 x 216)**

所有徽標都是必需的,並且在市場清單中的不同位置使用。

#### <a name="screenshots-optional"></a>螢幕擷取(選擇性的)

最多添加五個屏幕截圖,顯示您的優惠如何工作。 每個圖元的大小和 .png 格式必須為 1280 x 720 圖元。

#### <a name="videos-optional"></a>視訊(選擇性的)

最多添加四個視頻,演示您的優惠。 輸入影片&#39;名稱、網址 (URL) 和影片的縮略圖 .png 圖像,大小為 1280 x 720 圖元。

選擇 **"保存"草稿**,然後再繼續下一節"定價和可用性"。

## <a name="pricing-and-availability"></a>價格與可用性

在這裡,您將定義定價、市場和私鑰等元素。

1. **市場**: 設定您的報價將可用的市場. 您只能根據報價選擇一個市場。
    1. 對於美國或加拿大市場,選擇 **「編輯」州**(或**省**)以指定您的優惠可用位置。
2. **預覽受眾**:設定用於為產品/服務設定私人受眾的**隱藏金鑰**。
3. **定價**:指定您的優惠是**免費**還是**付費**優惠。

    > [!NOTE]
    > 諮詢服務優惠僅適用於上市。 任何交易都將直接發生在商業市場之外。

4. 對於已付要約,請指定**價格和貨幣**以及價格是**固定**價格還是**估計**。 如果"估計",則必須在說明中指定哪些因素會影響價格。
5. 選擇 **「保存草稿**」。

## <a name="review-and-publish"></a>稽核和發佈

完成產品/服務所需的所有部分後,您可以提交報價以供審核和發佈。

1. 當您準備好發佈諮詢服務產品時,請按下 **「審閱」併發表**。
2. 查看最終提交頁面上的詳細資訊。
3. 如有必要,如果您認為產品/服務的任何詳細資訊需要解釋,請向認證團隊寫一份便條。
4. 準備就緒后,選擇"**提交**"。
5. **"產品/服務概述"** 頁顯示您的產品/服務處於哪些發佈階段。

有關您希望產品/服務在每個發佈階段的時間的詳細資訊,[請參閱檢查商業市場產品/服務的狀態](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status)。

## <a name="update-your-existing-consulting-service-offers"></a>更新您現有的諮詢服務優惠

- [更新商業市場中的現有產品/服務](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
