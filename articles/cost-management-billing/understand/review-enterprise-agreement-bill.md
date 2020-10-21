---
title: 檢閱您的 Azure Enterprise 合約帳單
description: 學習如何閱讀並了解 Azure Enterprise 合約的使用量和帳單。
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: d707051e20b4ce9d1289557aec6f8256c2e6bfba
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150067"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>了解您的 Azure Enterprise 合約帳單

當 Azure Enterprise 合約客戶超過組織的信用額度，或使用信用額度未涵蓋的服務時，他們就會收到發票。

貴組織的信用額度包含您的承諾用量金額。 承諾用量金額是貴組織針對 Azure 服務使用量所預付的金額。 您可以連絡您的 Microsoft 客戶經理或轉銷商，在 Enterprise 合約中增加承諾用量金額的資金。

本教學課程僅適用於具有 Azure Enterprise 合約的 Azure 客戶。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 檢閱已開立發票的費用
> * 檢閱服務的超額費用
> * 檢閱 Marketplace 發票

## <a name="prerequisites"></a>Prerequisites

若要檢閱並確認發票上的費用，您必須是企業系統管理員。 如需詳細資訊，請參閱[了解 Azure 中的 Azure Enterprise 合約系統管理角色](../manage/understand-ea-roles.md)。 如果您不知道誰是貴組織的企業系統管理員，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="review-invoiced-charges-for-most-customers"></a>檢閱多數客戶的已開立發票費用

本節不適用於澳洲、日本或新加坡的 Azure 客戶。

若在計費週期內發生下列任何事件，您會收到 Azure 發票：

- **服務超額**：組織的使用量費用超過您的信用額度餘額。
- **個別計費的費用**：信用額度未涵蓋貴組織所使用的服務。 無論點數餘額多寡，都會收到下列服務的發票：
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - 已註冊的使用者
    - Openlogic
    - 遠端存取權限 XenApp Essentials 已註冊的使用者
    - Ubuntu Advantage
    - Visual Studio Enterprise (每月)
    - Visual Studio Enterprise (每年)
    - Visual Studio Professional (每月)
    - Visual Studio Professional (每年)
- **Marketplace 費用**：貴組織的點數不會支付 Azure Marketplace 購買項目和使用量。 因此，無論點數餘額多寡，您都會收到 Marketplace 費用的發票。 在企業版入口網站中，企業系統管理員可以啟用和停用 Marketplace 購買。

您的發票會先顯示 Azure 使用量費用以及相關的成本，再來列出所有 Marketplace 費用。 如果您尚有點數餘額，則會套用至 Azure 使用量，而發票會顯示 Azure 使用量和 Marketplace 使用量，而不會有任何費用。

您可以比較在企業版入口網站的 [報表] > [使用量摘要] 中顯示的合計總金額與您的 Azure 發票。 [使用量摘要] 中的金額不含稅金。

登入 [Azure EA 入口網站](https://ea.azure.com)。 然後，選取 [報告]。 在索引標籤右上角，將檢視從 **M** 切換為 **C**，以符合發票上的期間。  

![在 [使用量摘要] 中顯示 M + C 選項的螢幕擷取畫面。](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

[總使用量] 與 [Azure Marketplace] 的合計金額應符合發票上的 [應收金額總計]。 若要取得關於費用的詳細資訊，請移至 [下載使用量]。  

![顯示 [下載使用量] 索引標籤的螢幕擷取畫面](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>檢閱其他客戶的已開立發票費用

本節僅適用於澳洲、日本或新加坡的 Azure 客戶。

發生下列任何事件時，您會收到一或多張 Azure 發票：

- **服務超額**：組織的使用量費用超過您的信用額度餘額。
- **個別計費的費用**：信用額度未涵蓋貴組織所使用的服務。 系統已為您開立下列服務的發票：
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - 已註冊的使用者
    - Openlogic
    - 遠端存取權限 XenApp Essentials 已註冊的使用者
    - Ubuntu Advantage
    - Visual Studio Enterprise (每月)
    - Visual Studio Enterprise (每年)
    - Visual Studio Professional (每月)
    - Visual Studio Professional (每年)
- **Marketplace 費用**：貴組織的點數不會支付 Azure Marketplace 購買項目和使用量，這兩者會個別計費。 在企業版入口網站中，企業系統管理員可以啟用和停用 Marketplace 購買。

如果您在計費期間因服務超額而產生費用，且同時有個別計費的費用，您將會收到一張發票。 其中包含這兩種類型的費用。 Marketplace 費用的發票一律會單獨開立。

## <a name="review-service-overage-charges-for-other-customers"></a>檢閱其他客戶的服務超額費用

本節內容僅適用於澳洲、日本或新加坡的 Azure 客戶。

在企業版入口網站中，比較您在 [報表] > [使用量摘要] 中顯示的總使用量金額與服務超額發票。 服務超額發票包含超過組織信用額度的使用量，和/或信用額度未涵蓋的服務。 **使用量摘要**的金額不含稅。

登入 [Azure EA 入口網站](https://ea.azure.com)，然後選取 [報告]。 在索引標籤右上角，將檢視從 **M** 切換為 **C**，以符合發票上的期間。  

![在 [使用量摘要] 中顯示 M + C 選項的螢幕擷取畫面。](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

[總使用量] 金額應該符合您服務超額發票上的 [應收金額總計]。 若要取得費用的詳細資訊，請前往 [下載使用量] > [進階報表下載]。 此報表不包含保留的稅金或費用，或是 Marketplace 費用。  

![在 [下載使用量] 索引標籤上顯示 [進階報表下載] 的螢幕擷取畫面。](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

下表列出發票上和企業版入口網站中 [使用量摘要] 上所顯示的詞彙和說明：

|發票詞彙|使用量摘要詞彙|描述|
|---|---|---|
|應收金額總計|總使用量|套用信用額度之前針對特定期間的稅前使用量費用總計。|
|已使用的承諾用量|已使用的承諾用量|在該特定期間內套用的信用額度。|
|銷售總額|總超額|超過您信用額度金額的使用量費用總計。 此金額不含稅。|
|稅額|不適用|適用於特定期間之銷售總額的稅金。|
|總金額|不適用|在套用信用額度並增加稅金之後發票的應付金額。|

### <a name="review-marketplace-invoice"></a>檢閱 Marketplace 發票

本節內容僅適用於澳洲、日本或新加坡的 Azure 客戶。

在企業入口網站的 [報表] > [使用量摘要] 上，比較您的 Azure Marketplace 總計和 Marketplace 發票。 Marketplace 發票僅適用於 Azure Marketplace 購買和使用量。 **使用量摘要**上的數量已包含由發行者決定的稅金。

登入 [Enterprise 入口網站](https://ea.azure.com)，然後選取 [報告]。 在索引標籤右上角，將檢視從 **M** 切換為 **C**，以符合發票上的期間。  

![在 [使用量摘要] 上顯示 M + C 選項的螢幕擷取畫面。](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

**Azure Marketplace** 總計應該符合 Marketplace 發票上的**銷售總額**。 若要取得依使用量計算的費用詳細資訊，請前往 [下載使用量]。 在 [Marketplace 費用] 下方，選取 [下載]。 Marketplace 價格包含發行者所決定的稅額。 客戶不會收到來自發行者的個別發票，以收集交易的稅額。

![在 Marketplace 費用下方顯示下載選項的螢幕擷取畫面。](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="view-price-sheet-information"></a>檢視價位表資訊

企業系統管理員可以檢視與他們 Azure 服務註冊相關聯的價格清單。

若要檢視目前的價位表：

1. 在 Azure 企業版入口網站中選取 [報表]，然後選取 [價位表]。
2. 檢視價位表，或選取 [下載]。

![顯示價位表資訊的範例](./media/review-enterprise-agreement-bill/ea-create-view-price-sheet-information.png)

若要下載價格記錄清單：

1. 在 Azure 企業版入口網站中選取 [報表]，然後選取 [下載使用量]。
2. 下載價位表。

![顯示如何下載較舊價位表的範例](./media/review-enterprise-agreement-bill/create-ea-view-price-sheet-download-historical-price-list.png)

價格差異的一些原因：

- 先前註冊和新註冊之間的價格可能已變更。 發生價格變更的原因是，價格是在合約開始日期到結束日期之間，針對特定註冊制定的。
- 當您轉移到新的註冊時，價格因為新合約而產生變更。 價格是由價位表所定義，新註冊中的價格可能較高。
- 如果註冊進入延長的期限，則價格也會變更。 價格會變更為隨用隨付費率。

## <a name="request-detailed-usage-information"></a>要求詳細的使用量資訊

企業系統管理員可以在 Azure 企業版入口網站中檢視其使用量資料的摘要、已使用的承諾用量金額，以及與其他使用量相關聯的費用。 費用會顯示在所有帳戶和訂用帳戶的摘要層級上。

若要檢視特定帳戶的詳細使用量，請移至 [報表] > [下載使用量]，來下載使用量詳細資料報表。

> [!NOTE]
> 使用量詳細資料報告不包含任何適用的稅金。 從使用量發生到使用量在報表上反映時，可能會有最多 8 小時的延遲時間。

針對間接註冊，您的合作夥伴必須啟用標記功能，才能看到任何成本相關資訊。

## <a name="reports"></a>報表

企業系統管理員可以在 Azure 企業版入口網站中檢視其使用量資料的摘要、已使用的承諾用量金額，以及與其他使用量相關聯的費用。 費用會顯示在所有帳戶和訂用帳戶的摘要層級上。

### <a name="azure-enterprise-reports"></a>Azure 企業版報告

- 使用量摘要和圖表
- 服務使用量報表
- 餘額和費用報表
- 使用量詳細資料報表
- Azure Marketplace 費用報表
- 價位表
- 進階報表下載
- CSV 報表格式

### <a name="to-view-the-usage-summary-reports-and-graphs"></a>若要檢視使用量摘要報告和圖表：

1. 前往 Azure 企業版入口網站。
1. 在左窗格中選取 [報告]。
1. 選取 [使用量摘要] 索引標籤。
1. 從左上方的日期範圍功能表中選取承諾用量期間。
1. 選取圖表上的期間或月份來檢視其他詳細資料。
1. 在此索引標籤上，您可以：
   - 檢視每月使用量的圖表，其中包含使用量明細、服務的超額部分、個別計費的費用和 Azure Marketplace 費用。
   - 依圖表下方的部門、帳戶和訂用帳戶進行篩選。
   - 在 [依服務收費] 明細和 [依階層收費] 明細之間做切換。
   - 檢視 Azure 服務、個別計費費用及 Azure Marketplace 費用的詳細資料。

## <a name="service-usage-report"></a>服務使用量報表

[服務使用量報表] 頁面可讓企業系統管理員檢視其服務使用量資料的摘要。 使用量會顯示在所有帳戶和訂用帳戶的摘要層級上。 若要檢視詳細的使用量，您可以依據帳戶或訂用帳戶來篩選報表。

> [!NOTE]
> 產生使用量的日期與使用量反映在此報表中的時間，可能會有最多五天的延遲。

### <a name="to-view-the-report"></a>若要檢視報表：

1. 登入 Azure 企業版入口網站。
1. 在左側導覽列中選取 [報告]。
1. 選取 [使用量摘要] 索引標籤。
1. 選取日期範圍。
1. 選擇要檢視的帳戶或訂用帳戶。
1. 您可以選擇性地執行下列動作：
   - 將檢視變更為 [依服務收費] 或 [依階層收費]，以顯示不同的明細。
   - 檢視服務名稱、測量單位、使用單位、生效費率和延伸成本的詳細資料。

## <a name="download-csv-reports"></a>下載 CSV 報表

企業系統管理員可以使用每月報表下載頁面，將數個報表下載為 CSV 檔案。 可下載的報表包括：

- 餘額和費用報表
- 使用量詳細資料報表
- Azure Marketplace 費用報表
- 價位表

### <a name="to-download-reports"></a>若要下載報表：

1. 在 Azure 企業版入口網站中，選取 [報表]。
1. 從頂端功能區選取 [使用量下載]。
1. 選取適當月份報表旁的 [下載]。

### <a name="csv-report-formatting-issues"></a>CSV 報表格式問題

在 Azure 企業版入口網站中以歐元檢視 CSV 報表的客戶，可能會遇到關於逗點和句點的格式問題。

例如，您可能會看到：

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| 小時 | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

您應該會看到：

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| 小時 | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

發生此格式問題的原因是 Excel 匯入功能中的預設設定。 Excel 會將所有欄位匯入為「一般」文字，並假設數字會以數學標準分隔。 例如："1,000.00"。

如果歐洲貨幣以句點 (.) 作為千位分隔符號，並以逗點作為小數點分隔符號 (,)，將會無法正確顯示。 例如："1.000,00"。 此匯入結果可能會隨著您的區域語言設定而不同。

### <a name="to-import-the-csv-file-without-formatting-issues"></a>若要匯入 CSV 檔案而不發生格式問題：

1. 在 Microsoft Excel 中，移至 [檔案] > [開啟]。
   「文字匯入精靈」隨即出現。
1. 在 [原始資料類型] 底下，選擇 [分隔符號]。  預設值為「固定寬度」。
1. 選取 [下一步] 。
1. 在 [分隔符號] 底下，選取 [逗點] 的核取方塊。 如果已選取 [Tab]，請將其清除。
1. 選取 [下一步] 。
1. 捲動至 [ResourceRate] 和 [ExtendedCost] 資料行。
1. 選取 [ResourceRate] 資料行。 其會顯示為黑色。
1. 在 [資料行資料格式] 區段底下，選取 [文字] (而非 [一般])。 資料行標頭會從 [一般] 變更為 [文字]。
1. 針對 [延伸成本] 資料行重複步驟 8 和 9，然後選取 [完成]。

> [!TIP]
> 如果您已設定為在 Excel 自動開啟 CSV 檔案，則必須在 Excel 中改用「開啟」功能。 在 Excel 中，移至 [檔案] > [開啟]。

## <a name="balance-and-charge-report"></a>餘額和費用報表

「餘額與費用報表」可提供餘額、新購買、Azure Marketplace 服務費用、調整，以及超額部分費用的每月摘要資訊。

Azure 服務承諾用量摘要資料表中的所有資料列每個月都會保持不變。 您可以在 [新購買詳細資料] 和 [調整詳細資料] 區段下找到所有購買和調整的詳細資料。

### <a name="download-the-balance-and-charge-report"></a>下載餘額和費用報表

1. 以企業系統管理員身分登入 Azure 企業版入口網站。
1. 在左窗格中選取 [報告]。
1. 選取 [報表下載] 索引標籤。
1. 在 [餘額和費用] 資料行底下選取適當的月份，然後選取以下載報表。

## <a name="usage-detail-report"></a>使用量詳細資料報表

使用量詳細資料報表會提供註冊所用服務和數量的每月摘要。 其中包括計量名稱、計量類型和已使用數量的資訊。

### <a name="download-the-usage-detail-report"></a>下載使用量詳細資料報表

1. 以企業系統管理員身分登入 Azure 企業版入口網站。
1. 在左側導覽列中選取 [報告]。
1. 選取 [下載使用量] 索引標籤。
1. 在 [使用量詳細資料] 資料行底下選取適當的月份，然後選取以下載報表。

## <a name="azure-marketplace-charges-in-azure-enterprise-portal-reports"></a>Azure 企業版入口網站報表中的 Azure Marketplace 費用

Azure Marketplace 費用有兩種類型：

- **依使用量：** 依交易單位測量產品。  例如，虛擬機器會按小時計費，Bing API 搜尋會依搜尋次數計費。
- **月費**：根據使用量或存取按月計費。

如需 Azure Marketplace 費用的詳細資訊，請參閱 [Azure Marketplace 常見問題集](https://azure.microsoft.com/marketplace/faq/)。

若要在 Azure 企業版入口網站中檢視不同的費用：

- **使用量摘要報表**：**同時**顯示依使用量和月費計算的 Azure Marketplace 費用。
- **Marketplace 費用報表**：**僅**顯示以使用量計算的 Azure Marketplace 費用。  一次性費用不會顯示。

> [!NOTE]
> Azure Marketplace 不適用於 MPSA 註冊。

## <a name="advanced-report-download"></a>進階報表下載

如需報告特定的日期範圍或帳戶，您可以使用進階報表下載。 自 2016 年 8 月 30 日起，輸出檔案的格式已變更為 CSV，可容納較大的記錄集。

1. 在 Azure 企業版入口網站中，選取 [進階報告下載]。
1. 選取 [適當日期範圍]。
1. 選取 [適當帳戶]。
1. 選取 [要求使用量資料]。
1. 選取 [重新整理] 按鈕，直到報表狀態更新為 [下載] 為止。
1. 下載報表。

## <a name="reporting-for-non-enterprise-administrators"></a>非企業系統管理員的報告

企業系統管理員可授與部門系統管理員 (DA) 和帳戶擁有者 (AO) 在註冊上檢視費用的權限。 具有存取權的帳戶擁有者可以下載其帳戶和訂用帳戶專屬的 CSV 報表。 他們也可以在 Azure 企業版入口網站的 [報表] 區段底下，透過視覺化方式查看資訊。

### <a name="to-enable-access"></a>若要啟用存取權：

 1. 以企業系統管理員身分登入 Azure 企業版入口網站。
 1. 選取左側導覽列上的 [管理]。
 1. 選取 [註冊] 索引標籤。
 1. 在 [註冊詳細資料] 區段底下，選取 [DA 檢視費用] 或 [AO 檢視費用] 旁的鉛筆圖示。
 1. 選取 [啟用] 。
 1. 選取 [儲存]。

### <a name="to-view-reports"></a>若要檢視報表：

1. 以部門系統管理員或帳戶擁有者的身分登入 Azure 企業版入口網站。
1. 在左側導覽列中選取 [報告]。
1. 選取 [使用量摘要] 索引標籤，以視覺化方式檢視帳戶和訂用帳戶的資訊。
1. 選取 [使用量下載] 以檢視 CSV 報表。

在使用「進階報表下載」功能時，部門系統管理員和帳戶擁有者無法檢視費用。 成本會顯示為 $0。

可檢視費用的帳戶擁有者權限範圍包括帳戶擁有者，以及所有具有相關訂用帳戶權限的使用者。 如果您是間接客戶，則必須由通路合作夥伴為您啟用成本功能。

## <a name="power-bi-reporting"></a>Power BI 報告

Power BI 報告適用於能夠檢視帳單資訊的 EA 直接、合作夥伴和間接客戶。

### <a name="power-bi-pro"></a>Power BI Pro

Power BI Pro 已可供 EA 客戶使用。 您可以使用 Power BI Pro 來產生和共用報表，以有效率地管理成本資料。 此外，還有其他共同作業和資料重新整理功能可供使用。 Power BI Pro 提供了更高的資料容量和資料串流限制。

<!--We plan to add new cost management features for Azure Enterprise customers.

Current Power BI (free) users who use the Microsoft Azure Consumption Insights content pack can get a 60-day free trial of Power BI Pro. After the trial is over, you can continue using Power BI Pro by adding a license.

To sign up for the free Power BI Pro trial:

1. From the gear icon in Power BI, select **Manage personal storage**.
1. Select **Try Pro for free** on the right.

See [Power BI self-service sign up](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial) for more information on the Power BI Pro free trial.

### Azure EA Power BI Pro trial terms

- **General purpose**: The extended Power BI Pro for the "Microsoft Azure Enterprise" content pack trial offer (the "Offer") is available to existing qualified users during the term of the Offer, to allow them to access certain insights related to their Azure consumption through the use of a specific Power BI content pack.
- **Eligibility**: Users under an Enterprise Agreement (EA) can participate in the Offer if they have a function related to their organization's Azure billing, service, or cost management.
- **Exclusions**:
  - Users already participating in the Extended Power BI Pro trial will continue to qualify under the pre-existing offer and can't enter into the Azure EA Power BI Pro trial offer.
  - Users participating in the Offer can only use Power BI Pro with the Microsoft Azure Enterprise content pack. Any other use of Power BI Pro is prohibited.
  - Term: The Offer began on June 1, 2017 and ended on May 31, 2018.  Acceptance can occur at any time during the 12-month period, though the offer will terminate on May 31, 2018 for all users regardless of when they accepted the Offer.
  -->

### <a name="to-access-microsoft-azure-consumption-insights"></a>若要存取 Microsoft Azure 使用量見解：

1. 移至 [Microsoft Azure 使用量見解](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)。
1. 選取 [立即取得]。
1. 提供註冊號碼和月份數，然後選取 [下一步]。
1. 提供您的 API 存取金鑰以進行連線。 您可以在[企業版入口網站](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)中找到註冊的金鑰。
1. 選取 [登入] 以自動啟動匯入程序。
1. 完成時，新的儀表板、報表和模型將會出現在導覽窗格中。 選取儀表板即可檢視匯入的資料。

> [!TIP]
>
> - 若要了解如何為您的註冊產生 API 金鑰，請參閱[企業版入口網站](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)上的 API 報表說明檔。
> - 如需如何將 Power BI 連線到您 Azure 使用量的詳細資訊，請參閱 [Microsoft Azure 使用量見解](/power-bi/desktop-connect-azure-cost-management)。

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>若要存取舊版 Power BI EA 內容套件：

1. 移至 [Power BI 網站](https://app.powerbi.com/getdata/services/azure-enterprise)。
1. 使用有效的公司或學校帳戶登入。

   公司或學校帳戶可以是您用於透過 Azure 企業版入口網站存取註冊的相同或不同帳戶。
1. 在服務的儀表板上選取 [Microsoft Azure 企業版]，然後選取 [連線]。
1. 在 [連線到 Azure 企業版] 畫面上，填入下列欄位：
    - Azure 環境 URL：[https://ea.azure.com](https://ea.azure.com/)
    - 月份數：1 到 36
    - 註冊號碼：您的註冊號碼
1. 選取 [下一步] 。
1. 在 [驗證金鑰] 方塊上，輸入 API 金鑰。

    您可以在 Azure 企業版入口網站的 [下載使用量] 索引標籤下取得 API 金鑰。選取 [API 存取金鑰]，然後將金鑰貼到 [帳戶金鑰] 方塊中。
1. 資料需要約 5-30 分鐘的時間才能載入至 Power BI，視資料集的大小而定。

## <a name="reports-faq"></a>報表常見問題集

本節將說明有關報表的常見問題。

### <a name="why-is-my-cost-showing-as-0"></a>為何我的成本顯示為 $0？

針對**直接註冊**客戶，企業系統管理員可以將使用量報告成本/定價資訊的存取權提供給帳戶擁有者和部門系統管理員。 請遵循下列步驟：

1. 在 Azure 企業版入口網站中，選取左側導覽區中的 [管理]。
1. 選取 DA (部門系統管理員) 檢視費用旁的藍色鉛筆。
1. 選取 [啟用] 並儲存。
1. 選取 AO (帳戶擁有者) 檢視費用旁的藍色鉛筆。
1. 選取 [啟用] 並儲存。

> [!NOTE]
> 如果您是帳戶擁有者或部門系統管理員，請洽詢企業系統管理員以啟用定價功能。

針對**間接註冊**的客戶，請向您的合作夥伴確認他們已為您啟用定價功能。 這只能由合作夥伴完成。 啟用之後，您就能以企業系統管理員的身分查看註冊的成本和價格。

想要為帳戶擁有者或部門系統管理員啟用 [檢視費用] 功能的合作夥伴，請遵循**直接註冊**底下的步驟。

### <a name="why-is-there-no-sku-information-on-my-usage-detail-report"></a>使用量詳細資料報表上為什麼沒有任何 SKU 資訊？

使用量詳細資料報表不包含任何 SKU 資訊。 不過，此報表會包含使用量資訊，因此您可以下載價位表報表來取得 SKU 資訊。

### <a name="why-doesnt-the-total-amount-on-azure-marketplace-match-the-reports-for-usage-summary-and-detail"></a>為什麼 Azure Marketplace 的總金額不符合使用量摘要和詳細資料的報表？

Azure Marketplace 費用報表只會顯示依使用量計算的 Marketplace 費用。 一次性費用不會顯示。 請查看使用量摘要頁面，以取得依使用量計算的費用和一次性費用的最新使用量。

### <a name="why-is-there-no-information-on-my-api-report"></a>為什麼我的 API 報表上沒有任何資訊？

API 金鑰每六個月就會過期。 如果您遇到問題，企業系統管理員應該會產生新的 API 金鑰。 請記得遵循 API 報表常見問題集中的步驟。

### <a name="why-isnt-my-power-bi-report-working"></a>為什麼我的 Power BI 報表無法運作？

針對 Power BI 的問題，請向 [Power BI 支援小組](https://support.powerbi.com)登記票證。

### <a name="why-dont-my-resource-tags-show-on-my-reports"></a>為什麼我的資源標籤不會顯示在我的報表中

資源標籤可從 Azure 入口網站管理。 您可以在 [Azure 入口網站](https://portal.azure.com)中連絡 Azure 訂用帳戶小組。 請依照[如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)一文中的步驟進行。

### <a name="why-does-my-resource-rate-change-every-day"></a>為何我的資源費率每天都會變更？

詳細使用量報告中所顯示的資源速率是計算值。 其代表服務收取的每月平均費率。 資源費率會以服務單位的每月承諾用量和每月超額費用的平均值來計算。 根據承諾用量和超額費率計費的使用量部分，將會持續變更到該月結束的當天為止。 因此，列出的資源費率也會在該月份期間變更。 資源費率會在當月結束後的第五天鎖定。

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>資源費率計算程序的詞彙

- **原始單位總計：** 詳細使用量報表中的已使用數量。
- **每單位的 MOCP 資源：** 上游使用量系統會以不同的單位發出每項服務的使用量。 (預設)
- **每單位使用量：** Azure 企業版測量單位。 (預設)
- **價格：** Azure 企業版入口網站中的單價。
- **總成本：** 詳細使用量報表中的延伸成本，或 Azure 企業版入口網站中已使用的承諾用量加上超額部分。

### <a name="charges-calculations"></a>費用計算

- **轉換成已使用的 MOCP 資源** = `ROUND(Total RAW Units * MOCP Resource Per Unit,4)`
- **轉換成使用的單位** = `Consumed MOCP Resources / Consumption per Unit`
- **計算總成本** = `Consumed Units * Price`

### <a name="logic-in-the-usage-calculation-logic"></a>使用量計算邏輯中的邏輯

**資源費率** = `Total Cost /(Total RAW Units / MOCP Resource Per Unit)`

資源費率會根據您的費用來衍生。 可能不符合價位表中的實際單價。

在 [下載使用量資料] 報表中，您可以看到原始資源使用量最多有六個小數位數。 此資料用於計算超額費用。 不過，Azure 企業版入口網站中顯示的使用量資料會捨入至四個小數位數作為承諾用量單位，並將超額單位截斷為零個小數位數。 在 Azure 企業版入口網站底下，所有超額使用量都只會以完整單位收費。 您可能會發現，以超額部分或混合月份計費的使用量，在單價和資源費率之間有很大的差異。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 檢閱已開立發票的費用
> * 檢閱服務的超額費用
> * 檢閱 Marketplace 發票

請繼續閱讀下一篇文章，以深入了解如何使用 Azure EA 入口網站。

> [!div class="nextstepaction"]
> [開始使用 Azure EA 入口網站](../manage/ea-portal-get-started.md)